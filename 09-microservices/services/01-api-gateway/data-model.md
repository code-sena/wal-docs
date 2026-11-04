# Data Model — API Gateway

> Not applicable. There is no gateway process, so there is no own database, cache or route file on
> a server. This document records where the equivalent configuration lives.

---

## Database engine

None. The three services share PostgreSQL 16; each one owns its schemas (`06-data/models.md`).

---

## In-memory state / Redis

None. Redis is not used in the project. The rate limit of Auth keeps its counters inside the Auth
process, per IP.

---

## Route configuration files

| Client | File | Content |
|--------|------|---------|
| Mobile | `src/services/serviceRoutes.ts` | Prefix → service URL (`EXPO_PUBLIC_API_URL`, `EXPO_PUBLIC_ALERT_API_URL`, `EXPO_PUBLIC_EVIDENCE_API_URL`) |
| Web | `src/app/core/api/service-routes.ts` + interceptor | Prefix → `apiUrl`, `alertApiUrl`, `evidenceApiUrl` from `environment*.ts` |

When a path moves to another service, both files must be updated in the same delivery.

---

## Correlations

- Routing table → `09-microservices/services/01-api-gateway/README.md`
- Data models → `06-data/models.md`
