# Modelo de datos — Alerta Mujer

**El único sitio donde vive el modelo de datos del proyecto.** Quien implemente no necesita abrir
el código ni conectarse al motor para saber qué hay, de qué tipo, con qué regla y **dónde vive esa
regla hoy**.

- **Fecha:** 2026-10-06
- **Verificado contra:** PostgreSQL 16, base `alerta_mujer`, inicializada con `backend/Backend-ALERTA-MUJER/db/init.sql`.
- **Rige bajo:** la documentación de `wal-docs/06-data/models.md` y los bounded contexts de
  `wal-docs/02-domain/domain-map.md`. Si este documento contradice al motor, **gana el motor**; si
  contradice a `wal-docs`, corrige este documento.
- **Tablo y columnas en inglés**, singular, `snake_case` (convención técnica del proyecto, ADR-001).

---

## Cómo se lee este documento

Toda regla del modelo lleva una marca, y **solo hay tres**:

| Marca | Significa |
|---|---|
| **motor** | Existe en Postgres ahora mismo (`init.sql`). Un `INSERT` manual la respeta o falla |
| **solo dominio/aplicación** | La garantiza el backend JPA/servicios y nada más. Un `INSERT` por `psql` la salta sin ruido |
| **pendiente** | No existe todavía; queda como trabajo futuro |

**Por qué la tercera columna es el corazón del documento.** Una invariante que solo vive en la
aplicación protege a la aplicación, no a los datos: cualquier `psql`, cualquier migración y
cualquier servicio futuro la saltan sin enterarse. Todo lo expresable en el motor y que aún no
está ahí se declara pendiente; no se promete.

---

## 0. Convenciones del esquema

- Tablas en singular y `snake_case` (`user`, `account`, `alert`, `zone`).
- PK `id` de tipo `integer` autoincremental.
- FKs con el patrón `[tabla_referenciada]_id` (p. ej. `user_profile_id`).
- Timestamps `timestamp` (sin zona horaria), con `DEFAULT CURRENT_TIMESTAMP` cuando aplica.
- Sin soft delete: el estado inactivo se resuelve con `status` o `is_active` propio de cada tabla.
- Sin columnas `created_by` / `updated_by`: la trazabilidad administrativa vive en `audit_log`.
- Cuatro esquemas en la base: `identity`, `alert`, `resource`, `admin`.
- Migraciones: el esquema inicial lo carga `db/init.sql` en el primer arranque de Docker; Liquibase
  queda como evolución planificada (ver `wal-docs/06-data/migration-strategy.md`).

---

## 1. Esquema `identity` — cuentas y dispositivos

### 1.1 `role`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| name | varchar(30) | No | `user` \| `administrator` | UNIQUE (**motor**) |
| description | varchar(255) | Sí | Descripción | — |

Seed: dos filas (`user`, `administrator`).

### 1.2 `users`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| role_id | integer | No | Rol asignado | FK → `role.id` (**motor**) |
| first_name | varchar(100) | No | Nombre | — |
| last_name | varchar(100) | No | Apellidos | — |
| telephone | varchar(20) | Sí | Teléfono | UNIQUE |
| email | varchar(150) | No | Correo | UNIQUE (**motor**) |
| document_number | varchar(20) | Sí | Documento | UNIQUE |
| document_type | varchar(20) | Sí | Tipo de documento | — |
| birthdate | date | Sí | Fecha de nacimiento | — |
| created_at | timestamp | No | Creación | DEFAULT (**motor**) |

### 1.3 `account`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| user_id | integer | No | Usuario dueño | FK → `users.id`, UNIQUE (1:1, **motor**) |
| password_hash | varchar(255) | No | Hash BCrypt | — |
| status | varchar(20) | No | `active` \| `blocked` \| `inactive` | — |
| last_access | timestamp | Sí | Último acceso | — |

Regla **solo dominio**: solo existe una cuenta por usuario y el hash nunca se expone por la API.

### 1.4 `user_profile`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| user_id | integer | No | Usuario | FK → `users.id`, UNIQUE (1:1) |
| profile_photo_url | varchar(500) | Sí | Foto de perfil | — |
| tutorial_completed | boolean | No | Tutorial completado | DEFAULT false |
| tutorial_seen_at | timestamp | Sí | Visto el | — |
| created_at / updated_at | timestamp | — | Auditoría | DEFAULT / NULL |

