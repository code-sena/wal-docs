# Data Dictionary — Woman Alert

This document defines the exact meaning of each important field in the system, with special attention to fields that may be ambiguous or have business rules. Critical for privacy and compliance given the sensitive nature of evidence (audio, video, location data).

**See also:**
- Data models with table structures → `06-data/models.md`
- Modeling conventions → `06-data/modeling-conventions.md`
- Security policy for data handling → `00-governance/security-policy.md`
- Non-functional requirements for data → `04-requirements/non-functional.md` (RNF1, RNF8, RNF16)

---

## Core principles

> **Evidence and location data are protected by privacy regulations.** Every field that stores personally identifiable information (PII) or sensitive evidence must be clearly documented with retention policies and access controls.

---

## Identity and Accounts Module

### `account.status`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| identity-service | account | ENUM | Current access status of the account. Controls whether the user can authenticate and use the system. | `active` — Account is fully functional<br>`blocked` — Account is temporarily blocked due to security concerns (e.g., suspicious activity)<br>`inactive` — Account is deactivated by user or administrator | Medium — Status itself is not PII, but blocking may indicate security incident |

### `user.telephone`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| identity-service | user | VARCHAR(20) | Primary phone number used for SMS notifications and authentication. Must be unique across the system. | E.164 format (e.g., +573001234567) | **High** — Direct contact identifier, protected by encryption at rest |

### `user.email`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| identity-service | user | VARCHAR(150) | Email address used for password recovery and secondary notifications. Must be unique. | Valid email format | **High** — Direct contact identifier, protected by encryption at rest |

### `user_profile.profile_photo_url`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| identity-service | user_profile | VARCHAR(500) | URL pointing to the user's profile photo in cloud storage. Photos are not publicly accessible; access requires authentication. | Cloud storage URL with signed token | **High** — Biometric-adjacent data; access requires user consent |

**Important:** Per RNF1, profile photos are never publicly accessible. The URL includes a time-limited signed token.

---

## Alert Management Module

### `alert.status`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| alert-service | alert | ENUM | Current state of the alert in its lifecycle. | `active` — Alert is currently in progress, location sharing enabled<br>`resolved` — Alert ended normally (user confirmed safety)<br>`cancelled` — Alert was cancelled by user during grace period<br>`completed` — All contacts confirmed receipt, alert lifecycle complete<br>`failed` — Alert failed to deliver to any channel | Medium — Status indicates emergency situation but does not reveal details |

### `alert.alert_type`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| alert-service | alert | ENUM | Type of alert triggered. | `main` — Full emergency alert with all channels and features<br>`mini` — Lightweight alert for non-critical situations<br>`reminder` — Follow-up alert to check on user's status | Low — Categorization only |

### `alert.activation_method`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| alert-service | alert | ENUM | How the alert was triggered. Used for analytics and improving UX. | `panic_button` — Manual tap on main panic button<br>`widget` — Tap on home screen widget<br>`sudden_movement` — Automatic trigger from accelerometer<br>`physical_button` — Long-press on device physical button<br>`discreet_confirmation` — Gesture pattern activation<br>`automatic` — System-initiated (e.g., inactivity timeout) | Low — UX analytics only |

### `alert_contact.status`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| alert-service | alert_contact | ENUM | Delivery status of the notification to a specific contact via a specific channel. | `pending` — Queued for delivery<br>`sent` — Successfully sent to channel provider<br>`delivered` — Confirmed delivered to recipient (push notification only)<br>`failed` — Delivery failed after all retries<br>`retrying` — Retry in progress via alternative channel | Medium — Indicates whether emergency communication succeeded |

---

## Evidence Module

### `evidence.media_type`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| resource-service | evidence | ENUM | Type of media file captured as evidence. | `photo` — Still image<br>`video` — Video recording<br>`audio` — Audio recording | **High** — Evidence type determines retention policy |

### `evidence.file_url`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| resource-service | evidence | VARCHAR(255) | URL pointing to the evidence file in cloud storage. URL includes time-limited signed token for security. | Cloud storage URL with signed token | **Critical** — Contains sensitive evidence of gender-based violence; access requires user consent and role-based authorization |

**Retention Policy:**
- Audio: 90 days, then permanently deleted (unless user opts to extend)
- Photo: 180 days, then permanently deleted
- Video: 365 days, then permanently deleted
- All evidence encrypted at rest (AES-256) and in transit (HTTPS/TLS)

---

## Location Module

