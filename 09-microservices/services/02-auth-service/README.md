# Auth Service

> Identity, sessions, phones, system configuration, administration and audit of Woman Alert. It is
> also the service that applies the Liquibase migrations of the whole database.

---

## Location in the architecture

| Field | Value |
|-------|-------|
| Number in catalog | 02 |
| Local port | 8081 |
| Production | `https://alerta-mujer-auth.onrender.com` (Render, Docker) |
| Repository | `Backend-ALERTA-MUJER/microservicios/auth-service` |
| DB engine | PostgreSQL 16 — schemas `identity` and `admin` |
| Communicates with | alert-service (`AlertsApi`, `ZonesApi`), Firebase, email provider |
| Consumed by | Web, mobile app, alert-service and evidence-service (`IdentityApi`, `DevicesApi`, `SettingsApi`) |

---

## Responsibilities (what this service DOES)

- Registration with acceptance of terms, login with email/password and with Google (token verified with its signature).
- JWT issuing: access token 24 h, refresh token 15 days (hash stored per device in `device_session`).
- Password recovery with a 6-digit code by email (15 minutes, 5 attempts, only the hash is stored).
- Profile of the user and account deletion; deletion requested from the admin panel needs a second administrator and anonymizes the data.
- Phones: registration, push token, permissions, main phone and the new-phone notice.
- System configuration (alert duration 30 to 1440 min, emergency number, contact limit, minimum app version) and the public `/api/settings`.
- Admin panel: users (paginated, block with reason), alerts, moderation, reports with PDF/CSV export, dashboard statistics, system status.
- Audit log reading with filters and export; admin session events with IP and origin.
- Rate limit of 5 attempts per minute per IP on authentication endpoints.
- Liquibase migrations of the five schemas when it starts.

## Out of scope (what it does NOT do)

- **Does not manage alerts, contacts or locations** — those belong to alert-service; Auth reads them through `AlertsApi`.
- **Does not store evidence files** — evidence-service does.
- **Does not validate tokens for the other services** — each service validates the JWT by itself.

---

## How to run it locally

```bash
# From the backend repository root (starts PostgreSQL, SeaweedFS and the three services)
cp .env.example .env      # fill in the variables
docker compose up --build

# Verify
curl http://localhost:8081/actuator/health

# Create a test user
curl -X POST http://localhost:8081/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"nombre":"Maria Garcia","email":"maria.garcia@example.com","password":"password123","telefono":"3001234567"}'
```

Tests: `cd microservicios && mvn clean test` (114 tests in Auth).

---

## Related documents

- [data-model.md](./data-model.md) — Tables of `identity` and `admin`
- [events.md](./events.md) — Events published and consumed
- [decisions.md](./decisions.md) — Technical decisions
- [runbook.md](./runbook.md) — Operation in production
- [API Contract](../../../07-api/contracts/openapi/auth-service.yaml)

---

## Emitted JWT structure

```json
{
  "sub": "1",
  "role": "user",
  "iat": 1696500000,
  "exp": 1696586400
}
```

**Algorithm:** HMAC with `JWT_SECRET`, shared by the three services. `sub` is `users.id`; the
administrator role travels as `admin`.

---

## Required environment variables

See `.env.example` at the backend root for the variable names. Real values are never written in the
repository or in the documentation.

| Variable | Purpose |
|----------|---------|
| `JWT_SECRET` | Signs and validates the tokens (same value in the three services) |
| `INTERNAL_API_KEY` | `X-Internal-Key` for `/internal/**` |
| `CORS_ORIGIN` | Allowed web origin |
| `DB_HOST`, `DB_PORT`, `DB_NAME` and database credentials | PostgreSQL connection |
| `SERVICES_AUTH_URL`, `SERVICES_ALERT_URL`, `SERVICES_EVIDENCE_URL` | URLs for RPC calls |
| Firebase service account (Secret File) | Google login verification and push |