Es la tabla referenciada por casi todos los demás módulos (alertas, contactos, zonas,
notificaciones). Solo aplica a usuarios con rol `user` (**solo dominio**).

### 1.5 `admin_profile`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| account_id | integer | No | Cuenta administradora | FK → `account.id`, UNIQUE (1:1) |
| created_at | timestamp | No | Creación | DEFAULT |

Simétrica a `user_profile`, para cuentas con rol `administrator`.

### 1.6 `recovery_request`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| account_id | integer | No | Cuenta | FK → `account.id` |
| method | varchar(20) | No | `email` \| `telephone` | — |
| token_hash | varchar(255) | No | Hash del token | UNIQUE |
| expires_at | timestamp | No | Expiración | — |
| used_at | timestamp | Sí | Usado el | — |
| status | varchar(20) | No | `pending` \| `verified` \| `used` \| `expired` \| `cancelled` | — |
| attempts | integer | No | Intentos | DEFAULT 0 |
| created_at | timestamp | Sí | Creación | DEFAULT |
| requested_ip | varchar(45) | Sí | IP de origen | — |

### 1.7 `device`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| account_id | integer | No | Cuenta dueña | FK → `account.id` |
| device_uuid | varchar(255) | No | UUID del dispositivo | UNIQUE |
| brand / model | varchar(50)/(100) | Sí | Marca / modelo | — |
| os_name | varchar(30) | No | `Android` | — |
| os_version / app_version | varchar(30) | Sí | Versiones | — |
| gps_status | varchar(20) | No | `active` \| `inactive` \| `unknown` | DEFAULT `unknown` |
| status | varchar(20) | No | `active` \| `blocked` \| `revoked` \| `inactive` | DEFAULT `active` |
| last_access | timestamp | Sí | Último acceso | — |
| created_at | timestamp | No | Creación | DEFAULT |

### 1.8 `device_permission`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| device_id | integer | No | Dispositivo | FK → `device.id` |
| permission_type | varchar(30) | No | `contacts` \| `calls` \| `location` \| `notifications` \| `microphone` \| `camera` | — |
| status | varchar(20) | No | `granted` \| `denied` \| `restricted` \| `not_requested` | — |
| updated_at | timestamp | No | Actualizado | DEFAULT |

UNIQUE compuesto `(device_id, permission_type)` (**motor**).

### 1.9 `alert_activation_setting`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| device_id | integer | No | Dispositivo | FK → `device.id` |
| activation_method | varchar(30) | No | `widget` \| `sudden_movement` \| `physical_button` \| `discreet_confirmation` | — |
| is_enabled | boolean | No | Habilitado | DEFAULT true |
| updated_at | timestamp | No | Actualizado | DEFAULT |

UNIQUE compuesto `(device_id, activation_method)` (**motor**).

### 1.10 `device_session`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| account_id | integer | No | Cuenta | FK → `account.id` |
| device_id | integer | No | Dispositivo | FK → `device.id` |
| refresh_token_hash | varchar(255) | No | Hash del refresh token | UNIQUE |
| status | varchar(20) | No | `active` \| `revoked` \| `expired` | DEFAULT `active` |
| expires_at / last_used_at / revoked_at | timestamp | — | Ciclo de vida | — |
| created_at | timestamp | No | Creación | DEFAULT |

### 1.11 `user_preference`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| user_profile_id | integer | No | Perfil | FK → `user_profile.id`, UNIQUE (1:1) |
| language | varchar(10) | No | Idioma | DEFAULT `es` |
| theme | varchar(20) | No | `light` \| `dark` \| `system` | DEFAULT `light` |
| created_at / updated_at | timestamp | — | Auditoría | — |

---

## 2. Esquema `alert` — alertas, contactos y ubicación

### 2.1 `emergency_contact`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| user_profile_id | integer | No | Usuaria dueña | FK → `user_profile.id` |
| contact_name | varchar(100) | No | Nombre del contacto | — |
| telephone | varchar(20) | No | Teléfono | — |
| email | varchar(150) | Sí | Correo | — |
| relationship | varchar(30) | Sí | Parentesco | — |
| created_at / updated_at | timestamp | — | Auditoría | — |

