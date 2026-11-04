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
| **Trigger** | A record is created in `user` + `account` + `user_profile` (RF-USU-001) |
| **Consumers** | Administration and Moderation (the user becomes available for lookup and management, RF-ADM-001) |

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
| **Trigger** | A `recovery_request` is created (`status = pending`) / the token moves to `used` (RF-USU-002) |
| **Consumers** | Notifications (sending the token via `method`: `email` \| `telephone`) |

---

### Event: EmergencyContactAdded

| Field | Value |
|-------|-------|
| **Name** | `EmergencyContactAdded` |
| **Bounded Context** | Emergency Contacts |
| **Aggregate** | EmergencyContact |
| **Trigger** | A record is created in `emergency_contact` (RF-CON-001) — requires at least `contact_name` and `telephone` (`telephone` is `NOT NULL`) |
| **Consumers** | Alert Management (to know who to notify via `alert_contact`) |

---

### Event: AlertActivated

| Field | Value |
|-------|-------|
| **Name** | `AlertActivated` |
| **Bounded Context** | Alert Management |
| **Aggregate** | Alert |
| **Trigger** | An `alert` is created with `status = 'active'`, a `device_id` (`NOT NULL`), and an `activation_method` of `panic_button \| contact \| suspicious_movement \| widget \| signal_lost \| auto` (RF-ACT) |
| **Consumers** | Notifications (creates records in `alert_contact` for each `emergency_contact`), Evidence, Administration |

**Payload (actual fields):**
```json
{
  "eventType": "AlertActivated",
  "aggregateId": "alert.id",
  "payload": {
    "user_profile_id": "int",
    "device_id": "int",
    "alert_type": "main",
    "activation_method": "panic_button | contact | suspicious_movement | widget | signal_lost | auto",
    "latitude": "numeric | null",
    "longitude": "numeric | null",
    "started_at": "timestamp"
  }
}
```

**What do consumers do with this event?**

| Consuming service | Action |
|------------------|--------|
| Notifications | Creates an `alert_contact` for each `emergency_contact` of the user, for each applicable channel (`email` always, `push` if the contact has the app with an active session); the email carries the tracking link |
| Evidence | May start receiving `evidence` associated with the `alert_id` |
| Alert Management | Starts inserting `location_log` records with `alert_id` while the alert is `active` |

---

### Event: LocationRecorded

| Field | Value |
|-------|-------|
| **Name** | `LocationRecorded` |
| **Bounded Context** | Alert Management |
| **Aggregate** | LocationLog |
| **Trigger** | A record is inserted in `location_log` (with or without `alert_id`) while the alert is active (RF-ALE-003) |
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
| **Trigger** | An `alert_reminder` is scheduled while the alert is active (for example the "Are you still in danger?" question at minute 25) / it is sent when the user does not answer (RF-ALE-005, RF-ALE-007) |
| **Consumers** | Notifications |

---

### Event: AlertActivationMethodToggled

| Field | Value |
|-------|-------|
| **Name** | `AlertActivationMethodToggled` |
| **Bounded Context** | Identity and Accounts |
| **Aggregate** | AlertActivationSetting |
| **Trigger** | `alert_activation_setting.is_enabled` changes value for a `device_id` + `activation_method` (RF-ACT-010) |
| **Consumers** | Alert Management (an alert can no longer be triggered by a disabled method) |
| **Business rule tied to this event** | Before applying a toggle to `false`, the Service layer checks that at least one other method for the same `device_id` remains `is_enabled = true`; a database `CHECK` constraint cannot express this because it needs to compare across sibling rows |

---

### Event: EvidenceRegistered

| Field | Value |
|-------|-------|
| **Name** | `EvidenceRegistered` |
| **Bounded Context** | Evidence |
| **Aggregate** | Evidence |
| **Trigger** | A record is inserted in `evidence` with `media_type = photo \| video \| audio` (RF-EVI-001, RF-EVI-002) |
| **Consumers** | Administration (lookup via RF-ADM-007) |

---

### Event: NotificationCreated

| Field | Value |
|-------|-------|
| **Name** | `NotificationCreated` |
| **Bounded Context** | Notifications |
| **Aggregate** | Notification |
| **Trigger** | A record is inserted in `notification` with `priority = normal \| high \| critical` (RF-NOT-002) |
| **Consumers** | The user herself (via app/web) |

