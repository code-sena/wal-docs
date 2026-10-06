# Architecture, Panoramic View — Alerta Mujer

**What talks to what, through where, and with what permission.** A single document to hold the
whole system in your head before opening any other.

- **Date:** 2026-10-06
- **Scope:** the shape of the system and the communication between `backend/Backend-ALERTA-MUJER`,
  `web/Frond-end-Web/alerta-mujer-web`, `movile/Frontend-movil` and `wal-docs`.
- **Where each statement comes from:** `docker-compose.yml`, `application.properties` and
  `wal-docs/05-architecture`. Documentation rules intent; code rules what is observable.

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

- **Mobile app** (`movile/Frontend-movil`) — React Native 0.81 + Expo 54, MVVM architecture per
  feature. The main channel: panic button, contacts, map, history.
- **Web** (`web/Frond-end-Web/alerta-mujer-web`) — Angular 21, SCSS/Tailwind/Bootstrap, Leaflet.
  Public landing, user portal, and admin panel.
- **Backend** (`backend/Backend-ALERTA-MUJER/backend-monolith`) — Spring Boot 3.2, Java 17,
  Spring Security + JWT, Spring Data JPA. Port **8080**.
- **PostgreSQL 16** — database `alerta_mujer`, schema loaded by `db/init.sql`, host **5433** via
  Docker.

Flow: both apps talk **only to the backend over HTTP** (`http://localhost:8080` on web; injectable
`EXPO_PUBLIC_API_URL` on mobile), with `Authorization: Bearer <jwt>` set by an interceptor
(`jwt.interceptor.ts` on web, the axios `api.ts` on mobile). The backend is the only one that
touches the database. The database is **never** exposed to the clients.

Planned external services (only the backend calls them): **Firebase Cloud Messaging** (push), SMS
provider (Twilio) and email service.

---

## 2. Target shape vs. real shape

Here lives the most important structural decision of the system, and it must be stated honestly:

- **Target (ADR-002, documented in `wal-docs`):** four Spring Boot microservices with hexagonal
  architecture — `identity-service` (:3001), `alert-service` (:3002), `resource-service` (:3003),
  `admin-service` (:3004) — behind a `gateway` that validates JWT, with per-service logical table
  ownership over a shared PostgreSQL.
- **Reality today (code):** a **monolith** (`backend-monolith`, :8080) that implements all domains
  with the same internal silhouette (`identity`, `alerts`, `devices`, `evidence`, `zones`,
  `resources`, `admin`, `infrastructure`), and the `am-*` services exist as skeleton (only
  `am-identity-service` has functional duplicated code). The `gateway/` routes, but in practice
  clients point at the monolith.

Practical implication: **everything new is documented against the monolith contract** ([
`api-contract.md`](api-contract.md)), which is what web and mobile really consume, and the OpenAPI
contracts in `contracts/openapi/` are the promise the four services should fulfill when split.

---

## 3. What crosses each boundary

| Hop | How it travels | Who authorizes | What it carries |
|---|---|---|---|
| Mobile → backend | HTTP/HTTPS to `:8080`, axios + interceptor | JWT in header | JSON (alerts, contacts, location…) |
| Web → backend | HTTP to `:8080`, HttpClient + interceptor | JWT in header | JSON |
| Backend → Postgres | TCP 5432 (inside Docker), JPA/Hibernate | `DATABASE_URL` connection string | SQL |
| Docker host → Postgres | TCP `:5433`, **development only** | `POSTGRES_USER/PASSWORD` | psql / tools |
| Backend → FCM/SMS/email | Outbound HTTPS | Keys in environment | Push, SMS, email |

Three things the endpoint list does not teach:

- **The token is set by an interceptor on each client.** No screen or component knows the header
  exists; 401/403 are translated into redirect to login (web: `role.guard.ts`).
- **`ddl-auto=none`.** The backend does **not** create or alter the schema: `db/init.sql` does it
  when the volume is created. An entity change without a SQL migration does not manifest (and that
  is where debt D3 of [`data-model.md`](data-model.md) is born).
- **Open CORS (`*`) in development.** Web (`localhost:4200`) and Expo do not cross origin against
  the same host in production; the value is parameterized via `CORS_ORIGIN`.

---

## 4. The same silhouette on both sides of the backend

The monolith repeats, per domain, the same cut:

```
<domain>/
  domain/model/          ← JPA entities (business rules)
  application/service/   ← use cases
  infrastructure/
    controller/          ← REST adapter
    repository/          ← JPA adapter
```

This is not decorative symmetry: it allows lifting the `identity` slice without touching `alerts`,
testing an alert rule without starting the server, and is the natural previous step to splitting
`am-identity-service`, `am-alert-service`, etc. The risk to watch: an infrastructure `new` showing
up outside its layer and silently breaking the hexagon — same as in ADR-002's target.

---

## 5. The dependency rule

Inside each domain the arrow goes once: `controller → service → repository → model`, and the model
**does not** know Spring Data or the database. That is what makes the architecture checkable: it is
verified by opening the packages/`pom.xml`, not by trusting anyone's discipline. Mobile repeats the
same discipline with MVVM per feature (`view` / `viewModel` / `models`), and web with
`core/services` + `features/` + `shared/`.

---

## 6. Startup, where order matters

`docker-compose.yml` chains the two gates by condition, not by order:

1. `postgres` starts and only when `pg_isready` answers (healthcheck)…
2. …`backend` starts, requires `DATABASE_URL` reachable and `JWT_SECRET` present, applies
   `ddl-auto=none` (does not touch the schema) and listens on `:8080`.
3. Web (`ng serve`) and mobile (Expo) start separately and point at `:8080`.

Two details are worth it by themselves:

- **Without `JWT_SECRET` the backend should not sign.** The compose ships a development default;
  in any real environment it must be overridden in `.env`, because a published default hands out
  forgeable tokens.
- **`db/init.sql` only runs with an empty volume.** Recreating the schema requires deleting the
  `postgres_data` volume; to evolve without deleting, the changelog is needed (debt D3).

Mixing startups (starting the monolith by hand and the compose at the same time) recreates
containers and drops published ports without warning.

---

## 7. Divergence found while drawing this

`wal-docs/05-architecture/overview.md` §4 describes **four services** and per-service table
ownership; `wal-docs/09-microservices/service-catalog.md` still speaks of `auth-service :3001`; the
real code is a monolith on `:8080` with four schemas in a single database. It is not force-fixed:
it is declared as debt A5/D4 in [`api-contract.md`](api-contract.md) and
[`data-model.md`](data-model.md), and this document governs the real anatomy of today.

## Correlations

- Real endpoints → [`api-contract.md`](api-contract.md)
- Tables and rules → [`data-model.md`](data-model.md)
- Decisions and patterns → `wal-docs/05-architecture/`
- How to bring everything up → root of `backend/Backend-ALERTA-MUJER` (`COMO_INICIAR.md`)