### 2.2 `alert`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| user_profile_id | integer | No | Usuaria | FK → `user_profile.id` |
| device_id | integer | No | Dispositivo origen | FK → `device.id` |
| alert_type | varchar(20) | No | `main` \| `mini` \| `reminder` | DEFAULT `main` |
| activation_method | varchar(30) | No | `panic_button` \| `widget` \| `sudden_movement` \| `physical_button` \| `discreet_confirmation` \| `automatic` | — |
| status | varchar(20) | No | `active` \| `resolved` \| `cancelled` \| `completed` \| `failed` | DEFAULT `active` |
| message | varchar(500) | Sí | Mensaje asociado | — |
| latitude / longitude | — | — | Ubicación inicial (agregar como columnas reales en el código; ver pendiente) | — |
| started_at / ended_at / cancelled_at / created_at | timestamp | — | Ciclo de vida | — |

Invariantes **solo dominio**: una alerta `active` tiene `started_at` y no `ended_at`; pasar a
`resolved`/`cancelled` exige poblar `ended_at`/`cancelled_at`.

### 2.3 `alert_contact`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| alert_id | integer | No | Alerta | FK → `alert.id` |
| emergency_contact_id | integer | No | Contacto | FK → `emergency_contact.id` |
| channel | varchar(20) | No | `sms` \| `email` \| `call` \| `push` | — |
| destination | varchar(150) | No | Destino de envío | — |
| status | varchar(20) | No | `pending` \| `sent` \| `delivered` \| `failed` \| `retrying` | DEFAULT `pending` |
| sent_at / delivered_at | timestamp | Sí | Tiempos de envío | — |
| attempts | integer | No | Intentos | DEFAULT 0 |
| error_message | varchar(500) | Sí | Error | — |
| created_at | timestamp | No | Creación | DEFAULT |

UNIQUE compuesto `(alert_id, emergency_contact_id, channel)` (**motor**).

### 2.4 `location_log`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| user_profile_id | integer | No | Usuaria | FK → `user_profile.id` |
| alert_id | integer | Sí | Alerta asociada | FK → `alert.id` |
| latitude / longitude | decimal(9,6) | No | Coordenadas (~11 cm) | — |
| accuracy | decimal(6,2) | Sí | Precisión GPS | — |
| recorded_at | timestamp | No | Registro | DEFAULT |

### 2.5 `notification`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| user_profile_id | integer | No | Usuaria | FK → `user_profile.id` |
| alert_id | integer | Sí | Alerta | FK → `alert.id` |
| type | varchar(30) | No | `reminder` \| `warning` \| `update` \| `emergency` | — |
| priority | varchar(20) | No | `normal` \| `high` \| `critical` | DEFAULT `normal` |
| title / message | varchar(100)/(500) | No | Contenido | — |
| is_read | boolean | No | Leída | DEFAULT false |
| is_persistent | boolean | No | Persistente mientras la alerta siga activa | DEFAULT false |
| expires_at / created_at | timestamp | — | Vigencia | — |

### 2.6 `evidence`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| alert_id | integer | No | Alerta | FK → `alert.id` |
| media_type | varchar(20) | No | `photo` \| `video` \| `audio` | — |
| file_url | varchar(255) | No | URL del archivo (el binario **no** se guarda en BD) | — |
| created_at | timestamp | No | Creación | DEFAULT |

### 2.7 `frequent_location`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| user_profile_id | integer | No | Usuaria | FK → `user_profile.id` |
| name | varchar(100) | No | Etiqueta | — |
| address / city | varchar | Sí | Ubicación | — |
| latitude / longitude | decimal(9,6) | No | Coordenadas | — |
| notes | varchar(255) | Sí | Notas | — |
| risk_level | varchar(20) | Sí | Nivel de riesgo declarado | — |
| is_active | boolean | No | Activa | DEFAULT true |
| created_at / updated_at | timestamp | — | Auditoría | — |

### 2.8 `alert_reminder`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| alert_id | integer | No | Alerta | FK → `alert.id` |
| reminder_type | varchar(20) | No | `mini_alert` \| `reminder` | — |
| sequence_number | integer | No | Secuencia | — |
| scheduled_at / sent_at | timestamp | — | Programado / enviado | — |
| status | varchar(20) | No | `scheduled` \| `sent` \| `failed` \| `cancelled` | DEFAULT `scheduled` |
| error_message | varchar(500) | Sí | Error | — |
| created_at | timestamp | No | Creación | DEFAULT |

---

## 3. Esquema `resource` — zonas y recursos de ayuda

