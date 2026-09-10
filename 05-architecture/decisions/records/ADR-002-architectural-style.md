# ADR-002: Architectural Style

## Context

The team is three developers building this as a SENA training project (ficha 3145556), with one
shared PostgreSQL database already modeled in `06-data/models.md`. A style decision was needed
before `05-architecture/overview.md` and `05-architecture/pattern-guide.md` could be filled in,
since both documents depend on knowing whether the backend is one deployable unit or several.

---

## Evaluated alternatives

### Alternative A — Modular Monolith with Hexagonal Architecture
- **Pros:** One Spring Boot application to build, test, and deploy; one shared PostgreSQL
  database with clear per-module table ownership; no network hop, message broker, or service
  discovery to operate; each bounded context still gets clean internal boundaries through
  hexagonal ports and adapters, so the domain logic doesn't depend on Spring or PostgreSQL
  directly; matches the team size (three developers) and the project's academic timeline.
- **Cons:** All modules scale together — there is no way to scale Alerts independently of, say,
  Administration; a bug in one module can in principle affect the same process as the others,
  though module boundaries and the hexagonal domain layer limit how far that reaches.

### Alternative B — Four Microservices with Hexagonal Architecture (CHOSEN)
- **Pros:** Independent deployment and scaling per service; failure isolation between services;
  logical bounded context boundaries preserved through hexagonal architecture; alert-service can
  scale independently for emergency processing; admin-service isolated from mobile API workload;
  matches the team size (three developers) while providing operational flexibility.
- **Cons:** Requires API Gateway, service discovery, and potential inter-service communication;
  uses shared PostgreSQL database (not Database per Service) to reduce operational complexity
  for the academic scope; requires coordination between services for some operations.

### Alternative C — Traditional layered monolith (no hexagonal architecture)
- **Pros:** Simplest possible structure; less upfront design effort.
- **Cons:** Business logic (the `Alert` activation flow, retry/backoff policy, notification
  channel selection) would be entangled with Spring and JPA, making it harder to test the domain
  in isolation and harder to swap a provider (e.g. changing the SMS vendor) without touching
  business rules.

---

## Decision

**Alternative B:** Four Spring Boot microservices with hexagonal architecture — identity-service,
alert-service, resource-service, and admin-service — each organized by business module (bounded
context), with hexagonal architecture (ports and adapters) applied inside each service so the
domain stays independent of Spring, PostgreSQL, and the external providers.

| Concern | Decision |
|---------|----------|
| Deployable units | Four Spring Boot services (identity-service, alert-service, resource-service, admin-service), plus the two client apps (mobile, web) |
| Database | One shared PostgreSQL instance, tables grouped and owned by service (logical ownership) |
| Inter-service communication | REST between services; internal event bus (Observer pattern) for domain events within each service |
| External providers (FCM, SMS, email) | Reached through adapters behind domain-owned ports, with Circuit Breaker at that boundary |
| Client-to-backend communication | REST through API Gateway — both clients call services through the gateway |

---

## Consequences

**Positive:**
- Independent scaling per service — alert-service can scale independently for emergency processing
- Failure isolation between services — a problem in admin-service doesn't affect mobile API
- Hexagonal architecture keeps the domain independent of Spring, PostgreSQL, and external providers
- Logical bounded context boundaries preserved through hexagonal ports and adapters
- Shared PostgreSQL reduces operational complexity for the academic scope while maintaining logical ownership

**Negative:**
- Requires API Gateway for client-to-backend communication
- Requires service discovery and inter-service coordination
- More complex deployment than a single monolith
- Potential network latency between services

**Mitigation:**
- Use shared PostgreSQL to avoid distributed transaction complexity (no Saga needed initially)
- Group related bounded contexts in the same service (e.g., Alert Management + Emergency Contacts + Evidence + Notifications in alert-service)
- Implement API Gateway only when services are deployed
- Revisit this ADR (mark it Superseded) if operational complexity outweighs benefits

---

## References

- Bounded contexts → `02-domain/domain-map.md`
- Data model and shared database → `06-data/models.md`
- Pattern-by-pattern reasoning that follows from this decision → `05-architecture/pattern-guide.md`
- System and container diagrams → `05-architecture/overview.md`
