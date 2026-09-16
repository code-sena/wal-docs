# Entities, Value Objects, and Business Rules

> Catalog of entities, attributes, and business rules for Woman Alert, organized by bounded context according to the SRS Functional Requirements (RF1–RF13) and use cases 1–4.
> **Updated to match the final 27-table database schema.**

---

## Bounded Context: Identity and Accounts

### Entity: Role

**Belongs to:** Identity and Accounts
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK, auto-increment | Yes | — |
| name | varchar(30) | `user` \| `administrator` | Yes | Unique |
| description | varchar(255) | Role description | No | — |

---

### Entity: User (Aggregate Root)

**Belongs to:** Identity and Accounts
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| role_id | integer | FK → `role.id` | Yes | — |
| first_name | varchar(100) | First name | Yes | — |
| last_name | varchar(100) | Last name | Yes | — |
| telephone | varchar(20) | Phone number | No | Unique |
| email | varchar(150) | Email | Yes | Unique |
| document_number | varchar(20) | ID document number | No | Unique |
| document_type | varchar(20) | ID document type | No | — |
| birthdate | date | Date of birth | No | — |
| created_at | timestamp | Creation date | Yes | Default `CURRENT_TIMESTAMP` |

**Business rules (invariants):**
- [ ] `email` is unique across the entire system.
- [ ] `telephone` and `document_number`, if provided, are unique.
- [ ] `role_id` determines whether the user is a `user` (regular user) or `administrator`.

---

### Entity: Account

**Belongs to:** Identity and Accounts
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| user_id | integer | FK → `user.id` | Yes | Unique (1:1 with `user`) |
| password_hash | varchar(255) | Password hash | Yes | Never plain text |
| status | varchar(20) | `active` \| `blocked` \| `inactive` | Yes | — |
| last_access | timestamp | Last access | No | — |

**Business rules (invariants):**
- [ ] Each `user` has one and only one `account` (1:1 relationship).
- [ ] An account with `status = blocked` should not be able to authenticate (business rule not enforced at the DB level, to be implemented in the backend).

---

### Entity: UserProfile

**Belongs to:** Identity and Accounts (this is the "business actor" referenced by almost all other contexts)
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| user_id | integer | FK → `user.id` | Yes | Unique (1:1 with `user`) |
| profile_photo_url | varchar(500) | Profile photo | No | — |
| tutorial_completed | boolean | Whether the tutorial was viewed (RF3.1) | Yes | Default `false` |
| tutorial_seen_at | timestamp | When it was viewed | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |
| updated_at | timestamp | Last update | No | — |

**Business rules (invariants):**
- [ ] A `user_profile` row must only ever be created for a `user` whose `role_id` corresponds to `user` (regular user), never for `administrator` — enforced in the Service layer, since a plain FK cannot filter by the related row's role.

---

### Entity: AdminProfile

**Belongs to:** Identity and Accounts (structural counterpart of `UserProfile`, exclusive to administrator accounts)
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| account_id | integer | FK → `account.id` | Yes | Unique (1:1 with `account`) |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |

**Business rules (invariants):**
- [ ] An `admin_profile` row must only ever be created for an `account` whose `user.role_id` corresponds to `administrator`, never for `user` — enforced in the Service layer, for the same reason as `UserProfile`.
- [ ] Every FK across the system that represents "the administrator who performed this action" points to `admin_profile.id`, never directly to `account.id`: `zone.created_by_admin_profile_id`, `zone_report.reviewed_by_admin_profile_id`, `user_report.reviewed_by_admin_profile_id`, `moderation_action.admin_profile_id`, `system_configuration.updated_by_admin_profile_id`.

---

### Entity: RecoveryRequest

**Belongs to:** Identity and Accounts
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| account_id | integer | FK → `account.id` | Yes | — |
| method | varchar(20) | `email` \| `telephone` | Yes | — |
| token_hash | varchar(255) | Recovery token hash | Yes | Unique |
| expires_at | timestamp | Token expiration | Yes | — |
| used_at | timestamp | When it was used | No | — |
| status | varchar(20) | `pending` \| `verified` \| `used` \| `expired` \| `cancelled` | Yes | — |
| attempts | integer | Attempts made | Yes | Default 0 |
| created_at | timestamp | Creation | No | Default `CURRENT_TIMESTAMP` |
| requested_ip | varchar(45) | Origin IP (IPv4/IPv6) | No | — |

