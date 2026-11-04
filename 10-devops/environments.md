# Deployment Environments

> Environments, variables and pipeline of Woman Alert as of 28/10/2026. Real values are never written
> in the repository or in this documentation: only the variable names.

---

## Project environments

| Environment | Where | Branch | Database | Who uses it |
|-------------|-------|--------|----------|-------------|
| Local | Developer machine: Docker Compose (PostgreSQL, SeaweedFS, Auth :8081, Alert :8082, Evidence :8083), web on :4200, development APK | `feature/*`, `fix/*`, `refactor/*` | PostgreSQL in Docker with test data (`dev` context) | Each developer |
| Integration | GitHub Actions on every push and pull request | `develop`, `qa` | Tests only | Team |
| Production | Render (three services + PostgreSQL 16, free plan), Vercel (web), GitHub Releases (APK v1.0.0) | `main` | Render PostgreSQL 16 | Users, contacts, administrators |

Each repository keeps only `develop`, `qa` and `main`. Changes enter by pull request to `develop`,
are promoted to `qa` and then to `main`, which is production since 28/10/2026.

---

## Environment variables per environment

### Naming convention

- Backend: `UPPER_SNAKE_CASE` environment variables, shared through a group in `render.yaml`.
- Mobile: `EXPO_PUBLIC_*` (visible in the APK, so never secrets).
- Web: properties of `environment.ts` / `environment.prod.ts`.

| Component | Variables |
|-----------|-----------|
| Microservices (all) | `JWT_SECRET`, `INTERNAL_API_KEY`, `CORS_ORIGIN`, `DB_HOST`, `DB_PORT`, `DB_NAME`, database credentials, `SERVICES_AUTH_URL`, `SERVICES_ALERT_URL`, `SERVICES_EVIDENCE_URL` |
| Auth and Alert | Firebase service account file (Secret File in Render) |
| Alert | `EMAIL_PROVIDER`, `EMAIL_API_KEY`, `EMAIL_FROM`, `notifications.web-base-url` (base of the tracking link) |
| Evidence | S3 storage (Backblaze B2): endpoint, bucket and keys |
| Mobile | `EXPO_PUBLIC_API_URL`, `EXPO_PUBLIC_ALERT_API_URL`, `EXPO_PUBLIC_EVIDENCE_API_URL`; `google-services.json` (FCM and Google, not committed with real data) |
| Web | `apiUrl`, `alertApiUrl`, `evidenceApiUrl` and `firebase.*` |

### Variables per environment (example values — real ones in Render, Vercel and EAS)

| Variable | Local | Production |
|----------|-------|------------|
| `SERVICES_AUTH_URL` | `http://auth-service:8081` | `https://alerta-mujer-auth.onrender.com` |
| `SERVICES_ALERT_URL` | `http://alert-service:8082` | `https://alerta-mujer-alert.onrender.com` |
| `SERVICES_EVIDENCE_URL` | `http://evidence-service:8083` | `https://alerta-mujer-evidence.onrender.com` |
| `CORS_ORIGIN` | `http://localhost:4200` | Vercel domain of the web |
| `EMAIL_PROVIDER` | empty (emails are written to the log) | `resend` |
| Evidence storage | SeaweedFS (local S3) | Backblaze B2 |
| `EXPO_PUBLIC_API_URL` | `http://<computer IP>:8081` (written by `scripts/update-env.js`; `10.0.2.2` in the emulator) | Auth production URL (`eas.json`) |

The services do not start if `JWT_SECRET` is empty or has the value that was once published.

---

## CI/CD Pipeline

| Repository | Workflow `.github/workflows/ci.yml` | Result on 28/10/2026 |
|------------|--------------------------------------|----------------------|
| Mobile | `npm ci`, `npx tsc --noEmit`, Jest | 163/163, 0 type errors |
| Web | `npm ci`, tests (Vitest), production `ng build` | 39/39, builds |
| Backend | `mvn -B -ntp test` in `microservicios/` | 351/351 |

- Runs on every push and pull request; green since 28/10/2026.
- Deployment is not done by the workflow: Render and Vercel publish automatically when `main` changes.
- The repositories keep a `Jenkinsfile`, but there is no verified Jenkins server in use.
- The APK is built locally with EAS (`eas build --local`) and uploaded to GitHub Releases.

---

## Production deploy strategy

### Available options

| Option | Used | Notes |
|--------|------|-------|
| Automatic deploy on push to `main` (Render, Vercel) | **Yes** | Each service is rebuilt from its `Dockerfile` |
| Blue/green or canary | No | Not available on the free plan |
| Manual APK release | **Yes** | v1.0.0 on GitHub Releases, linked from the landing page |
| Production AAB | No | Never executed |

### Production deploy checklist

- [ ] Pull request reviewed and CI green in `develop` and `qa`
- [ ] New Liquibase changeset added at the end of the master (if the database changes)
- [ ] No secrets, `.env` or keys in the diff
- [ ] Variables added in Render or Vercel before merging
- [ ] After the merge: three health endpoints answer `UP`
- [ ] Quick test of the affected feature in production
- [ ] If the APK changes: new version, `eas.json` with the production URLs, release published

### Rollback

```bash
# Backend: redeploy the previous commit from the Render dashboard,
# or revert the pull request so Render publishes again
git revert <merge-commit>
git push origin main

# Verify the rollback
curl https://alerta-mujer-auth.onrender.com/actuator/health

# If a migration caused the problem: never edit the applied changeset;
# write a new changeset that corrects it
```

---

## Deploy log

| Date | Change |
|------|--------|
| 23/10/2026 | Lock screen and power button activation removed; push tested between two phones in production |
| 24/10/2026 | New features frozen |
| 25/10/2026 | Monolith, gateway and old services removed; ArchUnit tests added; web translated to English, Portuguese and French |
| 27/10/2026 | Changeset 25 corrects automatic protection in production; 553 tests pass |
| 28/10/2026 | Render and Vercel publish from `main`; GitHub Actions in the three repositories; APK v1.0.0 on GitHub Releases; JWT secret rotated |

---

## Correlations

- Deployment shape → `05-architecture/deployment.md`
- Local setup → `10-devops/local-setup.md`
- Migrations → `06-data/migration-strategy.md`
