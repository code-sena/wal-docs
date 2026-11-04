# Technical Decisions — API Gateway

> Decisions about the entry point of the system. The current state is: no gateway, routing in the
> clients and authentication validated in each service.

---

## Decision: Authentication in the gateway vs. in each service

**Context:** with three services and no gateway, someone has to validate the JWT.

**Decision:** each service validates the JWT with the same key (`JWT_SECRET`), using the security
configuration of the `shared-kernel`. No service calls Auth to validate a token.

**Consequences:**
- No extra network hop per request, which matters with the cold start of the free plan.
- The secret must be the same in the three services; it was rotated on 28/10/2026 and the services
  refuse to start with an empty value or with the value that was once published.

---

## Decision: Rate limiting by IP vs. by user

**Decision:** by IP, only on the authentication endpoints of Auth (login, Google, registration and
recovery), 5 attempts per minute, using the real client IP (`forward-headers-strategy`). Afterwards
the answer is 429.

**Why:** these endpoints are public and are the target of brute force. Authenticated endpoints rely on
domain limits (one active alert per user, chat message limit, contact limit).

---

## Decision: Gateway implementation

**Context:** the earlier design had a gateway in front of four services; the code had a `gateway/`
folder and the clients actually pointed at the monolith.

**Decision:** remove the gateway together with the monolith (25/10/2026) and let the clients route by
path. Whether to add a gateway again is open (DIS-ARQ-04).

**Trade-offs:**
- Fewer processes on the free plan and one less cold start.
- The routing is duplicated in the mobile app and the web.
- No central place for correlation ids, access logs or global rate limits.

---

## Correlations

- ADR-002 → `05-architecture/decisions/records/ADR-002-architectural-style.md`
- Cross-cutting concerns → `05-architecture/cross-cutting.md`
