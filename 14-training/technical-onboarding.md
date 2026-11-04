# Technical Onboarding

> Welcome to the team. This document is your guide for the first days.
> Goal: you can make your first commit in 3 days.
> If anything in this document is unclear or outdated, fix it yourself — that is your first contribution.

---

## Day 1 — Setup and context

### Morning: Access and environment

- [ ] Get access to the three repositories (`Backend-ALERTA-MUJER`, `Frond-end-Web`, `AlertaMujer`) and to `wal-docs` (confirm with the project leader)
- [ ] Join the team group
- [ ] Ask for the Firebase service account file and `google-services.json` (never commit them)
- [ ] Configure your local environment following `10-devops/local-setup.md`
- [ ] Verify that `curl http://localhost:8081/actuator/health` (and 8082, 8083) responds `{"status":"UP"}`
- [ ] Install the APK v1.0.0 from GitHub Releases on an Android phone and try the main flow

### Afternoon: Read core documentation

Read in this order — each builds on the previous:

1. `00-sdd-guide.md` — How the team works (30 min)
2. `01-context/overview.md` — What we are building (20 min)
3. `02-domain/domain-map.md` — The business domain (30 min)
4. `05-architecture/overview.md` — How it is built (30 min)
5. `00-governance/git-conventions.md` — How we manage code (20 min)

### Day 1 meetings

- [ ] Meet & greet with the team
- [ ] 1:1 with the project leader (30 min) — project context and your responsibilities
- [ ] Product demo (if there is a recording, watch it beforehand)

---

## Day 2 — Understand the domain

### Read domain and requirements documentation

- [ ] `02-domain/entities-and-rules.md` — Entities and business rules
- [ ] `02-domain/domain-events.md` — System events
- [ ] `04-requirements/functional.md` and `04-requirements/user-stories.md` — requirements (RF-XXX-NNN) and stories
- [ ] `01-context/glossary.md` — Project terms

### Explore the code

- [ ] Read `09-microservices/service-catalog.md` and the folder of the service you will work on
- [ ] Backend: follow `com.alertamujer.<module>/domain|application|adapter` — it must match `05-architecture/hexagonal-architecture.md`
- [ ] Mobile: follow `src/features/<feature>/view|viewModel|style` and the Kotlin module `modules/shake-sensor`
- [ ] Web: follow `src/app/core|features|shared`
- [ ] Run the tests: `mvn clean test` (backend), `npx ng test --watch=false` (web), `npx tsc --noEmit && npx jest` (mobile) — all must be green
- [ ] Run the services locally and test the main endpoints (`07-api/api-contract.md`)

### Day 2 meeting

- [ ] Domain walkthrough session with the project leader (1 hour)
  - Ask them to explain the alert path: activation → 5-second window → notices → location → expiration
  - Take notes on terms you do not know — add them to the glossary

---

## Day 3 — First contribution

### Your first task

The project leader will assign you a small task labeled `good-first-issue`:
- It should be a small bug fix or documentation improvement
- The goal is to learn the workflow, not the complexity of the task

### TDD workflow for your first task

1. Read the ticket and the acceptance criteria
2. Write the test that verifies the criterion (`🔴 RED`)
3. Implement the minimum code to make the test pass (`🟢 GREEN`)
4. Refactor if necessary (`♻️ REFACTOR`)
5. Create the PR following the conventions in `00-governance/git-conventions.md`

### Checklist before opening the PR

- [ ] The tests of the repository pass locally (GitHub Actions will run them again)
- [ ] No `.env`, keys, tokens or real personal data in the diff
- [ ] The commit title follows `type(scope): description` (Conventional Commits)
- [ ] The branch is named `feature/<description>`, `fix/<description>` or `refactor/<description>` and the pull request targets `develop`

---

## Week 1 — Go deeper

| Day | Activity |
|-----|----------|
| 4 | Code review (participate in the team's code review — observe first) |
| 5 | Report your progress in the team group with something concrete |
| 5 | Read `05-architecture/pattern-guide.md` — the patterns we use |
| 5 | Read `11-quality/tdd-guide.md` and `11-quality/testing-strategy.md` in full |

---

## Week 2 — Guided independence

- [ ] Complete your first HU independently
- [ ] Actively participate in a code review
- [ ] Read `07-api/api-contract.md` and the OpenAPI file of the Auth service
- [ ] Record what you learned or fixed in the documentation

---

## Architecture: The 5 concepts you must understand first

Before writing code, understand these 5 concepts from the project's architecture:

### 1. Bounded Contexts
Three services group the seven bounded contexts: Auth (identity, administration), Alert (alerts, contacts, notifications, zones and resources) and Evidence.
See: `02-domain/domain-map.md`

### 2. Hexagonal Architecture
The domain is the center. Nothing from the framework enters the domain.
See: `05-architecture/hexagonal-architecture.md`

### 3. Ports and Adapters
Interfaces live in the domain. Implementations in infrastructure.
See: `05-architecture/hexagonal-architecture.md`

### 4. The flow of a request
```
App (Axios + JWT, routed by path)
  → AlertController (adapter/in/web, request DTO)
  → CreateAlertUseCase (application/port/in) → service (application/service)
  → Alert (domain/model — one active alert per user, state transitions)
  → AlertRepositoryPort (application/port/out) → JPA adapter (adapter/out/persistence)
  → PostgreSQL
  → event after commit → notices by email (Resend) and push (FCM)
```

### 5. Domain events
Inside a service, modules talk with Spring events published after the commit; between services, with `*Api` contracts over RPC and events forwarded to `/internal/events`.
See: `02-domain/domain-events.md`

---

## Frequently asked questions (FAQ)

**Why not `console.log` in the mobile app?**
Because it can expose personal data in the logs of a phone. Logs must stay inside `__DEV__`.

**Can I commit directly to `main`?**
No. Everything goes through a pull request to `develop`, then `qa`, then `main`; `main` is production (Render and Vercel).

**Can I change the database schema directly?**
No. Every change is a new Liquibase changeset at the end of the master in `auth-service`. Never edit an applied one. See `06-data/migration-strategy.md`.

**How do I know if my API change breaks consumers?**
There are no contract tests yet. Compare the JSON before and after your change (same keys, same order), and remember that web and mobile route by path.

**Where do I ask for help if I am stuck?**
1. Search this documentation first
2. Ask in the team group
3. Do not wait more than 1 hour before asking for help — the team's time is valuable

**What do I do if I find incorrect or outdated documentation?**
Fix it and open a PR. Documentation is code.

---

## Additional resources

| Resource | URL / Location | Purpose |
|----------|---------------|---------|
| Git guide | `00-governance/git-conventions.md` | Commit and branch conventions |
| Project ADRs | `05-architecture/decisions/` | Understand decisions made and why |
| Service runbook | `09-microservices/services/[your-service]/runbook.md` | Operate and troubleshoot |
| Technical, installation and user manuals | Final delivery documents | Full detail with screenshots |
| Spring Boot, Liquibase, Angular, React Native, Expo | spring.io, docs.liquibase.com, angular.dev, reactnative.dev, docs.expo.dev | Official documentation of the stack |

---

## Correlations

- Technical setup → `10-devops/local-setup.md`
- TDD process → `11-quality/tdd-guide.md`
- Code conventions → `00-governance/git-conventions.md`
- Your service → `09-microservices/services/[name]/`
