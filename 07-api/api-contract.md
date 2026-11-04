# API Contract — Alerta Mujer

> **What this document is.** The shape of the requests and responses that the web and the mobile
> app consume today, based on the controllers (`adapter/in/web`) of the three services in
> `Backend-ALERTA-MUJER/microservicios` (`main`, 28/10/2026) and on the technical manual. When each
> module moved to hexagonal architecture, a smoke test compared the old and the new JSON (same keys
> and same order), so these examples are valid for the deployed version.

**What it is for.** So that web (`Frond-end-Web/alerta-mujer-web`) and mobile
(`AlertaMujer/Frontend-movil`) can integrate without inventing anything. The complete list of
endpoints (Auth 51, Alert 93, Evidence 14) is in the controllers of each module; this document
covers the ones the clients use.

---

## 1. Common conventions

| Aspect | Value |
|---|---|
| Services | Auth `:8081`, Alert `:8082`, Evidence `:8083` (local); `https://alerta-mujer-{auth,alert,evidence}.onrender.com` (production) |
| Routing | No gateway. `/api/evidences` → Evidence; `/api/alerts`, `/api/contacts`, `/api/location-logs`, `/api/zones`, `/api/resources`, `/api/saved-routes`, `/api/frequent-locations`, `/api/received-alerts`, `/api/zone-reports`, `/api/resource-calls`, `/api/safe-trips`, `/api/auto-protection`, `/api/public/**` → Alert; everything else → Auth |
| Format | JSON, UTF-8; evidence upload as `multipart/form-data` |
| Field names | camelCase in JSON; each module has its own request and response DTOs |
| Authentication | `Authorization: Bearer <jwt>` |
| Token | Access token 24 h, refresh token 15 days, payload `{ sub, role, iat, exp }` |
| Identifiers | `bigint` integers |
| Dates | ISO 8601 (`2026-10-05T10:30:00`); birth date `yyyy-MM-dd` |
| CORS | Restricted to the web domain (`CORS_ORIGIN`) |
| Health check | `GET /actuator/health` → `{ "status": "UP" }` (public, each service) |

### 1.1 Roles

Two, and only two: `user` (role_id 1, `role: user` in the token) and `administrator` (role_id 2,
`role: admin`). `/api/admin/**` requires `hasRole('ADMIN')`; the rest require an authenticated
account. Each user only sees her own data: the owner is taken from the JWT. Public endpoints: register,
login, Google, refresh, password recovery, `GET` of zones and resources, `/api/settings` (public
configuration), `/api/public/tracking/{code}`, `/api/public/auto-protection/**` (device key) and
`/actuator/health`.

### 1.2 Error format

```json
{
  "status": 404,
  "error": "Not Found",
  "message": "Alert not found with id: 99"
}
```

Validation errors add `fieldErrors`. Between services the error type is preserved.

| Code | When |
|---|---|
| 200 | Success with body |
| 400 | Validation error in the body, a database constraint failed (without showing the SQL) or a required header is missing |
| 401 | Invalid credentials, account blocked or expired token (the mobile app refreshes it) |
| 403 | Role without permission, resource of another user or missing internal key |
| 404 | Resource not found |
| 409 | Email already exists, an active alert already exists, contact limit reached, route limit reached |
| 413 | Evidence larger than 50 MB |
| 429 | More than 5 attempts per minute in authentication |
| 500 | Unexpected error |

### 1.3 Important note about DTOs

Controllers no longer return JPA entities: every module has its own request and response DTOs and
the JPA entities are package-private inside `adapter/out/persistence`. Validation happens at the
boundary (Bean Validation) and in the domain models.

---

## 2. Authentication — `/api/auth` (Auth)

### `POST /api/auth/register`

```json
{ "nombre": "Maria Garcia", "email": "maria.garcia@example.com", "password": "********", "telefono": "3001234567" }
```

