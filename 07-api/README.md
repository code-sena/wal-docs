# 07 — API Contracts

> **What is this?** The formal specification of how the backend pieces communicate with each other
> and with the outside world. In Alerta Mujer the contract in force for web and mobile is the
> monolith's on `:8080` (see [`api-contract.md`](api-contract.md)); this directory holds the
> per-service OpenAPI contracts for when the monolith is split into ADR-002's four services.

## Why contracts are critical in microservices

In a monolith, changing a function is easy: the compiler tells you what you break. In
microservices, changing an API can silently break other services in production.

**Contract-first development:** design the API before implementing it. This way web, mobile, and
the services work in parallel with an agreed contract.

---

## What is here and how to fill it in

### `contracts/openapi/api-gateway.yaml` ✅ REAL (partial)
Planned single entry point: validates JWT, routes, applies rate limiting/CORS. Today clients point
directly at the monolith; this contract is the gateway's promise.

### `contracts/openapi/auth-service.yaml` ✅ REAL (partial)
The identity service contract (registration, login, session renewal). Divergence note: current
code exposes these endpoints under `/api/auth/**` in the monolith and in `am-identity-service`
(:3001), the only service with functional code.

### `contracts/openapi/_shared.yaml`
Reusable schemas and components across contracts (pagination, errors, security).

### `contracts/openapi/_template-service.yaml`
Template for adding a new service contract. Duplicate it as `service-name.yaml`.

### Missing (debt)
| Service | Port | Bounded contexts | Status |
|---|---|---|---|
| identity-service | 3001 | Identity and Accounts | Partial code; missing own `identity-service.yaml` |
| alert-service | 3002 | Alert Management, contacts, evidence, notifications | Skeleton → missing `alert-service.yaml` |
| resource-service | 3003 | Zones and help resources | Skeleton → missing `resource-service.yaml` |
| admin-service | 3004 | Administration and moderation | Skeleton → missing `admin-service.yaml` |

### Minimum structure for each contract

```yaml
openapi: 3.0.3
info:
  title: [Service Name] API
  version: 1.0.0
servers:
  - url: http://localhost:3001
paths:
  /resource:
    get:
      summary: List resources
      responses:
        '200': { description: OK }
components:
  securitySchemes:
    bearerAuth: { type: http, scheme: bearer, bearerFormat: JWT }
security:
  - bearerAuth: []
```

---

## Correlations with the rest of the project

| This directory | Fed by | Feeds into |
|---|---|---|
| `contracts/openapi/` | `api-contract.md`, `wal-docs/04-requirements/` | `09-microservices/services/` (implementation) |
| `_shared.yaml` | `api-contract.md` §1.2 (errors) | All contracts |
| `api-gateway.yaml` | `wal-docs/05-architecture/overview.md` | Web and mobile clients |

## Questions this section must answer

- What is each endpoint called and what does it do? → `api-contract.md`
- What data does it receive and return? → schemas in each `*.yaml`
- How does the client authenticate? → `bearerAuth` JWT, 24 h
- What errors can it return? → `_shared.yaml` and `api-contract.md` §1.2
- How do I version without breaking consumers? → `/api/v1` prefix when the formal v1 ships (pending)