**Lifecycle / States:**

```
PENDING ──(token verified)──▶ VERIFIED ──(password changed)──▶ USED
   │
   ├──(expires_at reached)──▶ EXPIRED
   └──(cancelled)──▶ CANCELLED
```

**Business rules (invariants):**
- [ ] A token can only be used once (`used_at` is set when used).
- [ ] After `expires_at`, the status must move to `expired`.

---

### Entity: Device

**Belongs to:** Identity and Accounts
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| account_id | integer | FK → `account.id` | Yes | — |
| device_uuid | varchar(255) | Unique device identifier | Yes | Unique |
| brand | varchar(50) | Brand | No | RF13.6 |
| model | varchar(100) | Model | No | RF13.6 |
| os_name | varchar(30) | `Android` | Yes | — |
| os_version | varchar(30) | OS version | No | — |
| app_version | varchar(30) | App version | No | — |
| gps_status | varchar(20) | `active` \| `inactive` \| `unknown` | Yes | Default `unknown` — RF13.6 |
| status | varchar(20) | `active` \| `blocked` \| `revoked` \| `inactive` | Yes | Default `active` |
| last_access | timestamp | Last access | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |

---

### Entity: DevicePermission

**Belongs to:** Identity and Accounts
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| device_id | integer | FK → `device.id` | Yes | — |
| permission_type | varchar(30) | `contacts` \| `calls` \| `location` \| `notifications` \| `microphone` \| `camera` (RF1.1–RF1.6) | Yes | — |
| status | varchar(20) | `granted` \| `denied` \| `restricted` \| `not_requested` | Yes | — |
| updated_at | timestamp | Last update | Yes | Default `CURRENT_TIMESTAMP` |

**Business rules (invariants):**
- [ ] A device has at most one record per permission type (`device_id` + `permission_type` is unique).

---

### Entity: DeviceSession

**Belongs to:** Identity and Accounts
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| account_id | integer | FK → `account.id` | Yes | — |
| device_id | integer | FK → `device.id` | Yes | — |
| refresh_token_hash | varchar(255) | JWT refresh token hash | Yes | Unique, never plain text |
| status | varchar(20) | `active` \| `revoked` \| `expired` | Yes | Default `active` |
| expires_at | timestamp | Session expiration | Yes | — |
| last_used_at | timestamp | Last use | No | — |
| revoked_at | timestamp | Revocation | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |

---

### Entity: AlertActivationSetting

**Belongs to:** Identity and Accounts (device configuration)
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| device_id | integer | FK → `device.id` | Yes | — |
| activation_method | varchar(30) | `widget` \| `sudden_movement` \| `physical_button` \| `discreet_confirmation` (RF5.1–RF5.4) | Yes | — |
| is_enabled | boolean | Whether the user has this method turned on/off | Yes | Default `true` — all methods start enabled |
| updated_at | timestamp | Last update | Yes | Default `CURRENT_TIMESTAMP` |

**Business rules (invariants):**
- [ ] A device has at most one configuration per activation method (`device_id` + `activation_method` is unique).
- [ ] The user can only enable/disable a method, not configure its sensitivity or parameters (scope decision — the `configuration` column originally considered was dropped, since it would never hold data under this scope).
- [ ] At least one activation method must remain enabled per device at all times — this cannot be expressed as a single-row `CHECK` constraint (it requires counting sibling rows for the same `device_id`), so it is enforced in the Service layer before any `UPDATE` that would disable the last remaining method.

---

### Entity: UserPreference

**Belongs to:** Identity and Accounts
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| user_profile_id | integer | FK → `user_profile.id` | Yes | Unique (1:1) |
| language | varchar(10) | Language (RF10.1) | Yes | Default `es` |
| theme | varchar(20) | `light` \| `dark` \| `system` (RF11.1) | Yes | Default `light` |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |
| updated_at | timestamp | Last update | No | — |

