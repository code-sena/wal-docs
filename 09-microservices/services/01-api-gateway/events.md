# Events — API Gateway

> Not applicable: there is no gateway process. Domain events are published and consumed by the
> services themselves.

---

## Published events

None.

---

## Consumed events

None.

---

## Access logs (not domain events)

There are no central access logs. Each service writes its own logs, which are read from the **Logs**
tab of each service in Render. The authentication attempts blocked by the rate limit appear in the
Auth logs as 429 responses, and the admin session events (login, failed attempt, password change,
logout) are stored in `admin.audit_log` with IP and origin.

---

## Correlations

- Domain events → `02-domain/domain-events.md`
- Observability → `13-operations/observability.md`