Validations: `nombre` 2 to 100 characters, valid `email`, `password` 8 to 100 characters, `telefono`
max. 20. Creates the user, her account and her profile.

```json
{
  "token": "eyJhbGciOiJIUzI1NiJ9...",
  "refreshToken": null,
  "user": { "id": 1, "firstName": "Maria", "lastName": "Garcia", "email": "maria.garcia@example.com",
            "telephone": "3001234567", "roleId": 1, "roleName": "user", "createdAt": "2026-10-15T10:30:00" }
}
```

Errors: `409` if the email exists; `400` with `fieldErrors`.

### `POST /api/auth/login`

`{ "email": "...", "password": "..." }` → `{ token, refreshToken, user }`. Max. 5 attempts per minute
per IP. Errors: `401` "Invalid credentials" or "Account blocked"; `429`.

### `POST /api/auth/google` and `POST /api/auth/refresh`

- Google: `{ "idToken": "..." }` (Firebase token on web, Google ID token on mobile). Verified with its
  signature on the server; creates the account if the email does not exist. Same response as login.
- Refresh: `{ "refreshToken": "..." }` → `{ "token": "..." }`; `401` if it expired.

### `POST /api/auth/forgot-password`

Sends a 6-digit code to the email. The answer is the same whether the account exists or not. Same
rate limit as login.

### `POST /api/auth/reset-password`

Verifies the code (valid 15 minutes, 5 attempts, only the hash is stored) and saves the new password.
There is also a code verification endpoint used by the clients before this step.

---

## 3. User profile — `/api/users` (Auth)

### `GET /api/users/me` → `User`

Data of the authenticated user. Without a token, `403`.

### `PUT /api/users/me`

Updates `firstName`, `lastName`, `telephone`, `documentNumber`, `documentType`, `birthdate`
(`yyyy-MM-dd`). The account can also be deleted by the user from the app (Mi perfil > Eliminar cuenta).

---

## 4. Alerts — `/api/alerts` (Alert)

`POST /api/alerts` (JWT). The owner comes from the token. Only one active alert per user.

```json
{ "deviceId": 1, "alertType": "main", "activationMethod": "panic_button", "status": "active",
  "message": "Alerta SOS activada desde la app", "latitude": 2.693470, "longitude": -75.329360 }
```

`activationMethod`: `panic_button`, `contact` (may notify only that contact) or
`suspicious_movement` (shake). Response: `{ id, userProfileId, deviceId, alertType, activationMethod,
status, message, latitude, longitude, startedAt, endedAt, cancelledAt }`. On creation the service
notifies the contacts.

| Method | Route | Description |
|---|---|---|
| GET | `/api/alerts/me` | Alerts of the user, newest first |
| GET | `/api/alerts/{id}` | One alert (only if it is hers; otherwise `403`) |
| GET | `/api/alerts/{id}/locations` | GPS route of the alert |
| PUT | `/api/alerts/{id}` | Change the status (e.g. `cancelled`); triggers the end notice |
| GET, POST | `/api/alerts/{id}/messages` | Alert chat |
| GET | `/api/received-alerts` | Alerts of the people who have the user as a contact |
| GET | `/api/public/tracking/{code}` | Public tracking (no JWT, code from the email link) |

States: `active`, `resolved` (attended), `cancelled`, `expired`. Push types: `ALERTA_SOS` and
`ALERTA_FINALIZADA`.

---

## 5. Emergency contacts — `/api/contacts` (Alert)

| Method | Route | Description |
|---|---|---|
| GET | `/api/contacts/user/{userProfileId}` | Contacts of a user (JWT) |
| POST | `/api/contacts` | Create (profile taken from the token) |
| PUT | `/api/contacts/{id}` | Update (owner only) |
| DELETE | `/api/contacts/{id}` | Delete (owner only) |

Body: `{ "contactName": "Juan Pérez", "telephone": "3111234567", "email": "juan@example.com",
"relationship": "Amigo" }`. Phone 7 to 20 characters with optional leading `+`. The email is where the
notice arrives. The server rejects a new contact when the configured limit is reached (`409`).

