# Modeling Conventions — Woman Alert

This document defines the naming and style conventions for the project's databases. All data models must follow these conventions unless explicitly overridden by an ADR.

**See also:**
- Data models implementing these conventions → `06-data/models.md`
- Data dictionary for field meanings → `06-data/data-dictionary.md`
- Architecture decisions that override these conventions → `05-architecture/decisions/records/ADR-*.md`

---

## Table naming conventions

### General rules

- **Format:** `snake_case`, singular (not plural)
- **Examples:** `user`, `account`, `alert`, `zone` (NOT `users`, `accounts`, `alerts`, `zones`)
- **Justification:** Singular naming matches ORM conventions (Hibernate, JPA) and reduces cognitive load when thinking about entity relationships

### Composite names

- **Format:** `snake_case` with words separated by underscores
- **Examples:** `emergency_contact`, `device_permission`, `alert_activation_setting`
- **Justification:** Readability and consistency with column naming

---

## Column naming conventions

### General rules

- **Format:** `snake_case`, all lowercase
- **Examples:** `first_name`, `last_name`, `created_at`, `updated_at`
- **Justification:** Consistency with table naming and PostgreSQL best practices

### Primary keys

- **Name:** `id`
- **Type:** `integer` (auto-increment)
- **Justification:** Simple, predictable primary key naming. Using integer rather than UUID for performance in joins and simpler indexing.

**Note:** For distributed systems requiring global uniqueness, UUIDs may be considered (ADR required). Current architecture uses shared PostgreSQL, so integer is sufficient.

### Foreign keys

