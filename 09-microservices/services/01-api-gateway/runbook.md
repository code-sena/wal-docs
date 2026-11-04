# Runbook — API Gateway

> There is no gateway to operate. This runbook explains how to check the three entry points that
> the clients call directly.

---

## 1. Quick information

| Field | Value |
|-------|-------|
| Process | None — routing in the clients |
| Entry points | `alerta-mujer-auth`, `alerta-mujer-alert`, `alerta-mujer-evidence` on Render |
| Hosting | Render, free plan (services sleep after 15 minutes) |
| Monitor | UptimeRobot, `/actuator/health` every 5 minutes |
| Owner | Backend team |

---

## 2. Check health

```bash
curl https://alerta-mujer-auth.onrender.com/actuator/health
curl https://alerta-mujer-alert.onrender.com/actuator/health
curl https://alerta-mujer-evidence.onrender.com/actuator/health
# Expected response for each:
# {"status":"UP"}
```

The first request to a sleeping service can take up to about 90 seconds.

---

## 3. Frequent alerts

### The app or the web cannot reach a service

1. Check the three health endpoints above.
2. If one does not answer, open its **Logs** tab in Render and look at the last startup.
3. Check that the client points to the right URL (`eas.json` for the APK, `environment.prod.ts` for the web).

### Many 401 responses

1. The token expired: the mobile app refreshes it automatically; the web closes the session.
2. If all users get 401 at once, check that `JWT_SECRET` is the same in the three services.

### Many 403 responses from the web

1. Check `CORS_ORIGIN` in the three services: it must be the current Vercel domain.
2. For `/internal/**`, check `INTERNAL_API_KEY` in the three services.

---

## 4. Rollback

Each service is rolled back from Render by redeploying a previous commit of `main`, or by reverting
the pull request and letting Render publish again. The routing in the clients changes only with a new
web deployment (Vercel) or a new APK.

---

## 5. Scaling

Not applicable on the free plan. Moving to a plan without sleep is the first step if traffic grows.

---

## 6. Post-incident

Record what happened, the affected service, the time to recover and the action taken in
`13-operations/incident-management.md`.
