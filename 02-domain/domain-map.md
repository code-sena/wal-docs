# Domain Map: Bounded Contexts

> Domain map built by cross-referencing the database tables with the Functional Requirements of the SRS (RF1–RF13) and the actors defined in section 2.2 (User, Administrator).

> **Updated to match the final 27-table database schema** (adds `admin_profile` and changes administrative audit/review FKs from `account_id` to `admin_profile_id`).

---

## 1. Domain Overview

Alerta Mujer manages the complete emergency lifecycle: from user registration, account and device management, through alert activation that shares real-time location and notifies trusted contacts and emergency resources, to evidence registration, safe/risk zone management, emergency resource directory management, and administrative control (audit, user reports, moderation) through the web administration panel.

---

## 2. Identified Bounded Contexts

### Bounded Context: Identity and Accounts

| Field | Value |
|-------|-------|
| **Name** | Identity and Accounts |
| **Responsibility** | Registration, authentication, account recovery, user profile, administrator profile, devices, sessions, and user preferences |
| **Tables** | `role`, `user`, `account`, `user_profile`, `admin_profile`, `recovery_request`, `device`, `device_permission`, `device_session`, `alert_activation_setting`, `user_preference` |
| **Database** | PostgreSQL |
| **Ubiquitous Language** | User, Account, User Profile, Administrator Profile, Device, Session |

**Context-specific terms (Ubiquitous Language):**

| Term | Meaning in THIS context | Different in another context? |
|------|--------------------------|-------------------------------|
| `user` | Person with name, identification document, and role (user or administrator) | In the rest of the system, most business activity depends on `user_profile`, not directly on `user` |
| `account` | Access credentials and account state for a `user` | This is what is authenticated; it is different from the personal-data profile |
| `user_profile` | `user` extension, exclusive to accounts with `role=user`, used as the FK in most application contexts (alerts, zones, notifications, locations) | This is the actual end-user business actor within the domain |
| `admin_profile` | `account` extension, exclusive to accounts with `role=administrator` | Structural counterpart of `user_profile`: it guarantees by database design that only administrators can appear as authors of administrative actions (official zones, report reviews, moderation actions, system configuration changes, and audit entries) |

---

### Bounded Context: Emergency Contacts

| Field | Value |
|-------|-------|
| **Name** | Emergency Contacts |
| **Responsibility** | Create, find, edit, and delete a user's trusted emergency contacts |
| **Tables** | `emergency_contact` |
| **Database** | PostgreSQL |
| **Ubiquitous Language** | Emergency Contact |

---

### Bounded Context: Alert Management (Core Domain)

| Field | Value |
|-------|-------|
| **Name** | Alert Management |
| **Responsibility** | Activate alerts, coordinate emergency-contact notification, track location, manage mini alerts/reminders, and manage frequent locations |
| **Tables** | `alert`, `alert_contact`, `location_log`, `alert_reminder`, `frequent_location` |
| **Database** | PostgreSQL |
| **Ubiquitous Language** | Alert, activation, delivery channel, recorded location |

---

### Bounded Context: Evidence

| Field | Value |
|-------|-------|
| **Name** | Evidence |
| **Responsibility** | Register photo, video, and audio evidence associated with an alert |
| **Tables** | `evidence` |
| **Database** | PostgreSQL for metadata and `file_url`; cloud storage for the actual media files |
| **Ubiquitous Language** | Evidence |

**Storage rule:** PostgreSQL stores evidence metadata and the `file_url` reference only. Large photo, audio, and video files are stored in a cloud storage service (Cloud Storage). The database does not store the binary media payload.

---

### Bounded Context: Notifications (Supporting)

| Field | Value |
|-------|-------|
| **Name** | Notifications |
| **Responsibility** | Internal user notifications, separate from emergency-contact delivery records managed through `alert_contact` |
| **Tables** | `notification` |
| **Database** | PostgreSQL |
| **Ubiquitous Language** | Notification, priority, persistent |

---

### Bounded Context: Help Areas and Resources

| Field | Value |
|-------|-------|
| **Name** | Help Areas and Resources |
| **Responsibility** | Safe/risk zones, user-submitted zone risk classifications, emergency resource directory, and calls made to those resources |
| **Tables** | `zone`, `zone_report`, `emergency_resource`, `resource_call` |
| **Database** | PostgreSQL |
| **Ubiquitous Language** | Zone, risk level, emergency resource, call |

