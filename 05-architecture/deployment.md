# Deployment

---

## 1. Environments

| Environment | Purpose | Status |
|-------------|---------|--------|
| Local (developer machine) | Development and manual testing | In use — `docker compose up --build` starts PostgreSQL, SeaweedFS (local S3) and the three services; the web runs with `npm start` on port 4200 and the app with a development APK |
| Integration / QA | Pull requests to `develop`, promoted to `qa`; GitHub Actions runs the tests of the three repositories | In use since 28/10/2026 |
| Production | Render (Auth, Alert, Evidence and PostgreSQL), Vercel (web), GitHub Releases (APK) | In use — published from `main` since 28/10/2026 |

Each repository keeps only the `develop`, `qa` and `main` branches. Changes enter by pull request to `develop` and are promoted to `qa` and then to `main`, which is the branch that Render and Vercel publish.

---

## 2. What gets deployed

| Component | Package format | Notes |
|-----------|----------------|-------|
| `auth-service` | Docker image built from its `Dockerfile` (Java 17, Spring Boot 3.2.0) | Render, Root Directory `microservicios`; applies the Liquibase migrations when it starts |
| `alert-service` | Docker image | Render; uses the Firebase service account (Secret File) and the email provider |
| `evidence-service` | Docker image | Render; uses Backblaze B2 through the S3 API |
| PostgreSQL 16 | Render managed database (free plan) | Migrated by Auth at startup; free plan with an expiration date |
| `alerta-mujer-web` | Angular production build (static assets) | Vercel, `vercel.json`, deploys on every push to `main` |
| Mobile app | Android APK built locally with EAS (`eas build --local`) | v1.0.0 published on GitHub Releases on 28/10/2026 and downloaded from the landing page; `eas.json` holds the URLs of the three services. A production build (AAB) has never been run |

`render.yaml` describes the three services and a group of shared environment variables.

---

## 3. Infrastructure diagram

```
  [Browser] --HTTPS--> [Vercel: Angular build]
       |                        |
       |                        +--HTTPS + JWT (routed by path)--+
       |                                                         |
  [Android APK v1.0.0] --HTTPS + JWT (routed by path)------------+
                                                                 v
              +--------------------- Render (free plan, Docker) ----------------------+
              |  alerta-mujer-auth      alerta-mujer-alert      alerta-mujer-evidence  |
              |        |   <-- /internal/rpc, /internal/events (X-Internal-Key) -->    |
              |        +-------------------+-------------------+                      |
              |                            v                                          |
              |                  Render PostgreSQL 16                                 |
              +-----------------------------------------------------------------------+
                     |                       |                        |
                     v                       v                        v
           Firebase (FCM, Google)   Resend (email API)         Backblaze B2

  UptimeRobot --GET /actuator/health every 5 min--> the three services
```

---

## 4. Containerization

- Each service has its own `Dockerfile` and `application.properties`.
- `docker-compose.yml` (backend repository root) starts PostgreSQL, SeaweedFS as local S3 storage and the three services on ports 8081 (Auth), 8082 (Alert) and 8083 (Evidence).
- In production Render builds the three images from the `microservicios` directory of the `main` branch.
- The repositories keep a `Jenkinsfile` (the backend one tests the three services and builds their images), but there is no verified Jenkins server in use; continuous integration is GitHub Actions.

---

## 5. Network configuration

| Service | Local (Docker) | Production |
|---------|----------------|------------|
| Auth | `http://localhost:8081` | `https://alerta-mujer-auth.onrender.com` |
| Alert | `http://localhost:8082` | `https://alerta-mujer-alert.onrender.com` |
| Evidence | `http://localhost:8083` | `https://alerta-mujer-evidence.onrender.com` |
| Web | `http://localhost:4200` | Vercel domain (the only allowed CORS origin) |

- There is no gateway or reverse proxy of our own: clients route by path (see `overview.md` §3).
- CORS is restricted to the web domain with `CORS_ORIGIN`; another origin receives 403.
- `/internal/**` is reachable over the public URL but requires `X-Internal-Key`; without it the answer is 403.
- The mobile app reads `EXPO_PUBLIC_API_URL`, `EXPO_PUBLIC_ALERT_API_URL` and `EXPO_PUBLIC_EVIDENCE_API_URL`; in local development `scripts/update-env.js` writes the computer's IP (or `10.0.2.2` for the emulator).
- Metro uses port 8081 by default, the same as Auth in local; Expo offers another port when both run on the same machine.
- Cleartext HTTP (`usesCleartextTraffic`) is only enabled for local development and disabled in the EAS profiles.

---

## 6. Hardware / resource requirements

No load test or capacity planning has been done. The current deployment runs on free plans:

- Each Render service sleeps after 15 minutes without traffic; the first request afterwards can take up to about 90 seconds. UptimeRobot keeps them awake with a request every 5 minutes, and the team wakes the services before each presentation.
- Scheduled tasks (expiration, resend, trips, retention) only run while the service is awake.
- The free PostgreSQL database has an expiration date.
- Evidence files are limited to 50 MB per upload (`413` above that).

Moving to a plan without sleep is the first step if the system goes beyond the academic delivery.

---

## Correlations

- Container-level diagram → `05-architecture/overview.md` §3
- Architectural style this deployment shape follows from → `ADR-002-architectural-style.md`
- Data model and database engine → `06-data/models.md`
- Environments and variables → `10-devops/environments.md`
- Local setup → `10-devops/local-setup.md`
