# Domain map: bounded contexts

> Map constructed by crossing the database tables with the SRS Functional Requirements (RF1–RF13) and the actors defined in section 2.2 (User, Administrator).
> **Updated to match final 27-table database schema** (adds `admin_profile` and renames audit/review FKs from `account_id` to `admin_profile_id`).

---

## 1. Domain Overview

Alerta Mujer manages the entire life cycle of an emergency: from the registration of the user, her account and her device, through the activation of an alert that shares location in real time and notifies trusted contacts and help centers, to the registration of evidence, the map of risk/safe areas, the resource directory and administrative control (audit, user reports, moderation) from a web panel.

---

## 2. Bounded contexts identified

### Bounded context: identity and accounts

| Field | Value |
|-------|-------|
| **Name** | Identity and Accounts |
| **Liability** | Registration, authentication, account recovery, profile, administrator profile, devices, sessions and preferences |
| **Tables** | `role`, `user`, `account`, `user_profile`, `admin_profile`, `recovery_request`, `device`, `device_permission`, `device_session`, `alert_activation_settings`, `user_preference` |
| **Database** | PostgreSQL |
| **Ubiquitous language** | User, Account, Profile, Administrator Profile, Device, Session |

**Context-specific terms (ubiquitous language):**

| Term | Meaning in THIS context | Different in another context? |
|------|----------------------|----------------------|
| `user` | Person with name, identity document and role (user or administrator) | In the rest of the system, almost everything depends on `user_profile`, not directly on `user` |
| `account` | Access credentials (password, status) for a `user` | This is what is authenticated; different from the personal data profile |
| `user_profile` | `user` extension, exclusive for accounts with `role=user`, used as FK in almost all other contexts (alerts, zones, notifications, locations) | This is the true "business actor" (the end user of the application) within the domain |
| `admin_profile` | `account` extension, exclusive for accounts with `role=administrator` | Structural counterpart to `user_profile`: Ensures, by design, that only administrators can appear as authors of an administrative action (zones created, reports reviewed, moderation actions, system configuration changes) |

---

### Limited context: emergency contacts

| Field | Value |
|-------|-------|
| **Name** | Emergency contacts |
| **Liability** | Create, search, edit and delete a user's trusted contacts |
| **Tables** | `emergency_contact` |
| **Database** | PostgreSQL |
| **Ubiquitous language** | Emergency contact |

---

### Bounded context: alert management (main domain)

| Field | Value |
|-------|-------|
| **Name** | Alert management |
| **Liability** | Activate alerts, send to contacts, location tracking, mini alerts/reminders, frequent locations |
| **Tables** | `alert`, `contact_alert`, `check-in_location`, `reminder_alert`, `frequent_location` |
| **Database** | PostgreSQL |
| **Ubiquitous language** | Alert, activation, delivery channel, registered location |

---

### Bounded context: evidence

| Field | Value |
|-------|-------|
| **Name** | Evidence |
| **Liability** | Recording of files (photo/video/audio) associated with an alert |
| **Tables** | `evidence` |
| **Database** | PostgreSQL (+ external storage referenced by `file_url`) |
| **Ubiquitous language** | Evidence |

---

### Limited context: notifications (support)

| Field | Value |
|-------|-------|
| **Name** | Notifications |
| **Liability** | Internal user notifications (other than sending contacts, which resides in `alert_contact`) |
| **Tables** | `notification` |
| **Database** | PostgreSQL |
| **Ubiquitous language** | Notification, priority, persistent |

---

### Limited context: help areas and resources

| Field | Value |
|-------|-------|
| **Name** | Help and resource areas |
| **Liability** | Safe/risk zones, zone reports/classification by users, directory of emergency resources and calls made to those resources |
| **Tables** | `zone`, `reporting_zone`, `emergency_resource`, `call_resource` |
| **Database** | PostgreSQL |
| **Ubiquitous language** | Area, risk level, emergency resource, call |

---

