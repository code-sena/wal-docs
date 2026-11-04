# API Gateway

> **Not deployed.** Woman Alert has no API Gateway in production. The gateway and the old
> monolith were deleted from the repository on 25/10/2026. This folder keeps the number 01 of the
> catalog to document how requests reach the three services today: **each client routes by path**.

---

## Location in the architecture

| Field | Value |
|-------|-------|
| Number in catalog | 01 |
| Local port | — (no process) |
| Repository | Routing lives in `AlertaMujer/Frontend-movil` and `Frond-end-Web/alerta-mujer-web` |
| DB engine | — |
| Communicates with | auth-service (:8081), alert-service (:8082), evidence-service (:8083) |
| Consumed by | Web frontend, mobile app |

---

## Responsibilities (what this service DOES)

What a gateway would do is distributed today as follows:

- **Routing by path:** `services/serviceRoutes.ts` (mobile) and `core/api/service-routes.ts` plus an interceptor (web) send each request to Auth, Alert or Evidence.
- **JWT validation:** each service validates the token with the same key, without calling Auth.
- **Rate limiting:** `RateLimitFilter` in Auth, 5 attempts per minute per IP on authentication endpoints.
- **CORS:** each service restricts browser requests to the web domain (`CORS_ORIGIN`).
- **Health:** `GET /actuator/health` in each service.

## Out of scope (what it does NOT do)

- **No business logic** — it does not exist as a process.
- **No correlation id** — there is no distributed tracing between services.
- **No aggregation** of responses from several services.

---

## How to run it locally

```bash
# There is no gateway to start. Start the three services:
docker compose up --build

# Verify each service
curl http://localhost:8081/actuator/health
curl http://localhost:8082/actuator/health
curl http://localhost:8083/actuator/health
```

**Expected response:**
```json
{ "status": "UP" }
```

---

## Related documents

- [data-model.md](./data-model.md) — Not applicable (no own DB)
- [events.md](./events.md) — Not applicable (does not emit domain events)
- [decisions.md](./decisions.md) — Why there is no gateway today
- [runbook.md](./runbook.md) — How to check the entry points in production
- [Entry points contract](../../../07-api/contracts/openapi/api-gateway.yaml)

---

## Design decisions

See: `09-microservices/services/01-api-gateway/decisions.md`

Relevant decisions include:
- **ADR-002** — Three microservices with hexagonal architecture, without a gateway
- **DIS-ARQ-04** (open) — Whether to add a gateway and the criteria to split or merge modules

---

## Routing pattern

```
Client → /api/evidences/**                                   → evidence-service :8083
Client → /api/alerts/**, /api/contacts/**, /api/location-logs/**,
         /api/zones/**, /api/resources/**, /api/saved-routes/**,
         /api/frequent-locations/**, /api/received-alerts/**,
         /api/zone-reports/**, /api/resource-calls/**,
         /api/safe-trips/**, /api/auto-protection/**, /api/public/** → alert-service :8082
Client → everything else (/api/auth, /api/users, /api/devices,
         /api/admin, /api/settings)                          → auth-service :8081
```

Routing configuration: `services/serviceRoutes.ts` (mobile) and `src/app/core/api/service-routes.ts` (web).