- **Name:** `[referenced_table]_id`
- **Type:** `integer` (matching referenced table's primary key)
- **Examples:** `user_id`, `account_id`, `alert_id`, `emergency_contact_id`
- **Justification:** Clear relationship indication and self-documenting schema

### Timestamps

- **Created timestamp:** `created_at`
- **Updated timestamp:** `updated_at` (nullable, only if record can be modified)
- **Type:** `timestamp` (no timezone)
- **Default:** `CURRENT_TIMESTAMP` for `created_at`
- **Justification:** Consistent timestamp naming across all tables. Using `timestamp` without timezone to match PostgreSQL default behavior; application layer handles timezone conversion.

### Boolean flags

- **Naming:** `is_[status]` or `has_[feature]` (prefix with `is_` or `has_`)
- **Type:** `boolean`
- **Examples:** `is_active`, `is_enabled`, `is_read`, `is_persistent`
- **Justification:** Clear semantic meaning and avoids ambiguity

### Status columns

- **Naming:** `status` (for state machines)
- **Type:** `varchar(20)` or ENUM
- **Examples:** `status` in `account`, `alert`, `notification`
- **Justification:** Generic `status` column with domain-specific values allows flexible state transitions

---

## Data type conventions

### Common types

| Data type | When to use | Example |
|-----------|-------------|---------|
| `integer` | IDs, foreign keys, counters | `id`, `user_id`, `attempts` |
| `varchar(n)` | Variable-length text | `first_name varchar(100)`, `email varchar(150)` |
| `text` | Long-form text (no fixed length) | `description`, `error_message` |
| `boolean` | True/false flags | `is_active`, `tutorial_completed` |
| `date` | Date without time | `birthdate` |
| `timestamp` | Date and time | `created_at`, `started_at` |
| `decimal(p,s)` | Precise numeric values | `latitude decimal(9,6)`, `accuracy decimal(6,2)` |
| `enum` | Fixed set of values | `status varchar(20)` with documented values |

### Length conventions for VARCHAR

| Field type | Max length | Justification |
|------------|-------------|---------------|
| Names (first, last) | 100 | Accommodates long names across cultures |
| Email | 150 | RFC 5321 specifies 254 max, 150 is practical |
| Phone | 20 | E.164 format with country code |
| URLs | 500 | Cloud storage URLs with signed tokens |
| Short text (status, type) | 20-30 | Enums and short codes |
| Medium text (descriptions) | 255 | Standard short text field |
| Long text (messages) | 500 | Alert messages, error messages |

---

## Constraint conventions

### Primary keys

- **Constraint:** `PRIMARY KEY`
- **Default:** Every table has an `id` column as primary key
- **Exception:** Junction tables for many-to-many relationships use composite primary key

### Unique constraints

- **Naming:** Unique on columns that must be unique per business rule
- **Examples:** `email UNIQUE`, `telephone UNIQUE`, `document_number UNIQUE`
- **Justification:** Data integrity and prevent duplicates

### Foreign key constraints

- **Constraint:** `FOREIGN KEY ([column]) REFERENCES [table](id)`
- **Behavior:** `ON DELETE RESTRICT` by default (prevent orphaned records)
- **Exception:** Use `ON DELETE CASCADE` only for dependent records that have no independent existence

### Composite unique constraints

- **Format:** Unique on combination of columns
- **Examples:** `(device_id, permission_type)`, `(alert_id, emergency_contact_id, channel)`
- **Justification:** Prevent duplicate records in junction tables

### NOT NULL constraints

- **Default:** Business-critical columns are NOT NULL
- **Examples:** `user.first_name NOT NULL`, `alert.status NOT NULL`
- **Exception:** Optional fields (nullable) are explicitly marked

---

## Indexing conventions

### When to create indexes

1. **Foreign key columns:** Every foreign key should have an index for join performance
2. **Unique constraints:** Automatically create index, but document it
3. **Query patterns:** Columns frequently used in WHERE clauses
4. **Composite indexes:** For multi-column WHERE conditions

### Index naming

- **Format:** `idx_[table]_[column]` or `idx_[table]_[composite]`
- **Examples:** `idx_user_email`, `idx_alert_status`, `idx_alert_contact_composite`

### What NOT to index

- Low-cardinality columns (e.g., boolean flags with few distinct values)
- Columns rarely queried
- Tables with very few rows (index overhead > benefit)

---

## Soft delete vs hard delete

### Convention: Hard delete by default

- **Default:** Records are permanently deleted using `DELETE`
- **Justification:** Simpler schema, no orphaned data, compliant with privacy regulations (right to be forgotten)

### When to use soft delete

- **Exception:** Tables requiring audit trail or undo capability may use soft delete
- **Implementation:** Add `deleted_at timestamp NULL` column
- **Example:** Not currently used in Woman Alert; if needed, requires ADR

### Alternative: Status-based deletion

- **Approach:** Use `status` column instead of soft delete
- **Examples:** `account.status = 'inactive'`, `device.status = 'revoked'`
- **Justification:** More explicit state management, common in business domains

---

## Audit trail conventions

### Convention: Separate audit_log table

- **Implementation:** All critical actions are logged in `audit_log` table
- **No `created_by` / `updated_by` columns:** Tracking who did what is handled by `audit_log`
- **Justification:** Centralized audit trail, cleaner schema, avoids row-level audit proliferation

### What is audited

- User account changes (status changes, password resets)
- Alert lifecycle events (activation, resolution, cancellation)
- Administrative actions (zone creation, user modifications)
- Evidence access (who viewed which evidence files)

---

## Normalization level

### Target: Third Normal Form (3NF)

- **Rule:** Every non-key attribute is dependent only on the primary key
- **Example:** `user` table contains personal data; `account` table contains credentials; separate because credentials depend on account, not user

### Intentional denormalizations

| Table | Denormalization | Justification |
|-------|----------------|---------------|
| `alert` | Includes `user_profile_id` and `device_id` | Avoid joins for common alert queries |
| `location_log` | Includes `alert_id` (nullable) | Allows location queries with or without alert context |
| `notification` | Includes `alert_id` (nullable) | Notifications can exist without alerts (system messages) |

---

## Schema versioning

### Tool: Liquibase

- **Implementation:** All schema changes are versioned using Liquibase changelogs
- **Location:** `resources/db/changelog/` in each microservice
- **Naming:** `YYYYMMDDHHMMSS_description.xml`

### Rollback policy

- **Development:** Rollbacks allowed for any change
- **Staging:** Rollbacks require team approval
- **Production:** Rollbacks prohibited; use forward-only migrations (create new migration to revert)

---

## Correlations

* Data models → `06-data/models.md`
* Data dictionary → `06-data/data-dictionary.md`
* Architecture decisions that override these conventions → `05-architecture/decisions/records/ADR-*.md`
* Migration strategy → `06-data/migration-strategy.md` (to be created)
