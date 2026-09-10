# ADR-002: Architectural Style

## Context

The team is three developers building this as a SENA training project (ficha 3145556), with one
shared PostgreSQL database already modeled in `06-data/models.md`. A style decision was needed
before `05-architecture/overview.md` and `05-architecture/pattern-guide.md` could be filled in,
since both documents depend on knowing whether the backend is one deployable unit or several.

---

## Evaluated alternatives

### Alternative A — Modular Monolith with Hexagonal Architecture (CHOSEN)
- **Pros:** One Spring Boot application to build, test, and deploy; one shared PostgreSQL
  database with clear per-module table ownership; no network hop, message broker, or service
  discovery to operate; each bounded context still gets clean internal boundaries through
  hexagonal ports and adapters, so the domain logic doesn't depend on Spring or PostgreSQL
  directly; matches the team size (three developers) and the project's academic timeline.
- **Cons:** All modules scale together — there is no way to scale Alerts independently of, say,
  Administration; a bug in one module can in principle affect the same process as the others,
  though module boundaries and the hexagonal domain layer limit how far that reaches.

### Alternative B — Microservices per bounded context
- **Pros:** Independent deployment and scaling per module; failure isolation between services;
  the industry-standard pattern for large teams.
- **Cons:** Requires an API Gateway, service discovery, a message broker, and either Database per
  Service or a distributed-transaction strategy (Saga) — all operational overhead with no current
  requirement driving it; three developers cannot realistically operate 7 independently deployed
  services and a shared academic delivery timeline doesn't justify it; `06-data/models.md` already
  assumes a single shared database.

### Alternative C — Traditional layered monolith (no hexagonal architecture)
- **Pros:** Simplest possible structure; less upfront design effort.
- **Cons:** Business logic (the `Alert` activation flow, retry/backoff policy, notification
  channel selection) would be entangled with Spring and JPA, making it harder to test the domain
  in isolation and harder to swap a provider (e.g. changing the SMS vendor) without touching
  business rules.

---

## Decision

**Alternative A:** Modular Monolith — one Spring Boot backend, organized internally by business
module (bounded context), with hexagonal architecture (ports and adapters) applied inside each
module so the domain stays independent of Spring, PostgreSQL, and the external providers.

| Concern | Decision |
|---------|----------|
| Deployable units | One backend (`alerta-mujer-backend`), plus the two client apps (mobile, web) |
| Database | One shared PostgreSQL instance, tables grouped and owned by module |
| Inter-module communication | In-process, through an internal event bus (Observer pattern) for domain events; direct calls only within the same module |
| External providers (FCM, SMS, email) | Reached through adapters behind domain-owned ports, with Circuit Breaker at that boundary |
| Client-to-backend communication | REST, no API Gateway — both clients call the backend directly |

---

## Consequences

**Positive:**
- Matches the team's size and the project's timeline — no infrastructure to operate beyond the
  backend, the database, and the two client apps
- Hexagonal architecture keeps the door open to extracting a module into its own service later,
  without a full rewrite, since the domain layer doesn't depend on the framework
- `pattern-guide.md`'s rejection of Database per Service, Saga, and CQRS follows directly from
  this decision, instead of being argued module-by-module

**Negative:**
- No independent scaling or independent deployment per module — releasing a fix to Alert
  Management means redeploying the whole backend
- If the team or user base grows significantly, this decision will need to be revisited

**Mitigation:**
- Module boundaries are enforced at the code level (per-module packages, no cross-module table
  access outside the foreign keys already modeled in `06-data/models.md`), so splitting a module
  out later is a refactor, not a rewrite
- Revisit this ADR (mark it Superseded) if a specific module's load or team ownership needs
  diverge enough to justify the operational cost of Alternative B

---

## References

- Bounded contexts → `02-domain/domain-map.md`
- Data model and shared database → `06-data/models.md`
- Pattern-by-pattern reasoning that follows from this decision → `05-architecture/pattern-guide.md`
- System and container diagrams → `05-architecture/overview.md`