---

### Event: ZoneRegistered

| Field | Value |
|-------|-------|
| **Name** | `ZoneRegistered` |
| **Bounded Context** | Zones and Help Resources |
| **Aggregate** | Zone |
| **Trigger** | A `zone` is created with `zone_type = safe \| risk`, optionally linked to `created_by_admin_profile_id` (RF-UBI-002) |
| **Consumers** | Administration (to review/manage, RF-ADM-003) |

---

### Event: ZoneReported ⚠️

| Field | Value |
|-------|-------|
| **Name** | `ZoneReported` |
| **Bounded Context** | Zones and Help Resources |
| **Aggregate** | ZoneReport |
| **Trigger** | A `zone_report` is created (`status = pending`) |
| **Consumers** | Administration (approves/rejects, via `reviewed_by_admin_profile_id`) |
| **Relationship with the SRS** | RF-UBI-002 only describes registering a zone by whoever creates it; this event covers reclassification by other users, a feature under evaluation by the team |

---

### Event: ResourceCallStarted / ResourceCallEnded ⚠️

| Field | Value |
|-------|-------|
| **Name** | `ResourceCallStarted`, `ResourceCallEnded` |
| **Bounded Context** | Zones and Help Resources |
| **Aggregate** | ResourceCall |
| **Trigger** | A `resource_call` is created (`status = started`) / changes to `answered` \| `unanswered` \| `failed` \| `cancelled` |
| **Consumers** | Administration |
| **Relationship with the SRS** | RF-REC-002 (call permission) and RF-REC-001 (directory) do not require storing a call history; this event covers that record, a feature under evaluation by the team |

---

### Event: UserReported

| Field | Value |
|-------|-------|
| **Name** | `UserReported` |
| **Bounded Context** | Administration and Moderation |
| **Aggregate** | UserReport |
| **Trigger** | A `user_report` is created (`status = pending`) (RF-ADM-009) |
| **Consumers** | Moderation |

---

### Event: ModerationActionRegistered

| Field | Value |
|-------|-------|
| **Name** | `ModerationActionRegistered` |
| **Bounded Context** | Administration and Moderation |
| **Aggregate** | ModerationAction |
| **Trigger** | A `moderation_action` is created with `action_type = review \| warn \| block \| unblock \| reject \| close \| escalate`, authored by `admin_profile_id` (RF-ADM-009) |
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
| **Trigger** | Any relevant administrative action is inserted into `audit_log` (`entity_type`, `entity_id`, `action`) (RF-AUD-001) |
| **Consumers** | Admin panel, for traceability and control of operations (RF-AUD-001) |

---

## New events since the microservices split

| Event | Origin | Source table | What happens |
|-------|--------|--------------|--------------|
| AlertNoticeResent | Alert Management | `alert_contact` | No contact opened the notice in 5 minutes: resent once with the latest location (RF-ALE-006) |
| AlertSignalLost | Alert Management | `location_log` | No location for 5 minutes during an active alert: contacts warned with the last location (RF-ALE-009) |
| AlertDangerQuestionAsked / AlertExtended | Alert Management | `alert`, `alert_reminder` | Minute 25: "Are you still in danger?"; yes → extended 30 minutes; no answer → saving mode and notice (RF-ALE-007) |
| AlertExpired | Alert Management | `alert` | Absolute limit of 120 minutes reached |
| AlertMessageSent | Alert Management | `alert_message` | Chat message from the user or a contact |
| SafeTripStarted / SafeTripAlerted | Alert Management | `safe_trip` | Trip started; the server creates the alert if arrival is not confirmed or the heartbeat stops (RF-TRA-002) |
| AutoProtectionStarted / AutoProtectionAlerted | Alert Management | `safe_trip` (`mode = auto`), `protection_device` | Watch started when leaving a frequent place; alert by lost signal, no answer, not confirmed or duress PIN (RF-TRA-003) |
| DuressPinUsed | Alert Management | `alert`, `safe_trip` | The app looks cancelled while the contacts get a silent notice (RF-ACT-007) |
| TrackingLinkCreated / TrackingViewed | Notifications | `tracking_link`, `tracking_view` | Link sent by email; visits recorded so the user knows she is being watched |
| PasswordRecoveryRequested | Identity and Accounts | `recovery_request` | 6-digit code sent by email; forwarded between services through `/internal/events` |
| NewDeviceSignedIn | Identity and Accounts | `device`, `device_session` | The user's other phones are notified (RF-NOT-006) |
| AccountDeletionRequested / Approved | Administration and Moderation | `account_deletion_request` | Needs a second administrator; personal data anonymized |
| EvidenceRetentionNotice | Evidence | `evidence` | Notice 5 days before deleting evidence older than 30 days |

