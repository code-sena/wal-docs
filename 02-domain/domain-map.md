# Domain Map — Bounded Contexts

> Map built by cross-referencing the database tables with the Functional Requirements from the SRS (RF1–RF13) and the actors defined in section 2.2 (User, Administrator).

---

## 1. Domain overview

Alerta Mujer manages the complete lifecycle of a gender-based violence emergency: from the registration of the user, her account, and her device, through the activation of an alert that shares real-time location and notifies trusted contacts and help centers, to the recording of evidence, the map of risk/safe zones, the resource directory, and administrative oversight (auditing, user reports, moderation) from a web panel.

---

## 2. Identified Bounded Contexts

### Bounded Context: Identity and Accounts

| Field | Value |
|-------|-------|
| **Name** | Identity and Accounts |
| **Responsibility** | Registration, authentication, account recovery, profile, devices, sessions, and preferences |
| **Tables** | `role`, `user`, `account`, `user_profile`, `recovery_request`, `device`, `device_permission`, `device_session`, `alert_activation_setting`, `user_preference` |
| **Database** | PostgreSQL |
| **Ubiquitous Language** | User, Account, Profile, Device, Session |

**Context-specific terms (Ubiquitous Language):**

| Term | Meaning in THIS context | Different in another context? |
|------|------------------------|-------------------------------|
| `user` | Person with a name, ID document, and role (user or administrator) | In the rest of the system, almost everything hangs off `user_profile`, not directly off `user` |
| `account` | Access credentials (password, status) for a `user` | This is what gets authenticated; different from the personal-data profile |
| `user_profile` | Extension of `user` used as an FK by almost all other contexts (alerts, zones, notifications, locations) | This is the real "business actor" within the domain |

---

### Bounded Context: Emergency Contacts

| Field | Value |
|-------|-------|
| **Name** | Emergency Contacts |
| **Responsibility** | Creation, lookup, editing, and deletion of a user's trusted contacts |
| **Tables** | `emergency_contact` |
| **Database** | PostgreSQL |
| **Ubiquitous Language** | Emergency contact |

---

### Bounded Context: Alert Management (Core Domain)

| Field | Value |
|-------|-------|
| **Name** | Alert Management |
| **Responsibility** | Alert activation, sending to contacts, location tracking, mini-alerts/reminders, frequent locations |
| **Tables** | `alert`, `alert_contact`, `location_log`, `alert_reminder`, `frequent_location` |
| **Database** | PostgreSQL |
| **Ubiquitous Language** | Alert, activation, delivery channel, recorded location |

---

### Bounded Context: Evidence

| Field | Value |
|-------|-------|
| **Name** | Evidence |
| **Responsibility** | Recording of files (photo/video/audio) associated with an alert |
| **Tables** | `evidence` |
| **Database** | PostgreSQL (+ external storage referenced by `file_url`) |
| **Ubiquitous Language** | Evidence |

---

### Bounded Context: Notifications (Supporting)

| Field | Value |
|-------|-------|
| **Name** | Notifications |
| **Responsibility** | Internal notifications to the user (different from the contact-sending, which lives in `alert_contact`) |
| **Tables** | `notification` |
| **Database** | PostgreSQL |
| **Ubiquitous Language** | Notification, priority, persistent |

---

### Bounded Context: Zones and Help Resources

| Field | Value |
|-------|-------|
| **Name** | Zones and Help Resources |
| **Responsibility** | Safe/risk zones, zone reports/classification by users, directory of emergency resources, and calls made to those resources |
| **Tables** | `zone`, `zone_report`, `emergency_resource`, `resource_call` |
| **Database** | PostgreSQL |
| **Ubiquitous Language** | Zone, risk level, emergency resource, call |

---

### Bounded Context: Administration and Moderation

| Field | Value |
|-------|-------|
| **Name** | Administration and Moderation |
| **Responsibility** | Auditing of administrative actions, reports about users, moderation actions, and global system configuration |
| **Tables** | `audit_log`, `user_report`, `moderation_action`, `system_configuration` |
| **Database** | PostgreSQL |
| **Ubiquitous Language** | Audit, user report, moderation action, system configuration |

---

## 3. Context Map