**`zone_report` flow:** A `zone_report` represents a risk-level classification submitted by a user from the mobile application for a zone. The report is not immediately a public official zone classification. It requires review and approval by an Administrator through the web application, using `reviewed_by_admin_profile_id`. Once approved, the reported classification can be reflected as an official public `zone`.

**`resource_call` flow:** `resource_call.alert_id` is optional (`NULL` allowed). A user can call an emergency resource with or without an active alert. The mobile application does not contain a separate redundant call-history module; call records are displayed as part of the Alert History detail when they are associated with an alert.

---

### Bounded Context: Administration and Moderation

| Field | Value |
|-------|-------|
| **Name** | Administration and Moderation |
| **Responsibility** | Administrative auditing, user reports, moderation actions, and global system configuration |
| **Tables** | `audit_log`, `user_report`, `moderation_action`, `system_configuration` |
| **Database** | PostgreSQL |
| **Ubiquitous Language** | Audit, User Report, Moderation Action, System Configuration, Administrator Profile |

**Administrative authorization rule:** All foreign keys that identify the administrator responsible for an administrative action point to `admin_profile_id`, not directly to `account_id`. This includes official zone creation, zone-report review, user-report review, moderation actions, system configuration updates, and administrative audit authorship. Because `admin_profile` is structurally restricted to administrator accounts, the database relationship itself prevents a regular user account from being referenced as the actor of an administrative action.

---

## 3. Context Map

```text
┌────────────────────────────┐      U→D      ┌──────────────────────────┐
│ Identity and Accounts      │──────────────▶│ Emergency Contacts       │
│ (user, account,            │               │ (emergency_contact)       │
│ user_profile, admin_profile│               └────────────┬─────────────┘
│ device, sessions, prefs)  │                            │ U→D
└──────────────┬─────────────┘                            ▼
               │ U→D                         ┌────────────────────────────┐
               ▼                             │ Alert Management (Core)    │
       alert, alert_contact,                 │ alert, alert_contact,      │
       location_log,                         │ location_log,              │
       alert_reminder,                       │ alert_reminder,            │
       frequent_location                     │ frequent_location           │
                                             └──────┬──────┬──────┬──────┘
                                                    │      │      │
                                                    ▼      ▼      ▼
                                             ┌──────────┐ ┌──────────────┐
                                             │ Evidence │ │ Notifications│
                                             │ evidence │ │ notification │
                                             └──────────┘ └──────────────┘
                                                    │
                                                    ▼
                                             ┌────────────────────────────┐
                                             │ Help Areas and Resources   │
                                             │ zone, zone_report,         │
                                             │ emergency_resource,        │
                                             │ resource_call              │
                                             └────────────┬───────────────┘
                                                          │
                                                          ▼
                                             ┌────────────────────────────┐
                                             │ Administration and         │
                                             │ Moderation                 │
                                             │ audit_log, user_report,    │
                                             │ moderation_action,          │
                                             │ system_configuration        │
                                             │ -- administrative actors   │
                                             │    use admin_profile_id    │
                                             └────────────────────────────┘
```

### Relationship Table

| Context A | Relationship | Context B | Communication Channel | Contract |
|-----------|-------------|-----------|-----------------------|----------|
| Identity and Accounts | U → D (FK `user_id`, `account_id`) | Emergency Contacts | Internal (same database) | `emergency_contact.user_profile_id` |
| Identity and Accounts | U → D (FK `user_profile_id`, `device_id`) | Alert Management | Internal (same database) | `alert.user_profile_id`, `alert.device_id` |
| Emergency Contacts | U → D | Alert Management | Internal | `alert_contact.emergency_contact_id` |
| Alert Management | D → U (event) | Evidence | Internal | `evidence.alert_id` |
| Alert Management | D → U (event) | Notifications | Internal | `notification.alert_id` (optional) |
| Alert Management | D → U | Help Areas and Resources | Internal | `resource_call.alert_id` (optional), `location_log.alert_id` (optional) |
| Identity and Accounts | U → D (FK `admin_profile_id`) | Help Areas and Resources | Internal | `zone.created_by_admin_profile_id`, `zone_report.reviewed_by_admin_profile_id` |
| Identity and Accounts | U → D (FK `admin_profile_id`) | Administration and Moderation | Internal | `user_report.reviewed_by_admin_profile_id`, `moderation_action.admin_profile_id`, `system_configuration.updated_by_admin_profile_id`, `audit_log.admin_profile_id` |

---

## 4. Primary, Supporting, and Generic Domains

