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

### `contracts/openapi/api-gateway.yaml` ✅ REAL (entry points)
There is no gateway in production. This file documents the entry points that the clients use: the
three service URLs (local and Render), the routing by path that mobile and web apply, and the public
health check of each service.

### `contracts/openapi/auth-service.yaml` ✅ REAL (partial)
Contract of the Auth service for the endpoints the clients use: registration, login, Google login,
refresh, password recovery, profile, devices and public configuration. The admin endpoints are listed
in `api-contract.md` §13.

### `contracts/openapi/_shared.yaml`
Reusable schemas and components across contracts (pagination, errors, security).

### `contracts/openapi/_template-service.yaml`
Template for adding a new service contract. Duplicate it as `service-name.yaml`.

### Missing (debt)
| Service | Port | Bounded contexts | Status |
|---|---|---|---|
| Auth | 8081 | Identity and Accounts, Administration and Moderation | `auth-service.yaml` covers the client endpoints; admin endpoints only in `api-contract.md` |
| Alert | 8082 | Alerts, contacts, notifications, trips, zones and resources | Missing `alert-service.yaml`; documented in `api-contract.md` |
| Evidence | 8083 | Evidence | Missing `evidence-service.yaml`; documented in `api-contract.md` |

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
