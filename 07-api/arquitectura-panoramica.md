# Architecture, Panoramic View — Alerta Mujer

**What talks to what, through where, and with what permission.** A single document to hold the
whole system in your head before opening any other.

- **Date:** 2026-10-28
- **Scope:** the shape of the system and the communication between `Backend-ALERTA-MUJER`
  (`microservicios/`), `Frond-end-Web/alerta-mujer-web`, `AlertaMujer/Frontend-movil` and `wal-docs`.
- **Where each statement comes from:** `docker-compose.yml`, `render.yaml`, `application.properties`
  of each service, the technical manual and the technical audit (20/10/2026, updated 24/10/2026).
  Documentation rules intent; code rules what is observable.

**What this document deliberately leaves out:**

| Not here | It is in |
|---|---|
| The exact shape of each request and response | [`api-contract.md`](api-contract.md) |
| The data model table by table | [`data-model.md`](data-model.md) |
| The why of the microservices decision | `wal-docs/05-architecture/decisions/records/ADR-002-architectural-style.md` |
| Requirements and use cases | `wal-docs/04-requirements/` and `wal-docs/01-context/` |

> If this document ever contradicts the running code, the code wins.

---

## 1. Three clients, one backend, one database

The system has **three tips** and one heart:

- **Mobile app** (`AlertaMujer/Frontend-movil`) — Expo SDK 54, React Native 0.81, React 19.1,
  TypeScript 5.9, MVVM per feature (23 features) and a Kotlin Expo module (`modules/shake-sensor`,
  11 classes) for everything that must keep running with the app closed. The only channel that
  activates alerts.
- **Web** (`Frond-end-Web/alerta-mujer-web`) — Angular 21.2, standalone components, Bootstrap 5.3.8,
  Leaflet 1.9.4, Firebase 13. Public landing, public tracking page for contacts, user portal and
  admin panel under `/admin`.
- **Public tracking page** — part of the web, opened by contacts from the email link; no account, it
  uses the tracking code instead of a JWT.
- **Backend** (`Backend-ALERTA-MUJER/microservicios`) — Maven multi-module with `shared-kernel` and
  three Spring Boot 3.2.0 / Java 17 services: **Auth** (:8081), **Alert** (:8082), **Evidence**
  (:8083).
- **PostgreSQL 16** — one database, five schemas (`identity`, `alert`, `resource`, `admin`,
  `notification`), 35 tables, created by Liquibase when Auth starts.

Flow: both apps talk **only to the services over HTTPS** with `Authorization: Bearer <jwt>`, set by
an interceptor (`core/interceptors` on web, the single Axios client on mobile). There is no gateway:
each client picks the service by the start of the path. The services are the only ones that touch
the database. The database is **never** exposed to the clients.

External services called by the backend: **Firebase Cloud Messaging** (push), **Resend** (email by
HTTP API, Brevo as alternative; Render blocks SMTP), **Firebase / Google** (verification of the Google
login) and **Backblaze B2** (evidence). Maps are loaded by the clients (MapLibre + OpenFreeMap on
mobile, Leaflet + OpenStreetMap on web, Nominatim for addresses).

---

## 2. Target shape vs. real shape

- **Earlier target:** four services (`identity`, `alert`, `resource`, `admin`) behind a gateway that
  validated the JWT.
- **What happened:** the backend was a monolith (`backend-monolith`, :8080) with skeleton `am-*`
  services. During October 2026 every module was moved to hexagonal architecture and split into
  three services. When each module moved, the same smoke test was run against the old and the new
  version and the HTTP codes, keys and order of the JSON were identical. On 25/10/2026 the monolith,
  the gateway and the `am-*` services were deleted from the repository.
- **Reality today (`main`, 28/10/2026):** Auth (51 endpoints), Alert (93) and Evidence (14), no
  gateway, shared database with ownership by schema.

Practical implication: the contract in [`api-contract.md`](api-contract.md) is the one web and mobile
consume today, and it did not change during the split.

---

## 3. What crosses each boundary

