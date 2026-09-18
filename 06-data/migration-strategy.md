# Migration Strategy — Woman Alert

This document defines the strategy for migrating data between schema versions, including tooling, rollback policies, and how to handle migrations with data in production.

**See also:**
- Data models that require migrations → `06-data/models.md`
- Modeling conventions for schema changes → `06-data/modeling-conventions.md`
- DevOps deployment procedures → `10-devops/environments.md`
- Data dictionary for field changes → `06-data/data-dictionary.md`

---

## Migration tool

### Primary tool: Liquibase

- **Tool:** Liquibase 4.x
- **Format:** XML changelogs (YAML is alternative, but XML is project standard)
- **Location:** `src/main/resources/db/changelog/` in each Spring Boot microservice
- **Justification:** Native Spring Boot integration, database-agnostic, supports rollback operations, enterprise-grade features

### Alternative considerations

- **Flyway:** Considered but rejected due to less flexible rollback support
- **Manual SQL scripts:** Rejected due to lack of version control and audit trail

---

## Changelog structure

### Main changelog file

```xml
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
    xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
    http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-4.0.xsd">

    <include file="changelog/001-create-user-table.xml" relativeToChangelogFile="true"/>
    <include file="changelog/002-create-account-table.xml" relativeToChangelogFile="true"/>
    <include file="changelog/003-create-alert-table.xml" relativeToChangelogFile="true"/>
    <!-- ... -->

</databaseChangeLog>
```

### Individual changelog file naming

- **Format:** `NNN-description.xml`
- **NNN:** Sequential number (001, 002, 003, ...)
- **description:** kebab-case description of the change
- **Examples:** `001-create-user-table.xml`, `002-add-email-unique-constraint.xml`

### Changelog file template

```xml
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
    xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
    http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-4.0.xsd">

    <changeSet id="001-create-user-table" author="team">
        <comment>Create user table for storing personal data</comment>
        <createTable tableName="user">
            <column name="id" type="integer" autoIncrement="true">
                <constraints primaryKey="true" nullable="false"/>
            </column>
            <column name="first_name" type="varchar(100)">
                <constraints nullable="false"/>
            </column>
            <!-- ... -->
        </createTable>
        <rollback>
            <dropTable tableName="user"/>
        </rollback>
    </changeSet>

</databaseChangeLog>
```

---

## Migration lifecycle

### Development environment

1. **Create migration:** Write changelog file for schema change
2. **Test locally:** Run `mvn liquibase:update` to apply migration
3. **Test rollback:** Run `mvn liquibase:rollback` to verify rollback works
4. **Commit:** Include changelog file in commit
5. **Review:** Code review must include migration file review

### Staging environment

1. **Apply migration:** Run `mvn liquibase:update` during deployment
2. **Verify:** Check application starts correctly and data is intact
3. **Smoke test:** Run basic smoke tests to confirm functionality
4. **No rollback:** Rollbacks in staging require team approval

### Production environment

1. **Pre-flight check:** Verify staging deployment was successful
2. **Backup:** Create database backup before migration (automated by DevOps)
3. **Apply migration:** Run `mvn liquibase:update` during deployment
4. **Monitor:** Watch for errors during migration and application startup
5. **Post-deployment check:** Run automated smoke tests

---

## Rollback policy

### Development

- **Allowed:** Rollbacks are freely allowed for any change
- **Command:** `mvn liquibase:rollbackCount 1` (rollback last change)
- **Justification:** Fast iteration, safe experimentation

### Staging

- **Allowed:** Rollbacks require team approval
- **Process:** Discuss in team meeting, document reason, then rollback
- **Justification:** Staging is shared environment; rollbacks affect others

### Production

- **Prohibited:** Rollbacks are strictly prohibited
- **Alternative:** If migration fails, create forward-only migration to revert
- **Justification:** Rollbacks can cause data loss and inconsistent state; forward-only migrations are safer

### Example forward-only rollback

```xml
<changeSet id="004-revert-email-unique-constraint" author="team">
    <comment>Revert email unique constraint due to production issue</comment>
    <dropUniqueConstraint tableName="user" constraintName="uk_user_email"/>
    <rollback>
        <addUniqueConstraint tableName="user" constraintName="uk_user_email" columnNames="email"/>
    </rollback>
</changeSet>
```

---

## Data migration strategies

### Strategy 1: Additive changes (safe)

**When to use:** Adding new columns, tables, indexes

**Example:** Adding `profile_photo_url` column to `user_profile` table

```xml
<changeSet id="005-add-profile-photo-url" author="team">
    <addColumn tableName="user_profile">
        <column name="profile_photo_url" type="varchar(500)"/>
    </addColumn>
</changeSet>
```

