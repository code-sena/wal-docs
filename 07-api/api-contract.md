# API Contract — Alerta Mujer

> **What this document is.** The exact shape of every request and every response of the API, with
> **all** behavioral decisions taken. Based on the real code of
> `backend/Backend-ALERTA-MUJER/backend-monolith` (controllers, entities and `db/init.sql`) and on
> the OpenAPI contracts in `entregar/07-api/contracts/openapi/`.

**What it is for.** So that web (`web/Frond-end-Web/alerta-mujer-web`) and mobile
(`movile/Frontend-movil`) can integrate without inventing anything, and so that the
`am-identity-service`, `am-alert-service`, `am-resource-service` and `am-admin-service` services
implement the same contract when they are split out of the monolith.

---

## 1. Common conventions

| Aspect | Value |
|---|---|
| Direct base (monolith) | `http://localhost:8080` |
| Base via gateway (planned) | `http://localhost:8080` routing to `:3001`–`:3004` |
| Format | JSON, UTF-8 |
| Field names | camelCase in JSON (Jackson default behavior over JPA entities) |
| Authentication | `Authorization: Bearer <jwt>` |
| Token | Issued by `POST /api/auth/login`, expires in 24 h (`jwt.expiration=86400000`) |
| Identifiers | auto-increment `integer` (see debt note below) |
| Dates | ISO 8601 emitted by the server (`timestamp`) |
| CORS | Configurable with `CORS_ORIGIN` (`*` by default in development) |
| Health check | `GET /health` → `{status, service, type}` |

### 1.1 Roles

Two, and only two: `user` and `administrator` (`role` table). `/api/admin/**` routes require the
`administrator` role; the rest require any authenticated account. Public reads are
`GET /api/zones`, `GET /api/zones/{id}`, `GET /api/zones/city/{city}`, `GET /api/zones/type/{type}`,
`GET /api/resources`, `GET /api/resources/{id}`, `GET /api/resources/city/{city}`,
`GET /api/resources/type/{type}` and `/health`.

### 1.2 Error format

Today the backend responds with Spring Boot's default error body
(`timestamp`, `status`, `error`, `message`, `path`). Target unified format (pending implementation,
see debt):

```json
{
  "error": "VALIDATION_ERROR",
  "message": "The email field is required",
  "details": [{"field": "email", "message": "required"}]
}
```

| Code | When |
|---|---|
| 200 | Success with body |
| 201 | Successful creation |
| 204 | Success without body |
| 400 | Client validation error |
| 401 | Not authenticated (missing/invalid token) |
| 403 | Authenticated without permission (wrong role) |
| 404 | Resource not found |
| 409 | Conflict (uniqueness: email, device_uuid, …) |
| 500 | Internal error |

### 1.3 Important note about DTOs

The current monolith receives and returns **JPA entities directly** or `Map<String, Object>` in
register/login/partial updates. This is declared debt: entities expose more than needed (e.g.
`passwordHash` on `account` if serialized poorly) and no format validations are applied at the
boundary. The specification below documents observable behavior today and the target format.

---

## 2. Authentication — `/api/auth`

Public.

### `POST /api/auth/register`

Creates `users` + `account` + `user_profile` with role `user`.

**Request**
```json
{
  "nombre": "Ana Pérez",
  "email": "ana@example.com",
  "password": "Segura123*",
  "telefono": "+573001112233"
}
```

**Response 201**
```json
{ "id": 42, "email": "ana@example.com", "message": "Registration successful" }
```

**Errors:** 400 validation · 409 duplicate email/phone.

### `POST /api/auth/login`

**Request**
```json
{ "email": "ana@example.com", "password": "Segura123*" }
```

**Response 200**
```json
{ "token": "<jwt>", "type": "Bearer", "expiresIn": 86400000, "user": { "id": 42, "email": "ana@example.com", "role": "user" } }
```

**Errors:** 401 invalid credentials · 403 blocked account (`account.status != active`).

### `POST /api/auth/forgot-password`

**Request** `{ "email": "ana@example.com" }` → always 200 (does not reveal whether the email
exists). Today it is a mock: creates the `recovery_request` and sends no real email.

### `POST /api/auth/reset-password`

**Request** `{ "token": "<token>", "newPassword": "Nueva123*" }` → 200. Mock without real token
validation (debt).

---

