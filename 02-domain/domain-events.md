# Domain Events

> Catalog of Woman Alert's domain events, built from each table's state changes (`status`, `note:` with the allowed values) and from the SRS Functional Requirements that give rise to them.
> **Updated to match the final 27-table database schema** (adds `AdminProfileRegistered` and renames `*_account_id` fields to `*_admin_profile_id` on the events they appear in).

---

## Event catalog

### Event: UserRegistered

| Field | Value |
|-------|-------|
| **Name** | `UserRegistered` |
| **Bounded Context** | Identity and Accounts |
| **Aggregate** | User |
| **Trigger** | A record is created in `user` + `account` + `user_profile` (RF2.1) |
| **Consumers** | Administration and Moderation (the user becomes available for lookup and management, RF13.1) |

**Payload:**
```json
{
  "eventType": "UserRegistered",
  "aggregateId": "user.id",
  "payload": { "role_id": "int", "first_name": "string", "last_name": "string", "email": "string" }
}
```

---

### Event: AdminProfileRegistered

| Field | Value |
|-------|-------|
| **Name** | `AdminProfileRegistered` |
| **Bounded Context** | Identity and Accounts |
| **Aggregate** | AdminProfile |
| **Trigger** | A record is created in `admin_profile`, restricted to an `account` whose `user.role_id` is `administrator` |
| **Consumers** | Administration and Moderation (this administrator becomes a valid actor for `zone.created_by_admin_profile_id`, `zone_report.reviewed_by_admin_profile_id`, `user_report.reviewed_by_admin_profile_id`, `moderation_action.admin_profile_id`, `system_configuration.updated_by_admin_profile_id`) |

**Payload:**
```json
{
  "eventType": "AdminProfileRegistered",
  "aggregateId": "admin_profile.id",
  "payload": { "account_id": "int" }
}
```

---

### Event: RecoveryRequested / RecoveryCompleted

| Field | Value |
|-------|-------|
| **Name** | `RecoveryRequested`, `RecoveryCompleted` |
| **Bounded Context** | Identity and Accounts |
| **Aggregate** | RecoveryRequest |
| **Trigger** | A `recovery_request` is created (`status = pending`) / the token moves to `used` (RF2.3) |
| **Consumers** | Notifications (sending the token via `method`: `email` \| `telephone`) |

---

### Event: EmergencyContactAdded

| Field | Value |
|-------|-------|
| **Name** | `EmergencyContactAdded` |
| **Bounded Context** | Emergency Contacts |
| **Aggregate** | EmergencyContact |
| **Trigger** | A record is created in `emergency_contact` (RF2.2) — requires at least `contact_name` and `telephone` (`telephone` is `NOT NULL`) |
| **Consumers** | Alert Management (to know who to notify via `alert_contact`) |

---

### Event: AlertActivated

| Field | Value |
|-------|-------|
| **Name** | `AlertActivated` |
| **Bounded Context** | Alert Management |
| **Aggregate** | Alert |
| **Trigger** | An `alert` is created with `status = 'active'`, a `device_id` (`NOT NULL`), and an `activation_method` of `panic_button \| widget \| sudden_movement \| physical_button \| discreet_confirmation \| automatic` (RF5) |
| **Consumers** | Notifications (creates records in `alert_contact` for each `emergency_contact`), Evidence, Administration |

**Payload (actual fields):**
```json
{
  "eventType": "AlertActivated",
  "aggregateId": "alert.id",
  "payload": {
    "user_profile_id": "int",
    "device_id": "int",
    "alert_type": "main | mini | reminder",
    "activation_method": "panic_button | widget | sudden_movement | physical_button | discreet_confirmation | automatic",
    "started_at": "timestamp"
  }
}
```

**What do consumers do with this event?**

| Consuming service | Action |
|------------------|--------|
| Notifications | Creates an `alert_contact` for each `emergency_contact` of the user, for each applicable channel (`sms`\|`email`\|`call`\|`push`) |
| Evidence | May start receiving `evidence` associated with the `alert_id` |
| Alert Management | Starts inserting `location_log` records with `alert_id` while the alert is `active` |

---

### Event: LocationRecorded

| Field | Value |
|-------|-------|
| **Name** | `LocationRecorded` |
| **Bounded Context** | Alert Management |
| **Aggregate** | LocationLog |
| **Trigger** | A record is inserted in `location_log` (with or without `alert_id`) while the alert is active (RF4.3) |
| **Consumers** | Emergency contacts (real-time tracking) |

---

### Event: AlertContactNotified

| Field | Value |
|-------|-------|
| **Name** | `AlertContactNotified` |
| **Bounded Context** | Alert Management |
| **Aggregate** | AlertContact |
| **Trigger** | An `alert_contact` record changes from `status = pending` to `sent` / `delivered` / `failed` / `retrying` |
| **Consumers** | Administration (send auditing) |

