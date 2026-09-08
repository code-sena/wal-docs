## Patterns adopted in this project

> The selected patterns are based on the actual domain model, bounded contexts, domain events, functional requirements, and integration needs of Alerta Mujer.
>
> Patterns are adopted only when they solve a concrete architectural or domain problem. Patterns that introduce unnecessary complexity are explicitly rejected.

| Pattern                            | Adopted?                                             | Justification / ADR                                                                                                                                                                                                                                                                                                                                                            |
| ---------------------------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Factory Method**                 | **Yes**                                              | Used for controlled creation of domain aggregates and domain objects when creation requires validation or different creation paths. In particular, `Alert` creation can validate the activation method, alert type, originating device, and initial state before the aggregate is persisted. See `Factory Method` section and ADR-XXX.                                         |
| **Adapter**                        | **Yes**                                              | Used to isolate the domain/application layer from external providers such as SMS, email, push notifications, maps, cloud evidence storage, and other third-party APIs. The domain depends on internal ports while adapters translate external provider interfaces. See `Adapter` section and ADR-XXX.                                                                          |
| **Observer / Internal Event Bus**  | **Yes**                                              | Core pattern for the domain event architecture. Events such as `AlertActivated`, `LocationRecorded`, `AlertContactNotified`, `EvidenceRegistered`, and `AlertCancelled` can notify multiple consumers without coupling the `Alert` aggregate directly to notification, evidence, administration, or other components. See `Observer / Internal Event Bus` section and ADR-XXX. |
| **Strategy**                       | **Yes**                                              | Used for interchangeable notification delivery strategies. The notification system may select SMS, email, push notification, or call according to the available channel and business rules. New channels can be added without modifying the core alert logic. See `Strategy` section and ADR-XXX.                                                                              |
| **Retry with Exponential Backoff** | **Yes**                                              | Required for transient failures when sending emergency notifications or communicating with external services. The existing `alert_contact` model supports retry-oriented states and attempt tracking. Retries use bounded attempts and exponential backoff to avoid overwhelming an unavailable provider. See `Retry with Exponential Backoff` section and ADR-XXX.            |
| **API Gateway**                    | **Yes — if microservices are deployed**              | Provides a single entry point for the mobile and web clients and routes requests to the appropriate backend service. It centralizes concerns such as authentication, authorization, rate limiting, routing, and TLS termination. See `API Gateway` section and ADR-XXX.                                                                                                        |
| **Circuit Breaker**                | **Yes — for external/independent services**          | Protects Alerta Mujer from cascading failures when an external notification, evidence-storage, map, or other independent service becomes unavailable or slow. It should not be applied to ordinary calls inside the same service. See `Circuit Breaker` section and ADR-XXX.                                                                                                   |
| **Outbox Pattern**                 | **Yes — recommended for asynchronous domain events** | Ensures that important database changes and the corresponding domain-event publication are persisted reliably in the same transaction. This is particularly relevant for critical events such as `AlertActivated` and `AlertCancelled`, where losing an event could affect notification or auditing workflows. See `Outbox Pattern` section and ADR-XXX.                       |
| **Database per Service**           | **No**                                               | The project uses a shared PostgreSQL database for the academic architecture. Tables are logically owned by bounded contexts/services, but services share the same database. A separate database per service would add operational complexity without providing sufficient benefit for the current project scope.                                                               |
| **Saga (choreographed)**           | **No**                                               | Current business transactions do not require distributed transactions across independent databases. The shared PostgreSQL database allows the required transactional operations to be handled with normal ACID transactions. Saga would introduce unnecessary complexity at this stage.                                                                                        |
| **CQRS**                           | **No — initially**                                   | The current read and write models are not sufficiently different to justify separate command and query models. CQRS may be reconsidered if administrative dashboards or reporting requirements create materially different read workloads.                                                                                                                                     |
| **Event Sourcing**                 | **No**                                               | The system requires an audit trail but does not require rebuilding the complete state of every aggregate from historical events. The existing `audit_log` and domain-event approach are sufficient for the current requirements.                                                                                                                                               |
| **BFF**                            | **No**                                               | Mobile and web clients share most of the same business capabilities and API requirements. A standard API/Gateway layer is sufficient. A BFF would create additional APIs to maintain without a strong current need.                                                                                                                                                            |
| **Singleton**                      | **No — as an explicit domain pattern**               | Application configuration and shared infrastructure should be managed through dependency injection rather than manually implemented Singleton classes. The project does not require a domain object to enforce a single instance.                                                                                                                                              |
| **Builder**                        | **No — initially**                                   | The current domain entities do not require sufficiently complex construction to justify a Builder. It may be introduced later for complex test fixtures or configuration objects if needed.                                                                                                                                                                                    |
| **Decorator**                      | **Optional**                                         | May be used around application services or repositories for cross-cutting concerns such as logging, caching, metrics, validation, or rate limiting. It is not required for the initial domain implementation.                                                                                                                                                                  |
| **Template Method**                | **No**                                               | Current business processes do not contain a sufficiently stable algorithm with multiple interchangeable implementation steps to justify this pattern.                                                                                                                                                                                                                          |
| **Sidecar**                        | **No**                                               | The project does not currently require Kubernetes/service-mesh infrastructure or independent observability/network capabilities that would justify a Sidecar deployment pattern.                                                                                                                                                                                               |

### Core adopted patterns

The patterns considered part of the core Alerta Mujer architecture are:

1. **Observer / Internal Event Bus**
2. **Strategy**
3. **Adapter**
4. **Factory Method**
5. **Retry with Exponential Backoff**

These patterns directly support the domain model and its event-driven behavior.

### Infrastructure and microservices patterns

The following patterns complement the core architecture when the system is deployed as microservices:

1. **API Gateway**
2. **Circuit Breaker**
3. **Outbox Pattern**

These are infrastructure-level decisions rather than domain modeling patterns.

### Explicitly rejected patterns

The following patterns are intentionally not part of the initial architecture:

* **Database per Service**
* **Saga**
* **CQRS**
* **Event Sourcing**
* **BFF**
* **Explicit Singleton**
* **Template Method**
* **Sidecar**

They are excluded because their complexity or operational requirements are not justified by the current Alerta Mujer domain and project scope.

---