### Bounded context: administration and moderation

| Field | Value |
|-------|-------|
| **Name** | Administration and Moderation |
| **Liability** | Audit of administrative actions, user reports, moderation actions and global system configuration |
| **Tables** | `audit_log`, `user_report`, `moderation_action`, `system_configuration` |
| **Database** | PostgreSQL |
| **Ubiquitous language** | Audit, User Report, Moderation Action, System Settings, Administrator Profile |

---

## 3. Context map

```
┌────────────────────────┐ ┌──────────────────────────┐
│ Identity and Accounts │──U→D──▶│ Emergency Contacts │
│ (user, account, │ │ (emergency_contact) │
│ user_profile, │ └────────────┬─────────────┘
│ admin_profile, device) │ │ U→D
└───────────┬────────────┘ ▼
            │ U→D ┌────────────────────────────┐
            ▼ │ Alert management (core) │
alert, contact_alert, │
│ │ location_record, │
│ │ alert_reminder, │
│ │ frequent_location │
│ └───┬──────────┬─────────┬───┘
│ │ │ │
▼ ▼ ▼ ▼
┌─────────────┐ ┌───────────────┐ ┌──────────┐ ┌────────────────────┐
│ device_ │ │ Evidence │ │ Notification │ │ Zones and Help │
│ session, │ │ (evidence) │ │ cations │ │ Resources │
│ device_ │ └───────────────┘ │(notify_ │ │ (zone, report_zone, │
│ permission │ │ cation) │ │ emergency_resource, │
└─────────────┘ └──────────┘ │ call_resource) │
                                                              └──────────┬──────────┘
                                                                         │
                                                                         ▼
                                                          ┌──────────────────────────┐
                                                          │ Administration and │
                                                          │ Moderation │
                                                          │ (audit_log, user_report, │
                                                          │ moderation_action, │
                                                          │ system_config) │
                                                          │ ── all written by │
                                                          │ admin_profile ── │
                                                          └──────────────────────────┘
```

### Relationship table

| Context A | Relationship | Context B | Communication channel | Contract |
|-----------|-------------|-----------|----------------------|-----------------|
| Identity and Accounts | U → D (FK `user_id`, `account_id`) | Emergency contacts | Internal (same database) | `emergency_contact.user_profile_id` |
| Identity and Accounts | U → D (FK `user_profile_id`, `device_id`) | Alert management | Internal (same database) | `alert.user_profile_id`, `alert.device_id` |
| Emergency contacts | U → D | Alert management | Internal | `contact_alert.emergency_contact_id` |
| Alert management | D → U (event) | Evidence | Internal | `evidence.alert_id` |
| Alert management | D → U (event) | Notifications | Internal | `notification.alert_id` (optional) |
| Alert management | D → U | Help and resource areas | Internal | `resource_call.alert_id`, `location_log.alert_id` (optional) |
| Identity and Accounts | U → D (FK `admin_profile_id`) | Help and resource areas | Internal | `zone.created_by_admin_profile_id`, `zone_report.reviewed_by_admin_profile_id` |
| Identity and Accounts | U → D (FK `admin_profile_id`) | Administration and Moderation | Internal | `user_report.reviewed_by_admin_profile_id`, `moderation_action.admin_profile_id`, `system_configuration.updated_by_admin_profile_id` |
| All contexts | D → U (audit) | Administration and Moderation | Internal | `audit_log.account_id` logs actions on any entity (`entity_type`, `entity_id`) |

---

## 4. Primary, secondary and generic domain

| Bounded context | Type | Justification |
|----------------|------|---------------|
| Alert management | Core | This is the main objective of the system (activate and propagate an emergency) |
| Emergency contacts | Support | Necessary so that the alert has someone to notify |
| Help and resource areas | Support | Complements prevention and support |
| Evidence | Support | Strengthens the evidence supporting the alert |
| Administration and Moderation | Support | Necessary to operate the system |
| Identity and Accounts | Generic | Registration/login/devices/sessions are a problem already solved in the industry |
| Notifications | Generic | Standard internal notification |