---

## 6. Frequent locations — `/api/frequent-locations` (Alert)

| Method | Route | Description |
|---|---|---|
| GET | `/api/frequent-locations/me` | Saved places |
| POST / PUT / DELETE | `/api/frequent-locations` and `/{id}` | Create, edit, delete. `riskLevel`: `muy_segura`, `moderada`, `muy_insegura` |
| POST | `/api/location-logs` | Save a point `{ latitude, longitude, accuracy, alertId, recordedAt }` |
| GET | `/api/location-logs/me?limit=50`, `/me/latest`, `/alert/{alertId}` | Last points, last point, points of an alert |
| GET / POST / PATCH / DELETE | `/api/saved-routes/me`, `/api/saved-routes`, `/{id}` | Routes (max. 4, 2 starred; `409` above the limit) |

---

## 7. Evidence — `/api/evidences` (Evidence)

| Method | Route | Description |
|---|---|---|
| GET | `/api/evidences/me` | Evidence of the user |
| POST | `/api/evidences/upload` | `multipart/form-data` with `file` and `alertId`, max. 50 MB (`413`) |
| GET | `/api/evidences/{id}/file` | View the file (`?download=true` to download) |
| DELETE | `/api/evidences/{id}` | Delete the record and the file |

Each file is stored in Backblaze B2 with its SHA-256 hash and kept 30 days.

---

## 8. Devices — `/api/devices` (Auth)

| Method | Route | Description |
|---|---|---|
| GET | `/api/devices/user` | Devices of the user |
| POST | `/api/devices` | Register a device |
| PUT | `/api/devices/{id}` | Update (push token and permissions) |
| DELETE | `/api/devices/{id}` | Delete |
| GET | `/api/settings` | Public configuration (minimum version, etc.), no secrets |

---

## 9. Zones — `/api/zones` (Alert)

`GET /api/zones`, `/city/{city}`, `/type/{type}` are public. Types: `risk`, `safe`, `help`, `policia`,
`asistencia`. `POST`, `PUT /{id}`, `DELETE /{id}` are admin only.

```json
{ "id": 1, "name": "Parque Santander", "zoneType": "risk", "riskLevel": "high",
  "description": "Zona de alto riesgo", "city": "Neiva", "latitude": 2.927300,
  "longitude": -75.289500, "radiusMeters": 500.0, "isActive": true }
```

---

## 10. Zone reports — `/api/zone-reports` (Alert)

`POST /api/zone-reports` (user), `PUT /api/zone-reports/{id}/approve` and `/reject` (admin only).
States: `pending`, `approved`, `rejected`.

---

## 11. Emergency resources — `/api/resources` (Alert)

`GET /api/resources`, `/city/{city}`, `/type/{type}` are public. Writes are admin only (help points,
RF-ADM-012).

---

## 12. Resource calls — `/api/resource-calls` (Alert)

`POST /api/resource-calls` records a manual call started by the user from the dialer;
`GET /api/resource-calls/me` lists them (history shown in the web portal).

### 12.1 Trips and automatic protection (Alert)

| Method and route | Access | Use |
|---|---|---|
| `POST /api/safe-trips` | User | Start a trip with destination and arrival minutes (5 to 240) |
| `GET /api/safe-trips/active` | User | Active trip, if any |
| `POST /api/safe-trips/{id}/arrived`, `/extend`, `/cancel` | User | Confirm arrival, extend or cancel |
| `POST /api/safe-trips/{id}/heartbeat` | User | Heartbeat with location (every 2 minutes) |
| `POST /api/auto-protection/enable`, `/disable`, `GET /api/auto-protection` | User | Turn automatic protection on or off on a phone; enabling returns the device key once |
| `POST /api/public/auto-protection/start` | Phone, `X-Protection-Key` | Start a watch when leaving a frequent place |
| `POST /api/public/auto-protection/{id}/heartbeat`, `/end` | Phone | Heartbeat and end when arriving at a frequent place |
| `POST /api/public/auto-protection/{id}/no-answer`, `/unconfirmed`, `/duress` | Phone | Create the alert: no answer to "Is everything OK?", "Did you arrive OK?" not confirmed, or duress PIN (silent alert) |

