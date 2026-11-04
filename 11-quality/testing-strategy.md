# Testing Strategy

> Defines what, how much, and with which tools Woman Alert is tested at each layer.
> Complements the TDD guide (`tdd-guide.md`) that explains the process.
> This document is the project's quality contract. Figures as of 27–28/10/2026 (Test Plan v7.1).

---

## Project testing pyramid

```
                 /\
                /  \
               /    \
              /  E2E  \     ← None automated; manual tests on Android phones (PM-04 to PM-09 pending)
             /         \
            /────────────\
           /  Integration  \  ← Adapters (JWT, email, PDF/CSV exporter, RPC client) and smoke tests
          /                 \     in Docker (201 steps, 20/10/2026)
         /──────────────────\
        /   Contract Tests    \  ← JSON compared before and after each hexagonal migration
       /                       \
      /────────────────────────\
     /       Unit Tests         \  ← 553 automated tests: backend 351, web 39, mobile 163
    /                            \
   /────────────────────────────\
```

**Rule:** More tests at the bottom = more maintainable system at lower cost. The weak point today is
the top of the pyramid: the complete SOS flow on the phone is only tested by hand.

---

## Tier 1: Unit Tests

**Objective:** Test business logic in complete isolation.

| Aspect | Value |
|--------|-------|
| **Backend** | JUnit 5 + Mockito: domain models without Spring or database, use cases with mocked ports. 351 tests (shared-kernel 16, Auth 114, Alert 196, Evidence 25) |
| **Architecture** | `HexagonalArchitectureTest` (ArchUnit) in each service: fails if the domain depends on the application, adapters, Spring, JPA or Lombok, or a use case depends on an adapter |
| **Web** | Vitest 4 + jsdom: components, continuous evidence player and some services. 39 tests |
| **Mobile** | Jest 29, jest-expo 54, React Native Testing Library 13: shake detection and manufacturer gestures, protection service, resuming an alert, password recovery, recording upload queue, permissions and protection health, SOS alarm and its silencing, stopping automatic protection with PIN, trip, chat, HTTP client (401 and refresh), devices, geocoding, location queue, routes, routing to microservices and token storage. 163 tests + `tsc --noEmit` with 0 errors |
| **Speed** | The three suites run in a few minutes in GitHub Actions |

---

## Tier 2: Integration Tests

**Objective:** Test the adapters against real or simulated infrastructure.

| Aspect | Value |
|--------|-------|
| **Adapters covered** | JWT, email, PDF/CSV exporter, RPC client, trips and automatic protection |
| **Smoke tests in Docker (20/10/2026)** | Auth 33 steps, devices and configuration 35, admin panel 54, Alert 30, notifications 15, zones and resources 34 — all identical before and after the migration |
| **Emails** | Alert, end and recovery emails compared byte by byte before and after: identical |
| **Not covered** | Web guards and interceptors; controllers with role security |
| **Pending** | Save the smoke tests in the repository (`scripts/smoke/`) to reuse them |

---

## Tier 3: Contract Tests (Consumer-Driven Contract Testing)

There is no consumer-driven contract tool (Pact or similar). The contract protection used so far:

- Each module migration compared HTTP codes, keys and order of the JSON between the old and the new
  version, so web and mobile did not need changes.
- `*Api` contracts are Java interfaces in the `shared-kernel`: a breaking change fails the build of
  the caller.
- The mobile tests cover the routing of each path to the right microservice.

---

## Tier 4: End-to-End Tests (E2E)

No automated E2E tests. The manual plan (Test Plan v7.1) has 47 cases:

| Result | Cases |
|--------|-------|
| Approved (local, API or build only) | 12 — e.g. CP-ADM-001 to CP-ADM-006, CP-ADM-008, CP-WEB-001, CP-WEB-002, CP-SEG-002 |
| Partial | 2 — CP-ADM-007, CP-ADM-009 |
| Observed by the audit, not executed | 9 — e.g. CP-SOS-004, CP-ALE-001, CP-ACT-001, CP-EVI-001 |
| Failed in the old monolith, to revalidate | 1 — CP-SEG-003 |
| Pending | 23 — including the 8 new cases of v7.1 (widget, PIN and volume cancellation, trip, automatic protection, contact alarm, inbox, minimum version) |

Manual tests on devices: Samsung SM-A125M (Android 12) and Infinix X678B. The notices between two
phones were tested by hand in production on 23/10/2026 but the evidence is not archived yet. The
manual tests of the native features with the release APK v1.0.0 (PM-04 to PM-09) are pending.

---

## Performance Tests

Not executed. Targets defined in the SRS and still to be measured:

| Target | Requirement | Status |
|--------|-------------|--------|
| Alert dispatch to the providers in ≤ 2 s | RNF-REN-001 | Not measured |
| Battery use ≤ 10 % per hour of foreground service | RNF-REN-002 | Not measured |
| GPS accuracy 10 to 20 m | RNF-REN-003 | Not measured |
| Cold start of the backend | CP-REN-001 | Known: up to about 90 s on the free plan; no systematic measurement |

---

## CI configuration per pipeline

```yaml
# .github/workflows/ci.yml (simplified, one per repository)
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      # Backend
      - run: mvn -B -ntp test
        working-directory: microservicios
      # Web
      # - run: npm ci && npx ng test --watch=false && npx ng build --configuration production
      # Mobile
      # - run: npm ci && npx tsc --noEmit && npx jest
```

Green in the three repositories since 28/10/2026. A pull request is merged only with the pipeline in
green.

---

## Code coverage — Thresholds per layer

Coverage is not measured yet. Proposed thresholds for when it is enabled:

| Layer | Proposed threshold | Reason |
|-------|--------------------|--------|
| Backend domain | 80 % | Business rules of the alert path |
| Backend use cases | 70 % | Ports are mocked |
| Adapters | 50 % | Covered partly by smoke tests |
| Mobile ViewModels and services | 60 % | Most logic of the app |
| Web services | 50 % | Few tests today |

---

## Fixture and test data files

- Liquibase loads test data only in the `dev` and `test` contexts; production only gets roles and the
  base configuration.
- Backend tests build domain objects in memory; no shared database is needed.
- No real personal data is used in tests, screenshots or shared databases (test plan rule).
- Example data in the documentation uses invented names and `example.com` emails.

---

## Correlations

- TDD process → `11-quality/tdd-guide.md`
- Traceability with requirements → `04-requirements/traceability-matrix.md`
- Non-functional requirements → `04-requirements/non-functional.md`
- CI pipeline → `10-devops/environments.md`
