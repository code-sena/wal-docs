# Domain Events

Catalog of Alerta Mujer's domain events, built from each table's state changes (status, note: with the allowed values) and from the SRS Functional Requirements that give rise to them.

Updated to match the final 27-table database schema (adds `AdminProfileRegistered`, uses `admin_profile_id` for administrative actions, and confirms full domain integration for `zone_report` and `resource_call`).

## Event Catalog

### Event: UserRegistered

| Field | Value |
|---|---|
| Name | `UserRegistered` |
| Bounded Context | Identity and Accounts |
| Aggregate | User |
| Trigger | A record is created in `user` + `account` + `user_profile` (RF2.1) |
| Consumers | Administration and Moderation (the user becomes available for lookup and management, RF13.1) |

**Payload:**

```json
{
  "eventType": "UserRegistered",
  "aggregateId": "user.id",
  "payload": {
    "user_profile_id": "int",
    "account_id": "int",
    "role_id": "int",
    "first_name": "string",
    "last_name": "string",
    "email": "string"
  }
}
```

### Event: AdminProfileRegistered

| Field | Value |
|---|---|
| Name | `AdminProfileRegistered` |
| Bounded Context | Identity and Accounts |
| Aggregate | AdminProfile |
| Trigger | A record is created in `admin_profile`, restricted to an account whose `user.role_id` is administrator |
| Consumers | Administration and Moderation (this administrator becomes a valid actor for `zone.created_by_admin_profile_id`, `zone_report.reviewed_by_admin_profile_id`, `user_report.reviewed_by_admin_profile_id`, `moderation_action.admin_profile_id`, `system_configuration.updated_by_admin_profile_id`) |

**Payload:**

```json
{
  "eventType": "AdminProfileRegistered",
  "aggregateId": "admin_profile.id",
  "payload": {
    "admin_profile_id": "int",
    "account_id": "int"
  }
}
```

### Event: RecoveryRequested / RecoveryCompleted

| Field | Value |
|---|---|
| Name | `RecoveryRequested`, `RecoveryCompleted` |
| Bounded Context | Identity and Accounts |
| Aggregate | RecoveryRequest |
| Trigger | A `recovery_request` is created (`status = pending`) / the token moves to used (RF2.3) |
| Consumers | Notifications (sending the token via method: email \| telephone) |

### Event: EmergencyContactAdded

| Field | Value |
|---|---|
| Name | `EmergencyContactAdded` |
| Bounded Context | Emergency Contacts |
| Aggregate | EmergencyContact |
| Trigger | A record is created in `emergency_contact` (RF2.2) — requires at least `contact_name` and `telephone` (`telephone` is NOT NULL) |
| Consumers | Alert Management (resolves recipients during emergency activation) |

### Event: AlertActivated

| Field | Value |
|---|---|
| Name | `AlertActivated` |
| Bounded Context | Alert Management |
| Aggregate | Alert |
| Trigger | An alert is created with `status = 'active'`, a `device_id` (NOT NULL), and an `activation_method` of `panic_button` \| `widget` \| `sudden_movement` \| `physical_button` \| `discreet_confirmation` \| `automatic` (RF5) |
| Consumers | Emergency Contacts & Notifications (creates `alert_contact` dispatch queue), Evidence, Administration and Moderation |

**Payload:**

```json
{
  "eventType": "AlertActivated",
  "aggregateId": "alert.id",
  "payload": {
    "alert_id": "int",
    "user_profile_id": "int",
    "device_id": "int",
    "alert_type": "main | mini | reminder",
    "activation_method": "panic_button | widget | sudden_movement | physical_button | discreet_confirmation | automatic",
    "started_at": "timestamp"
  }
}
```

**What do consumers do with this event?**

| Consuming Context | Action |
|---|---|
| Emergency Contacts & Notifications | Resolves user's contacts and populates `alert_contact` records for available channels (`sms` \| `email` \| `call` \| `push`). Executed within `alert-service`. |
| Evidence | Prepares system to link incoming evidence records (photos, videos, audio) with the active `alert_id`. |
| Alert Management | Begins storing streaming GPS updates into `location_log` with `alert_id`. |

### Event: LocationRecorded

| Field | Value |
|---|---|
| Name | `LocationRecorded` |
| Bounded Context | Alert Management |
| Aggregate | LocationLog |
| Trigger | A record is inserted in `location_log` (with or without `alert_id`) while tracking is active (RF4.3) |
| Consumers | Emergency contacts (real-time tracking feed) |

### Event: AlertContactNotified

| Field | Value |
|---|---|
| Name | `AlertContactNotified` |
| Bounded Context | Alert Management |
| Aggregate | AlertContact |
| Trigger | An `alert_contact` record changes from `status = pending` to `sent` / `delivered` / `failed` / `retrying` |
| Consumers | Administration and Moderation (auditing dispatch status) |

### Event: AlertResolved / AlertCancelled / AlertCompleted / AlertFailed