---

### Event: AlertResolved / AlertCancelled / AlertCompleted / AlertFailed

| Field | Value |
|-------|-------|
| **Name** | `AlertResolved`, `AlertCancelled`, `AlertCompleted`, `AlertFailed` |
| **Bounded Context** | Alert Management |
| **Aggregate** | Alert |
| **Trigger** | `alert.status` changes from `active` to `resolved` \| `cancelled` \| `completed` \| `failed` (UC3/UC4: the user manually turns off the alarm → `cancelled`) |
| **Consumers** | Notifications, Administration |

**Payload:**
```json
{ "eventType": "AlertCancelled", "aggregateId": "alert.id", "payload": { "cancelled_at": "timestamp" } }
```

---

### Event: MiniAlertScheduled / MiniAlertSent

| Field | Value |
|-------|-------|
| **Name** | `MiniAlertScheduled`, `MiniAlertSent` |
| **Bounded Context** | Alert Management |
| **Aggregate** | AlertReminder |
| **Trigger** | An `alert_reminder` is created (`status = scheduled`) / changes to `sent` when there is no response from the user (RF4.5) |
| **Consumers** | Notifications |

---

### Event: AlertActivationMethodToggled

| Field | Value |
|-------|-------|
| **Name** | `AlertActivationMethodToggled` |
| **Bounded Context** | Identity and Accounts |
| **Aggregate** | AlertActivationSetting |
| **Trigger** | `alert_activation_setting.is_enabled` changes value for a `device_id` + `activation_method` (RF5) |
| **Consumers** | Alert Management (an alert can no longer be triggered by a disabled method) |
| **Business rule tied to this event** | Before applying a toggle to `false`, the Service layer checks that at least one other method for the same `device_id` remains `is_enabled = true`; a database `CHECK` constraint cannot express this because it needs to compare across sibling rows |

---

### Event: EvidenceRegistered

| Field | Value |
|-------|-------|
| **Name** | `EvidenceRegistered` |
| **Bounded Context** | Evidence |
| **Aggregate** | Evidence |
| **Trigger** | A record is inserted in `evidence` with `media_type = photo \| video \| audio` (RF6.1, RF6.2) |
| **Consumers** | Administration (lookup via RF13.7) |

---

### Event: NotificationCreated

| Field | Value |
|-------|-------|
| **Name** | `NotificationCreated` |
| **Bounded Context** | Notifications |
| **Aggregate** | Notification |
| **Trigger** | A record is inserted in `notification` with `priority = normal \| high \| critical` (RF8.1) |
| **Consumers** | The user herself (via app/web) |

---

### Event: ZoneRegistered

| Field | Value |
|-------|-------|
| **Name** | `ZoneRegistered` |
| **Bounded Context** | Zones and Help Resources |
| **Aggregate** | Zone |
| **Trigger** | A `zone` is created with `zone_type = safe \| risk`, optionally linked to `created_by_admin_profile_id` (RF9.2) |
| **Consumers** | Administration (to review/manage, RF13.3) |

---

### Event: ZoneReported ⚠️

| Field | Value |
|-------|-------|
| **Name** | `ZoneReported` |
| **Bounded Context** | Zones and Help Resources |
| **Aggregate** | ZoneReport |
| **Trigger** | A `zone_report` is created (`status = pending`) |
| **Consumers** | Administration (approves/rejects, via `reviewed_by_admin_profile_id`) |
| **Relationship with the SRS** | RF9.2 only describes registering a zone by whoever creates it; this event covers reclassification by other users, a feature under evaluation by the team |

---

### Event: ResourceCallStarted / ResourceCallEnded ⚠️

| Field | Value |
|-------|-------|
| **Name** | `ResourceCallStarted`, `ResourceCallEnded` |
| **Bounded Context** | Zones and Help Resources |
| **Aggregate** | ResourceCall |
| **Trigger** | A `resource_call` is created (`status = started`) / changes to `answered` \| `unanswered` \| `failed` \| `cancelled` |
| **Consumers** | Administration |
| **Relationship with the SRS** | RF1.2 (call permission) and RF7.1 (directory) do not require storing a call history; this event covers that record, a feature under evaluation by the team |

---

### Event: UserReported

| Field | Value |
|-------|-------|
| **Name** | `UserReported` |
| **Bounded Context** | Administration and Moderation |
| **Aggregate** | UserReport |
| **Trigger** | A `user_report` is created (`status = pending`) (RF13.10) |
| **Consumers** | Moderation |

---

### Event: ModerationActionRegistered

| Field | Value |
|-------|-------|
| **Name** | `ModerationActionRegistered` |
| **Bounded Context** | Administration and Moderation |
| **Aggregate** | ModerationAction |
| **Trigger** | A `moderation_action` is created with `action_type = review \| warn \| block \| unblock \| reject \| close \| escalate`, authored by `admin_profile_id` (RF13.10) |
| **Consumers** | Identity and Accounts (if `action_type = block/unblock`, it affects `account.status`) |

