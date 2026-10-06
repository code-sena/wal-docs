# Contrato de API — Alerta Mujer

> **Qué es este documento.** La forma exacta de cada petición y de cada respuesta de la API, con
> **todas** las decisiones de comportamiento tomadas. Basado en el código real de
> `backend/Backend-ALERTA-MUJER/backend-monolith` (controladores, entidades y `db/init.sql`) y en
> los contratos OpenAPI de `entregar/07-api/contracts/openapi/`.

**Para qué sirve.** Para que web (`web/Frond-end-Web/alerta-mujer-web`) y móvil
(`movile/Frontend-movil`) puedan integrarse sin inventarse nada, y para que los servicios
`am-identity-service`, `am-alert-service`, `am-resource-service` y `am-admin-service` implementen
el mismo contrato cuando se separen del monolito.

---

## 1. Convenciones comunes

| Aspecto | Valor |
|---|---|
| Base directa (monolito) | `http://localhost:8080` |
| Base vía gateway (planificado) | `http://localhost:8080` enrutando a `:3001`–`:3004` |
| Formato | JSON, UTF-8 |
| Nombres de campo | camelCase en JSON (comportamiento de Jackson sobre entidades JPA) |
| Autenticación | `Authorization: Bearer <jwt>` |
| Token | Lo emite `POST /api/auth/login`, expira en 24 h (`jwt.expiration=86400000`) |
| Identificadores | `integer` autoincremental (ver nota de deuda abajo) |
| Fechas | ISO 8601 emitidas por el servidor (`timestamp`) |
| CORS | Configurable con `CORS_ORIGIN` (`*` por defecto en desarrollo) |
| Health check | `GET /health` → `{status, service, type}` |

### 1.1 Roles

Dos, y solo dos: `user` y `administrator` (tabla `role`). Las rutas `/api/admin/**` exigen rol
`administrator`; el resto requiere cualquier cuenta autenticada. Las lecturas públicas son
`GET /api/zones`, `GET /api/zones/{id}`, `GET /api/zones/city/{city}`, `GET /api/zones/type/{type}`,
`GET /api/resources`, `GET /api/resources/{id}`, `GET /api/resources/city/{city}`,
`GET /api/resources/type/{type}` y `/health`.

### 1.2 Formato de error

Hoy el backend responde errores con el cuerpo por defecto de Spring Boot
(`timestamp`, `status`, `error`, `message`, `path`). Formato objetivo unificado (pendiente de
implementar, ver deuda):

```json
{
  "error": "VALIDATION_ERROR",
  "message": "El campo email es obligatorio",
  "details": [{"field": "email", "message": "required"}]
}
```

| Código | Cuándo |
|---|---|
| 200 | Éxito con cuerpo |
| 201 | Creación exitosa |
| 204 | Éxito sin cuerpo |
| 400 | Error de validación del cliente |
| 401 | No autenticado (token ausente/inválido) |
| 403 | Autenticado sin permiso (rol incorrecto) |
| 404 | Recurso no encontrado |
| 409 | Conflicto (unicidad: email, device_uuid, …) |
| 500 | Error interno |

### 1.3 Nota importante sobre DTOs

El monolito actual recibe y devuelve **entidades JPA directamente** o `Map<String, Object>` en
registro/login/actualizaciones parciales. Esto es deuda declarada: las entidades exponen más de lo
necesario (p. ej. `passwordHash` en `account` si se serializa mal) y no aplican validaciones de
formato en la frontera. La especificación de abajo documenta el comportamiento observable hoy y el
formato objetivo (los campos marcados ⭐).

---

## 2. Autenticación — `/api/auth`

Público.

### `POST /api/auth/register`