| Field | Value |
|---|---|
| Name | `AlertResolved`, `AlertCancelled`, `AlertCompleted`, `AlertFailed` |
| Bounded Context | Alert Management |
| Aggregate | Alert |
| Trigger | `alert.status` changes from `active` to `resolved` \| `cancelled` \| `completed` \| `failed` (UC3/UC4: user manual deactivation → `cancelled`) |
| Consumers | Notifications, Administration and Moderation |

**Payload:**

```json
{
  "eventType": "AlertCancelled",
  "aggregateId": "alert.id",
  "payload": {
    "alert_id": "int",
    "cancelled_at": "timestamp"
  }
}
```

### Event: MiniAlertScheduled / MiniAlertSent

| Field | Value |
|---|---|
| Name | `MiniAlertScheduled`, `MiniAlertSent` |
| Bounded Context | Alert Management |
| Aggregate | AlertReminder |
| Trigger | An `alert_reminder` is created (`status = scheduled`) / moves to `sent` when there is no response from the user (RF4.5) |
| Consumers | Notifications |

### Event: AlertActivationMethodToggled

| Field | Value |
|---|---|
| Name | `AlertActivationMethodToggled` |
| Bounded Context | Identity and Accounts |
| Aggregate | AlertActivationSetting |
| Trigger | `alert_activation_setting.is_enabled` changes value for a `device_id` + `activation_method` (RF5) |
| Consumers | Alert Management (validates available activation methods) |

**Business Rule**

Before applying `is_enabled = false`, the service layer checks that at least one other method for the same `device_id` remains true.

### Event: EvidenceRegistered

| Field | Value |
|---|---|
| Name | `EvidenceRegistered` |
| Bounded Context | Evidence |
| Aggregate | Evidence |
| Trigger | A record is inserted in `evidence` with `media_type = photo` \| `video` \| `audio` and a valid `file_url` referencing Cloud Storage (RF6.1, RF6.2) |
| Consumers | Administration and Moderation (lookup via RF13.7) |

### Event: NotificationCreated

| Field | Value |
|---|---|
| Name | `NotificationCreated` |
| Bounded Context | Notifications |
| Aggregate | Notification |
| Trigger | A record is inserted in `notification` with `priority = normal` \| `high` \| `critical` (RF8.1) |
| Consumers | Mobile/Web User interface |

Notification creation and emergency dispatch are executed internally within the `alert-service` process as part of the emergency propagation workflow.

### Event: ZoneRegistered

| Field | Value |
|---|---|
| Name | `ZoneRegistered` |
| Bounded Context | Help Areas and Resources |
| Aggregate | Zone |
| Trigger | An official public zone is created with `zone_type = safe` \| `risk`, authored by `created_by_admin_profile_id` (RF9.2) |
| Consumers | Administration and Moderation |

### Event: ZoneReported

| Field | Value |
|---|---|
| Name | `ZoneReported` |
| Bounded Context | Help Areas and Resources |
| Aggregate | ZoneReport |
| Trigger | A user submits a risk classification from the mobile app, creating a `zone_report` (`status = pending`) |
| Consumers | Administration and Moderation (requires administrator approval via `reviewed_by_admin_profile_id` to publish as an official zone) |

### Event: ResourceCallStarted / ResourceCallEnded

| Field | Value |
|---|---|
| Name | `ResourceCallStarted`, `ResourceCallEnded` |
| Bounded Context | Help Areas and Resources |
| Aggregate | ResourceCall |
| Trigger | A `resource_call` is initiated (`status = started`, `alert_id` optional) / completed (`answered` \| `unanswered` \| `failed` \| `cancelled`) |
| Consumers | Alert Management (associates call history with Alert History when `alert_id` is present), Administration and Moderation |

### Event: UserReported

| Field | Value |
|---|---|
| Name | `UserReported` |
| Bounded Context | Administration and Moderation |
| Aggregate | UserReport |
| Trigger | A `user_report` is created (`status = pending`) (RF13.10) |
| Consumers | Moderation |

### Event: ModerationActionRegistered

| Field | Value |
|---|---|
| Name | `ModerationActionRegistered` |
| Bounded Context | Administration and Moderation |
| Aggregate | ModerationAction |
| Trigger | A `moderation_action` is created (`action_type = review` \| `warn` \| `block` \| `unblock` \| `reject` \| `close` \| `escalate`), executed by `admin_profile_id` (RF13.10) |
| Consumers | Identity and Accounts (updates `account.status` when `action_type = block/unblock`) |

### Event: SystemConfigurationUpdated

| Field | Value |
|---|---|
| Name | `SystemConfigurationUpdated` |
| Bounded Context | Administration and Moderation |
| Aggregate | SystemConfiguration |
| Trigger | The single `system_configuration` row (`singleton_key = 'SYSTEM'`) is updated by `updated_by_admin_profile_id` |
| Consumers | All services reading global configuration (`sos_max_duration_minutes`, `emergency_phone_number`, etc.) |

