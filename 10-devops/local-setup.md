# Local Project Setup

> How to run Woman Alert on a developer machine: the three microservices with Docker, the Angular
> web and the Android app. The step-by-step version with screenshots is the installation manual.

---

## Prerequisites

### Universal tools (all stacks)

| Tool | Version | For what |
|------|---------|----------|
| Git | Recent | Clone the three repositories |
| Docker + Docker Compose | Recent | PostgreSQL, SeaweedFS (local S3) and the three services |
| An IDE | IntelliJ IDEA, VS Code or similar | — |

### Project language tool

| Tool | Version | For what |
|------|---------|----------|
| Java JDK / Maven | 17 / 3.9 | Microservices without Docker and tests |
| Node.js + npm | 20 or higher | Web and mobile |
| Android SDK + JDK 17 | — | Local APK (`eas build --local`) |
| EAS CLI + Expo account | 24.12 or higher | APK in the cloud |
| PostgreSQL 16 | Only without Docker | Database |

Ask the team for the Firebase service account file (Auth and Alert) and `google-services.json`
(mobile). Never commit them with real data.

---

## Local ports in use

| Port | Component |
|------|-----------|
| 5433 | PostgreSQL 16 (Docker, mapped from 5432) |
| 8081 | Auth Service |
| 8082 | Alert Service |
| 8083 | Evidence Service |
| 4200 | Angular web (`npm start`) |
| 8081 (default) | Metro bundler of Expo — same as Auth; accept the alternative port Expo offers |

Check whether a port is busy:

```bash
# Windows
netstat -ano | findstr :8081

# macOS / Linux
lsof -i :8081
```

---

## Step-by-step installation

### Step 1: Clone the repository

```bash
git clone https://github.com/JustinDanielaBahamon/Backend-ALERTA-MUJER.git Backend-ALERTA-MUJER
git clone https://github.com/JustinDanielaBahamon/Frond-end-Web.git Frond-end-Web
git clone https://github.com/JustinDanielaBahamon/AlertaMujer.git AlertaMujer
```

Create your branch from `develop`; changes enter by pull request to `develop`.

### Step 2: Environment variables

```bash
# Copy the example environment variables
cd Backend-ALERTA-MUJER
cp .env.example .env

# Edit .env and fill in the values
# (POSTGRES_PASSWORD, APP_DB_PASSWORD, JWT_SECRET and INTERNAL_API_KEY have no default value:
#  Docker Compose stops with a message if one is missing)
```

| Variable | Services | Description |
|----------|----------|-------------|
| `JWT_SECRET` | All | JWT signing key, the same in the three |
| `INTERNAL_API_KEY` | All | Key of the internal API (`X-Internal-Key`), the same in the three |
| `CORS_ORIGIN` | All | `http://localhost:4200` in local |
| `DB_HOST`, `DB_PORT`, `DB_NAME` and credentials | All | PostgreSQL connection |
| `SERVICES_AUTH_URL`, `SERVICES_ALERT_URL`, `SERVICES_EVIDENCE_URL` | All | URLs the services use to call each other |
| Firebase file | Auth and Alert | Google login and push |
| `EMAIL_PROVIDER`, `EMAIL_API_KEY`, `EMAIL_FROM` | Alert | Leave empty in development: emails are written to the log |
| `notifications.web-base-url` | Alert | Web URL used to build the tracking link |
| S3 endpoint, bucket and keys | Evidence | SeaweedFS in local |

### Step 3: Start the infrastructure

```bash
# Start PostgreSQL, SeaweedFS and the three services
docker compose up --build

# Verify they are healthy
docker compose ps
```

### Step 4: Install dependencies

```bash
# Web
cd Frond-end-Web/alerta-mujer-web
npm install

# Mobile
cd AlertaMujer/Frontend-movil
npm install
```

### Step 5: Run migrations

There is nothing to run by hand: **Auth applies the Liquibase migrations when it starts**. It creates
the schemas `identity`, `alert`, `resource`, `admin` and `notification`, the 35 tables, indexes, audit
triggers and the limited application user. Test data is loaded only in the `dev` and `test` contexts.

```bash
# Optionally: give a registered account the administrator role
psql -h localhost -p 5433 -U <user> -d <database> \
  -c "UPDATE identity.users SET role_id = 2 WHERE email = 'admin@example.com';"
# Log out and in again so the token carries the new role
```

### Step 6: Start the services

```bash
# Web (http://localhost:4200) — URLs in src/environments/environment.ts
cd Frond-end-Web/alerta-mujer-web
npm start

# Mobile — writes the computer IP into .env (10.0.2.2 for the emulator)
cd AlertaMujer/Frontend-movil
npm start
```

The app uses native modules (shake sensor, Notifee, Firebase, Google Sign-In), so it does not work in
Expo Go: install a development build or a test APK. The phone and the computer must be on the same
Wi-Fi network.

### Step 7: Verify it works

```bash
# Health check of each service
curl http://localhost:8081/actuator/health
curl http://localhost:8082/actuator/health
curl http://localhost:8083/actuator/health

# Expected response:
# {"status":"UP"}

# Security checks
curl -i http://localhost:8081/api/users/me                          # 403 without token
curl -i -X POST http://localhost:8081/internal/rpc/IdentityApi/test # 403 without internal key
```

---

## Daily workflow

```bash
# At the start of the day
git checkout develop && git pull
git checkout -b feature/my-change

# Rebuild if dependencies or migrations changed
docker compose up --build

# Run the tests before opening the pull request
cd microservicios && mvn clean test          # backend: 351 tests
npx ng test --watch=false && npx ng build    # web: 39 tests + build
npx tsc --noEmit && npx jest                 # mobile: types + 163 tests

# Logs of an installed APK
adb logcat -v time ReactNativeJS:V AndroidRuntime:E *:S
```

---

## Common issues

| Problem | Cause | Solution |
|---------|-------|----------|
| A service does not start | `JWT_SECRET` empty or with the value that was once published | Set a new long random secret in `.env` |
| Docker Compose stops at startup | A required variable is missing | Fill in `POSTGRES_PASSWORD`, `APP_DB_PASSWORD`, `JWT_SECRET`, `INTERNAL_API_KEY` |
| The app cannot reach the backend | Phone on another network or wrong IP | Same Wi-Fi; restart `npm start` so `update-env.js` writes the IP again |
| Metro and Auth fight for 8081 | Both use 8081 by default | Accept the other port Expo offers |
| Emails do not arrive in local | No email provider configured | Expected: read them in the Alert logs |
| The app closes in Expo Go | Native modules | Use a development build or an APK |

---

## Correlations

- Environments and variables → `10-devops/environments.md`
- Deployment → `05-architecture/deployment.md`
- Technical onboarding → `14-training/technical-onboarding.md`
