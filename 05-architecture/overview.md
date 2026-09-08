# System Architecture Overview

---

## 1. Adopted architectural style

**Style:** Modular Monolith, with hexagonal architecture (ports and adapters) applied inside the single backend.

**Justification:** Alerta Mujer runs as one Spring Boot backend organized by business modules (Identity, Contacts, Alerts, Evidence, Notifications, Zones, Administration — the same bounded contexts documented in `02-domain/domain-map.md`), backed by one shared PostgreSQL database (`06-data/models.md`). `ADR-001` already describes the project in terms of "a backend" and "a shared database" (singular), and `pattern-guide.md` explicitly rejects Database per Service, Saga, and CQRS for the current scope. A modular monolith gives each bounded context clear internal boundaries without the operational cost of running and coordinating separate services — appropriate for a single-team academic project with one shared datastore.

**Reference ADR:** not yet formalized as its own record — `ADR-001-idiom-documentation.md` is the only ADR the project has so far. This decision is a strong candidate for `ADR-002-architectural-style.md`.

---

## 2. C4 Diagram — System Level (Context)

```mermaid
graph TB
  USR[User<br/>emergency alerts, contacts, zones]
  ADM[Administrator<br/>users, alerts, zones, moderation]

  subgraph "Alerta Mujer"
    SYS[Alerta Mujer System]
  end

  FCM[Firebase Cloud Messaging<br/>push notifications]
  SMS[SMS provider<br/>e.g. Twilio]
  MAIL[Email provider]

  USR --> SYS
  ADM --> SYS
  SYS --> FCM
  SYS --> SMS
  SYS --> MAIL
```

The system has two human actors — the User (RF1–RF11) and the Administrator (RF13) — and three external systems it depends on to deliver an alert: Firebase Cloud Messaging for push, an SMS provider, and an email provider. There is no third-party system Alerta Mujer receives data from; all three external dependencies are outbound-only (sending, not receiving).

---

## 3. C4 Diagram — Container Level

```mermaid
graph TB
  subgraph "Alerta Mujer"
    MOB[Mobile App<br/>React Native + Expo, Android<br/>MVVM by feature]
    WEB[Web App<br/>Angular<br/>Feature-Based Architecture]
    BACK[Backend<br/>Spring Boot + Spring Security + Spring Data JPA<br/>Hexagonal architecture by business module]
    DB[(PostgreSQL<br/>shared database, versioned with Liquibase)]
  end

  FCM[Firebase Cloud Messaging]
  SMS[SMS provider]
  MAIL[Email provider]

  MOB -->|REST API| BACK
  WEB -->|REST API| BACK
  BACK --> DB
  BACK --> FCM
  BACK --> SMS
  BACK --> MAIL
```

There is no API Gateway or message bus in the current architecture — both clients talk directly to the single backend over REST, which fully decouples client from server per the SRS. The backend is the only component that talks to PostgreSQL and to the three external notification providers.

---

## 4. Service catalog

| # | Component | Responsibility | Port | DB | Communication type |
|---|-----------|----------------|------|-----|-------------------|
| 1 | alerta-mujer-backend | Identity, contacts, alerts, evidence, notifications, zones, and administration — all business modules of the system | Not yet assigned — the project hasn't reached deployment configuration | PostgreSQL (shared) | REST |
| 2 | alerta-mujer-mobile | User-facing Android app: panic button, contacts, history, zones, evidence | N/A (client) | — | REST (consumes backend API) |
| 3 | alerta-mujer-web | Public landing, authenticated user portal, and admin panel | Not yet assigned | — | REST (consumes backend API) |

There's a single deployable backend today, not a set of independently deployed services — the module breakdown inside it (Identity and Accounts, Emergency Contacts, Alert Management, Evidence, Notifications, Zones and Support Resources, Administration and Moderation) is internal, and is documented per-module in `02-domain/domain-map.md` rather than as separate rows here.

---

## 5. Architectural principles

### P1: Modular boundaries, shared database
Each business module (bounded context) owns its own tables and doesn't reach into another module's tables through anything other than the foreign keys already modeled in `06-data/models.md`. This is the monolith's substitute for "Database per Service" — same discipline about ownership, without a separate database per module.

