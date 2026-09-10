# Deployment

---

## 1. Environments

| Environment | Purpose | Status |
|-------------|---------|--------|
| Local (developer machine) | Development and manual testing | In use — each developer runs the backend, Angular dev server, and Expo bundler locally |
| Staging/QA | Not yet defined | — |
| Production | Not yet defined | — |

This project doesn't have staging or production environments provisioned yet — the SRS and
current architecture documents describe the target for the first delivered release, not a
deployment already running. This table gets updated once the team provisions infrastructure.

---

## 2. What gets deployed

Per the container diagram in `overview.md` §3, three components are deployed:

| Component | Package format | Notes |
|-----------|----------------|-------|
| `alerta-mujer-backend` | Spring Boot executable JAR (containerizable with Docker) | Runs the seven business modules described in `02-domain/domain-map.md`; connects to PostgreSQL and to FCM/SMS/email |
| `alerta-mujer-web` | Angular production build (static assets) | Served by any static host or reverse proxy in front of the backend |
| `alerta-mujer-mobile` | Android APK/AAB, built with Expo | Distributed outside app stores for the academic delivery, or via Google Play if the project goes further |

---

## 3. Infrastructure diagram

```
Replace this block once the team decides where the backend and database are hosted.

Minimum shape, given the current architecture (ADR-002, one backend + one shared database,
no API Gateway):

  [Angular build] ---static hosting--->  users (browser)
  [Android app]   ---REST over HTTPS---> [Backend container] ---> [PostgreSQL]
                                                |
                                                ---> Firebase Cloud Messaging
                                                ---> SMS provider
                                                ---> Email provider
```

---

## 4. Containerization

**Status:** not yet defined. No `Dockerfile` or `docker-compose.yml` exists in the repository
yet. When they're added:

- The backend is the natural candidate for a single `Dockerfile` (it's the only component with
  a runtime, per `overview.md` §4 — the two client apps are build artifacts, not running
  processes)
- A local `docker-compose.yml` with the backend + PostgreSQL would let any developer run the full
  stack without installing PostgreSQL directly — useful given the shared-database dependency
  already baked into `06-data/models.md`

---

## 5. Network configuration

**Status:** not yet defined — no ports, domains, or reverse-proxy configuration have been
assigned. `overview.md` §4 already flags this: the backend's port is "Not yet assigned — the
project hasn't reached deployment configuration."

Known constraints once this is defined:
- CORS must allow the Angular web app's origin to call the backend (`overview.md` §7 already
  flags this as needed but unconfigured)
- The mobile app needs the backend reachable over the public internet (or a VPN, if this stays an
  academic/internal deployment) since GPS-based alerts have to reach the backend from wherever the
  user is, not from a local network

---

## 6. Hardware / resource requirements

**Status:** not yet defined. No load testing or capacity planning has been done. Given the
academic scope (three developers, SENA ficha 3145556) and that the panic-button path is the one
flow that cannot silently fail (`overview.md` §5, P4), capacity planning should prioritize the
alert-activation → notification-fanout path over administrative/reporting endpoints once this
section is filled in.

---

## Correlations

- Container-level diagram → `05-architecture/overview.md` §3
- Architectural style this deployment shape follows from → `ADR-002-estilo-arquitectonico.md`
- Data model and database engine → `06-data/models.md`
