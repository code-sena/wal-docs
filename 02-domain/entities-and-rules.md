#Entities, Values and Business Rules

Catalog of entities, attributes and business rules for Alerta Mujer, organized by limited context according to the SRS Functional Requirements (RF1–RF13) and use cases 1–4. Updated to match the final 27-table database schema and Domain Events specification.

## Bounded context: identity and accounts

### Entity: Role

**Belongs to:** Identity and Accounts

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK, auto increment | Yes | — |
| name | varchar(30) | user \| administrator | Yes | Unique |
| description | varchar(255) | Role Description | No | — |

### Entity: User (added root)

**Belongs to:** Identity and Accounts

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| role_id | int | FK → role.id | Yes | — |
| first_name | varchar(100) | Name | Yes | — |
| last_name | varchar(100) | Surname | Yes | — |
| telephone | varchar(20) | Telephone number | No | Unique |
| email | varchar(150) | Email | Yes | Unique |
| document_number | varchar(20) | Identity document number | No | Unique |
| document_type | varchar(20) | Type of identity document | No | — |
| birth_date | date | Date of birth | No | — |
| created_at | timestamp | Creation date | Yes | Default CURRENT_TIMESTAMP |

**Business rules (invariants):**email is unique throughout the system. telephone and document_number, if provided, are unique. role_id determines whether the user is a user (normal user) or an administrator.

### Entity: Account

**Belongs to:** Identity and Accounts

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| user_id | int | FK → user.id | Yes | Unique (1:1 with user) |
| password_hash | varchar(255) | Password hash | Yes | Never plain text |
| status | varchar(20) | active \| blocked \| inactive | Yes | Default active |
| last_login_at | timestamp | Last access | No | — |

**Business rules (invariants):**Each user has one and only one account (1:1 relationship). An account with status = 'blocked' denies any authentication and token issuance attempts at the identity service layer.

### Entity: User Profile (UserProfile)

**Belongs to:** Identity and Accounts (business actor for interaction with the system)

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| user_id | int | FK → user.id | Yes | Unique (1:1 with user) |
| profile_picture_url | varchar(500) | Profile photo | No | — |
| tutorial_completed | boolean | If the tutorial (RF3.1) is completed | Yes | Default false |
| tutorial_completed_at | timestamp | End date/time | No | — |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |
| updated_at | timestamp | Last update | No | — |

**Business rules (invariants):**Only one row is created in user_profile for a user whose role_id corresponds to the user role. It is validated in the identity service.

### Entity: Administrator Profile (AdminProfile)

**Belongs to:** Identity and Accounts (exclusive actor for administrative actions)

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| account_id | int | FK → account.id | Yes | Unique (1:1 with account) |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |

**Business rules (invariants):**A row is only created in admin_profile if the linked account belongs to a user with role_id of administrator. All system FKs that audit or log authorship of an administrator point exclusively to admin_profile.id: zone.created_by_admin_profile_id, zone_report.reviewed_by_admin_profile_id, user_report.reviewed_by_admin_profile_id, moderation_action.admin_profile_id, and system_configuration.updated_by_admin_profile_id.

### Entity: RecoveryRequest

**Belongs to:** Identity and Accounts

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| account_id | int | FK → account.id | Yes | — |
| method | varchar(20) | email \| telephone | Yes | — |
| token_hash | varchar(255) | Recovery Token Hash | Yes | Unique |
| expires_at | timestamp | Token expiration | Yes | — |
| used_at | timestamp | When was it used | No | — |
| status | varchar(20) | pending \| verified \| used \| expired \| canceled | Yes | Default pending |
| attempts | int | Attempts made | Yes | Default 0 |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |
| ip_address | varchar(45) | Source IP (IPv4/IPv6) | No | — |

**Lifecycle / States:** PENDING ──(token verified)──▶ VERIFIED ──(password changed)──▶ USED
   │
   ├──(expires_at alcanzado)──▶ EXPIRED
   └──(cancelled)─────────────▶ CANCELLED