## 3. User profile — `/api/users`

Requires JWT.

### `GET /api/users/me` → `User`

```json
{
  "id": 42, "roleId": 1, "firstName": "Ana", "lastName": "Pérez",
  "telephone": "+573001112233", "email": "ana@example.com",
  "documentNumber": "1020304050", "documentType": "CC",
  "birthdate": "1995-04-12", "createdAt": "2026-03-01T10:00:00"
}
```

### `PUT /api/users/me`

**Request:** `Map` with fields to update (`firstName`, `lastName`, `telephone`, …).
**Response:** the updated `User`. **Errors:** 400 · 404.

---

## 4. Alerts — `/api/alerts`

Requires JWT. Core of the domain (ADR-002 / future alert-service).

| Method | Route | Description |
|---|---|---|
| GET | `/api/alerts` | List all (admin/debug) |
| GET | `/api/alerts/{id}` | Detail |
| GET | `/api/alerts/user/{userProfileId}` | History of a user |
| POST | `/api/alerts` | Create/activate alert |
| PUT | `/api/alerts/{id}` | Update (status, end, cancellation) |
| DELETE | `/api/alerts/{id}` | Delete |

**POST**
```json
{
  "userProfileId": 7, "deviceId": 3, "alertType": "main",
  "activationMethod": "panic_button", "status": "active",
  "message": "I need help", "latitude": 4.710989, "longitude": -74.072090
}
```

**Response 201**
```json
{ "id": 15, "status": "active", "startedAt": "2026-10-06T14:20:11", "alertType": "main" }
```

`alertType`: `main | mini | reminder` · `activationMethod`: `panic_button | widget | sudden_movement | physical_button | discreet_confirmation | automatic` · `status`: `active | resolved | cancelled | completed | failed`.

---

## 5. Emergency contacts — `/api/contacts`

Requires JWT. Full CRUD: `GET /api/contacts`, `GET /api/contacts/{id}`,
`GET /api/contacts/user/{userProfileId}`, `POST`, `PUT /api/contacts/{id}`, `DELETE /api/contacts/{id}`.

**POST example**
```json
{ "userProfileId": 7, "contactName": "Mom", "telephone": "+573109998877", "email": "mom@example.com", "relationship": "mother" }
```

---

## 6. Frequent locations — `/api/frequent-locations`

Requires JWT. `GET /api/frequent-locations`, `GET /api/frequent-locations/{id}`,
`GET /api/frequent-locations/user/{userId}`, `POST`, `PUT /{id}`, `DELETE /{id}`.

```json
{ "userProfileId": 7, "name": "Home", "address": "Cra 50 # 12-34", "city": "Bogotá",
  "latitude": 4.65, "longitude": -74.08, "notes": "", "riskLevel": "low", "isActive": true }
```

---

## 7. Evidence — `/api/evidences`

Requires JWT: `GET /`, `GET /{id}`, `POST`, `PUT /{id}`, `DELETE /{id}`.

```json
{ "alertId": 15, "mediaType": "video", "fileUrl": "https://storage.example/ev/15.mp4" }
```

`mediaType`: `photo | video | audio`. The binary lives outside the DB (the current contract only
stores the URL).

---

## 8. Devices — `/api/devices`

Requires JWT: `GET /`, `GET /{id}`, `POST`, `PUT /{id}`, `DELETE /{id}`.

```json
{ "accountId": 18, "deviceUuid": "uuid-…", "brand": "Samsung", "model": "A54",
  "osName": "Android", "osVersion": "14", "appVersion": "1.0.0", "gpsStatus": "active", "status": "active" }
```

---

## 9. Zones — `/api/zones`

Public GETs; POST/PUT/DELETE require JWT (ideally admin role).

| Method | Route |
|---|---|
| GET | `/api/zones` |
| GET | `/api/zones/{id}` |
| GET | `/api/zones/city/{city}` |
| GET | `/api/zones/type/{type}` (`safe` \| `risk`) |
| POST/PUT/DELETE | `/api/zones`, `/api/zones/{id}` |

```json
{ "name": "Central Park", "zoneType": "risk", "riskLevel": "high",
  "description": "…", "address": "…", "city": "Bogotá",
  "latitude": 4.61, "longitude": -74.07, "radiusMeters": 300, "isActive": true }
```

---