| Bounded Context | Type | Justification |
|-----------------|------|---------------|
| Alert Management | Core | This is the main purpose of the system: activate and propagate an emergency |
| Emergency Contacts | Supporting | Required so that an alert has trusted recipients to notify |
| Help Areas and Resources | Supporting | Complements prevention and emergency assistance |
| Evidence | Supporting | Strengthens the evidence associated with an emergency |
| Administration and Moderation | Supporting | Required to operate and govern the platform |
| Identity and Accounts | Generic | Registration, authentication, devices, and sessions are common capabilities already solved in the industry |
| Notifications | Generic | Standard internal notification capability |

---

## 5. Domain Modeling Decisions

### Device ownership

`device` belongs to Identity and Accounts because it is associated with an account and contains device-specific permissions and activation settings.

This includes:

- Contact permissions
- Call permissions
- Location permissions
- Notification permissions
- Microphone permissions
- Camera permissions
- Emergency activation methods

---

### Alert activation settings

`alert_activation_setting` is conceptually related to Alert Management because it determines how an alert can be activated.

However, it remains in Identity and Accounts because:

- It belongs to a specific device.
- It represents user/device configuration.
- It does not represent an individual alert lifecycle.
- Moving it would unnecessarily fragment the domain.

The service layer must verify that disabling one activation method does not leave the user without any enabled emergency activation method.

---

### Frequent locations

`frequent_location` could theoretically become a separate bounded context such as "User Locations".

For the current academic scope, it remains within Alert Management to avoid excessive fragmentation.

A separate context could be introduced in the future if the functionality grows to include:

- Location sharing
- Location categories
- Advanced location history
- Location-based automation
- Geofencing

---

### Administrator profile

`admin_profile` is the structural counterpart of `user_profile`.

Administrative actions must reference `admin_profile.id` instead of directly referencing `account.id`.

This applies to:

- Zone creation
- Zone report review
- User report review
- Moderation actions
- System configuration updates

The purpose is to enforce the administrator boundary at the database relationship level.

This means that administrative foreign keys use `admin_profile_id`, ensuring that the entity referenced as the actor of an administrative operation belongs to the administrator profile structure.

---

### Zone reports

`zone_report` represents a risk-level classification submitted by a user from the mobile application.

This is a definitive business flow and is not an experimental or optional feature.

The workflow is:

```text
User
 │
 │ classifies a zone from the mobile application
 ▼
zone_report
 │
 │ status = pending
 ▼
Administrator reviews the report from the web application
 │
 ├── rejected
 │
 └── approved
        │
        ▼
Official public zone classification
        │
        ▼
zone
```

The user does not directly create or modify an official public `zone` through the mobile application.

Instead:

1. The user submits a classification from the mobile application.
2. The system creates a `zone_report`.
3. The report remains pending until reviewed.
4. An Administrator reviews the report through the Angular web application.
5. The review is recorded through `reviewed_by_admin_profile_id`.
6. When approved, the reported classification becomes an official public `zone`.

This workflow separates user-generated classifications from official public zone information.

The administrative review is therefore a mandatory business rule for promoting a user-submitted classification into official public zone data.

---

### Resource calls

`resource_call` belongs to Zones and Emergency Resources because its main aggregate is the emergency resource.

The `alert_id` field is **optional and nullable**.

This is intentional because a user can contact an emergency resource directly from the emergency-resource directory without activating a panic alert.

Two valid scenarios therefore exist:

```text
Scenario A — Call without an active alert

User
 │
 │ opens emergency resource directory
 ▼
Emergency Resource
 │
 │ calls resource
 ▼
resource_call
 │
 └── alert_id = NULL
```

```text
Scenario B — Call during an active alert

User
 │
 │ has an active alert
 ▼
Emergency Resource
 │
 │ calls resource
 ▼
resource_call
 │
 └── alert_id = active alert
```

Therefore, `resource_call.alert_id` must remain nullable.

The mobile application does not contain a separate independent "Resource Call History" module.

When a resource call is associated with an alert, its historical information is displayed inside the corresponding **Alert History detail**.

This avoids creating a redundant mobile module while still preserving the call record.

---

### Evidence storage

Evidence files are separated from relational metadata.

PostgreSQL stores only the evidence metadata and the URL used to access the corresponding file in external storage.

The `evidence` table contains information such as:

- `id`
- `alert_id`
- `media_type`
- `file_url`
- `file_hash`
- timestamps

The actual heavy multimedia files are stored in a dedicated **Cloud Storage** service.

The storage model is:

```text
Mobile Application
       │
       │ photo / audio / video
       ▼
   Cloud Storage
       │
       │ file_url
       ▼
    PostgreSQL
       │
       └── evidence metadata
```