---

## Bounded Context: Emergency Contacts

### Entity: EmergencyContact

**Belongs to:** Emergency Contacts
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| user_profile_id | integer | FK → `user_profile.id` | Yes | — |
| contact_name | varchar(100) | Contact's name | Yes | — |
| telephone | varchar(20) | Phone number | **Yes** | RF2.2 explicitly requires a phone number for every contact |
| email | varchar(150) | Email | No | — |
| relationship | varchar(30) | Relationship to the user | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |
| updated_at | timestamp | Last update | No | — |

**Business rules (invariants):**
- [ ] A contact belongs to a single `user_profile` (RF2.2).
- [ ] `telephone` is mandatory at the database level (`NOT NULL`); `email` remains optional, since RF2.2 only requires a phone number.

---

## Bounded Context: Alert Management (Core Domain)

### Entity: Alert (Aggregate Root)

**Belongs to:** Alert Management
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| user_profile_id | integer | FK → `user_profile.id` | Yes | — |
| device_id | integer | FK → `device.id` | **Yes** | Every alert must originate from a registered device, so RF13.6 (device oversight) can always trace an alert back to its device |
| alert_type | varchar(20) | `main` \| `mini` \| `reminder` | Yes | Default `main` |
| activation_method | varchar(30) | `panic_button` \| `widget` \| `sudden_movement` \| `physical_button` \| `discreet_confirmation` \| `automatic` (RF5) | Yes | — |
| status | varchar(20) | `active` \| `resolved` \| `cancelled` \| `completed` \| `failed` | Yes | Default `active` |
| message | varchar(500) | Associated message | No | — |
| started_at | timestamp | Start | Yes | Default `CURRENT_TIMESTAMP` |
| ended_at | timestamp | End | No | — |
| cancelled_at | timestamp | Cancellation | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |

**Lifecycle / States:**

```
ACTIVE ──(the user confirms it was a misunderstanding, UC3/UC4)──▶ CANCELLED
ACTIVE ──(handled and closed successfully)──▶ RESOLVED / COMPLETED
ACTIVE ──(could not be notified/handled)──▶ FAILED
```

**Business rules (invariants):**
```
INV-001: When moving to the ACTIVE state, a notification attempt must be generated to each
  emergency_contact of the user via alert_contact (RF4.2).
INV-002: While alert.status = 'active', records must keep being generated in
  location_log associated with the alert (RF4.3).
INV-003: Only the user herself can move the alert to 'cancelled' (UC3/UC4:
  "the user manually turns off the alarm" to shut it off).
```

**Behaviors (domain methods):**
- `activate(activation_method)`: creates the alert in the `active` state.
- `resolve() / complete() / cancel() / markFailed()`: state transition.

---

### Entity: AlertContact

**Belongs to:** Alert Management
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| alert_id | integer | FK → `alert.id` | Yes | — |
| emergency_contact_id | integer | FK → `emergency_contact.id` | Yes | — |
| channel | varchar(20) | `sms` \| `email` \| `call` \| `push` | Yes | — |
| destination | varchar(150) | Send destination | Yes | — |
| status | varchar(20) | `pending` \| `sent` \| `delivered` \| `failed` \| `retrying` | Yes | Default `pending` |
| sent_at | timestamp | Sent | No | — |
| delivered_at | timestamp | Delivered | No | — |
| attempts | integer | Attempts | Yes | Default 0 |
| error_message | varchar(500) | Error, if any | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |

**Business rules (invariants):**
- [ ] The same contact cannot have two records with the same channel for the same alert (`alert_id` + `emergency_contact_id` + `channel` is unique).

---

### Entity: LocationLog

**Belongs to:** Alert Management
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| user_profile_id | integer | FK → `user_profile.id` | Yes | — |
| alert_id | integer | FK → `alert.id` | No | A location can be logged without an active alert (e.g., RF3.2) |
| latitude | decimal(9,6) | Latitude | Yes | Precision ≈ 11 cm |
| longitude | decimal(9,6) | Longitude | Yes | Precision ≈ 11 cm |
| accuracy | decimal(6,2) | GPS accuracy | No | RNF010 |
| recorded_at | timestamp | Time of recording | Yes | Default `CURRENT_TIMESTAMP` |

