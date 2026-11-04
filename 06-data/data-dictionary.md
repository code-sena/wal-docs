# Data Dictionary — Woman Alert

This document defines the exact meaning of the most important fields in the system, with special attention to fields that may be ambiguous or carry business rules. It is critical for privacy because the system stores location, contacts and evidence of people who may be at risk. Values are taken from the CHECK constraints of the schema generated on 28/10/2026 (Liquibase v1.0.0 to v1.6.0) and from the technical manual.

**See also:**
- Data models with table structures → `06-data/models.md`
- Modeling conventions → `06-data/modeling-conventions.md`
- Security policy for data handling → `00-governance/security-policy.md`
- Non-functional requirements for data → `04-requirements/non-functional.md` (RNF-SEG-006, RNF-PRI-001, RNF-PRI-002)

---

## Core principles

> **Location, contacts and evidence are personal data, and in some cases sensitive data, under Law 1581 of 2012.** Every field that stores personally identifiable information or evidence must state its retention and who can see it. The administration never sees the personal data of trusted contacts, and only sees the route of active or attended alerts.

---

## Identity and Accounts Module

### `account.status`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Auth | identity.account | varchar(20) | Access status of the account. Controls whether the user can log in. Login with a blocked account answers 401 "Account blocked" | `active` — normal use<br>`inactive` — not in use<br>`blocked` — blocked by an administrator (with reason and capture)<br>`pending` — not completed | Internal |

### `users.telephone`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Auth | identity.users | varchar(20), unique, nullable | User's phone number | Regex `^\+?[0-9][0-9 -]{5,18}[0-9]$` | PII — not shown to other users |

### `users.email`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Auth | identity.users | varchar(150), unique, required | Login identifier and address for the recovery code. Registering an existing email answers 409 | Valid email format (CHECK with regex) | PII |

### `users.terms_accepted_at`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Auth | identity.users | timestamp | Date and time the user accepted the terms and conditions and the privacy policy at registration (RF-USU-007) | Timestamp or null for older accounts | Internal |

### `user_profile.id` vs `users.id`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Auth | identity.user_profile | bigint | `users.id` is the value in the JWT (`sub`). Alerts, contacts, locations, routes and trips store `user_profile.id`. Mixing them is the most common source of errors | Positive integer | Internal |

---

## Alert Management Module

### `alert.status`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | alert.alert | varchar(20) | Lifecycle of the alert. Only one active alert per user. Changing to `cancelled` triggers the end-of-alert notice | `active` — in progress<br>`resolved` — attended<br>`cancelled` — cancelled by the user (with PIN if configured)<br>`expired` — closed at the 120-minute limit | Sensitive |

### `alert.alert_type`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | alert.alert | varchar(20), default `main` | Kind of alert. The apps only create `main` | `main`, `medical`, `police`, `fire`, `custom` | Internal |

### `alert.activation_method`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | alert.alert | varchar(30) | How the alert started. It is only a label for the history: it does not change the priority | In use: `panic_button`, `contact`, `suspicious_movement` (shake), `widget`, `signal_lost`, `auto`. Allowed by the CHECK but not used: `gesture`, `voice`, `timer`, `manual`, `power_button` (retired) | Internal |

### `alert_contact.status`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | alert.alert_contact | varchar(20) | Result of each notice sent to a contact through a channel (`email` or `push`), with `attempts` and `error_message`. A pending record is not a confirmation of delivery (RN-05). `seen_at` stores when the contact opened it | `pending`, `sent`, `failed`, `expired` | Sensitive (destination address) |

### `alert_message.sender_type`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | alert.alert_message | varchar | Who wrote the chat message. Contacts have a message limit. The table has no audit trigger because the content is sensitive | `owner`, `contact` | Sensitive |

### `safe_trip.status` / `safe_trip.alert_reason`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | alert.safe_trip | varchar | Trip or automatic protection watch (`mode = auto`). Arrival time 5 to 240 minutes, heartbeat every 2 minutes; the server alerts if the signal is lost for 20 minutes in automatic protection | Status: `active`, `arrived`, `cancelled`, `alerted`, `ended`<br>Reason: `not_confirmed`, `signal_lost`, `no_answer`, `unconfirmed`, `duress` | Sensitive (destination) |

---

## Evidence Module

### `evidence.media_type`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Evidence | alert.evidence | varchar(20) | Kind of file. During an alert the app records video and then audio | `photo`, `video`, `audio`, `document` | Sensitive |

### `evidence.file_url` / `storage_key` / `sha256`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Evidence | alert.evidence | varchar(255) / varchar(100) unique / char(64) | Location of the file in Backblaze B2, its storage key and the SHA-256 hash calculated on upload. Maximum size 50 MB (`413` above that). `review_status`: `pending`, `verified`, `rejected` | Object key in the bucket; hexadecimal hash | Sensitive — files travel over HTTPS but are not encrypted with an own key |

**Retention:** 30 days, with a notice to the user 5 days before deletion, except the evidence of the alerts she decides to keep (up to 5). The app also keeps a private copy outside the gallery.

---

## Location Module