Therefore:

```text
PostgreSQL
    └── metadata + file_url

Cloud Storage
    └── actual photos
    └── actual audio files
    └── actual video files
```

This prevents PostgreSQL from being used as a binary multimedia repository and keeps the relational database focused on transactional data and evidence metadata.

---

### Administrative reports

Administrative reports are generated through queries over existing domain tables.

There is no separate report table because the system does not need to persist every generated report as a domain entity.

---

### System configuration

`system_configuration` is designed as a singleton configuration record.

A unique `singleton_key` ensures that only one configuration record exists.

Example configuration values include:

- `sos_max_duration_minutes`
- `emergency_phone_number`
- `default_notification_priority`
- `default_theme`

---

### Key Decisions

| Decision | Alternative | Reason |
|---|---|---|
| Separate `user` from `user_profile` | Store all application activity directly in `user` | Most business-domain relationships use `user_profile_id` |
| Add `admin_profile` | Use `account_id` directly for administrative actions | `account_id` alone cannot structurally distinguish administrators from normal users |
| Use `admin_profile_id` for administrative FKs | Validate administrator identity only in application code | Database relationships should reinforce the authorization boundary |
| Keep `resource_call` in Zones and Emergency Resources | Move it to Alert Management because it has `alert_id` | `alert_id` is nullable and the main entity is `emergency_resource` |
| Keep `zone_report` as a user-submitted classification requiring administrative approval | Allow mobile users to directly modify public `zone` data | Official public zone information requires administrative review |
| Store evidence metadata in PostgreSQL | Store multimedia binaries directly in PostgreSQL | Large files are stored more appropriately in Cloud Storage |
| Keep `frequent_location` in Alert Management | Create another bounded context | Avoid unnecessary fragmentation for the current scope |
| Display resource call history inside Alert History | Create a separate mobile call-history module | Avoid redundant mobile functionality |
| Use one PostgreSQL database | Database per Service | The current academic architecture intentionally uses a shared database with logical ownership |
| Use four implementation services | Deploy one microservice for every bounded context | Seven logical contexts are grouped into four Spring Boot services |

## 6. Physical Microservices Mapping

To avoid over-design and preserve the Domain-Driven Design boundaries, the seven logical Bounded Contexts are deployed across four physical Spring Boot microservices.

The logical bounded contexts remain independent domain concepts even when multiple contexts are deployed inside the same physical service.

| Logical Bounded Context (DDD) | Target Microservice (Spring Boot) | Deployment Justification |
|---|---|---|
| Identity and Accounts | `identity-service` | Independently manages authentication, JWT tokens, user profiles, devices and global preferences. |
| Alert Management | `alert-service` | Central emergency engine. It is grouped with the emergency-critical contexts to provide ultra-low-latency processing when an alert is triggered. |
| Emergency Contacts | `alert-service` | Emergency contacts are required directly during alert propagation. Keeping them in the same process avoids unnecessary inter-service network communication during an emergency. |
| Evidence | `alert-service` | Evidence registration is directly associated with the emergency lifecycle. Keeping it within the same physical service allows evidence metadata to be associated with an active alert without unnecessary service-to-service overhead. Actual multimedia files remain in Cloud Storage. |
| Notifications | `alert-service` | Notification coordination is part of the emergency propagation flow. Keeping it in the same physical service allows the alert engine to coordinate notification creation and delivery with minimal internal network overhead. |
| Zones and Emergency Resources | `resource-service` | Manages read-intensive information such as the emergency directory, safe/risk zone map, zone reports and resource call records. |
| Administration and Moderation | `admin-service` | Handles heavy audit, moderation, user-report and global-configuration queries for the Angular Web Administration Panel, avoiding unnecessary performance impact on the mobile API. |

### Deployment Grouping

The seven logical bounded contexts are grouped into four physical services as follows:

```text
7 Logical Bounded Contexts
             │
             ▼
     4 Spring Boot Services
             │
 ┌───────────┼───────────────┬────────────────┐
 │           │               │                │
 ▼           ▼               ▼                ▼
identity-   alert-         resource-       admin-
service     service        service         service
 │           │               │                │
 │           │               │                │
 │           ├── Alert       ├── Zones        ├── Administration
 │           │   Management  │   and          │   and
 │           │               │   Emergency    │   Moderation
 │           ├── Emergency   │   Resources    │
 │           │   Contacts    │               │
 │           │               │               │
 │           ├── Evidence    │               │
 │           │               │               │
 │           └── Notifications│              │
 │                                           │
 └── Identity and Accounts                   │
                                             │
```

