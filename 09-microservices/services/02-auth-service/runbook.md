# Runbook — Auth Service

---

## 1. Quick information

| Field | Value |
|-------|-------|
| URL | `https://alerta-mujer-auth.onrender.com` |
| Hosting | Render, free plan, Docker, branch `main`, Root Directory `microservicios` |
| Database | Render PostgreSQL 16 (migrated by this service at startup) |
| Monitor | UptimeRobot on `/actuator/health` every 5 minutes |
| Logs | **Logs** tab of the service in Render |
| Owner | Backend team |

---

## 2. Check health

```bash
# Health check
curl https://alerta-mujer-auth.onrender.com/actuator/health
# Expected response:
# {"status":"UP"}

# If there are problems, check the admin panel:
# Configuration > System status (database connection, push, active alerts, last expiration review)
```

The first request after 15 minutes without traffic can take up to about 90 seconds.

---

## 3. Frequent alerts

### High rate of 401 on login

1. Check the logs for repeated failed attempts from the same IP (the rate limit answers 429 after 5 per minute).
2. If the users are blocked (`Account blocked`), review the admin panel: an administrator blocks accounts manually.

### Users with an active session cannot refresh

1. Check that `JWT_SECRET` did not change in one service only; it must be the same in the three.
2. Check the `device_session` of the user: a `revoked` or `expired` session requires logging in again.

### The service does not start

1. `JWT_SECRET` empty or with the value that was once published: `JwtKeyProvider` stops the startup.
2. A Liquibase changeset failed: read the startup logs; never edit an applied changeset, create a new one.
3. The database is unreachable: check `DB_HOST`, `DB_PORT`, `DB_NAME` and the free database expiration.

---

## 4. JWT secret rotation

1. Generate a new long random secret.
2. Update `JWT_SECRET` in the shared group of variables in Render (it applies to the three services).
3. Redeploy the three services. Every existing session becomes invalid and users must log in again.
4. Check the three health endpoints and log in from the web and the app.

The last rotation was on 28/10/2026.

---

## 5. Rollback

Redeploy the previous commit of `main` in Render, or revert the pull request and let Render publish
again. A rollback does not undo applied migrations: if a changeset caused the problem, write a new
changeset that corrects it (as changeset 25 did on 27/10/2026).

---

## 6. Maintenance operations

### Wake the services before a presentation

Call the three `/actuator/health` endpoints a few minutes before.

### Unblock an account

From the admin panel (Users), with the reason recorded in the audit log.

### Delete an account

Requested by one administrator and approved by a second one; the personal data is anonymized.

---

## 7. Post-incident

Record the cause, the affected users, the recovery time and the action taken in
`13-operations/incident-management.md`.