Crea `users` + `account` + `user_profile` con rol `user`.

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
{ "id": 42, "email": "ana@example.com", "message": "Registro exitoso" }
```

**Errores:** 400 validación · 409 email/teléfono duplicado.

### `POST /api/auth/login`

**Request**
```json
{ "email": "ana@example.com", "password": "Segura123*" }
```

**Response 200**
```json
{ "token": "<jwt>", "type": "Bearer", "expiresIn": 86400000, "user": { "id": 42, "email": "ana@example.com", "role": "user" } }
```

**Errores:** 401 credenciales inválidas · 403 cuenta bloqueada (`account.status != active`).

### `POST /api/auth/forgot-password`

**Request** `{ "email": "ana@example.com" }` → 200 siempre (no revela si el correo existe).
Hoy es un mock: crea el `recovery_request` y no envía correo real.

### `POST /api/auth/reset-password`

**Request** `{ "token": "<token>", "newPassword": "Nueva123*" }` → 200. Mock sin validación real
del token (deuda).

---

## 3. Perfil de usuaria — `/api/users`

Requiere JWT.

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

**Request:** `Map` con campos a actualizar (`firstName`, `lastName`, `telephone`, …).
**Response:** el `User` actualizado. **Errores:** 400 · 404.

---

## 4. Alertas — `/api/alerts`

Requiere JWT. Núcleo del dominio (ADR-002 / alert-service futuro).

| Método | Ruta | Descripción |
|---|---|---|
| GET | `/api/alerts` | Lista todas (admin/depuración) |
| GET | `/api/alerts/{id}` | Detalle |
| GET | `/api/alerts/user/{userProfileId}` | Historial de una usuaria |
| POST | `/api/alerts` | Crear/activar alerta |
| PUT | `/api/alerts/{id}` | Actualizar (estado, fin, cancelación) |
| DELETE | `/api/alerts/{id}` | Borrar |

**POST**
```json
{
  "userProfileId": 7, "deviceId": 3, "alertType": "main",
  "activationMethod": "panic_button", "status": "active",
  "message": "Necesito ayuda", "latitude": 4.710989, "longitude": -74.072090
}
```

**Response 201**
```json
{ "id": 15, "status": "active", "startedAt": "2026-10-06T14:20:11", "alertType": "main" }
```

`alertType`: `main | mini | reminder` · `activationMethod`: `panic_button | widget | sudden_movement | physical_button | discreet_confirmation | automatic` · `status`: `active | resolved | cancelled | completed | failed`.

---

## 5. Contactos de emergencia — `/api/contacts`

Requiere JWT. CRUD completo: `GET /api/contacts`, `GET /api/contacts/{id}`,
`GET /api/contacts/user/{userProfileId}`, `POST`, `PUT /api/contacts/{id}`, `DELETE /api/contacts/{id}`.

**POST ejemplo**
```json
{ "userProfileId": 7, "contactName": "Mamá", "telephone": "+573109998877", "email": "mama@example.com", "relationship": "madre" }
```

---

## 6. Ubicaciones frecuentes — `/api/frequent-locations`

Requiere JWT. `GET /api/frequent-locations`, `GET /api/frequent-locations/{id}`,
`GET /api/frequent-locations/user/{userId}`, `POST`, `PUT /{id}`, `DELETE /{id}`.

```json
{ "userProfileId": 7, "name": "Casa", "address": "Cra 50 # 12-34", "city": "Bogotá",
  "latitude": 4.65, "longitude": -74.08, "notes": "", "riskLevel": "low", "isActive": true }
```

---

## 7. Evidencia — `/api/evidences`

Requiere JWT: `GET /`, `GET /{id}`, `POST`, `PUT /{id}`, `DELETE /{id}`.

```json
{ "alertId": 15, "mediaType": "video", "fileUrl": "https://storage.example/ev/15.mp4" }
```

`mediaType`: `photo | video | audio`. El binario vive fuera de la BD (el contrato actual guarda
solo la URL).

---

## 8. Dispositivos — `/api/devices`

Requiere JWT: `GET /`, `GET /{id}`, `POST`, `PUT /{id}`, `DELETE /{id}`.

```json
{ "accountId": 18, "deviceUuid": "uuid-…", "brand": "Samsung", "model": "A54",
  "osName": "Android", "osVersion": "14", "appVersion": "1.0.0", "gpsStatus": "active", "status": "active" }
```

---

## 9. Zonas — `/api/zones`

GET públicos; POST/PUT/DELETE requieren JWT (idealmente rol admin).

| Método | Ruta |
|---|---|
| GET | `/api/zones` |
| GET | `/api/zones/{id}` |
| GET | `/api/zones/city/{city}` |
| GET | `/api/zones/type/{type}` (`safe` \| `risk`) |
| POST/PUT/DELETE | `/api/zones`, `/api/zones/{id}` |

```json
{ "name": "Parque Central", "zoneType": "risk", "riskLevel": "high",
  "description": "…", "address": "…", "city": "Bogotá",
  "latitude": 4.61, "longitude": -74.07, "radiusMeters": 300, "isActive": true }