### `location_log.latitude` / `location_log.longitude`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| alert-service | location_log | DECIMAL(9,6) | GPS coordinates of the user at the time of recording. Accuracy depends on GPS signal quality (RNF7: 10-20m target). | Decimal degrees (WGS84) | **Critical** — Direct location data; highly sensitive. Access requires user consent and role-based authorization |

**Important:** Location data is stored as raw coordinates. Geocoding (converting to address) is done on-demand for display, not stored, to minimize PII exposure.

### `location_log.accuracy`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| alert-service | location_log | DECIMAL(6,2) | GPS accuracy in meters. Lower values indicate higher precision. | 0.00–999.99 meters | Low — Metadata only |

---

## Zones Module

### `zone.zone_type`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| admin-service | zone | ENUM | Classification of the zone's safety level. | `safe` — Area with lower reported incidents<br>`risk` — Area with higher reported incidents | Low — Community-sourced classification |

### `zone.risk_level`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| admin-service | zone | ENUM | Detailed risk assessment for the zone. | `low` — Minimal risk<br>`medium` — Moderate risk<br>`high` — Elevated risk<br>`critical` — Severe risk | Low — Administrative classification |

### `zone_report.status`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| admin-service | zone_report | ENUM | Moderation status of a user-submitted zone classification. | `pending` — Awaiting admin review<br>`approved` — Classification accepted and published<br>`rejected` — Classification rejected (does not affect zone) | Low — Moderation status only |

---

## Notifications Module

### `notification.type`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| alert-service | notification | ENUM | Category of in-app notification. | `reminder` — Reminder to check in or confirm safety<br>`warning` — Warning about system status or potential issue<br>`update` — Informational update about alert or account<br>`emergency` — Critical emergency notification | Low — Categorization only |

### `notification.priority`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| alert-service | notification | ENUM | Display priority for the notification. | `normal` — Standard display<br>`high` — Prominent display<br>`critical` — Full-screen or persistent display | Low — UX priority only |

---

## Device Module

### `device.gps_status`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| identity-service | device | ENUM | Current status of GPS capability on the device. | `active` — GPS is functional and providing location<br>`inactive` — GPS is disabled or not providing location<br>`unknown` — Status cannot be determined | Low — Technical status only |

### `device_permission.status`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| identity-service | device_permission | ENUM | Permission grant status for a specific capability. | `granted` — User has granted permission<br>`denied` — User has denied permission<br>`restricted` — Permission is partially available (e.g., only when app is foreground)<br>`not_requested` — Permission has not been requested yet | Low — Permission status only |

---

## Recovery Module

### `recovery_request.status`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| identity-service | recovery_request | ENUM | Status of the password or account recovery request. | `pending` — Token sent, awaiting verification<br>`verified` — Token verified, password change allowed<br>`used` — Recovery completed, token consumed<br>`expired` — Token expired without use<br>`cancelled` — Recovery cancelled by user or system | Medium — Indicates account recovery attempt |

---

## Ambiguous fields with business rules

### `alert.message`

| Service | Table | Type | Detailed description | Possible values | Privacy |
|---------|-------|------|---------------------|-----------------|---------|
| alert-service | alert | VARCHAR(500) | Optional free-text message the user can attach to an alert. Used to provide context to contacts. | Free text, max 500 characters | **High** — May contain sensitive information about the emergency situation |

**Business rule:** If the message contains personally identifiable information of third parties, it must be logged separately for GDPR-like compliance requirements.

---

## Data retention summary

| Data type | Retention period | Deletion method | Legal basis |
|-----------|------------------|-----------------|-------------|
| User account (inactive) | 2 years after last activity | Soft delete → hard delete after 2 years | Account management |
| Alert records | 5 years | Hard delete after 5 years | Incident documentation |
| Evidence (audio) | 90 days | Hard delete after 90 days (user can extend) | Evidence preservation |
| Evidence (photo) | 180 days | Hard delete after 180 days (user can extend) | Evidence preservation |
| Evidence (video) | 365 days | Hard delete after 365 days (user can extend) | Evidence preservation |
| Location logs | 1 year | Hard delete after 1 year | Location history |
| Audit logs | 7 years | Hard delete after 7 years | Legal compliance |

---

## Correlations

* Data models → `06-data/models.md`
* Security rules for data handling → `00-governance/security-rules.md`
* Non-functional requirements for data → `04-requirements/non-functional.md` (RNF1, RNF8, RNF16)