---

## 13. Administration — `/api/admin` (Auth)

Requires JWT with the `administrator` role; otherwise `403`.

| Method | Route | Description |
|---|---|---|
| GET, PATCH | `/api/admin/users`, `/api/admin/users/{id}` | List (paginated), edit and block (with reason and capture) |
| GET | `/api/admin/alerts` | All alerts; marking as attended sends `status` |
| GET, POST, PUT, DELETE | `/api/admin/user-reports` and `/{id}` | Moderation cases |
| GET | `/api/admin/audit-logs` | Audit log with filters and export |
| GET | `/api/admin/evidences`, `/api/admin/devices`, `/api/admin/zones`, `/api/admin/reports`, `/api/admin/contacts` | Admin views (contacts only show send status and relationship) |

Also under `/api/admin`: dashboard statistics, alert reports with PDF and CSV export, account
deletion with two administrators, system configuration with confirmation and system status.

### 13.1 Internal API

`POST /internal/rpc/{Contract}/{method}` and `POST /internal/events`, only between services, with
`X-Internal-Key` equal to `INTERNAL_API_KEY`. Not for the clients.

---

## 14. Requirement → endpoint traceability

| Requirement (SRS v2.6) | Endpoint(s) |
|---|---|
| RF-PER | Mobile client; permissions reported in `PUT /api/devices/{id}` |
| RF-USU | `/api/auth/register`, `/login`, `/google`, `/refresh`, `/forgot-password`, `/reset-password`, `/api/users/me` |
| RF-CON | `/api/contacts/**`; limit from `system_configuration` |
| RF-ALE, RF-ACT | `POST/PUT /api/alerts`, `/api/alerts/{id}/locations`, `/api/alerts/{id}/messages`, `/api/public/tracking/{code}` |
| RF-TRA | `/api/safe-trips/**`, `/api/auto-protection/**`, `/api/public/auto-protection/**` |
| RF-EVI | `/api/evidences/**` |
| RF-UBI | `/api/location-logs/**`, `/api/frequent-locations/**`, `/api/saved-routes/**`, `GET /api/zones/**` |
| RF-REC | `GET /api/resources/**`, `/api/resource-calls/**` |
| RF-NOT | `/api/received-alerts`; FCM push |
| RF-WEB | `/api/users/me`, `/api/alerts/me`, `/api/evidences/me`, `/api/devices/user`, `/api/received-alerts` |
| RF-ADM, RF-AUD, RF-SES | `/api/admin/**`, writes on `/api/zones` and `/api/resources` |
| RF-CFG | `GET /api/settings` |
| RNF-SEG-004 | JWT and role checks (401/403) on all of the above |

## 15. Declared contract debt

| # | Debt | Plan |
|---|---|---|
| A1 | `GET /api/contacts` (all contacts, admin only) still exists | Remove it |
| A2 | Unknown routes must answer 404, not 500 | Revalidate CP-SEG-003 in the microservices |
| A3 | Routing by path repeated in mobile and web | Decide on a gateway (DIS-ARQ-04) |
| A4 | No pagination on most user lists | `?page=&size=` when volume grows (admin users already paginated) |
| A5 | OpenAPI files describe the earlier gateway design | Generate them from the controllers |
| A6 | No local queue for `POST /api/alerts` without connection | Client-side queue without duplicates |

## Correlations

- Data schema → [`data-model.md`](data-model.md)
- General architecture → [`arquitectura-panoramica.md`](arquitectura-panoramica.md)
- OpenAPI contracts per service → `contracts/openapi/`