**Business rules (invariants):**A token can only be consumed once (used_at records the date of consumption). Any token consumed after expires_at immediately transitions to expired.

### Entity: Device

**Belongs to:** Identity and Accounts

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| account_id | int | FK → account.id | Yes | — |
| device_uuid | varchar(255) | Unique device identifier | Yes | Unique |
| brand | varchar(50) | Brand | No | RF13.6 |
| model | varchar(100) | Model | No | RF13.6 |
| os_name | varchar(30) | Operating system (Android, iOS) | Yes | — |
| os_version | varchar(30) | Operating system version | No | — |
| app_version | varchar(30) | Woman Alert application version | No | — |
| gps_status | varchar(20) | active \| inactive \| unknown | Yes | Default unknown (RF13.6) |
| status | varchar(20) | active \| blocked \| revoked \| inactive | Yes | Default active |
| last_access_at | timestamp | Last access | No | — |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |

### Entity: Device Permission (DevicePermission)

**Belongs to:** Identity and Accounts

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| device_id | int | FK → device.id | Yes | — |
| permission_type | varchar(30) | contacts \| calls \| location \| notifications \| microphone \| camera | Yes | RF1.1–RF1.6 |
| status | varchar(20) | granted \| denied \| restricted \| not_requested | Yes | — |
| updated_at | timestamp | Last update | Yes | Default CURRENT_TIMESTAMP |

**Business rules (invariants):**There is at most one record per combination device_id + permission_type (unique index).

### Entity: Device Session (DeviceSession)

**Belongs to:** Identity and Accounts

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| account_id | int | FK → account.id | Yes | — |
| device_id | int | FK → device.id | Yes | — |
| refresh_token_hash | varchar(255) | Refresh Token JWT Hash | Yes | Unique |
| status | varchar(20) | active \| revoked \| expired | Yes | Default active |
| expires_at | timestamp | Session expiration | Yes | — |
| last_used_at | timestamp | Last use | No | — |
| created_at| timestamp | Revocation | No | — |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |

### Entity: Alert Activation Setting (AlertActivationSetting)

**Belongs to:** Identity & Accounts (Device Settings)

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| device_id | int | FK → device.id | Yes | — |
| activation_method | varchar(30) | widget \| sudden_movement \| physical_button \| discreet_confirmation | Yes | RF5.1–RF5.4 |
| is_enabled | boolean | If the method is actived by the user | Yes | Default true |
| updated_at | timestamp | Last update | Yes | Default CURRENT_TIMESTAMP |

**Business rules (invariants):**There is a maximum of one record per device_id + activation_method pair.

**Operational Fallback Rule:** When attempting to disable a method (is_enabled = false), the service validates that at least one other activation method remains active (true) for the same device_id.

### Entity: UserPreference (UserPreference)

**Belongs to:** Identity and Accounts

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| user_profile_id | int | FK → user_profile.id | Yes | Unique (1:1) |
| language | varchar(10) | Preferred language (RF10.1) | Yes | Default 'is' |
| theme | varchar(20) | light \| dark \| system (RF11.1) | Yes | Default 'light' |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |
| updated_at | timestamp | Last update | No | — |

## Limited context: emergency contacts

### Entity: Emergency Contact (EmergencyContact)

**Belongs to:** Emergency Contacts

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| user_profile_id | int | FK → user_profile.id | Yes | — |
| contact_name | varchar(100) | Contact name | Yes | — |
| telephone | varchar(20) | Telephone number | Yes | Mandatory by RF2.2 (NOT NULL) |
| email | varchar(150) | Email | No | — |
| relationship | varchar(30) | Relationship with the user | No | — |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |
| updated_at | timestamp | Last update | No | — |

**Business rules (invariants):**Every contact belongs exclusively to a single user_profile (RF2.2). telephone is strictly mandatory to enable dispatch of alerts via SMS or call.

