# Cross-Cutting Concerns

> Concerns that apply across the three backend services (Auth, Alert and Evidence) and the two
> clients. Most of them live in the `shared-kernel` module so every service applies them the same
> way. This expands on the summary table in `overview.md` §8.

---

## 1. Authentication / Authorization

**Status:** implemented.

- Passwords hashed with BCrypt (8 to 100 characters) from an output adapter of the identity module.
- JWT access token of 24 hours with the user id and role, and refresh token of 15 days, both signed
  with `JWT_SECRET`. `JwtKeyProvider` rejects an empty secret and the value that was once
  published, so the service does not start with them. The secret was rotated on 28/10/2026.
- Every service validates the JWT with the same key, without calling Auth.
- Google login: the Firebase token (web) or the Google ID token (mobile) is verified with its
  signature on the server (`GoogleVerificadorAdapter`); if the email does not exist, the account is
  created.
- Roles: `user` (role_id 1) and `administrator` (role_id 2, `role: admin` in the token). Admin
  endpoints use `@PreAuthorize("hasRole('ADMIN')")`. In the use cases of alerts, contacts,
  locations, routes and zone reports the owner is taken from the JWT, never from the body.
- Web: `authGuard`, `userGuard` and `adminGuard`; admin routes exist only under `/admin`. The guards
  only guide the navigation: the access decision is made on the server.
- Mobile: tokens in `expo-secure-store`; the HTTP client refreshes the token automatically on 401.
- Automatic protection uses its own device key (`X-Protection-Key`, only its hash is stored in
  `alert.protection_device`) so that it keeps working when the session expires.

---

## 2. Logging

**Status:** basic.

- Spring Boot logs of each service, read from the **Logs** tab in Render.
- Without an email provider configured, emails are written to the log (development only).
- Mobile: HTTP client logs only in development (`__DEV__`); 64 `console` calls still remain outside
  `__DEV__` and must be reviewed so they do not expose personal data (CAL-10).
- Installed APK: `adb logcat -v time ReactNativeJS:V AndroidRuntime:E *:S`.

---

## 3. Tracing

**Status:** not adopted. There is no correlation id or distributed tracing between the services.
The RPC client keeps the error type between services, which helps to locate where a 404, 403, 400
or 409 started.

---

## 4. Health Checks

**Status:** implemented. `GET /actuator/health` is public in each service and returns
`{ "status": "UP" }`. Render uses it, and UptimeRobot calls the three endpoints every 5 minutes.
The admin panel has a "System status" page with the database connection, push notifications,
active alerts and the last review of expired alerts.

---

## 5. Error format

**Status:** implemented in the shared kernel.

```json
{
  "status": 404,
  "error": "Not Found",
  "message": "Alert not found with id: 99"
}
```

Codes in use: 400 (with `fieldErrors` for validation, or a generic message when a database
constraint fails, without showing the SQL), 401, 403, 404, 409, 413, 429 and 500. Domain exceptions
are translated to HTTP in the shared kernel, and the type is preserved between services. A missing
required header answers 400. An unknown route must answer 404 (RNF-API-001, to be revalidated in
the microservices).

---

## 6. Rate Limiting

**Status:** implemented in Auth. `RateLimitFilter` allows 5 attempts per minute on login, Google,
registration and password recovery, counting the real client IP
(`forward-headers-strategy`). Afterwards it answers 429. Other limits live in the domain: one
active alert per user, a message limit for contacts in the alert chat, 4 saved routes (2 starred)
and the configurable contact limit.

---

## 7. CORS

**Status:** implemented. Each service restricts browser requests to the web domain configured in
`CORS_ORIGIN`; the `*` wildcard is not used in production. A different origin receives 403
(checked in production). The mobile app is not affected by CORS.

---

## 8. Circuit Breaker

**Status:** not adopted. Instead, each notice is recorded per contact and channel with its result
(`sent`, `failed`, `expired`), number of attempts and reason, so a provider failure does not stop
the rest of the notices. RPC calls between services wait up to 90 seconds to tolerate the cold
start of the free plan.

---

## 9. Feature flags

**Status:** user-level switches only. The user can enable or disable each activation method (SOS
button, single contact, shake, trip and widget) and automatic protection. The administrator
controls system parameters (alert duration, emergency number, contact limit and minimum app
version), which the clients read from `/api/settings`. There is no runtime feature-flag tool.

---

## Correlations

- Summary table → `05-architecture/overview.md` §8
- Security threat model → `05-architecture/security-threat-model.md`
- Security policy → `00-governance/security-policy.md`
- Observability → `13-operations/observability.md`
