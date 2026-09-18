# Normalization Assessment — Woman Alert

This document analyzes the normalization level of the database schema and justifies any intentional denormalizations.

---

## Target normalization level

### Goal: Third Normal Form (3NF)

**Definition:**
- **1NF:** All attributes contain atomic values, no repeating groups
- **2NF:** All non-key attributes are fully dependent on the entire primary key (no partial dependencies)
- **3NF:** All non-key attributes are dependent only on the primary key (no transitive dependencies)

**Justification:** 3NF provides good balance between data integrity and query performance. Higher normal forms (BCNF, 4NF) introduce complexity with diminishing returns for this application's scale.

---

## Normalization analysis by module

### Module: Identity and Accounts

#### Table: `user`

**Normalization level:** 3NF ✅

**Analysis:**
- Primary key: `id`
- Non-key attributes: `role_id`, `first_name`, `last_name`, `telephone`, `email`, `document_number`, `document_type`, `birthdate`, `created_at`
- Dependencies: All non-key attributes depend directly on `id` (the user's unique identifier)
- No transitive dependencies: Attributes like `first_name` depend on `id`, not on another non-key attribute

**Justification:** Correctly normalized. User personal data belongs to the user entity.

---

#### Table: `account`

**Normalization level:** 3NF ✅

**Analysis:**
- Primary key: `id`
- Foreign key: `user_id` (unique, 1:1 relationship)
- Non-key attributes: `password_hash`, `status`, `last_access`
- Dependencies: All attributes depend on `id` (the account's unique identifier)
- Separation from `user` table is correct: Credentials are account-specific, not user-specific

**Justification:** Correctly normalized. Separation of user identity (personal data) from account credentials (access data) follows security best practices.

---

#### Table: `user_profile`

**Normalization level:** 3NF ✅

**Analysis:**
- Primary key: `id`
- Foreign key: `user_id` (unique, 1:1 relationship)
- Non-key attributes: `profile_photo_url`, `tutorial_completed`, `tutorial_seen_at`, `created_at`, `updated_at`
- Dependencies: All attributes depend on `id`
- Separation from `user` table is correct: Profile data is behavioral state, not identity

**Justification:** Correctly normalized. Behavioral state (tutorial completion) is separate from core identity.

---

#### Table: `device`

**Normalization level:** 3NF ✅

**Analysis:**
- Primary key: `id`
- Foreign key: `account_id`
- Non-key attributes: `device_uuid`, `brand`, `model`, `os_name`, `os_version`, `app_version`, `gps_status`, `status`, `last_access`, `created_at`
- Dependencies: All attributes depend on `id` (the device's unique identifier)
- No transitive dependencies: Device attributes like `brand` depend on the device, not on the account

**Justification:** Correctly normalized. Device metadata is device-specific, not account-specific.

---

### Module: Emergency Contacts

#### Table: `emergency_contact`

**Normalization level:** 3NF ✅

**Analysis:**
- Primary key: `id`
- Foreign key: `user_profile_id`
- Non-key attributes: `contact_name`, `telephone`, `email`, `relationship`, `created_at`, `updated_at`
- Dependencies: All attributes depend on `id` (the contact's unique identifier)
- No transitive dependencies

**Justification:** Correctly normalized. Each emergency contact is a distinct entity.

---

### Module: Alert Management

#### Table: `alert`

**Normalization level:** 3NF ✅

**Analysis:**
- Primary key: `id`
- Foreign keys: `user_profile_id`, `device_id`
- Non-key attributes: `alert_type`, `activation_method`, `status`, `message`, `started_at`, `ended_at`, `cancelled_at`, `created_at`
- Dependencies: All attributes depend on `id` (the alert's unique identifier)
- Includes both `user_profile_id` and `device_id` as foreign keys (intentional denormalization, see below)

**Justification:** Correctly normalized despite intentional denormalization (see below).

---

#### Table: `alert_contact`

**Normalization level:** 3NF ✅

**Analysis:**
- Primary key: `id`
- Foreign keys: `alert_id`, `emergency_contact_id`
- Non-key attributes: `channel`, `destination`, `status`, `sent_at`, `delivered_at`, `attempts`, `error_message`, `created_at`
- Dependencies: All attributes depend on `id`
- Composite unique constraint: `(alert_id, emergency_contact_id, channel)` prevents duplicate notification attempts

**Justification:** Correctly normalized. Junction table for many-to-many relationship between alerts and contacts, with channel as additional dimension.

---

#### Table: `location_log`

**Normalization level:** 3NF ✅

**Analysis:**
- Primary key: `id`
- Foreign keys: `user_profile_id`, `alert_id` (nullable)
- Non-key attributes: `latitude`, `longitude`, `accuracy`, `recorded_at`
- Dependencies: All attributes depend on `id`
- `alert_id` is nullable to allow location logging without an active alert

**Justification:** Correctly normalized. Location can exist independently of alerts (e.g., frequent location logging).

---

### Module: Evidence

#### Table: `evidence`

**Normalization level:** 3NF ✅

**Analysis:**
- Primary key: `id`
- Foreign key: `alert_id`
- Non-key attributes: `media_type`, `file_url`, `created_at`
- Dependencies: All attributes depend on `id`
- No transitive dependencies

**Justification:** Correctly normalized. Evidence belongs to an alert but is a distinct entity.

---

### Module: Zones and Support Resources

#### Table: `zone`

**Normalization level:** 3NF ✅

**Analysis:**
- Primary key: `id`
- Foreign key: `created_by_admin_profile_id` (nullable)
- Non-key attributes: `name`, `zone_type`, `risk_level`, `description`, `address`, `city`, `latitude`, `longitude`, `radius_meters`, `is_active`, `created_at`, `updated_at`
- Dependencies: All attributes depend on `id`
- `created_by_admin_profile_id` is nullable (zones can be system-created)

**Justification:** Correctly normalized. Zone metadata is zone-specific.

---

#### Table: `zone_report`

**Normalization level:** 3NF ✅

**Analysis:**
- Primary key: `id`
- Foreign keys: `zone_id`, `user_profile_id`, `reviewed_by_admin_profile_id` (nullable)
- Non-key attributes: `classification`, `comment`, `status`, `reviewed_at`, `created_at`
- Dependencies: All attributes depend on `id`
- Composite unique constraint: `(zone_id, user_profile_id)` prevents duplicate reports

**Justification:** Correctly normalized. Junction table for many-to-many relationship between zones and users.

---

## Intentional denormalizations

### Denormalization 1: `alert` table includes both `user_profile_id` and `device_id`

**Denormalized design:**
```sql
alert (
    id,
    user_profile_id,  -- FK to user_profile
    device_id,         -- FK to device
    -- other columns
)
```

**Normalized alternative would require:**
- Joining `alert` → `device` → `account` → `user_profile` to get user from alert
- Or storing only `device_id` and always joining through device chain

**Justification for denormalization:**
- **Performance:** Most alert queries need both user and device information
- **Query complexity:** Avoids 3-table joins for common alert operations
- **Use case frequency:** Alert-related queries are the most frequent in the system

**Trade-off:**
- **Pros:** Faster queries, simpler SQL
- **Cons:** Requires application logic to ensure consistency (user must own the device)
- **Mitigation:** Application validation ensures `device.account_id` matches `user_profile.user_id.account_id`

---

### Denormalization 2: `location_log` includes optional `alert_id`

**Denormalized design:**
```sql
location_log (
    id,
    user_profile_id,
    alert_id,  -- nullable FK to alert
    -- other columns
)
```

**Normalized alternative would require:**
- Separate tables for `alert_location_log` and `general_location_log`
- Or always requiring an alert context for location logging

**Justification for denormalization:**
- **Flexibility:** Allows location logging both during alerts and for frequent location tracking
- **Query simplicity:** Single table for all location queries
- **Use case:** Frequent location logging (home, work) happens without alerts

**Trade-off:**
- **Pros:** Flexible data model, single table for all location data
- **Cons:** Nullable foreign key requires application logic to handle both cases
- **Mitigation:** Application layer distinguishes between alert-related and general location logging

---

### Denormalization 3: `notification` includes optional `alert_id`

**Denormalized design:**
```sql
notification (
    id,
    user_profile_id,
    alert_id,  -- nullable FK to alert
    -- other columns
)
```

**Normalized alternative would require:**
- Separate tables for `alert_notification` and `system_notification`

**Justification for denormalization:**
- **Flexibility:** Notifications can be alert-related or system-wide (e.g., maintenance warnings)
- **Query simplicity:** Single table for all notification queries
- **Use case:** System notifications exist independently of alerts

**Trade-off:**
- **Pros:** Flexible data model, single table for all notifications
- **Cons:** Nullable foreign key requires application logic to handle both cases
- **Mitigation:** Application layer distinguishes between alert-related and system notifications

---

## Normalization violations analysis

### No violations found

All tables in the Woman Alert schema comply with 3NF requirements. The intentional denormalizations listed above are justified by performance and flexibility requirements, not by normalization errors.

---

## Future normalization considerations

### Potential 4NF candidate: `device_permission` table

**Current design:**
```sql
device_permission (
    id,
    device_id,
    permission_type,  -- ENUM: contacts, calls, location, notifications, microphone, camera
    status,
    updated_at
)
```

**Analysis:** This table represents a multivalued dependency (device → multiple permissions). However, since permissions are a fixed set defined by the system (not user-defined), 3NF is sufficient.

**Justification for current design:**
- Fixed permission set (6 types) → no explosion of combinations
- Query pattern is usually "get all permissions for a device" → current design is optimal
- 4NF would require separate tables per permission type → over-engineering

**Conclusion:** Keep current 3NF design.

---

## Normalization vs. performance summary

| Table | Normalization level | Performance impact | Justification |
|-------|-------------------|-------------------|---------------|
| `user` | 3NF | Neutral | Standard user entity |
| `account` | 3NF | Neutral | Security separation |
| `user_profile` | 3NF | Neutral | Behavioral state separation |
| `device` | 3NF | Neutral | Device metadata |
| `emergency_contact` | 3NF | Neutral | Standard junction table |
| `alert` | 3NF with denormalization | Positive (faster queries) | Includes both user and device FKs |
| `alert_contact` | 3NF | Neutral | Junction table with channel dimension |
| `location_log` | 3NF with denormalization | Positive (flexible queries) | Nullable alert_id for flexibility |
| `evidence` | 3NF | Neutral | Standard child table |
| `zone` | 3NF | Neutral | Zone metadata |
| `zone_report` | 3NF | Neutral | Junction table |
| `notification` | 3NF with denormalization | Positive (flexible queries) | Nullable alert_id for system notifications |

---

## Correlations

* Data models → `06-data/models.md`
* Modeling conventions → `06-data/modeling-conventions.md`
* Data dictionary → `06-data/data-dictionary.md`