```

---

## 10. Reportes de zona — `/api/zone-reports`

Requiere JWT.

`GET /` · `GET /{id}` · `GET /zone/{zoneId}` · `GET /my/{userProfileId}` · `GET /status/{status}` ·
`POST /` · `PUT /{id}/approve` (`{ "adminId": 1 }`) · `PUT /{id}/reject` (`{ "adminId": 1 }`) ·
`DELETE /{id}`.

```json
{ "zoneId": 6, "userProfileId": 7, "classification": "dangerous", "comment": "Se ve alumbrado deficiente", "status": "pending" }
```

`classification`: `safe | dangerous | regular` · `status`: `pending | approved | rejected`.

---

## 11. Recursos de emergencia — `/api/resources`

GET públicos; escritura con JWT.

`GET /` · `GET /{id}` · `GET /city/{city}` · `GET /type/{type}` · `POST` · `PUT /{id}` · `DELETE /{id}`.

`resourceType`: `emergency_line | hospital | health_center | police_station | police_post | shelter | support_organization`.

## 12. Llamadas a recursos — `/api/resource-calls`

Requiere JWT: `GET /`, `GET /{id}`, `GET /user/{userProfileId}`, `GET /resource/{emergencyResourceId}`,
`GET /alert/{alertId}`, `POST`, `PUT /{id}`, `DELETE /{id}`.

```json
{ "emergencyResourceId": 2, "userProfileId": 7, "alertId": 15, "deviceId": 3,
  "telephoneDialed": "123", "status": "started", "startedAt": "…", "durationSeconds": null }
```

`status`: `started | answered | unanswered | failed | cancelled`.

---

## 13. Administración — `/api/admin`

Requiere JWT con rol `administrator`.

| Método | Ruta | Descripción |
|---|---|---|
| GET | `/api/admin/users` | Todas las usuarias |
| GET | `/api/admin/alerts` | Todas las alertas |
| GET | `/api/admin/contacts` | Todos los contactos |
| GET | `/api/admin/evidences` | Toda la evidencia |
| GET | `/api/admin/devices` | Todos los dispositivos |
| GET | `/api/admin/zones` | Zonas |
| GET | `/api/admin/reports` | Reportes de zona |
| GET | `/api/admin/audit-logs` | Auditoría |
| POST | `/api/admin/audit-logs` | Registrar entrada |
| GET | `/api/admin/user-reports` | Reportes de usuarios |
| GET | `/api/admin/user-reports/pending` | Pendientes |
| PUT | `/api/admin/user-reports/{id}` | Resolver (`{ "status": "resolved", "adminId": 1 }`) |

---

## 14. Trazabilidad requisito → endpoint

| Requisito (SRS) | Endpoint(s) |
|---|---|
| FR1 permisos dispositivo | (cliente móvil) · se reflejan en `device_permission` |
| FR2 cuentas | `POST /api/auth/register`, `POST /api/auth/login`, `POST /api/auth/forgot-password` |
| FR4 alertas | `POST/PUT /api/alerts`, `/api/alerts/user/{id}` |
| FR4.3 contactos | `/api/contacts/**` |
| FR6 evidencia | `/api/evidences/**` |
| FR9 zonas/mapa | `GET /api/zones/**`, `/api/zone-reports/**` |
| FR7 directorio | `GET /api/resources/**`, `/api/resource-calls/**` |
| FR12 portal usuaria | `/api/users/me`, `/api/frequent-locations/**`, `/api/evidences/**` |
| FR13 panel admin | `/api/admin/**`, `/api/zones` escritura |
| RNF14 RBAC | guards JWT (401/403) en todos los anteriores |

## 15. Deuda declarada del contrato

| # | Deuda | Plan |
|---|---|---|
| A1 | Controllers exponen entidades JPA y `Map` en vez de DTOs | Introducir capa DTO + validación Bean Validation |
| A2 | `POST /api/auth/forgot-password` y `reset-password` son mock | Flujo real con `recovery_request` |
| A3 | IDs `integer` en JSON; `wal-docs` habla de `uuid` | Unificar con el contrato a publicar |
| A4 | Error format de Spring por defecto | Adoptar el formato §1.2 |
| A5 | Gateway (`gateway/`) y servicios `am-*` sin lógica | Implementar contratos por servicio (`entregar/07-api/contracts/openapi/`) |
| A6 | Sin paginación en listados | `?page=&size=` cuando crezca el volumen |

## Correlaciones

- Esquema de datos → [`data-model.md`](data-model.md)
- Arquitectura general → [`arquitectura-panoramica.md`](arquitectura-panoramica.md)
- Contratos OpenAPI por servicio → `07-api/contracts/openapi/`