| Hop | How it travels | Who authorizes | What it carries |
|---|---|---|---|
| Mobile → service | HTTPS, Axios (20 s timeout, refresh on 401), routed by `serviceRoutes.ts` | JWT in header | JSON; evidence as multipart |
| Web → service | HTTPS, HttpClient + interceptor, routed by `service-routes.ts` | JWT in header | JSON |
| Contact's browser → Alert | HTTPS `/api/public/tracking/{code}` | Tracking code (only its hash is stored) | Map, route, chat, recordings |
| Phone → Alert (automatic protection) | HTTPS `/api/public/auto-protection/**` | `X-Protection-Key` (device key) | Heartbeat and location |
| Service → service | HTTPS `/internal/rpc/{Contract}/{method}` and `/internal/events` | `X-Internal-Key` | Contract calls and events |
| Service → Postgres | JDBC (JPA/Hibernate) | `DB_HOST`, `DB_PORT`, `DB_NAME` and credentials | SQL |
| Alert / Auth → FCM, Resend | Outbound HTTPS | Firebase service account (Secret File), `EMAIL_API_KEY` | Push, email |
| Evidence → Backblaze B2 | Outbound HTTPS (S3 API) | Endpoint, bucket and keys | Files |

Three things the endpoint list does not teach:

- **The token is set by an interceptor on each client.** No screen knows the header exists. On the
  web, a 401 or inactivity closes the session; on mobile, a 401 triggers the refresh.
- **Liquibase owns the schema.** Only Auth applies migrations at startup; the other two services
  just connect. An entity change without a changeset does not reach the database.
- **CORS is restricted** to the Vercel domain with `CORS_ORIGIN`; the mobile app is not affected.

---

## 4. The same silhouette on both sides of the backend

Every module of the three services repeats the same hexagonal cut:

```
com.alertamujer.<module>/
  domain/model/                 ← pure models and business rules (no Spring, JPA or Lombok)
  application/port/in/          ← use cases (interfaces)
  application/port/out/         ← what the use case needs from outside
  application/service/          ← use case implementations, only know ports
  adapter/in/web|scheduler|eventos|modulo   ← REST + DTOs, scheduled tasks, events, *Api contract
  adapter/out/persistence|...   ← JPA entities (package-private), Firebase, email, storage, PDF
```

This is not decorative symmetry: it allows a module to move to another service without touching its
use cases (the `*Api` contract becomes an RPC client), testing a rule without starting the server,
and checking the structure automatically with `HexagonalArchitectureTest` (ArchUnit).

---

## 5. The dependency rule

Inside each module the arrow goes inward: adapters → application → domain. The domain does not know
Spring, JPA or the database; use cases do not know adapters, JWT, Firebase or OpenPDF. That is what
makes the architecture checkable: ArchUnit fails the build if the rule breaks. Mobile repeats the
same discipline with MVVM per feature (`view` / `viewModel` / `style`), except in the map views, and
web with `core/` + `features/` + `shared/`.

---

## 6. Startup, where order matters

`docker-compose.yml` chains the gates by condition, not by order:

1. `postgres` starts and only when it answers its healthcheck…
2. …SeaweedFS (local S3) and **Auth** start; Auth applies the Liquibase changesets and listens on
   `:8081`.
3. **Alert** (:8082) and **Evidence** (:8083) start; they need `JWT_SECRET`, `INTERNAL_API_KEY` and
   the URLs of the other services (`SERVICES_AUTH_URL`, `SERVICES_ALERT_URL`, `SERVICES_EVIDENCE_URL`).
4. Web (`npm start`, :4200) and mobile (`npm start`, development APK) start separately.

Two details are worth it by themselves:

- **Without a valid `JWT_SECRET` the services do not start.** `JwtKeyProvider` rejects an empty
  secret and the value that was once published.
- **Metro and Auth both use 8081 by default.** If they run on the same machine, accept the other port
  that Expo offers.

In production Render keeps the same order: Auth migrates the database when it starts, and the
first request to a sleeping service can take up to about 90 seconds.

---

## 7. Divergence found while drawing this

- The service routing by path lives in two places (mobile `serviceRoutes.ts`, web
  `service-routes.ts` + interceptor); adding a gateway or not is still open (DIS-ARQ-04).
- `GET /api/contacts` (all contacts, admin only) still exists and is pending removal.
- The web keeps three duplicated zone services and two evidence services.
- The OpenAPI files in `contracts/openapi/` describe the earlier gateway design and are kept as
  reference; the real contract is [`api-contract.md`](api-contract.md).

## Correlations

- Real endpoints → [`api-contract.md`](api-contract.md)
- Tables and rules → [`data-model.md`](data-model.md)
- Decisions and patterns → `wal-docs/05-architecture/`
- How to bring everything up → `wal-docs/10-devops/local-setup.md` and the installation manual
