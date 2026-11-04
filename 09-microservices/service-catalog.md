# Microservices Catalog

> The complete inventory of the system services in `main` (28/10/2026).
> For the detail of each service, go to `09-microservices/services/NN-service-name/`.
> This catalog is the executive view — one row per service.

---

## Services map

```
  External clients: Mobile app (Expo/React Native + Kotlin), Web (Angular), contacts' browsers
                 │
                 │ HTTPS + JWT — the client picks the service by path (no gateway)
     ┌───────────┼──────────────────────────┬──────────────────────────┐
     ▼           ▼                          ▼                          │
┌──────────────────┐  ┌───────────────────────────┐  ┌──────────────────┐
│   Auth Service   │  │       Alert Service       │  │ Evidence Service │
│      :8081       │  │          :8082            │  │      :8083       │
│ identity devices │  │ alerts notifications      │  │ evidence         │
│ settings admin   │  │ zones resources           │  │                  │
└────────┬─────────┘  └─────────────┬─────────────┘  └────────┬─────────┘
         │   /internal/rpc · /internal/events (X-Internal-Key)  │
         └──────────────────────────┼───────────────────────────┘
                                    ▼
                         ┌─────────────────────┐
                         │   PostgreSQL 16     │
                         │ identity · alert ·  │
                         │ resource · admin ·  │
                         │ notification        │
                         └─────────────────────┘
   External: Firebase (FCM, Google) · Resend · Backblaze B2
```

There is no message broker: events inside a service are Spring events published after the
transaction commits, and between services they are forwarded by HTTP to `/internal/events`.

---

## Service registry

| # | Service | Port (local) | Production URL | Owner | Status |
|---|---------|--------------|----------------|-------|--------|
| 01 | Client routing (no `api-gateway`) | — | — | Mobile and web teams | Gateway removed on 25/10/2026; routing in the clients |
| 02 | `auth-service` | 8081 | `https://alerta-mujer-auth.onrender.com` | Backend | In production (`main`) — 51 endpoints |
| 03 | `alert-service` | 8082 | `https://alerta-mujer-alert.onrender.com` | Backend | In production (`main`) — 93 endpoints |
| 04 | `evidence-service` | 8083 | `https://alerta-mujer-evidence.onrender.com` | Backend | In production (`main`) — 14 endpoints |
| — | `shared-kernel` | — | — | Backend | Library used by the three services |

---

## Detail per service

### 01 — `api-gateway`

| Field | Value |
|-------|-------|
| **Status** | Not deployed. The gateway and the old monolith were deleted from the repository on 25/10/2026 |
| **What replaces it** | Each client chooses the service by the start of the path: `services/serviceRoutes.ts` (mobile) and `core/api/service-routes.ts` + interceptor (web) |
| **Where JWT is validated** | In each service, with the same key, without calling Auth |
| **Open decision** | DIS-ARQ-04: whether to add a gateway and the criteria to split or merge modules |
| **Docs** | `services/01-api-gateway/` |

### 02 — `auth-service`

| Field | Value |
|-------|-------|
| **Responsibility** | Registration, login with JWT and Google, refresh, password recovery, profile, phones and push tokens, system configuration, admin panel, moderation, reports, account deletion with two administrators, audit and admin session |
| **Modules** | `identity`, `devices`, `settings`, `admin` |
| **Contracts it exposes** | `IdentityApi`, `DevicesApi`, `SettingsApi` |
| **Contracts it consumes** | `AlertsApi`, `ZonesApi` |
| **Database** | Schemas `identity` (11 tables) and `admin` (6 tables); applies all the Liquibase migrations at startup |
| **External** | Firebase (Google token verification, push for the new-phone notice), email provider (recovery code) |
| **Tests** | 114 (JUnit 5, Mockito, ArchUnit) |
| **Docs** | `services/02-auth-service/` |

### 03 — `alert-service`

