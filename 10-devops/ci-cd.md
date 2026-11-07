# CI/CD Pipeline

> Continuous integration and continuous deployment for Alerta Mujer. GitHub Actions runs on every push to `develop`, `qa`, and `main`; Jenkins can be used for additional validation.

---

## Overview

| Branch | Trigger | Tests | Build | Deploy |
|--------|---------|-------|-------|--------|
| `develop` | Push/PR | ✅ All tests | ✅ Docker images (staging tag) | ❌ None |
| `qa` | Fast-forward from `develop` | ✅ All tests | ✅ Docker images (qa tag) | ✅ Render QA (manual approval) |
| `main` | Fast-forward from `qa` | ✅ All tests | ✅ Docker images (latest, version tag) | ✅ Render Production (automatic) |

---

## GitHub Actions Workflows

### Backend (all three microservices)

**File:** `.github/workflows/tests.yml` in `Backend-ALERTA-MUJER`

```yaml
name: Tests and Build

on:
  push:
    branches: [ develop, qa, main ]
  pull_request:
    branches: [ develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          java-version: 17
          distribution: 'temurin'
      - name: Run tests (351 tests across auth, alert, evidence)
        run: cd microservicios && mvn clean test
      - name: Build Docker images
        if: success()
        run: docker build -t alerta-mujer-auth:${{ github.sha }} ./microservicios/auth-service
```

**Tests:** 351 unit and integration tests across three microservices.

### Web Frontend

**File:** `.github/workflows/tests.yml` in `Frond-end-Web`

```yaml
name: Tests and Build

on:
  push:
    branches: [ develop, qa, main ]
  pull_request:
    branches: [ develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install dependencies
        run: cd alerta-mujer-web && npm ci
      - name: Run unit tests (39 tests)
        run: cd alerta-mujer-web && npx ng test --watch=false
      - name: Build for production
        run: cd alerta-mujer-web && npx ng build
      - name: Type check
        run: cd alerta-mujer-web && npx tsc --noEmit
```

**Tests:** 39 unit tests; builds for production after passing tests.

### Mobile App

**File:** `.github/workflows/tests.yml` in `AlertaMujer`

```yaml
name: Tests and Build

on:
  push:
    branches: [ develop, qa, main ]
  pull_request:
    branches: [ develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install dependencies
        run: cd Frontend-movil && npm ci
      - name: Type check
        run: cd Frontend-movil && npx tsc --noEmit
      - name: Run tests (163 tests)
        run: cd Frontend-movil && npx jest
```

**Tests:** 163 unit tests; type checking with TypeScript; native modules tested locally only.

---

## Deployment to Render (Backend + Database)

### Automatic Deployment on `main`

When code is pushed to `main`:

1. **GitHub Actions builds Docker images** for auth, alert, and evidence services.
2. **Images are pushed to GitHub Container Registry (GHCR)** with tag `latest` and version tag (e.g., `v1.0.1`).
3. **Render webhook** detects the push to `main` and re-deploys all three services using the latest images.

**Configuration file:** `render.yaml` (root of `Backend-ALERTA-MUJER`)

```yaml
services:
  - type: web
    name: alerta-mujer-auth
    runtime: docker
    plan: free
    buildCommand: docker build -t alerta-mujer-auth .
    startCommand: java -jar target/auth-service.jar
    envVars:
      - key: SPRING_DATASOURCE_URL
        fromDatabase:
          name: alerta-mujer-db
          property: connectionString
      - key: JWT_SECRET
        sync: false

  - type: web
    name: alerta-mujer-alert
    runtime: docker
    # ... similar config ...

  - type: web
    name: alerta-mujer-evidence
    runtime: docker
    # ... similar config ...

databases:
  - name: alerta-mujer-db
    plan: free
    databaseName: alerta_mujer
    user: app_user
```

### Manual QA Deployment

To deploy to the QA environment (branch `qa`):

1. **Manually trigger** the GitHub Actions workflow with environment `qa`.
2. **Render re-deploys** using the `qa` tag.

```bash
gh workflow run deploy.yml -f environment=qa
```

---

## Deployment to Vercel (Web Frontend)

### Automatic Deployment on `main`

When code is pushed to `main` in `Frond-end-Web`:

1. **Vercel detects the push** (webhook configured in Vercel settings).
2. **Runs `npm ci && npm run build`** to produce static assets.
3. **Deploys to production** at `https://alerta-mujer.vercel.app`.

**Configuration file:** `vercel.json`

