# Entities, Value Objects, and Business Rules

> Catalog of entities, attributes, and business rules for Alerta Mujer, organized by bounded context according to the SRS Functional Requirements (RF1–RF13) and use cases 1–4.

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
| requested_ip | varchar(45) | Origin IP | No | — |

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

### Entity: AlertActivationSetting

**Belongs to:** Identity and Accounts (device configuration)
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| device_id | integer | FK → `device.id` | Yes | — |
| activation_method | varchar(30) | `widget` \| `sudden_movement` \| `physical_button` \| `discreet_confirmation` (RF5.1–RF5.4) | Yes | — |
| is_enabled | boolean | Whether it is active | Yes | Default `false` |
| configuration | varchar(255) | Configuration parameters (e.g., movement sensitivity) | No | — |
| updated_at | timestamp | Last update | Yes | Default `CURRENT_TIMESTAMP` |

**Business rules (invariants):**
- [ ] A device has at most one configuration per activation method (`device_id` + `activation_method` is unique).

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
| telephone | varchar(20) | Phone number | No | — |
| email | varchar(150) | Email | No | — |
| relationship | varchar(30) | Relationship to the user | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |
| updated_at | timestamp | Last update | No | — |

**Business rules (invariants):**
- [ ] A contact belongs to a single `user_profile` (RF2.2).
- [ ] It must have at least one contact method (`telephone` or `email`) — business rule not enforced at the DB level.

---

## Bounded Context: Alert Management (Core Domain)

### Entity: Alert (Aggregate Root)

**Belongs to:** Alert Management
**Identifier:** `id`

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | integer | PK | Yes | — |
| user_profile_id | integer | FK → `user_profile.id` | Yes | — |
| device_id | integer | FK → `device.id` | No | — |
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
| latitude | decimal(9,6) | Latitude | Yes | — |
| longitude | decimal(9,6) | Longitude | Yes | — |
| accuracy | decimal(6,2) | GPS accuracy | No | RNF010 |
| recorded_at | timestamp | Time of recording | Yes | Default `CURRENT_TIMESTAMP` |

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
| name | varchar(100) | Name/label | Yes | RF3.2 |
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
| media_type | varchar(20) | `photo` \| `video` \| `audio` (RF6.1, RF6.2) | Yes | — |
| file_url | varchar(255) | File URL | Yes | — |
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
| alert_id | integer | FK → `alert.id` | No | — |
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
| created_by_account_id | integer | FK → `account.id` | No | — |
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
| reviewed_by_account_id | integer | FK → `account.id` | No | — |
| reviewed_at | timestamp | Review | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |

**Business rules (invariants):**
- [ ] A user can only report/classify the same zone once (`zone_id` + `user_profile_id` is unique).
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
| telephone | varchar(30) | Phone number | No | — |
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
| telephone_dialed | varchar(30) | Number dialed | Yes | — |
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
| account_id | integer | FK → `account.id` | Yes | — |
| action | varchar(100) | Action executed | Yes | RF13.9 — Audit |
| entity_type | varchar(50) | Type of affected entity | Yes | — |
| entity_id | integer | Id of the affected entity | No | — |
| description | varchar(500) | Description | No | — |
| ip_address | varchar(45) | Origin IP | No | — |
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
| reviewed_by_account_id | integer | FK → `account.id` | No | — |
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
| account_id | integer | FK → `account.id` (acting administrator) | Yes | — |
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
| sos_max_duration_minutes | integer | Maximum SOS duration | Yes | — |
| emergency_phone_number | varchar(20) | General emergency number | Yes | — |
| admin_email | varchar(150) | Administrator's email | Yes | — |
| notifications_enabled | boolean | Notifications globally enabled | Yes | Default `true` |
| default_notification_priority | varchar(20) | `normal` \| `high` \| `critical` | Yes | Default `normal` |
| default_theme | varchar(20) | `light` \| `dark` | Yes | Default `light` |
| updated_by_account_id | integer | FK → `account.id` | No | — |
| created_at | timestamp | Creation | Yes | Default `CURRENT_TIMESTAMP` |
| updated_at | timestamp | Last update | No | — |

---

## Summary table of tactical building blocks

| Name | Bounded Context | Aggregate Root? |
|------|----------------|----------------|
| Role, User, Account, UserProfile | Identity and Accounts | User is the root; Account/UserProfile are 1:1 with User |
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

---

## Correlation with code

| Domain artifact | Actual table | Notes |
|----------------|-----------|------|
| Aggregate Root `Alert` | `alert` | See enums `alert_type`, `activation_method`, `status` |
| `AlertContact` | `alert_contact` | One record per channel and contact |
| `EmergencyResource` / `ResourceCall` | `emergency_resource` / `resource_call` | `resource_call` under evaluation for removal |
| `SystemConfiguration` | `system_configuration` | Single row (`id` default 1) |

> Pending decision with the team: whether to remove `zone_report` and/or `resource_call`, and whether a cloud backup field (`backed_up_at`) is missing on `evidence` (RF6.3) and a notification tone field (RF8.3) is missing on `emergency_contact` or `user_preference`.
