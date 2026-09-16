# Cross-Cutting Concerns

> Concerns that apply across all four backend services (identity-service, alert-service,
> resource-service, admin-service) and the API Gateway. No single bounded context owns these —
> they are configured at the infrastructure layer. This expands on the summary table in
> `overview.md` §8.

---

## 1. Authentication / Authorization

**Status:** partially defined.

- Backed by Spring Security, using the `account` (credentials) and `role` (user vs.
  administrator) entities already modeled in `06-data/models.md`
- **Open decision:** token strategy (JWT vs. server-side session) is not detailed in the SRS and
  has no ADR yet — this is the most concrete gap in this section, since both the web app and the
  mobile app need to know how to hold and send credentials on every request
- Authorization is role-based: `user` (RF1–RF11 — alerts, contacts, zones, evidence) vs.
  `administrator` (RF13 — user/alert/zone management, moderation, reporting)

---

## 2. Logging

**Status:** not defined yet.

No structured logging format, log level policy, or centralized log destination has been chosen.
When this is defined, the alert-activation path (`Alert` creation → `alert_contact` dispatch)
should be the first flow with guaranteed log coverage, since it's the one path the project
explicitly cannot let fail silently (`overview.md` §5, P4).

---

## 3. Tracing

**Status:** not defined yet. Distributed tracing is now **high priority** given the four-service
architecture (ADR-002) — cross-service call chains between identity-service, alert-service,
resource-service, and admin-service will be difficult to debug without it. Must be defined
before the first staging deployment.

---

## 4. Health Checks

**Status:** not defined yet. No `/health` or readiness endpoint exists in the SRS or in the
architecture documents so far.

---

## 5. Error format

**Status:** not defined yet. No standard error-response shape has been agreed for the REST API
consumed by the mobile and web apps.

---

## 6. Rate Limiting

**Status:** to be configured at the API Gateway layer (ADR-002). The API Gateway sits in front
of all four services and is the right place for rate limiting — particularly on the alert
activation endpoint to prevent abuse (repeated false alerts) and accidental DoS of the one path
that cannot fail (`overview.md` §6, P4).

---

## 7. CORS

**Status:** to be configured at the API Gateway layer. The API Gateway is the single entry
point for both the Angular web app and the mobile app, so CORS policy (allowed origins,
methods, credentials) is defined once there rather than per service.

---

## 8. Circuit Breaker

**Status:** adopted, scope defined. Applies specifically to the three external notification
providers — Firebase Cloud Messaging, the SMS provider, and the email provider — called by
`alert-service`, per `pattern-guide.md`. Not applied to inter-service calls between
identity-service, alert-service, resource-service, and admin-service for now; those calls
go through the API Gateway over the internal network with low expected latency.

---

## 9. Feature flags

**Status:** not discussed. No requirement in the SRS currently calls for gradual rollout or
runtime feature toggling.

---

## Correlations

- Summary table → `05-architecture/overview.md` §7
- Circuit Breaker justification → `05-architecture/pattern-guide.md`
- Roles and credentials data model → `06-data/models.md`