### `identity-service`

`identity-service` owns the Identity and Accounts bounded context.

Its responsibilities include:

- User registration
- Authentication
- JWT token management
- Account status
- User profiles
- Administrator profiles
- Device registration
- Device permissions
- Device sessions
- Emergency activation settings
- User preferences
- Account recovery

The service provides the identity information required by the other services.

---

### `alert-service`

`alert-service` is the **Central Emergency Engine** of the platform.

It physically groups four logical bounded contexts:

```text
Alert Management
        +
Emergency Contacts
        +
Evidence
        +
Notifications
        =
alert-service
```

This grouping is intentional.

When an emergency is triggered, the system must execute several operations immediately:

```text
Emergency Activation
        │
        ▼
Create Alert
        │
        ├──────────────► Resolve Emergency Contacts
        │
        ├──────────────► Create Notification Records
        │
        ├──────────────► Register Evidence
        │
        └──────────────► Record Real-Time Location
```

Keeping these emergency-critical contexts inside the same Spring Boot process minimizes unnecessary inter-service network communication during the most time-sensitive workflow.

The objective is **ultra-low-latency emergency processing**, especially for:

- Alert activation
- Emergency contact resolution
- Alert recipient creation
- Notification coordination
- Evidence registration
- Location tracking
- Alert lifecycle management

The grouping is a physical deployment decision and does not eliminate the logical bounded-context boundaries defined in the domain model.

---

### `resource-service`

`resource-service` owns the Zones and Emergency Resources bounded context.

Its responsibilities include:

- Safe zone management
- Risk zone management
- User-submitted zone reports
- Administrative zone report review
- Official public zone classification
- Emergency resource directory
- Resource calls
- Resource-call history

This service is separated from the emergency engine because its information is primarily used for directory, map and resource-oriented operations rather than the critical alert activation path.

The `resource-service` handles both:

```text
zone_report
```

and:

```text
resource_call
```

including calls where:

```text
resource_call.alert_id = NULL
```

and calls associated with an active alert.

---

### `admin-service`

`admin-service` owns Administration and Moderation.

Its responsibilities include:

- Audit logs
- User reports
- Moderation actions
- Global system configuration
- Administrative review of zone reports
- Administrative operations through the Angular Web Panel

The service is physically separated to prevent heavy administrative queries from unnecessarily affecting the performance of mobile-oriented operations.

Examples of administrative workloads include:

- Filtering large audit logs
- Reviewing user reports
- Moderating users
- Reviewing zone reports
- Generating administrative statistics
- Reading system configuration

---

### Shared PostgreSQL Database

The four physical microservices use the same PostgreSQL database in the current academic architecture.

The database is logically organized according to bounded-context ownership.

```text
                         PostgreSQL
                              │
          ┌───────────────────┼───────────────────┐
          │                   │                   │
          ▼                   ▼                   ▼
 identity-service        alert-service       resource-service
          │                   │                   │
          │                   │                   │
 Identity tables       Alert tables        Zone/Resource tables
 Account tables        Contact tables      Resource call tables
 Device tables         Evidence tables      Zone report tables
 Profile tables        Notification tables
                              │
                              │
                              ▼
                         admin-service
                              │
                       Administration
                          tables
```

The shared database does not mean that every microservice should freely access every table.

Each service maintains logical ownership of its assigned tables.

Cross-context communication should preferably use:

- REST service contracts
- Domain events
- Internal application events
- Controlled database relationships where required by the shared-database architecture

The current architecture intentionally avoids Database-per-Service because the project uses a shared PostgreSQL database as an academic and deployment simplification.

---

### Official Microservice Ownership Matrix

| Microservice | Logical Bounded Contexts | Main Responsibility |
|---|---|---|
| `identity-service` | Identity and Accounts | Authentication, users, accounts, profiles, administrators, devices, sessions and preferences |
| `alert-service` | Alert Management, Emergency Contacts, Evidence, Notifications | Central emergency processing, contact coordination, evidence metadata and notification coordination |
| `resource-service` | Zones and Emergency Resources | Zones, zone reports, emergency resources and resource calls |
| `admin-service` | Administration and Moderation | Auditing, moderation, user reports and system configuration |

This mapping is the **official physical deployment decision** for the current Alerta Mujer architecture.

The seven bounded contexts remain valid at the DDD level, while the four microservices define the physical Spring Boot deployment boundaries.