# Cross-Cutting Concerns

> Concerns that apply across every business module inside `alerta-mujer-backend`, not owned by
> any single bounded context. This expands on the summary table in `overview.md` §7.

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

**Status:** not defined yet. Not urgent under the current Modular Monolith style (ADR-002) —
distributed tracing solves cross-service call chains, and there's only one backend process today.
Revisit if a module is ever extracted into its own service.

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

**Status:** not applicable in the current architecture — there is no API Gateway (`overview.md`
§3), and rate limiting at the application layer hasn't been discussed. Worth revisiting if abuse
of the panic-button endpoint (repeated false alerts) becomes a concern.

---

## 7. CORS

**Status:** needed, not yet configured. The Angular web app and the Spring Boot backend run as
separate origins, so CORS has to allow the web app's origin to call the backend API. Exact policy
(allowed origins, methods, credentials) is not detailed in the SRS.

---

## 8. Circuit Breaker

**Status:** adopted, scope defined. Applies specifically to the three external notification
providers — Firebase Cloud Messaging, the SMS provider, and the email provider — per
`pattern-guide.md`. Not applied to in-process calls between business modules, since the overhead
isn't justified inside a single backend (ADR-002).

---

## 9. Feature flags

**Status:** not discussed. No requirement in the SRS currently calls for gradual rollout or
runtime feature toggling.

---

## Correlations

- Summary table → `05-architecture/overview.md` §7
- Circuit Breaker justification → `05-architecture/pattern-guide.md`
- Roles and credentials data model → `06-data/models.md`