**Business rules (invariants):**
- [ ] `latitude`/`longitude` use `DECIMAL(9,6)` instead of `FLOAT` to avoid the binary rounding error that floating-point types introduce for GPS coordinates.

---

### Entity: AlertReminder

**Belongs to:** Alert Management
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| alert_id | integer | FK → `alert.id` | Yes | — |
| reminder_type | varchar(20) | `mini_alert` \| `reminder` (RF4.5) | Yes | — |
| sequence_number | integer | Reminder order | Yes | — |
| scheduled_at | timestamp | Scheduled for | Yes | — |
| sent_at | timestamp | Sent at | No | — |
| status | varchar(20) | `scheduled` \| `sent` \| `failed` \| `cancelled` | Yes | Default `scheduled` |
| error_message | varchar(500) | Error, if any | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |

---

### Entity: FrequentLocation

**Belongs to:** Alert Management
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| user_profile_id | integer | FK → `user_profile.id` | Yes | — |
| name | varchar(100) | Name/label (e.g., "Home", "Work") | Yes | RF3.2 |
| address | varchar(255) | Address | No | — |
| city | varchar(100) | City | No | — |
| latitude | decimal(9,6) | Latitude | Yes | — |
| longitude | decimal(9,6) | Longitude | Yes | — |
| notes | varchar(255) | Notes | No | — |
| is_active | boolean | Active/logically deleted | Yes | Default `true` |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |
| updated_at | timestamp | Last update | No | — |

---

## Bounded Context: Evidence

### Entity: Evidence

**Belongs to:** Evidence
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| alert_id | integer | FK → `alert.id` | Yes | — |
| media_type | varchar(20) | `photo` \| `video` \| `audio` (RF6.1, RF6.2) | Yes | `photo` under evaluation — not explicitly described in RF6, kept as a scope extension |
| file_url | varchar(255) | File URL (external storage) | Yes | The file itself is never stored in the relational database |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |

**Business rules (invariants):**
- [ ] A piece of evidence always belongs to an alert.
- [ ] The table does not indicate whether `file_url` has already been backed up to the cloud (RF6.3) — to confirm with the team whether that field is missing.

---

## Bounded Context: Notifications

### Entity: Notification

**Belongs to:** Notifications
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| user_profile_id | integer | FK → `user_profile.id` | Yes | — |
| alert_id | integer | FK → `alert.id` | No | Not every notification originates from an alert (e.g., a permission reminder) |
| type | varchar(30) | `reminder` \| `warning` \| `update` \| `emergency` | Yes | — |
| priority | varchar(20) | `normal` \| `high` \| `critical` (RF8.1) | Yes | Default `normal` |
| title | varchar(100) | Title | Yes | — |
| message | varchar(500) | Message | Yes | — |
| is_read | boolean | Read | Yes | Default `false` |
| is_persistent | boolean | Persistent while the alert is active (RF8.2) | Yes | Default `false` |
| expires_at | timestamp | Expiration | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |

---

## Bounded Context: Zones and Help Resources

### Entity: Zone

**Belongs to:** Zones and Help Resources
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| created_by_admin_profile_id | integer | FK → `admin_profile.id` | No | Renamed from `created_by_account_id`: only an administrator's profile can appear here, structurally |
| name | varchar(150) | Name (RF9.2) | Yes | — |
| zone_type | varchar(20) | `safe` \| `risk` | Yes | — |
| risk_level | varchar(20) | `low` \| `medium` \| `high` \| `critical` | Yes | Default `medium` |
| description | varchar(500) | Description | No | — |
| address | varchar(255) | Address | No | — |
| city | varchar(100) | City | Yes | — |
| latitude | decimal(9,6) | Latitude | Yes | — |
| longitude | decimal(9,6) | Longitude | Yes | — |
| radius_meters | decimal(8,2) | Zone radius | Yes | — |
| is_active | boolean | Active | Yes | Default `true` |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |
| updated_at | timestamp | Last update | No | — |

---

### Entity: ZoneReport