---

## 5. Decision modeling

- "Device" is placed in Identity and Accounts, because it hangs on the "account" and groups the permissions (RF1.1–RF1.6) and activation methods (RF5.1–RF5.4) specific to the user's device.
- `alert_activation_setting` is conceptually related to Alert Management (it determines how an alert can be activated), but was kept within Identity and Accounts because it depends on `device_id` and is, in practice, a device/user setting rather than something that changes as part of an alert's own lifecycle. This is a deliberate trade-off to avoid fragmenting the domain into an additional bounded context for a single table, not an oversight.
- `FrequentLocation` could conceptually justify its own limited context ("User Locations"), since saving a location (RF3.2) is a user-independent feature and not something that happens as part of the lifecycle of an active alert. Maintained within Alert Management to avoid excessive domain fragmentation for a 27-table academic project; A separate "User Locations" context is a reasonable future evolution if the feature grows (e.g. sharing saved locations, tagging them by category), but is not necessary in the current scope.
- Added `admin_profile` as a structural counterpart to `user_profile`: any FK that represents "the administrator who performed this action" (create a zone, review a report, run a moderation action, update system settings) now points to `admin_profile.id` instead of `account.id` directly. This makes it structurally impossible for an account with "role=user" to appear as the author of an administrative action; The same "user_profile" guarantee already offered to normal users is symmetrically applied to administrators.
- Help Areas and Resources includes `resource_call` (calls to help centers): it complements the directory of emergency resources (RF7.1) and the consultation of nearby centers (RF9.3), although the SRS does not explicitly describe keeping a historical record of the call.
- `zone_report` complements zone registration (RF9.2) by allowing other users to classify an existing zone; The team has it under evaluation because the SRS only describes the record of whoever creates the zone, not a subsequent reclassification.
- Administrative reports with filters and export (RF13.4) are generated by direct queries on the other tables; There is no "reports" table because there is no need to maintain a history of generated reports.
- `system_configuration` enforces a single row at the database level via a `singleton_key` column with a `UNIQUE` constraint and a fixed default value (`'SYSTEM'`): a single-row FK/UNIQUE key trick instead of a check constraint, since a check constraint cannot be compared across rows.

### Key decisions and discarded alternatives

| Decision | Discarded alternative | Reason |
|----------|------------|--------|
| Separate `user` (identity) from `user_profile` (business actor) | Use a single `user` table | Almost all FKs in the rest of the system point to `user_profile_id`, reflecting that the SRS distinguishes between log data (RF2.1) and user activity within the application (RF3, RF4, RF9) |
| Add `admin_profile` as a 1:1 extension of `account` and repoint all FKs "created/reviewed by an administrator" to it | Keep `account_id` directly in `zone`, `zone_report`, `user_report`, `system_configuration` and `moderation_action` | A simple `account_id` cannot distinguish an administrator from a normal user at the database level; applying that distinction only in the application code (service layer) leaves the rule unprotected against any code path that forgets to check the function |
| Put `resource_call` in "Help and Resource Zones" instead of "Alert Management" | Put it in Alerts because it has an optional `alert_id` | Its parent entity is `emergency_resource` and `alert_id` is nullable: a helpdesk call can be made (RF7.1) without an active alert |

---

## 6. How to update this map

1. If the team confirms the removal of `zone_report` or `resource_call`, update this map and the corresponding relationships.
2. Validate this map with the team before using it as a basis for the microservices architecture.
3. Sync with the C4 diagram in `05-architecture/overview.md` once it exists.
4. **Pending:** Assign these bounded contexts to the 4 committed microservices (`identity-service`, `alert-service`, `resource-service`, `admin-service`) once the microservices architecture document is written. Please note that "Emergency Contacts", "Evidence" and "Notifications" are separate bounded contexts here, but have been grouped into "alert service" for implementation purposes.