Implementation: Spring events published after the transaction commits; between services they are forwarded by HTTP to `/internal/events` with `X-Internal-Key`. There is no message broker and no outbox.

---

## Event flow: Panic button activation (real, with table names)

```
User
  │
  │  activation (SOS button | contact | shake | widget) → 5-second countdown
  │  (cancelled in time or volume-down ×4 → nothing is registered)
  │
  │  POST /api/alerts (activationMethod: panic_button | contact | suspicious_movement)
  ▼
[alert.alert] status = 'active', device_id = <required>, lat/long if available
  │
  │  AlertActivated (after commit)
  ├──────────────────────────────────────────┐
  │                                            ▼
  │                                  [For each emergency_contact]
  │                                  creates notification.tracking_link (hash only)
  │                                  creates alert_contact (email; push if the contact has the app)
  │                                  → Resend / FCM
  │                                  → status sent | failed (attempts, error_message)
  │                                  → nobody opened it in 5 min → resent once
  │
  │  LocationRecorded (every 15 s for 5 min, then 30 s / 60 s in saving mode)
  │  ────────────────────────────▶ alert.location_log (alert_id = alert.id)
  │  (no location for 5 min) ────▶ AlertSignalLost → notice to contacts
  │
  │  Minute 25: "Are you still in danger?" ──▶ yes: +30 min | no answer: saving mode + notice
  │
  │  "I'm OK" (+ cancellation PIN)  ──▶ status = 'cancelled' → ALERTA_FINALIZADA (email + push)
  │  Administrator                  ──▶ status = 'resolved'
  │  120 minutes                    ──▶ status = 'expired'
  ▼
Tracking link stops working 2 hours after the end
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
| AlertNoticeResent, AlertSignalLost, AlertExpired | Alert Management | `alert_contact`, `location_log`, `alert` |
| SafeTripStarted / SafeTripAlerted, AutoProtectionStarted / Alerted | Alert Management | `safe_trip`, `protection_device` |
| TrackingLinkCreated / TrackingViewed | Notifications | `tracking_link`, `tracking_view` |
| AccountDeletionRequested / Approved | Administration and Moderation | `account_deletion_request` |
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

⚠️ = these tables were under evaluation; both are in use today (zone reports approved by the administration; manual call history in the web portal).

---

## Policies — Reactions to events

| Trigger event | Policy | Result |
|--------------|--------|----------|
| AlertActivated | For each `emergency_contact` of the user, send email (and push if available) with the tracking link | Notice per contact and channel (RF-ALE-002) |
| AlertContactNotified (nobody opened it in 5 min) | Resend once with the latest location | Second notice (RF-ALE-006) |
| No location for 5 minutes | Warn the contacts with the last location | Lost-signal notice (RF-ALE-009) |
| Minute 25 without answer | Saving mode and notice to the contacts | Alert keeps running until 120 minutes (RF-ALE-007) |
| Trip not confirmed / heartbeat lost | Server creates the alert | Alert with the last location (RF-TRA-002) |
| ModerationAction (`action_type = block`) | Block the associated account | `account.status = 'blocked'` |
| AlertActivationMethodToggled (would disable the last enabled method) | Reject the toggle | The Service layer blocks the `UPDATE`, since a device must always keep at least one activation method enabled |

---

## Resilience patterns for events

`alert_contact` records each send with `attempts`, `status` (`pending`, `sent`, `failed`, `expired`) and `error_message`, so a provider failure is visible per contact and channel and does not stop the other notices (RNF-DIS-003). The notice is resent once if nobody opens it in 5 minutes. Events are published only after the transaction commits, so a rolled-back operation never sends an email; there is no outbox, so an event can be lost if the service stops right after the commit. Scheduled tasks (expiration, resend, signal check, trips, retention) only run while the service is awake on the free plan.
