# Events — Auth Service

> Auth uses Spring events published after the transaction commits. When the listener lives in another
> service, the event is forwarded by HTTP to `POST /internal/events` with `X-Internal-Key`. There is no
> message broker.

---

## Published events

### Password recovery requested

| Field | Value |
|-------|-------|
| Trigger | `POST /api/auth/forgot-password` with an existing account |
| Payload | Account, email and the 6-digit code (the code is never stored, only its hash) |
| Listener | Email adapter: sends the recovery code |
| Delivery | After commit; if no email provider is configured, the email is written to the log |

### New phone signed in

| Field | Value |
|-------|-------|
| Trigger | Login from a device that was not registered for the account |
| Payload | User and new device data |
| Listener | Notification to the user's other phones (RF-NOT-006) |
| Delivery | Forwarded between services through `/internal/events` |

### Admin session events

| Field | Value |
|-------|-------|
| Trigger | Successful login, failed attempt, password change, logout (voluntary or by inactivity) of an admin account |
| Payload | Account, role, IP, origin (web, mobile or scheduled task), date and time |
| Listener | Audit adapter: writes to `admin.audit_log` |
| Delivery | After commit |

### Configuration changed

| Field | Value |
|-------|-------|
| Trigger | Update of `admin.system_configuration` from the panel (with confirmation) |
| Payload | Changed columns |
| Listener | Database trigger `trg_audit_system_configuration` |
| Delivery | Same transaction |

---

## Consumed events

Auth does not listen to events of other services. When it needs alert or zone data (dashboard, admin
views) it calls `AlertsApi` and `ZonesApi` by RPC.

---

## Delivery guarantees

- Events are published only after the transaction commits, so a rolled-back operation does not send
  an email.
- There is no outbox: if the service stops right after the commit, the event can be lost.
- Internal HTTP forwarding waits up to 90 seconds for a sleeping service.

---

## Correlations

- Domain events catalog → `02-domain/domain-events.md`
- Internal API → `07-api/api-contract.md` §13.1