### 3.1 `zone`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| created_by_admin_id | integer | Sí | Admin que la creó | FK (**solo dominio** en el código actual) |
| name | varchar(150) | No | Nombre | — |
| zone_type | varchar(20) | No | `safe` \| `risk` | — |
| risk_level | varchar(20) | No | `low` \| `medium` \| `high` \| `critical` | DEFAULT `medium` |
| description / address / city | varchar | — | Detalle | — |
| latitude / longitude | decimal(9,6) | No | Centro | — |
| radius_meters | decimal(8,2) | No | Radio | — |
| is_active | boolean | No | Activa | DEFAULT true |
| created_at / updated_at | timestamp | — | Auditoría | — |

### 3.2 `zone_report`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| zone_id | integer | No | Zona reportada | FK → `zone.id` |
| user_profile_id | integer | No | Usuaria reportante | FK → `user_profile.id` |
| classification | varchar(20) | No | `safe` \| `dangerous` \| `regular` | — |
| comment | varchar(500) | Sí | Comentario | — |
| status | varchar(20) | No | `pending` \| `approved` \| `rejected` | DEFAULT `pending` |
| reviewed_by_admin_id / reviewed_at | — | Sí | Revisión | — |
| created_at | timestamp | No | Creación | DEFAULT |

UNIQUE compuesto `(zone_id, user_profile_id)` — una usuaria reporta una zona una vez (**motor**).

### 3.3 `emergency_resource`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| name | varchar(150) | No | Nombre | — |
| resource_type | varchar(40) | No | `emergency_line` \| `hospital` \| `health_center` \| `police_station` \| `police_post` \| `shelter` \| `support_organization` | — |
| telephone / secondary_telephone / email | varchar | Sí | Contacto | — |
| address / city | varchar | Sí/No | Ubicación | — |
| latitude / longitude | decimal(9,6) | Sí | Coordenadas | — |
| description | varchar(500) | Sí | Descripción | — |
| is_active | boolean | No | Activo | DEFAULT true |
| created_at / updated_at | timestamp | — | Auditoría | — |

### 3.4 `resource_call`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| emergency_resource_id | integer | No | Recurso llamado | FK → `emergency_resource.id` |
| user_profile_id | integer | No | Usuaria | FK → `user_profile.id` |
| alert_id / device_id | integer | Sí | Contexto | FKs |
| telephone_dialed | varchar(30) | No | Número marcado | — |
| status | varchar(20) | No | `started` \| `answered` \| `unanswered` \| `failed` \| `cancelled` | — |
| started_at / ended_at | timestamp | — | Ciclo | — |
| duration_seconds | integer | Sí | Duración | — |
| created_at | timestamp | No | Creación | DEFAULT |

---

## 4. Esquema `admin` — auditoría y moderación

### 4.1 `audit_log`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| account_id | integer | No | Cuenta que actuó | FK → `account.id` |
| action | varchar(100) | No | Acción | — |
| entity_type | varchar(50) | No | Tipo de entidad afectada | — |
| entity_id | integer | Sí | Id afectado | — |
| description / ip_address | varchar | Sí | Detalle | — |
| created_at | timestamp | No | Fecha | DEFAULT |

### 4.2 `user_report`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| reported_user_id / reporter_user_id | integer | — | Usuarios involucrados | FKs → `users.id` |
| reason | varchar(100) | No | Motivo | — |
| description | varchar(1000) | No | Descripción | — |
| status | varchar(20) | No | `pending` \| `under_review` \| `resolved` \| `rejected` \| `closed` | DEFAULT `pending` |
| reviewed_by_admin_id / reviewed_at | — | Sí | Revisión | — |
| resolution_notes | varchar(1000) | Sí | Resolución | — |
| created_at / updated_at | timestamp | — | Auditoría | — |

### 4.3 `moderation_action`

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK |
| user_report_id | integer | No | Reporte | FK → `user_report.id` |
| admin_profile_id | integer | No | Admin actuante | FK → `admin_profile.id` |
| action_type | varchar(30) | No | `review` \| `warn` \| `block` \| `unblock` \| `reject` \| `close` \| `escalate` | — |
| notes | varchar(1000) | Sí | Notas | — |
| created_at | timestamp | No | Fecha | DEFAULT |

### 4.4 `system_configuration`

Fila única de configuración global.