```json
{
  "buildCommand": "cd alerta-mujer-web && npm run build",
  "outputDirectory": "alerta-mujer-web/dist",
  "env": {
    "NG_APP_API_AUTH_URL": "https://alerta-mujer-auth.onrender.com",
    "NG_APP_API_ALERT_URL": "https://alerta-mujer-alert.onrender.com",
    "NG_APP_API_EVIDENCE_URL": "https://alerta-mujer-evidence.onrender.com"
  }
}
```

---

## GitHub Releases (Mobile APK)

### Manual Release Process

1. **Create a GitHub release** on `AlertaMujer` repository with tag `v1.0.x`.
2. **Build the APK locally** with EAS:

```bash
cd Frontend-movil
eas build --local --platform android --output app-release.apk
```

3. **Upload the APK** as an asset to the GitHub release.
4. **Update the download link** on the landing page to point to the latest release.

**Current release:** [v1.0.1](https://github.com/JustinDanielaBahamon/AlertaMujer/releases/tag/v1.0.1)

**Download URL:** `https://github.com/JustinDanielaBahamon/AlertaMujer/releases/latest/download/AlertaMujer.apk`

---

## Jenkins (Optional Additional Validation)

Jenkins can be used for additional checks beyond GitHub Actions, such as security scanning, performance testing, or deployment to staging.

### Sample Jenkins Pipeline

```groovy
pipeline {
  agent any
  options {
    timeout(time: 30, unit: 'MINUTES')
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Build and Test') {
      steps {
        sh 'mvn clean test'
        sh 'docker build -t alerta-mujer-auth:${BUILD_NUMBER} .'
      }
    }
    stage('Security Scan') {
      steps {
        sh 'trivy image alerta-mujer-auth:${BUILD_NUMBER}'
      }
    }
    stage('Deploy to Staging') {
      when {
        branch 'main'
      }
      steps {
        sh 'curl -X POST https://render.com/webhooks/deploy'
      }
    }
  }
  post {
    always {
      junit '**/target/surefire-reports/*.xml'
      publishHTML([reportDir: 'target/jacoco-report', reportFiles: 'index.html', reportName: 'Code Coverage'])
    }
  }
}
```

---

## Test Coverage

| Repository | Test Type | Count | Framework |
|---|---|---|---|
| Backend | Unit + Integration | 351 | JUnit 5 + Testcontainers (PostgreSQL in Docker) |
| Web | Unit | 39 | Jasmine/Karma (Angular) |
| Mobile | Unit | 163 | Jest (React Native) |
| **Total** | | **553** | |

To run tests locally:

```bash
# Backend
cd Backend-ALERTA-MUJER/microservicios && mvn clean test

# Web
cd Frond-end-Web/alerta-mujer-web && npx ng test --watch=false

# Mobile
cd AlertaMujer/Frontend-movil && npx jest
```

---

## Branching Strategy

```
main (production, protected)
 ↑
 ↓ (fast-forward merge after approval)
qa (staging, protected)
 ↑
 ↓ (fast-forward merge after CI passes)
develop (integration, everyone pushes here)
 ↑
 ↓ (pull requests from feature branches)
feature/... (temporary branches, one per task)
```

- **No force pushes** on `develop`, `qa`, or `main`.
- **All PRs require approval** from at least one team member.
- **All tests must pass** before merging to `develop`.

---

## GitHub Secrets Used

| Secret | Used in | Purpose |
|--------|---------|---------|
| `DOCKER_REGISTRY_TOKEN` | Backend workflow | Push Docker images to GitHub Container Registry |
| `RENDER_API_KEY` | Backend/Web workflows | Trigger Render deployments |
| `FIREBASE_SERVICE_ACCOUNT` | Backend workflow (Secret File) | Auth and Alert services authenticate with Firebase |
| `BACKBLAZE_B2_KEY` | Backend workflow (Secret File) | Evidence service uploads to Backblaze B2 |

Never commit secrets. Use GitHub Secrets for all sensitive values.

---

## Troubleshooting

| Problem | Cause | Solution |
|---------|-------|----------|
| GitHub Actions stuck on "Running tests" | Docker image too large or network issue | Check the workflow logs; Docker Hub might be throttled |
| Deployment to Render fails | `JWT_SECRET` or other env var missing | Add the secret in Render dashboard under "Environment" |
| Vercel build fails with "module not found" | Dependencies not installed | Check `package-lock.json` is committed and versions are compatible |
| APK build fails locally | EAS or Expo version mismatch | Run `npm install -g eas-cli@latest` and `expo-cli@latest` |
| Tests timeout | Tests are slow or database connection hangs | Increase timeout in GitHub Actions or remove flaky tests |

---

## Correlations

- Test documentation → `05-test-documentation/`
- Deployment infrastructure → `05-architecture/deployment.md`
- Environment setup → `10-devops/environments.md`
- Local setup → `10-devops/local-setup.md`