### Event: ActionAudited

| Field | Value |
|---|---|
| Name | `ActionAudited` |
| Bounded Context | Administration and Moderation |
| Aggregate | AuditLog |
| Trigger | An operation is logged in `audit_log`, recording the administrative actor or user actor, the affected entity, and the action (RF13.9) |
| Consumers | Web Administration Panel |

**Audit payload:**

```json
{
  "eventType": "ActionAudited",
  "aggregateId": "audit_log.id",
  "payload": {
    "admin_profile_id": "int | null",
    "account_id": "int | null",
    "entity_type": "string",
    "entity_id": "int",
    "action": "string"
  }
}
```

`admin_profile_id` is used for administrative actions, while `account_id` is used when the audited actor is a normal user.

## Event Flow: Emergency Alert Activation Lifecycle

```text
User
  │
  │  POST /alerts (activation_method: panic_button|widget|sudden_movement|...)
  ▼
[alert] status = 'active', device_id = <required>
  │
  │  AlertActivated
  ├──────────────────────────────────────────┐
  │                                          ▼
  │                                  [For each emergency_contact]
  │                                  creates alert_contact (status=pending)
  │                                  → attempts multichannel delivery (SMS/email/Push)
  │                                  → updates status (sent | delivered | failed)
  │
  │  LocationRecorded (continuous)
  │  ────────────────────────────▶ location_log (alert_id = alert.id)
  │
  │  EvidenceRegistered (as media is captured)
  │  ────────────────────────────▶ evidence (file_url -> Cloud Storage)
  │
  │  ResourceCallStarted (optional)
  │  ────────────────────────────▶ resource_call (alert_id = alert.id)
  │
  │  (User deactivates alarm manually)
  ▼
alert.status = 'cancelled', cancelled_at = now()
  │
  ▼
AlertCancelled
```

## Event Summary Table

| Event | Origin Context | Source Table | Target Physical Service |
|---|---|---|---|
| `UserRegistered` | Identity and Accounts | `user`, `account`, `user_profile` | `identity-service` |
| `AdminProfileRegistered` | Identity and Accounts | `admin_profile` | `identity-service` |
| `RecoveryRequested` / `RecoveryCompleted` | Identity and Accounts | `recovery_request` | `identity-service` |
| `AlertActivationMethodToggled` | Identity and Accounts | `alert_activation_setting` | `identity-service` |
| `EmergencyContactAdded` | Emergency Contacts | `emergency_contact` | `alert-service` |
| `AlertActivated` | Alert Management | `alert` | `alert-service` |
| `LocationRecorded` | Alert Management | `location_log` | `alert-service` |
| `AlertContactNotified` | Alert Management | `alert_contact` | `alert-service` |
| `AlertResolved` / `AlertCancelled` / `AlertCompleted` / `AlertFailed` | Alert Management | `alert` | `alert-service` |
| `MiniAlertScheduled` / `MiniAlertSent` | Alert Management | `alert_reminder` | `alert-service` |
| `EvidenceRegistered` | Evidence | `evidence` | `alert-service` |
| `NotificationCreated` | Notifications | `notification` | `alert-service` |
| `ZoneRegistered` | Help Areas and Resources | `zone` | `resource-service` |
| `ZoneReported` | Help Areas and Resources | `zone_report` | `resource-service` |
| `ResourceCallStarted` / `ResourceCallEnded` | Help Areas and Resources | `resource_call` | `resource-service` |
| `UserReported` | Administration and Moderation | `user_report` | `admin-service` |
| `ModerationActionRegistered` | Administration and Moderation | `moderation_action` | `admin-service` |
| `SystemConfigurationUpdated` | Administration and Moderation | `system_configuration` | `admin-service` |
| `ActionAudited` | Administration and Moderation | `audit_log` | `admin-service` |

## Policies — Reactions to Events

| Trigger Event | Policy Result |
|---|---|
| `AlertActivated` | Query user's `emergency_contact` records and insert an `alert_contact` for each channel |
| `AlertContactNotified` (`status = failed`) | Retry delivery attempt if retry limit is not reached |
| `AlertReminder` (`status = scheduled`, no response) | Send mini alert notification; status moves to `sent` (RF4.5) |
| `ModerationAction` (`action_type = block`) | Block user access; `account.status = 'blocked'` |
| `ZoneReported` (`status = pending`) | Notify web administration panel; enqueue report for review by an administrator (`reviewed_by_admin_profile_id`) |
| `AlertActivationMethodToggled` | Re-evaluate device configuration; prevent disabling if it is the last enabled method on the device |

## Resilience Patterns for Events

`alert_contact` models delivery retries at the database level (`attempts`, `status = retrying`, `error_message`), consistent with `alert_reminder` and `recovery_request`. This satisfies the SRS fault-tolerance requirement for emergency notification processing (RNF007).