**Belongs to:** Zones and Help Resources
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| zone_id | integer | FK → `zone.id` | Yes | — |
| user_profile_id | integer | FK → `user_profile.id` | Yes | — |
| classification | varchar(20) | `safe` \| `dangerous` \| `regular` | Yes | — |
| comment | varchar(500) | Comment | No | — |
| status | varchar(20) | `pending` \| `approved` \| `rejected` | Yes | Default `pending` |
| reviewed_by_admin_profile_id | integer | FK → `admin_profile.id` | No | Renamed from `reviewed_by_account_id` |
| reviewed_at | timestamp | Review | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |

**Business rules (invariants):**
- [ ] A user can report/classify the same zone more than once over time — the `zone_id` + `user_profile_id` unique index protects against a duplicate row, so the application performs an `UPDATE` on the existing report instead of an `INSERT` when one already exists for that pair.
- ⚠️ RF9.2 describes registering a zone by whoever creates it, with its name, risk level, and location — not a later reclassification by other users, which is what this table covers. It is under evaluation by the team.

---

### Entity: EmergencyResource

**Belongs to:** Zones and Help Resources
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| name | varchar(150) | Name (RF7.1) | Yes | — |
| resource_type | varchar(40) | `emergency_line` \| `hospital` \| `health_center` \| `police_station` \| `police_post` \| `shelter` \| `support_organization` | Yes | Matches SRS section 2.4 |
| telephone | varchar(30) | Primary phone number | No | — |
| secondary_telephone | varchar(30) | Secondary phone number | No | — |
| email | varchar(150) | Email | No | — |
| address | varchar(255) | Address | No | — |
| city | varchar(100) | City | Yes | — |
| latitude | decimal(9,6) | Latitude (RF9.3) | No | — |
| longitude | decimal(9,6) | Longitude (RF9.3) | No | — |
| description | varchar(500) | Description | No | — |
| is_active | boolean | Active | Yes | Default `true` |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |
| updated_at | timestamp | Last update | No | — |

---

### Entity: ResourceCall

**Belongs to:** Zones and Help Resources
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| emergency_resource_id | integer | FK → `emergency_resource.id` | Yes | — |
| user_profile_id | integer | FK → `user_profile.id` | Yes | — |
| alert_id | integer | FK → `alert.id` | No | The call may not come from an active alert |
| device_id | integer | FK → `device.id` | No | — |
| telephone_dialed | varchar(30) | Number actually dialed | Yes | Kept separately from `emergency_resource.telephone`/`secondary_telephone`, since the resource may have two possible numbers and this field records which one was used |
| status | varchar(20) | `started` \| `answered` \| `unanswered` \| `failed` \| `cancelled` | Yes | — |
| started_at | timestamp | Start | Yes | Default `CURRENT_TIMESTAMP` |
| ended_at | timestamp | End | No | — |
| duration_seconds | integer | Duration | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |

⚠️ RF1.2 covers the permission to make emergency calls and RF7.1 covers the directory of help centers, but neither requires storing a history of calls made — that is what this table adds. It is under evaluation by the team.

---

## Bounded Context: Administration and Moderation

### Entity: AuditLog

**Belongs to:** Administration and Moderation
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| account_id | integer | FK → `account.id` | Yes | RF13.9 — Audit |
| action | varchar(100) | Action executed | Yes | — |
| entity_type | varchar(50) | Type of affected entity | Yes | — |
| entity_id | integer | Id of the affected entity | No | — |
| description | varchar(500) | Description | No | — |
| ip_address | varchar(45) | Origin IP (IPv4/IPv6) | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |

---

### Entity: UserReport

**Belongs to:** Administration and Moderation
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| reported_user_id | integer | FK → `user.id` (reported user) | Yes | — |
| reporter_user_id | integer | FK → `user.id` (who is reporting) | No | — |
| reason | varchar(100) | Reason | Yes | RF13.10 — Moderation |
| description | varchar(1000) | Description | Yes | — |
| status | varchar(20) | `pending` \| `under_review` \| `resolved` \| `rejected` \| `closed` | Yes | Default `pending` |
| reviewed_by_admin_profile_id | integer | FK → `admin_profile.id` | No | Renamed from `reviewed_by_account_id` |
| reviewed_at | timestamp | Review | No | — |
| resolution_notes | varchar(1000) | Resolution notes | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |
| updated_at | timestamp | Last update | No | — |