| Campo | Tipo | Nulo | Descripción | Restricciones |
|---|---|---|---|---|
| id | integer | No | Identificador | PK, DEFAULT 1 |
| singleton_key | varchar(10) | No | Clave fija | UNIQUE, DEFAULT `'SYSTEM'` (**motor**) |
| sos_max_duration_minutes | integer | No | Duración máx. SOS | — |
| emergency_phone_number | varchar(20) | No | Número de emergencia | — |
| admin_email | varchar(150) | No | Correo admin | — |
| notifications_enabled | boolean | No | Notificaciones globales | DEFAULT true |
| default_notification_priority | varchar(20) | No | Prioridad por omisión | DEFAULT `normal` |
| default_theme | varchar(20) | No | Tema por omisión | DEFAULT `light` |
| updated_by_admin_profile_id | integer | Sí | Quién actualizó | FK |
| created_at / updated_at | timestamp | — | Auditoría | — |

---

## 5. Diagrama de relaciones

```mermaid
erDiagram
    ROLE ||--o{ USERS : clasifica
    USERS ||--|| ACCOUNT : tiene
    USERS ||--|| USER_PROFILE : tiene
    ACCOUNT ||--o| ADMIN_PROFILE : tiene
    ACCOUNT ||--o{ RECOVERY_REQUEST : solicita
    ACCOUNT ||--o{ DEVICE : registra
    ACCOUNT ||--o{ DEVICE_SESSION : abre
    DEVICE ||--o{ DEVICE_PERMISSION : otorga
    DEVICE ||--o{ ALERT_ACTIVATION_SETTING : configura
    USER_PROFILE ||--o{ EMERGENCY_CONTACT : registra
    USER_PROFILE ||--o{ ALERT : dispara
    USER_PROFILE ||--o{ LOCATION_LOG : genera
    USER_PROFILE ||--o{ FREQUENT_LOCATION : guarda
    USER_PROFILE ||--o{ NOTIFICATION : recibe
    USER_PROFILE ||--o{ ZONE_REPORT : reporta
    USER_PROFILE ||--o{ RESOURCE_CALL : coloca
    USER_PROFILE ||--|| USER_PREFERENCE : configura
    DEVICE ||--o{ ALERT : origina
    ALERT ||--o{ ALERT_CONTACT : notifica
    ALERT ||--o{ ALERT_REMINDER : programa
    ALERT ||--o{ EVIDENCE : respalda
    ALERT ||--o{ LOCATION_LOG : rastrea
    EMERGENCY_CONTACT ||--o{ ALERT_CONTACT : recibe
    ADMIN_PROFILE ||--o{ ZONE : crea
    ZONE ||--o{ ZONE_REPORT : recibe
    EMERGENCY_RESOURCE ||--o{ RESOURCE_CALL : atiende
    USERS ||--o{ USER_REPORT : reporta
    USER_REPORT ||--o{ MODERATION_ACTION : resuelve
    ADMIN_PROFILE ||--o{ MODERATION_ACTION : ejecuta
    ACCOUNT ||--o{ AUDIT_LOG : audita
```

---

## 6. Inventario de tablas

| Esquema | Tablas |
|---|---|
| `identity` | role, users, account, user_profile, admin_profile, recovery_request, device, device_permission, device_session, alert_activation_setting, user_preference |
| `alert` | emergency_contact, alert, alert_contact, location_log, notification, evidence, frequent_location, alert_reminder |
| `resource` | zone, zone_report, emergency_resource, resource_call |
| `admin` | audit_log, user_report, moderation_action, system_configuration |

27 tablas en total, coincidentes con `wal-docs/06-data/models.md`.

## 7. Deuda declarada del modelo

| # | Deuda | Plan |
|---|---|---|
| D1 | Las entidades JPA del backend actual declaran menos reglas que `init.sql` (p. ej. unicidad de `email` en `users` vs validación solo de aplicación) | Migrar reglas expresables al motor o declararlas explícitas |
| D2 | `alert` en código no persiste lat/long inicial | Alinear entidad con los requisitos FR4 |
| D3 | El esquema se crea por `init.sql`; Liquibase solo planificado | Introducir changelog versionado (ver `wal-docs/06-data/migration-strategy.md`) |
| D4 | Servicios `am-alert-service`, `am-resource-service`, `am-admin-service` son esqueleto sin lógica | Ver `wal-docs/09-microservices/service-catalog.md` |

## Correlaciones

- Entidades de dominio que motivaron este esquema → `wal-docs/02-domain/entities-and-rules.md`
- Convenciones técnicas → `wal-docs/05-architecture/decisions/records/ADR-001-language-documentation.md`
- Datos expuestos por la API → [`api-contract.md`](api-contract.md)