### P2: Domain independent of the framework (Hexagonal Architecture)
The domain layer (aggregates, invariants, domain events) has no dependency on Spring, PostgreSQL, or any external provider — those are adapters plugged in through ports, per `05-architecture/hexagonal-architecture.md`.

### P3: Fail Fast, Recover Gracefully — for external providers
Validate early at the edge of the backend. When FCM, the SMS provider, or the email provider fails or is slow, use a Circuit Breaker (see `pattern-guide.md`) instead of letting the failure cascade into the alert flow. This directly supports the fault-tolerance requirement in the SRS (section 2.3) for notification delivery.

### P4: Reliability of the alert path over everything else
The panic-button flow (`Alert` activation → contact notification → location sharing) is the one path in the system that cannot silently fail. Retries with backoff (`alert_contact`, `alert_reminder`) and the planned Outbox Pattern exist specifically to protect this path, even where other parts of the system don't have the same guarantees yet.

### P5: Not yet defined
Observability (structured logging, metrics, tracing) isn't addressed in the SRS or in any architecture document so far — there's no adopted logging format, metrics tool, or tracing solution to state here yet. This principle is a placeholder for whenever the team defines one.

---

## 6. Adopted architectural patterns

| Pattern | Adopted | Reference |
|---------|---------|-----------|
| API Gateway | Only if split into services later | `05-architecture/pattern-guide.md` |
| Database per Service | No — shared PostgreSQL database | `05-architecture/pattern-guide.md` |
| CQRS | No, for now | `05-architecture/pattern-guide.md` |
| Event Sourcing | No | `05-architecture/pattern-guide.md` |
| Circuit Breaker | Yes — for external providers only | `05-architecture/pattern-guide.md` |
| Saga (choreographed) | No | `05-architecture/pattern-guide.md` |
| Outbox Pattern | Yes — once alert delivery moves to async | `05-architecture/pattern-guide.md` |
| Factory Method | Yes | `05-architecture/pattern-guide.md` |
| Adapter | Yes | `05-architecture/pattern-guide.md` |
| Observer / Internal Event Bus | Yes | `05-architecture/pattern-guide.md` |
| Strategy | Yes | `05-architecture/pattern-guide.md` |
| Retry with Exponential Backoff | Yes | `05-architecture/pattern-guide.md` |

Full reasoning for every pattern, including the rejected ones, is in `05-architecture/pattern-guide.md`.

---

## 7. Cross-cutting concerns

| Concern | Adopted solution | Where it is configured |
|---------|----------------|------------------------|
| Authentication / Authorization | Spring Security, backed by `account` (credentials) and `role` (user vs administrator) | Backend — exact token strategy (JWT vs session) not detailed in the SRS |
| Logging | Not defined yet | — |
| Tracing | Not defined yet | — |
| Health Checks | Not defined yet | — |
| Error format | Not defined yet | — |
| Rate Limiting | Not applicable without an API Gateway | — |
| CORS | Needed for the Angular web app to call the backend; exact policy not detailed in the SRS | Backend |
| Circuit Breaker | For FCM, the SMS provider, and the email provider | Pattern in `pattern-guide.md` |

Several rows here don't have an adopted solution yet — that's an accurate account of where the project stands (SRS + architecture docs), not a gap in this document.

---

## 8. Registered architectural technical debt

No technical debt has been logged yet. This table gets populated as items are opened in `15-project-control/technical-backlog.md`.

---

## 9. Planned evolution

No versioned architectural roadmap exists yet — the SRS and current architecture documents describe the target for the first release, not a v2. Candidates that came up while documenting scope (`01-context/scope.md`) — like iOS support, which the SRS explicitly limits to Android — would belong here once the team schedules them.

---

## Key correlations

- Domain bounded contexts → `02-domain/domain-map.md`
- Specific decision ADRs → `05-architecture/decisions/`
- Hexagonal architecture per module → `05-architecture/hexagonal-architecture.md`
- Applied patterns → `05-architecture/pattern-guide.md`
- Data model → `06-data/models.md`