---

### Entity: ModerationAction

**Belongs to:** Administration and Moderation
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| user_report_id | integer | FK → `user_report.id` | Yes | — |
| admin_profile_id | integer | FK → `admin_profile.id` (acting administrator) | Yes | Renamed from `account_id`, structurally guaranteed to be an administrator |
| action_type | varchar(30) | `review` \| `warn` \| `block` \| `unblock` \| `reject` \| `close` \| `escalate` | Yes | RF13.10 |
| notes | varchar(1000) | Notes | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |

---

### Entity: SystemConfiguration

**Belongs to:** Administration and Moderation
**Identifier:** `id` (single row)

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK, single system row | Yes | Default 1 |
| singleton_key | varchar(10) | Fixed key used to guarantee a single row exists | Yes | Unique, default `'SYSTEM'` — this is what actually enforces "only one row" at the database level, since a plain `DEFAULT 1` on `id` does not stop a second row with a different `id` from being inserted |
| sos_max_duration_minutes | integer | Maximum SOS duration | Yes | — |
| emergency_phone_number | varchar(20) | General emergency number | Yes | — |
| admin_email | varchar(150) | Administrator's email | Yes | — |
| notifications_enabled | boolean | Notifications globally enabled | Yes | Default `true` |
| default_notification_priority | varchar(20) | `normal` \| `high` \| `critical` | Yes | Default `normal` |
| default_theme | varchar(20) | `light` \| `dark` | Yes | Default `light` |
| updated_by_admin_profile_id | integer | FK → `admin_profile.id` | No | Renamed from `updated_by_account_id` |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |
| updated_at | timestamp | Last update | No | — |

---

## Summary table of tactical building blocks

| Name | Bounded Context | Aggregate Root? |
|------|----------------|----------------|
| Role, User, Account, UserProfile, AdminProfile | Identity and Accounts | User is the root; Account/UserProfile/AdminProfile are 1:1 with User/Account |
| RecoveryRequest | Identity and Accounts | No (depends on Account) |
| Device, DevicePermission, DeviceSession, AlertActivationSetting | Identity and Accounts | Device is the root |
| UserPreference | Identity and Accounts | No (1:1 with UserProfile) |
| EmergencyContact | Emergency Contacts | Yes |
| Alert | Alert Management | Yes |
| AlertContact, LocationLog, AlertReminder | Alert Management | No (depend on Alert) |
| FrequentLocation | Alert Management | Yes |
| Evidence | Evidence | Yes (but always tied to an Alert) |
| Notification | Notifications | Yes |
| Zone | Zones and Help Resources | Yes |
| ZoneReport ⚠️ | Zones and Help Resources | No (candidate for removal) |
| EmergencyResource | Zones and Help Resources | Yes |
| ResourceCall ⚠️ | Zones and Help Resources | No (candidate for removal) |
| AuditLog, UserReport, ModerationAction, SystemConfiguration | Administration and Moderation | UserReport is the root of its flow; the others are independent |

**Total: 27 tables / entities.**

---

## Correlation with code

| Domain artifact | Actual table | Notes |
|----------------|-----------|------|
| Aggregate Root `Alert` | `alert` | See enums `alert_type`, `activation_method`, `status`; `device_id` is `NOT NULL` |
| `AlertContact` | `alert_contact` | One record per channel and contact |
| `AdminProfile` | `admin_profile` | 1:1 with `account`; only for `role = administrator` |
| `EmergencyResource` / `ResourceCall` | `emergency_resource` / `resource_call` | `resource_call` under evaluation for removal |
| `SystemConfiguration` | `system_configuration` | Single row, enforced via `singleton_key UNIQUE` |

> Pending decision with the team: whether to remove `zone_report` and/or `resource_call`, and whether a cloud backup field (`backed_up_at`) is missing on `evidence` (RF6.3) and a notification tone field (RF8.3) is missing on `emergency_contact` or `user_preference`.