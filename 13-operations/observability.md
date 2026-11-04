# System Observability

> What Woman Alert can observe today, where to look and what is missing. The project runs on free
> plans, so observability is basic: health checks, service logs, database records and the system
> status page of the admin panel. There is no metrics stack or distributed tracing.

**See also:**
- Incident response → `13-operations/incident-management.md`
- Runbooks per service → `09-microservices/services/`
- Cross-cutting concerns → `05-architecture/cross-cutting.md`

---

## The 3 pillars of observability

```
                    ┌──────────────────────────────────────────┐
                    │   Auth :8081   Alert :8082   Evidence :8083 │
                    └──────┬──────────────┬────────────────┬─────┘
                           │              │                │
           [LOGS]      [HEALTH]      [RECORDS IN DB]   [CLIENT LOGS]
              │            │              │                │
       Render Logs tab  /actuator/health  audit_log        adb logcat (APK)
                           │              alert_contact    browser console (web,
                      UptimeRobot         tracking_view     development only)
                       (every 5 min)      device_session
                           │
                    Admin panel: Configuration > System status
```

| Pillar | Today | Gap |
|--------|-------|-----|
| Logs | Spring Boot logs per service in Render | No central search, no structured JSON format, short retention on the free plan |
| Metrics | Health endpoints and the admin dashboard (alerts per state, per hour, per zone) | No technical metrics (latency, error rate) |
| Traces | None | No correlation id between services |

---

## Logs

### Standard format (structured JSON)

Not adopted: the services use the default Spring Boot text format. When a structured format is
adopted, each line should include at least the timestamp, level, service, module, user id (never the
email or the location) and the request path.

### Log levels

| Level | Use in the project |
|-------|--------------------|
| ERROR | A notice failed for a contact, an RPC call failed, a scheduled task failed |
| WARN | A sleeping service delayed an RPC call, a rate limit was reached |
| INFO | Service startup, migrations applied, scheduled task executed |
| DEBUG | Only in local development |

### What NOT to log

- Passwords, recovery codes, JWT or refresh tokens, `X-Internal-Key`, `X-Protection-Key`, tracking codes.
- Exact coordinates, contact emails or phones, chat messages, evidence content.
- In the mobile app, HTTP client logs only appear in `__DEV__`; 64 `console` calls still remain outside
  `__DEV__` and must be reviewed (CAL-10).
- Without an email provider configured, emails are written to the log: this must only happen in local.

### Implementation

| Component | Where to read |
|-----------|---------------|
| Auth, Alert, Evidence | Render → service → **Logs** |
| Local backend | `docker compose logs -f auth-service` (or `alert-service`, `evidence-service`) |
| Installed APK | `adb logcat -v time ReactNativeJS:V AndroidRuntime:E *:S` |
| Web | Browser console in development |

---

## Metrics

### RED Metrics (Rate, Errors, Duration)

Not collected. The closest signals available today:

| Signal | Source |
|--------|--------|
| Service up or down | `/actuator/health` + UptimeRobot |
| Notice errors | `alert.alert_contact` with `status = 'failed'`, `attempts` and `error_message` |
| Authentication abuse | 429 responses in the Auth logs |
| Cold start | First response time after 15 minutes without traffic (up to about 90 s) |

### Business metrics (USE method for resources)

The admin dashboard shows the number of users, attended and cancelled alerts, alerts by time of day,
zones with more incidents and comparisons. The system status page shows the database connection, push
notifications, active alerts and the last review of expired alerts.

### Metrics endpoint

Only `/actuator/health` is public. No Prometheus endpoint is exposed.

---

## Distributed traces

Not implemented.

### Context propagation

A request of the app can cross two services (for example Alert asking Auth for the user's devices
through `IdentityApi`). Today the only link between both sides is the time and the error type, which
the RPC client preserves (404, 403, 400, 409).

### Implementation with OpenTelemetry

Not adopted. If it is added, the first spans worth having are: `POST /api/alerts` → notice per contact
→ FCM / Resend, and the RPC calls between services.

### Create manual spans for business logic

Not applicable yet.

---

## Grafana Dashboards

There is no Grafana. The equivalents in use:

### Main dashboard — Executive view

Admin panel → Dashboard: users, alerts by state, by hour and by zone.

### Per-service dashboard

Render dashboard of each service (deploys, logs, status) and Configuration > System status in the
admin panel.

---

## Alerts

### Standard alert rules

| Condition | Who detects it | Action |
|-----------|----------------|--------|
| A health endpoint does not answer | UptimeRobot (every 5 min) | Follow the runbook of the service |
| CI fails on `main` | GitHub Actions | Fix before any new merge |
| Many `failed` notices | Review of `alert_contact` | Check the email provider and Firebase |
| Database close to expiring | Render notice | Move the database before the date |

There is no Alertmanager or PagerDuty; notifications go to the team group.

---

## Correlations

- Incident management → `13-operations/incident-management.md`
- Health checks and error format → `05-architecture/cross-cutting.md`
- Non-functional requirement RNF-OBS-001 → `04-requirements/non-functional.md`