**Risk:** Low — existing data unaffected

---

### Strategy 2: Destructive changes (risky)

**When to use:** Dropping columns, tables, constraints

**Example:** Dropping `old_column` from `user` table

```xml
<changeSet id="006-drop-old-column" author="team">
    <dropColumn tableName="user" columnName="old_column"/>
</changeSet>
```

**Risk:** High — data loss if not careful

**Mitigation:**
1. Verify column is not used in code
2. Verify column has no data (or data is no longer needed)
3. Test in staging before production
4. Have database backup ready

---

### Strategy 3: Data transformation (complex)

**When to use:** Changing column type, migrating data between tables

**Example:** Changing `telephone` from VARCHAR(20) to VARCHAR(25)

```xml
<changeSet id="007-extend-telephone-length" author="team">
    <modifyDataType tableName="user" columnName="telephone" newDataType="varchar(25)"/>
</changeSet>
```

**Risk:** Medium — data truncation possible if new type is smaller

**Mitigation:**
1. Verify data fits in new type
2. Test with sample data
3. Run in maintenance window if large table

---

### Strategy 4: Default value addition

**When to use:** Adding new NOT NULL column with default value

**Example:** Adding `is_active` column to `zone` table

```xml
<changeSet id="008-add-is-active-to-zone" author="team">
    <addColumn tableName="zone">
        <column name="is_active" type="boolean" defaultValueBoolean="true">
            <constraints nullable="false"/>
        </column>
    </addColumn>
</changeSet>
```

**Risk:** Low — existing rows get default value

---

## Handling large table migrations

### Problem: Large tables (millions of rows) cause locks and downtime

### Solution: Online schema change (OSC) pattern

1. **Create new table:** Create new table with desired schema
2. **Copy data:** Copy data from old table to new table in batches
3. **Sync changes:** Use triggers to keep new table in sync during migration
4. **Switch:** Atomically rename tables (old → old_backup, new → old)
5. **Cleanup:** Drop old table after verification

**Tools:** `pt-online-schema-change` (Percona Toolkit) for PostgreSQL alternatives

**Note:** Woman Alert currently has small datasets; OSC is future consideration for scale.

---

## Migration testing

### Unit tests for migrations

- **Tool:** Liquibase unit test support or custom Spring Boot tests
- **What to test:** Migration applies successfully, rollback works, data integrity maintained

### Integration tests

- **Tool:** Testcontainers with PostgreSQL
- **What to test:** Full migration suite on fresh database, application starts after migration

### Smoke tests after migration

- **Tool:** Automated smoke test suite
- **What to test:** Basic CRUD operations work, no N+1 query issues introduced

---

## Migration checklist

Before committing a migration:

- [ ] Changelog file follows naming convention (`NNN-description.xml`)
- [ ] ChangeSet has descriptive comment
- [ ] Rollback tag is included (except in production where rollback is prohibited)
- [ ] Migration tested locally (apply + rollback)
- [ ] Migration tested in staging
- [ ] Database backup created before production migration
- [ ] Data model documentation updated (`06-data/models.md`)
- [ ] Data dictionary updated if new/modified fields (`06-data/data-dictionary.md`)

---

## Common migration scenarios

### Scenario 1: Adding foreign key constraint

```xml
<changeSet id="009-add-user-profile-fk" author="team">
    <addForeignKeyConstraint
        baseTableName="user_profile"
        baseColumnNames="user_id"
        referencedTableName="user"
        referencedColumnNames="id"
        constraintName="fk_user_profile_user"/>
</changeSet>
```

**Risk:** Medium — may fail if orphaned records exist

**Pre-migration check:** Run query to identify orphaned records

---

### Scenario 2: Changing column from nullable to NOT NULL

```xml
<changeSet id="010-make-email-not-null" author="team">
    <addNotNullConstraint tableName="user" columnName="email" defaultDefaultValue="unknown@example.com"/>
</changeSet>
```

**Risk:** High — fails if NULL values exist

**Pre-migration check:** Update NULL values to default before migration

---

### Scenario 3: Renaming column

```xml
<changeSet id="011-rename-telephone-to-phone" author="team">
    <renameColumn tableName="user" oldColumnName="telephone" newColumnName="phone"/>
</changeSet>
```

**Risk:** Medium — breaks code references

**Post-migration action:** Update all code references to new column name

---

## Correlations

* Data models → `06-data/models.md`
* Modeling conventions → `06-data/modeling-conventions.md`
* Database engine justification → `06-data/models.md` (Services Overview section)
* DevOps deployment procedures → `10-devops/environments.md`