## Bounded context: alert management (main domain)

### Entity: Alert (Alert - added root)

**Belongs to:** Alert Management

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| user_profile_id | int | FK → user_profile.id | Yes | — |
| device_id | int | FK → device.id | Yes | Ensures traceability of physical origin (RF13.6) |
| alert_type | varchar(20) | main \| mini \| reminder | Yes | Default 'main' |
| activation_method | varchar(30) | panic_button \| widget \| sudden_movement \| physical_button \| discreet_confirmation \| automatic | Yes | RF5 |
| status | varchar(20) | active \| resolved \| canceled \| completed \| failed | Yes | Default 'active' |
| message | varchar(500) | Optional message or note | No | — |
| started_at | timestamp | Start of alert | Yes | Default CURRENT_TIMESTAMP |
| ended_at | timestamp | Technical completion | No | — |
| canceled_at | timestamp | Cancellation by the user | No | — |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |

**Life cycle / States:**ACTIVE ──(manual deactivation by the user, UC3/UC4)───────▶ CANCELLED ACTIVE ──(attended / closed with success)─────────────────▶ RESOLVED / COMPLETED ACTIVE ──(propagation failure or system)────────────────▶ FAILED

**Business rules (invariants):**INV-001: When an alert is registered in the active state, the system consults the contacts in emergency_contact and generates the dispatch queue in alert_contact (RF4.2). INV-002: While alert.status = 'active', the client continuously outputs coordinates to location_log tied to the alert_id (RF4.3). INV-003: The transition to the canceled state is executed by the user herself when entering her PIN or cancellation confirmation in the app (UC3/UC4).

### Entity: Alert Contact (AlertContact)

**Belongs to:** Alert Management

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| alert_id | int | FK → alert.id | Yes | — |
| emergency_contact_id | int | FK → emergency_contact.id | Yes | — |
| channels | varchar(20) | sms \| email \| call \| push | Yes | — |
| destination | varchar(150) | Destination (phone, email or Push token) | Yes | — |
| status | varchar(20) | pending \| sent \| delivered \| failed \| retry | Yes | Default 'pending' |
| sent_at | timestamp | Shipment date | No | — |
| delivered_at | timestamp | Confirmed delivery date | No | — |
| attempts | int | Cumulative retry attempts | Yes | Default 0 |
| error_message | varchar(500) | Error log if fails | No | — |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |

**Business rules (invariants):**There is at most one record per combination alert_id + emergency_contact_id + channel (unique index).

### Entity: Location Log (LocationLog)

**Belongs to:** Alert Management

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| user_profile_id | int | FK → user_profile.id | Yes | — |
| alert_id | int | FK → alert.id | No | Optional (enables passive or pre-activation tracking, RF3.2) |
| latitude | decimal(9,6) | GPS Latitude | Yes | Millimeter precision (~11 cm) |
| longitude | decimal(9,6) | GPS Longitude | Yes | Millimeter precision (~11 cm) |
| accuracy | decimal(6,2) | Margin of error in meters | No | Complies with RNF010 |
| recorded_at | timestamp | Capture date/time on device | Yes | Default CURRENT_TIMESTAMP |

**Business rules (invariants):**The use of DECIMAL(9,6) is required to avoid inaccuracies of binary float representation in emergency coordinates.

### Entity: Alert Reminder (AlertReminder)

**Belongs to:** Alert Management

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| alert_id | int | FK → alert.id | Yes | — |
| reminder_type | varchar(20) | mini_alert \| reminder (RF4.5) | Yes | — |
| sequence_number | int | Reminder sequence number | Yes | — |
| scheduled_at | timestamp | Scheduled dispatch date/time | Yes | — |
| sent_at | timestamp | Effective sending date/time | No | — |
| status | varchar(20) | scheduled \| sent \| failed \| canceled | Yes | Default 'scheduled' |
| error_message | varchar(500) | Error log | No | — |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |

### Entity: FrequentLocation

**Belongs to:** Alert Management

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| user_profile_id | int | FK → user_profile.id | Yes | — |
| name | varchar(100) | Label (e.g. "Home", "Work") | Yes | RF3.2 |
| address | varchar(255) | Physical address | No | — |
| city ​​| varchar(100) | City | No | — |
| latitude | decimal(9,6) | Latitude | Yes | — |
| longitude | decimal(9,6) | Longitude | Yes | — |
| notes | varchar(255) | Descriptive notes | No | — |
| is_active | boolean | Logical Delete | Yes | Default true |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |
| updated_at | timestamp | Last update | No | — |

## Bounded context: evidence

### Entity: Evidence

**Belongs to:** Evidence

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| alert_id | int | FK → alert.id | Yes | Strictly linked to an alert |
| media_type | varchar(20) | photo \| video \| audio | Yes | RF6.1, RF6.2 |
| file_url | varchar(255) | File URI in cloud storage | Yes | Multimedia files are never stored in the relational database |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |

## Bounded context: notifications

### Entity: Notification

**Belongs to:** Notifications

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| user_profile_id | int | FK → user_profile.id | Yes | — |
| alert_id | int | FK → alert.id | No | Optional (e.g. system notifications) |
| type | varchar(30) | reminder \| warning \| update \| emergency | Yes | — |
| priority | varchar(20) | normal \| high \| critical | Yes | Default 'normal' (RF8.1) |
| title | varchar(100) | Notification Title | Yes | — |
| message | varchar(500) | Message body | Yes | — |
| is_read | boolean | Reading status | Yes | Default false |
| is_persistent | boolean | Persistence on active screen | Yes | Default false (RF8.2) |
| expires_at | timestamp | Expiration | No | — |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |

## Limited context: help areas and resources

### Entity: Zone (Zone)

**Belongs to:** Help Areas and Resources

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| created_by_admin_profile_id | int | FK → admin_profile.id | No | Administrator profiles only |
| name | varchar(150) | Zone name | Yes | RF9.2 |
| zone_type | varchar(20) | safe \| risks | Yes | — |
| risk_level | varchar(20) | low \| medium \| high \| critical | Yes | Default 'medium' |
| description | varchar(500) | Description | No | — |
| address | varchar(255) | Physical address | No | — |
| city ​​| varchar(100) | City | Yes | — |
| latitude | decimal(9,6) | Centroid latitude | Yes | — |
| longitude | decimal(9,6) | Centroid Longitude | Yes | — |
| radius_meters | decimal(8,2) | Geographic coverage radio | Yes | — |
| is_active | boolean | Publication status | Yes | Default true |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |
| updated_at | timestamp | Last update | No | — |

### Entity: Zone Report (ZoneReport)

**Belongs to:** Help Areas and Resources

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| zone_id | int | FK → zone.id | Yes | — |
| user_profile_id | int | FK → user_profile.id | Yes | — |
| classification | varchar(20) | safe \| dangerous \| normal | Yes | — |
| comment | varchar(500) | Comment or feedback | No | — |
| status | varchar(20) | pending \| approved \| rejected | Yes | Default 'pending' |
| reviewed_by_admin_profile_id | int | FK → admin_profile.id | No | Reviewer Administrator |
| reviewed_at | timestamp | Review date | No | — |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |

**Business rules (invariants):**Allows users to provide feedback on the level of perceived risk in a given zone. To avoid accumulation of duplicate open records, if a user forwards an assessment about the same zone, the service layer updates the existing record if it is in pending state instead of duplicating the row. A report in approved state by an administrator directly modifies the risk_level of the linked Zone entity.

### Entity: EmergencyResource

