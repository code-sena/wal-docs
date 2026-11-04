# ADR-002: Architectural Style

## Context

The team is three developers building this as a SENA training project (group 3145556), with one
shared PostgreSQL database modeled in `06-data/models.md`. The backend started as a modular
monolith; earlier versions of the documentation described it that way. During October 2026 the
team split it into microservices and moved every module to a hexagonal structure. The technical
audit of 20/10/2026 and its update of 24/10/2026 confirmed the result, and on 25/10/2026 the old
monolith (`backend-monolith/`), the gateway and the previous `am-*` services were deleted from the
repository. This ADR records the style that is actually deployed in `main`.

---

## Evaluated alternatives

### Alternative A — Modular Monolith with Hexagonal Architecture
- **Pros:** One Spring Boot application to build, test, and deploy; one shared PostgreSQL
  database with clear per-module table ownership; no network hop between modules; each bounded
  context still gets clean internal boundaries through hexagonal ports and adapters.
- **Cons:** All modules scale and fail together; a slow evidence upload or a scheduled task shares
  the same process as login and alert creation; on the free hosting plan a single large process
  also takes longer to start.

### Alternative B — Three Microservices with Hexagonal Architecture (CHOSEN)
- **Pros:** Identity, alerts and evidence are deployed and restarted independently; evidence
  uploads (up to 50 MB) do not compete with the alert path; each service owns its schemas; the
  `*Api` contracts let a module be called locally or remotely without changing the use case.
- **Cons:** Internal HTTP calls and a shared internal key to protect; three cold starts on the
  free Render plan instead of one; the routing by path is repeated in the mobile app and in the
  web because there is no gateway; the database is still shared, so services are not fully
  isolated.

### Alternative C — Traditional layered monolith (no hexagonal architecture)
- **Pros:** Fastest to start; familiar controller–service–repository structure.
- **Cons:** Business rules end up depending on Spring and JPA; harder to test without a database;
  it was the starting point of the project and the team moved away from it.

---

## Decision

The backend is a Maven multi-module project with a **shared kernel** and **three deployable
microservices**, each organized with hexagonal architecture:

| Service | Modules | Responsibility | Endpoints in `main` |
|---------|---------|----------------|---------------------|
| Auth | identity, devices, settings, admin | Registration, login (JWT and Google), sessions, recovery, phones, configuration, admin panel and audit. Applies the Liquibase migrations | 51 |
| Alert | alerts, notifications, zones, resources | Alerts, location, chat, contacts, email and push notices, public tracking, trips, automatic protection, zones and help resources | 93 |
| Evidence | evidence | Photo, video and audio in Backblaze B2, SHA-256 hash and 30-day retention | 14 |

- **Shared kernel:** JWT security, error handling, `*Api` contracts and the RPC client.
- **Inter-service communication:** each module publishes a contract (`IdentityApi`, `AlertsApi`,
  `DevicesApi`, `SettingsApi`, `ZonesApi`, `EvidenceApi`). Inside the same service the caller gets
  the local implementation; from another service it gets `RpcClientFactory`, which calls
  `POST /internal/rpc/{Contract}/{method}`. Errors keep their type (404, 403, 400, 409).
- **Events:** Spring asynchronous events published after the transaction commits; between services
  (password recovery and new phone) they are forwarded by HTTP to `/internal/events`.
- **Internal security:** everything under `/internal/**` requires `X-Internal-Key`.
- **No API Gateway:** each client chooses the service by the start of the path. Whether to add a
  gateway and the criteria to split or merge modules (DIS-ARQ-04) are still open.
- **Database:** one PostgreSQL 16 instance; each service owns its schemas and never reads another
  service's tables.

---

## Consequences

**Positive:**
- The domain of the nine modules does not import Spring, JPA or Lombok, and use cases do not import
  adapters. `HexagonalArchitectureTest` (ArchUnit) in each service fails the build if this changes
  (since 25/10/2026).
- When each module was migrated, the same smoke test was run against the old and the new version
  (201 steps in Docker on 20/10/2026) and the HTTP codes, keys and order of the JSON were identical,
  so the clients did not need changes.
- 351 backend tests pass (shared kernel 16, Auth 114, Alert 196, Evidence 25) and run on every pull
  request with GitHub Actions.

**Negative / accepted trade-offs:**
- Three services on the free Render plan: each one sleeps after 15 minutes and the first request
  can take up to about 90 seconds. RPC calls wait up to 90 seconds and UptimeRobot pings the health
  endpoints every 5 minutes.
- The service routing is duplicated in `services/serviceRoutes.ts` (mobile) and
  `core/api/service-routes.ts` plus an interceptor (web).
- The shared database is a single point of failure and its free plan has an expiration date.
- Scheduled tasks only run while their service is awake.

---

## References

- Software Design v5.2 (DIS-ARQ-01 to DIS-ARQ-04)
- Implementation v9.1 and Technical Manual (sections 1 and 2)
- `05-architecture/overview.md`
- `05-architecture/hexagonal-architecture.md`
- `09-microservices/service-catalog.md`
- ADR-001 (documentation language)