| Field | Value |
|-------|-------|
| **Responsibility** | Alerts and their lifecycle, location, chat, contacts, email and push notices, resend after 5 minutes, lost-signal notice, public tracking, trips, automatic protection, zones, zone reports, help resources and calls |
| **Modules** | `alerts`, `notifications`, `zones`, `resources` |
| **Contracts it exposes** | `AlertsApi`, `ZonesApi` |
| **Contracts it consumes** | `IdentityApi`, `DevicesApi`, `SettingsApi`, `EvidenceApi` (public tracking) |
| **Database** | Schemas `alert` (except `evidence`), `resource` and `notification` |
| **External** | Firebase Cloud Messaging, Resend (Brevo as alternative) |
| **Scheduled tasks** | Alert expiration, resend and signal check, trips and automatic protection, location retention |
| **Tests** | 196 |
| **Docs** | Detailed in `05-architecture/overview.md`, `07-api/api-contract.md` and the technical manual (no own folder in this repository yet) |

### 04 — `evidence-service`

| Field | Value |
|-------|-------|
| **Responsibility** | Upload (multipart, max. 50 MB), SHA-256 hash, storage in Backblaze B2, download, admin review and 30-day retention with a notice 5 days before |
| **Modules** | `evidence` |
| **Contracts it exposes** | None (the module implements `EvidenceApi` for Alert) |
| **Contracts it consumes** | `AlertsApi`, `IdentityApi` |
| **Database** | Table `alert.evidence` |
| **External** | Backblaze B2 (S3 API); SeaweedFS as local S3 in Docker |
| **Tests** | 25 |
| **Docs** | Detailed in `07-api/api-contract.md` §7 and the technical manual |

---

## Service communication matrix

| Caller ↓ / Called → | Auth | Alert | Evidence | Communication type |
|---------------------|------|-------|----------|--------------------|
| **Mobile / Web** | ✅ | ✅ | ✅ | HTTPS + JWT, routed by path |
| **Auth** | — | ✅ (`AlertsApi`, `ZonesApi`) | ❌ | RPC `/internal/rpc`; events to `/internal/events` |
| **Alert** | ✅ (`IdentityApi`, `DevicesApi`, `SettingsApi`) | — | ✅ (`EvidenceApi`) | RPC `/internal/rpc` |
| **Evidence** | ✅ (`IdentityApi`) | ✅ (`AlertsApi`) | — | RPC `/internal/rpc` |

All internal calls require `X-Internal-Key`, keep the error type (404, 403, 400, 409) and wait up to
90 seconds because of the cold start of the free plan.

---

## Data ownership matrix

| Data | Owner | Who else reads it (through a contract) |
|------|-------|----------------------------------------|
| Users, accounts, profiles, roles, recovery | Auth (`identity`) | Alert, Evidence (`IdentityApi`) |
| Devices, push tokens, sessions, activation settings, preferences | Auth (`identity`) | Alert (`DevicesApi`) |
| System configuration, audit, moderation, reports, account deletion | Auth (`admin`) | Alert (`SettingsApi`) |
| Alerts, notices, chat, locations, contacts, places, routes, trips, protection devices, notifications | Alert (`alert`) | Auth, Evidence (`AlertsApi`) |
| Zones, zone reports, help resources, calls | Alert (`resource`) | Auth (`ZonesApi`) |
| Tracking links and visits | Alert (`notification`) | — |
| Evidence files | Evidence (`alert.evidence`) | Alert (`EvidenceApi`) |

---

## How to add a new service

1. Write an ADR that explains why the module needs its own deployment.
2. Create the Maven module under `microservicios/` depending on `shared-kernel`.
3. Keep the hexagonal structure and add `HexagonalArchitectureTest`.
4. Expose what other services need through a `*Api` contract; callers get an RPC client.
5. Add its `Dockerfile`, its entry in `docker-compose.yml` and `render.yaml`, and its URL variable.
6. Update the routing by path in the mobile app and the web.
7. Add it to this catalog and create its folder in `services/`.

---

## Correlations

- Architecture overview → `05-architecture/overview.md`
- Architectural style → `05-architecture/decisions/records/ADR-002-architectural-style.md`
- API contract → `07-api/api-contract.md`
- Data models → `06-data/models.md`