## 10. Zone reports — `/api/zone-reports`

Requires JWT.

`GET /` · `GET /{id}` · `GET /zone/{zoneId}` · `GET /my/{userProfileId}` · `GET /status/{status}` ·
`POST /` · `PUT /{id}/approve` (`{ "adminId": 1 }`) · `PUT /{id}/reject` (`{ "adminId": 1 }`) ·
`DELETE /{id}`.

```json
{ "zoneId": 6, "userProfileId": 7, "classification": "dangerous", "comment": "Poor street lighting", "status": "pending" }
```

`classification`: `safe | dangerous | regular` · `status`: `pending | approved | rejected`.

---

## 11. Emergency resources — `/api/resources`

Public GETs; writes require JWT.

`GET /` · `GET /{id}` · `GET /city/{city}` · `GET /type/{type}` · `POST` · `PUT /{id}` · `DELETE /{id}`.

`resourceType`: `emergency_line | hospital | health_center | police_station | police_post | shelter | support_organization`.

## 12. Resource calls — `/api/resource-calls`

Requires JWT: `GET /`, `GET /{id}`, `GET /user/{userProfileId}`, `GET /resource/{emergencyResourceId}`,
`GET /alert/{alertId}`, `POST`, `PUT /{id}`, `DELETE /{id}`.

```json
{ "emergencyResourceId": 2, "userProfileId": 7, "alertId": 15, "deviceId": 3,
  "telephoneDialed": "123", "status": "started", "startedAt": "…", "durationSeconds": null }
```

`status`: `started | answered | unanswered | failed | cancelled`.

---

## 13. Administration — `/api/admin`

Requires JWT with the `administrator` role.

| Method | Route | Description |
|---|---|---|
| GET | `/api/admin/users` | All users |
| GET | `/api/admin/alerts` | All alerts |
| GET | `/api/admin/contacts` | All contacts |
| GET | `/api/admin/evidences` | All evidence |
| GET | `/api/admin/devices` | All devices |
| GET | `/api/admin/zones` | Zones |
| GET | `/api/admin/reports` | Zone reports |
| GET | `/api/admin/audit-logs` | Audit log |
| POST | `/api/admin/audit-logs` | Register entry |
| GET | `/api/admin/user-reports` | User reports |
| GET | `/api/admin/user-reports/pending` | Pending |
| PUT | `/api/admin/user-reports/{id}` | Resolve (`{ "status": "resolved", "adminId": 1 }`) |

---

## 14. Requirement → endpoint traceability

| Requirement (SRS) | Endpoint(s) |
|---|---|
| FR1 device permissions | (mobile client) · reflected in `device_permission` |
| FR2 accounts | `POST /api/auth/register`, `POST /api/auth/login`, `POST /api/auth/forgot-password` |
| FR4 alerts | `POST/PUT /api/alerts`, `/api/alerts/user/{id}` |
| FR4.3 contacts | `/api/contacts/**` |
| FR6 evidence | `/api/evidences/**` |
| FR9 zones/map | `GET /api/zones/**`, `/api/zone-reports/**` |
| FR7 directory | `GET /api/resources/**`, `/api/resource-calls/**` |
| FR12 user portal | `/api/users/me`, `/api/frequent-locations/**`, `/api/evidences/**` |
| FR13 admin panel | `/api/admin/**`, `/api/zones` writes |
| RNF14 RBAC | JWT guards (401/403) on all of the above |

## 15. Declared contract debt

| # | Debt | Plan |
|---|---|---|
| A1 | Controllers expose JPA entities and `Map` instead of DTOs | Introduce DTO layer + Bean Validation |
| A2 | `POST /api/auth/forgot-password` and `reset-password` are mock | Real flow with `recovery_request` |
| A3 | `integer` ids in JSON; `wal-docs` speaks of `uuid` | Unify with the published contract |
| A4 | Spring's default error format | Adopt format §1.2 |
| A5 | Gateway (`gateway/`) and `am-*` services without logic | Implement contracts per service (`entregar/07-api/contracts/openapi/`) |
| A6 | No pagination on list endpoints | `?page=&size=` when volume grows |

## Correlations

- Data schema → [`data-model.md`](data-model.md)
- General architecture → [`arquitectura-panoramica.md`](arquitectura-panoramica.md)
- OpenAPI contracts per service → `contracts/openapi/`