---

### Event: SystemConfigurationUpdated

| Field | Value |
|-------|-------|
| **Name** | `SystemConfigurationUpdated` |
| **Bounded Context** | Administration and Moderation |
| **Aggregate** | SystemConfiguration |
| **Trigger** | The single `system_configuration` row (`singleton_key = 'SYSTEM'`) is updated, tracked via `updated_by_admin_profile_id` |
| **Consumers** | All contexts that read global settings (`sos_max_duration_minutes`, `emergency_phone_number`, `default_notification_priority`, `default_theme`) |

---

### Event: ActionAudited

| Field | Value |
|-------|-------|
| **Name** | `ActionAudited` |
| **Bounded Context** | Administration and Moderation |
| **Aggregate** | AuditLog |
| **Trigger** | Any relevant administrative action is inserted into `audit_log` (`entity_type`, `entity_id`, `action`) (RF13.9) |
| **Consumers** | Admin panel, for traceability and control of operations (RF13.9) |

---

## Event flow: Panic button activation (real, with table names)

```
User
  │
  │  POST /alerts (activation_method: widget|sudden_movement|physical_button|
  │                discreet_confirmation|panic_button|automatic)
  ▼
[alert] status = 'active', device_id = <required>
  │
  │  AlertActivated
  ├──────────────────────────────────────────┐
  │                                            ▼
  │                                  [For each emergency_contact]
  │                                  creates alert_contact (status=pending)
  │                                  → attempts to send (sms/email/call/push)
  │                                  → updates status to sent/delivered/failed
  │
  │  LocationRecorded (repeated)
  │  ────────────────────────────▶ location_log (alert_id = alert.id)
  │
  │  (no response from the user within the configured time)
  │  ────────────────────────────▶ alert_reminder (reminder_type=mini_alert)
  │
  │  (the user confirms it was a misunderstanding — UC3/UC4)
  ▼
alert.status = 'cancelled', cancelled_at = now()
  │
  ▼
AlertCancelled
```

---

## Event summary table

| Event | Origin context | Source table |
|-------|---------------|--------------|
| UserRegistered | Identity and Accounts | `user`, `account`, `user_profile` |
| AdminProfileRegistered | Identity and Accounts | `admin_profile` |
| RecoveryRequested / Completed | Identity and Accounts | `recovery_request` |
| EmergencyContactAdded | Emergency Contacts | `emergency_contact` |
| AlertActivated | Alert Management | `alert` |
| LocationRecorded | Alert Management | `location_log` |
| AlertContactNotified | Alert Management | `alert_contact` |
| AlertResolved/Cancelled/Completed/Failed | Alert Management | `alert` |
| MiniAlertScheduled / Sent | Alert Management | `alert_reminder` |
| AlertActivationMethodToggled | Identity and Accounts | `alert_activation_setting` |
| EvidenceRegistered | Evidence | `evidence` |
| NotificationCreated | Notifications | `notification` |
| ZoneRegistered | Zones and Help Resources | `zone` |
| ZoneReported ⚠️ | Zones and Help Resources | `zone_report` |
| ResourceCallStarted/Ended ⚠️ | Zones and Help Resources | `resource_call` |
| UserReported | Administration and Moderation | `user_report` |
| ModerationActionRegistered | Administration and Moderation | `moderation_action` |
| SystemConfigurationUpdated | Administration and Moderation | `system_configuration` |
| ActionAudited | Administration and Moderation | `audit_log` |

⚠️ = table/event under evaluation for removal by the team (no direct RF in the SRS).

---

## Policies — Reactions to events

| Trigger event | Policy | Result |
|--------------|--------|----------|
| AlertActivated | For each `emergency_contact` of the user, create an `alert_contact` per available channel | Multichannel delivery (RF4.2) |
| AlertContactNotified (`status = failed`) | Retry the send | `attempts` is incremented, moves to `retrying` |
| AlertReminder (`status = scheduled`, no response from the user) | Send mini alert | Updated to `sent` (RF4.5) |
| ModerationAction (`action_type = block`) | Block the associated account | `account.status = 'blocked'` |
| AlertActivationMethodToggled (would disable the last enabled method) | Reject the toggle | The Service layer blocks the `UPDATE`, since a device must always keep at least one activation method enabled |

---

## Resilience patterns for events

`alert_contact` models retries at the data level (`attempts`, `status = retrying`, `error_message`), just like `alert_reminder` and `recovery_request` — consistent with the SRS requirement for fault tolerance in sending critical notifications and alerts (section 2.3, RNF007). The maximum number of retries and the backoff time between each one still need to be defined, which the SRS does not set.