**Belongs to:** Help Areas and Resources

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| name | varchar(150) | Name of the entity / institution | Yes | RF7.1 |
| resource_type | varchar(40) | emergency_line \| hospital \| health_center \| police_station \| police_post \| shelter \| support_organization | Yes | SRS Section 2.4 |
| telephone | varchar(30) | Main phone | No | — |
| secondary_telephone | varchar(30) | Alternative phone | No | — |
| email | varchar(150) | Contact email | No | — |
| address | varchar(255) | Physical address | No | — |
| city ​​| varchar(100) | City | Yes | — |
| latitude | decimal(9,6) | Latitude | No | RF9.3 |
| longitude | decimal(9,6) | Longitude | No | RF9.3 |
| description | varchar(500) | Description of services provided | No | — |
| is_active | boolean | Active/inactive status | Yes | Default true |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |
| updated_at | timestamp | Last update | No | — |

### Entity: Resource Call (ResourceCall)

**Belongs to:** Help Areas and Resources

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| emergency_resource_id | int | FK → emergency_resource.id | Yes | — |
| user_profile_id | int | FK → user_profile.id | Yes | — |
| alert_id | int | FK → alert.id | No | Present if the call occurs within the flow of an active alert |
| device_id | int | FK → device.id | No | — |
| dialed_telephone | varchar(30) | Dialed phone number | Yes | Allows you to audit whether the primary or secondary telephone was used |
| status | varchar(20) | started \| answered \| unanswered \| failed \| canceled | Yes | Default 'started' |
| started_at | timestamp | Dialing Home | Yes | Default CURRENT_TIMESTAMP |
| ended_at | timestamp | End of dialing | No | — |
| duration_seconds | int | Total duration in seconds | No | — |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |

**Business rules (invariants):**Maintains traceability and support metrics in support calls and hotlines actived from the application or in moments of panic.

## Bounded context: administration and moderation

### Entity: Audit Log (AuditLog)

**Belongs to:** Administration and Moderation

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| admin_profile_id | int | FK → admin_profile.id | No | Present if the action was executed by an administrator |
| account_id | int | FK → account.id | No | Present if the action was executed by a user |
| action | varchar(100) | Action executed | Yes | RF13.9 |
| entity_type | varchar(50) | Name of the affected entity | Yes | — |
| entity_id | int | ID of the affected entity | No | — |
| description | varchar(500) | Detail of the change | No | — |
| ip_address | varchar(45) | Source IP (IPv4/IPv6) | No | — |
| created_at | timestamp | Registration date | Yes | Default CURRENT_TIMESTAMP |

### Entity: User Report (UserReport)

**Belongs to:** Administration and Moderation

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| reported_user_id | int | FK → user.id (reported user) | Yes | — |
| reporter_user_id | int | FK → user.id (who reports) | No | Optional (supports anonymous or system reports) |
| reason | varchar(100) | Reason for the complaint | Yes | RF13.10 |
| description | varchar(1000) | Descriptive detail | Yes | — |
| status | varchar(20) | pending \| under_review \| resolved \| rejected \| closed | Yes | Default 'pending' |
| reviewed_by_admin_profile_id | int | FK → admin_profile.id | No | Administrator in charge |
| reviewed_at | timestamp | Review date | No | — |
| resolution_notes | varchar(1000) | Resolution remarks | No | — |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |
| updated_at | timestamp | Last update | No | — |

### Entity: Moderation Action (ModerationAction)

**Belongs to:** Administration and Moderation

**Identifier:** id

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | — |
| user_report_id | int | FK → user_report.id | Yes | — |
| admin_profile_id | int | FK → admin_profile.id | Yes | Intervening administrator |
| action_type | varchar(30) | review \| warn \| block \| unblock \| reject \| close \| escalate | Yes | RF13.10 |
| notes | varchar(1000) | Internal notes | No | — |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |

**Business rules (invariants):**When action_type takes the values ​​block or unblock, it emits an event consumed by Identity and Accounts that updates the account.status attribute to 'blocked' or 'active', respectively.