```
┌────────────────────────┐        ┌──────────────────────────┐
│ Identity and Accounts  │──U→D──▶│ Emergency Contacts        │
│ (user, account,        │        │ (emergency_contact)       │
│  user_profile, device)  │        └────────────┬─────────────┘
└───────────┬────────────┘                      │ U→D
            │ U→D                                ▼
            ▼                        ┌────────────────────────────┐
┌────────────────────────────────────│  Alert Management (Core)   │
│                                     │  alert, alert_contact,     │
│                                     │  location_log,             │
│                                     │  alert_reminder,           │
│                                     │  frequent_location         │
│                                     └───┬──────────┬─────────┬───┘
│                                         │          │         │
▼                                        ▼          ▼         ▼
┌─────────────┐              ┌───────────────┐ ┌──────────┐ ┌────────────────────┐
│ device_     │              │ Evidence      │ │ Notifi-  │ │ Zones and Help      │
│ session,    │              │ (evidence)    │ │ cations  │ │ Resources           │
│ device_     │              └───────────────┘ │(notifi-  │ │ (zone, zone_report, │
│ permission  │                                │ cation)  │ │ emergency_resource, │
└─────────────┘                                └──────────┘ │ resource_call)      │
                                                              └──────────┬──────────┘
                                                                         │
                                                                         ▼
                                                          ┌──────────────────────────┐
                                                          │ Administration and       │
                                                          │ Moderation                │
                                                          │ (audit_log, user_report, │
                                                          │ moderation_action,       │
                                                          │ system_configuration)    │
                                                          └──────────────────────────┘
```

### Relationships table

| Context A | Relationship | Context B | Communication channel | Contract |
|-----------|-------------|-----------|----------------------|---------|
| Identity and Accounts | U → D (FK `user_id`, `account_id`) | Emergency Contacts | Internal (same DB) | `emergency_contact.user_profile_id` |
| Identity and Accounts | U → D (FK `user_profile_id`, `device_id`) | Alert Management | Internal (same DB) | `alert.user_profile_id`, `alert.device_id` |
| Emergency Contacts | U → D | Alert Management | Internal | `alert_contact.emergency_contact_id` |
| Alert Management | D → U (event) | Evidence | Internal | `evidence.alert_id` |
| Alert Management | D → U (event) | Notifications | Internal | `notification.alert_id` (optional) |
| Alert Management | D → U | Zones and Help Resources | Internal | `resource_call.alert_id`, `location_log.alert_id` (optional) |
| Zones and Help Resources | U → D | Administration and Moderation | Internal | `zone_report.reviewed_by_account_id` |
| All contexts | D → U (audit) | Administration and Moderation | Internal | `audit_log.account_id` logs actions on any entity (`entity_type`, `entity_id`) |

---

## 4. Core Domain, Supporting, Generic

| Bounded Context | Type | Justification |
|----------------|------|---------------|
| Alert Management | Core | This is the system's core purpose (activating and propagating an emergency) |
| Emergency Contacts | Supporting | Needed so the alert has someone to notify |
| Zones and Help Resources | Supporting | Complements prevention and support |
| Evidence | Supporting | Reinforces the alert's supporting evidence |
| Administration and Moderation | Supporting | Needed to operate the system |
| Identity and Accounts | Generic | Registration/login/devices/sessions are a problem already solved in the industry |
| Notifications | Generic | Standard internal notification |

---

## 5. Modeling decisions

- `Device` is placed in Identity and Accounts, because it hangs off `account` and groups permissions (RF1.1–RF1.6) and activation methods (RF5.1–RF5.4) specific to the user's device.
- `resource_call` (calls to help centers) is included within Zones and Help Resources: it complements the emergency resource directory (RF7.1) and the lookup of nearby centers (RF9.3), although the SRS does not explicitly describe keeping a historical record of the call.
- `zone_report` complements zone registration (RF9.2) by allowing other users to classify an already-existing zone; the team has it under evaluation because the SRS only describes registration by whoever creates the zone, not a later reclassification.
- Administrative reports with filters and export (RF13.4) are generated via direct queries on the other tables; there is no `report` table because there is no need to persist a history of generated reports.

### Key decisions and discarded alternatives

| Decision | Discarded alternative | Reason |
|----------|----------------------|--------|
| Separate `user` (identity) from `user_profile` (business actor) | Use a single `user` table | Almost all FKs in the rest of the system point to `user_profile_id`, reflecting that the SRS distinguishes between registration data (RF2.1) and the user's activity within the app (RF3, RF4, RF9) |
| Put `resource_call` in "Zones and Help Resources" instead of "Alert Management" | Put it in Alerts because it has an optional `alert_id` | Its main entity is `emergency_resource`, and `alert_id` is nullable: a call to a help center (RF7.1) can be made without an active alert |

---

## 6. How to update this map

1. If the team confirms the removal of `zone_report` or `resource_call`, update this map and the corresponding relationships.
2. Validate this map with the team before using it as the basis for the microservices architecture.
3. Sync with the C4 diagram in `05-architecture/overview.md` once it exists.