### `location_log.latitude` / `location_log.longitude`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | alert.location_log | numeric(9,6) | Point recorded by the phone. With `alert_id` it belongs to an alert (every 15 s for 5 min, then 30 s, or 60 s in saving mode); without it, it is the daily location of the main phone (every 50 m or 5 min) | Latitude −90 to 90; longitude −180 to 180 | Highly sensitive |

### `location_log.accuracy`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | alert.location_log | numeric(6,2) | Accuracy reported by the device in meters. The target is 10 to 20 m (RNF-REN-003, not measured) | `>= 0` or null | Internal |

**Retention:** daily location 7 days; alert locations 30 days. The last recorded location is always shown with its date and time and is never presented as the current one (RN-13).

---

## Zones Module

### `zone.zone_type`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | resource.zone | varchar(20) | Kind of zone managed by the administration and shown as a circle on the map (`radius_meters > 0`) | `risk`, `safe`, `help`, `policia`, `asistencia` | Public |

### `zone.risk_level`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | resource.zone | varchar(20), default `medium` | Risk classification of the zone | `low`, `medium`, `high` | Public |

### `frequent_location.risk_level`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | alert.frequent_location | varchar | Safety level the user gives to her own place. Frequent places are also the geofences (150 m) of automatic protection | `muy_segura`, `moderada`, `muy_insegura` | Sensitive (home, work) |

### `zone_report.status`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | resource.zone_report | varchar | Report of a user about a zone; only an administrator approves or rejects it | `pending`, `approved`, `rejected` | Internal |

---

## Notifications Module

### `notification.type`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | alert.notification | varchar(30) | Kind of notice shown in the app inbox. Push data uses `ALERTA_SOS` (opens the tracking with the code) and `ALERTA_FINALIZADA` | Risk zone notice, end of a contact's alert, new phone signed in, evidence about to be deleted | Internal |

### `notification.priority`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | alert.notification | varchar | Priority of the notification | `low`, `normal`, `high`, `urgent` | Internal |

### `tracking_link.token_hash`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | notification.tracking_link | char(64) | SHA-256 hash of the 256-bit random code sent by email; the code itself is never stored. The link stops working 2 hours after the alert ends. `tracking_view` records the visits with an anonymous id | Hexadecimal hash | Sensitive |

---

## Device Module

### `device.gps_status`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Auth | identity.device | varchar(20), default `unknown` | Last known state of the location service on the phone | `active`, `inactive`, `unknown`, `denied` | Internal |

### `device.status` / `device.fcm_token`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Auth | identity.device | varchar(20) / varchar(255) | Device status managed by the administration, and the push token linked when the user logs in | `active`, `inactive`, `blocked`, `lost` | Internal — the token is a credential for push |

### `device_session.status`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Auth | identity.device_session | varchar | Session per device; only the hash of the refresh token (15 days) is stored | `active`, `expired`, `revoked` | Internal |

### `protection_device.key_hash`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | alert.protection_device | varchar(64) | Hash of the device key returned once when automatic protection is enabled. The phone sends it in `X-Protection-Key` so protection works even if the session expired | Hash | Credential |

---

## Recovery Module

### `recovery_request` (code and attempts)

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Auth | identity.recovery_request | `token_hash` varchar(255), `attempts`, `expires_at`, `status` | 6-digit code sent by email; only its hash (`token_hash`) is stored, with the requesting IP. Valid 15 minutes and 5 attempts. The API answers the same whether the account exists or not | Hash, expiration, attempt counter, status | Credential |

---

## Ambiguous fields with business rules

### `alert.message`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Alert | alert.alert | varchar(500) | Text generated by the app when creating the alert (for example "SOS alert activated from the app"). It is not the chat: chat messages live in `alert_message` | Free text up to 500 characters | Sensitive |

### `system_configuration` (single row)

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| Auth | admin.system_configuration | single row | Alert duration (30 to 1440 min, default 30), emergency number, administrative email, contact limit and minimum app version. Changes require confirmation, are persisted on the server and are audited (RN-06) | `max_emergency_contacts` 1 to 20 (default 5); `min_app_version` like `1.0.0` | Internal |

---

## Data retention summary

| Data | Retention | Mechanism |
|------|-----------|-----------|
| Evidence | 30 days, notice 5 days before; kept alerts (up to 5) are not deleted | Scheduled task in Evidence |
| Daily location | 7 days | Scheduled task in Alert |
| Alert locations | 30 days | Scheduled task in Alert |
| Tracking link | Until 2 hours after the alert ends | Domain rule in notifications |
| Recovery code | 15 minutes | Domain rule in identity |
| Refresh token | 15 days | JWT expiration and `device_session` |
| Audit log | Not deleted; cannot be modified | Triggers |
| Deleted account | Personal data anonymized after approval of two administrators | Admin module |

Scheduled tasks only run while the service is awake (free Render plan).

---

## Correlations

- Data models → `06-data/models.md`
- Business rules → `02-domain/entities-and-rules.md`
- Security threat model → `05-architecture/security-threat-model.md`
- Security policy → `00-governance/security-policy.md`