### Entity: System Configuration (SystemConfiguration)

**Belongs to:** Administration and Moderation

**Identifier:** id (Single row type Singleton)

| Attribute | Type | Description | Required | Rules |
|---|---|---|---|---|
| id | int | PK | Yes | Default 1 |
| singleton_key | varchar(10) | Single row constraint key | Yes | Fixed value 'SYSTEM', UNIQUE index |
| sos_max_duration_minutes | int | Maximum Continuous SOS Time | Yes | — |
| emergency_phone_number | varchar(20) | Default phone number | Yes | — |
| admin_email | varchar(150) | General administration email | Yes | — |
| notifications_enabled | boolean | Global Notification Switch | Yes | Default true |
| default_notification_priority | varchar(20) | normal \| high \| critical | Yes | Default 'normal' |
| default_theme | varchar(20) | light \| dark | Yes | Default 'light' |
| updated_by_admin_profile_id | int | FK → admin_profile.id | No | Last administrator who configured the system |
| created_at | timestamp | Creation | Yes | Default CURRENT_TIMESTAMP |
| updated_at | timestamp | Last update | No | — |

**Business rules (invariants):**The unique constraint on the column singleton_key = 'SYSTEM' ensures that only a single row can exist in this table at the database level.

## Summary table of tactical building blocks

| Entity/Table | Bounded context | Added root? / Type |
|---|---|---|
| Role | Identity and Accounts | Catalog entity |
| User | Identity and Accounts | Added root |
| Account | Identity and Accounts | Entity (1:1 with User) |
| UserProfile | Identity and Accounts | Entity (1:1 with User, role user) |
| AdminProfile | Identity and Accounts | Entity (1:1 with Account, administrator role) |
| RecoveryRequest | Identity and Accounts | Dependent entity of Account |
| Device | Identity and Accounts | Added root |
| DevicePermission | Identity and Accounts | Dependent entity of Device |
| DeviceSession | Identity and Accounts | Dependent entity of Device and Account |
| AlertActivationSetting | Identity and Accounts | Dependent entity of Device |
| UserPreference | Identity and Accounts | Entity (1:1 with UserProfile) |
| EmergencyContact | Emergency Contacts | Added root |
| Alert | Alert Management | Added root |
| AlertContact | Alert Management | Alert dependent entity |
| LocationLog | Alert Management | Geographic registration entity |
| AlertReminder | Alert Management | Alert dependent entity |
| FrequentLocation | Alert Management | Added root |
| Evidence | Evidence | Added root (associated with Alert) |
| Notification | Notifications | Added root |
| Zone | Help Areas and Resources | Added root |
| ZoneReport | Help Areas and Resources | Zone and UserProfile dependent entity |
| EmergencyResource | Help Areas and Resources | Added root |
| ResourceCall | Help Areas and Resources | Call traceability entity |
| AuditLog | Administration and Moderation | Independent audit entity |
| UserReport | Administration and Moderation | Added root (Moderation Flow) |
| ModerationAction | Administration and Moderation | UserReport dependent entity |
| SystemConfiguration | Administration and Moderation | Aggregate Root (Singleton Pattern) |

**Total: 27 tables / entities.**

## Mapping to database code and object model

| Mastery Artifact | Table in database | Implementation Notes |
|---|---|---|
| Root User | user | Manages personal identity data. |
| Root Alert | alert | Uses alert_type, activation_method and status enumerations. device_id attribute is NOT NULL. |
| Entity AlertContact | alert_contact | Models dispatch retries (attempts, status, error_message). |
| AdminProfile Entity | admin_profile | Linked 1:1 to account. Ensures administrative authorship of actions in the system. |
| EmergencyResource/ResourceCall Entities | emergency_resource / resource_call | They allow you to store and audit the use of the quick assistance directory. |
| SystemConfiguration entity | system_configuration | Guaranteed single row using the singleton_key UNIQUE constraint. |