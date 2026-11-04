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

- **Tool:** Liquibase, through the Spring Boot integration
- **Format:** YAML master changelog (`db/changelog/db.changelog-master.yaml`) that includes one file per change in `changesets/`
- **Location:** `auth-service` only. Auth is the only service that applies migrations, when it starts; Alert and Evidence never run Liquibase
- **Contexts:** test data is loaded only in the `dev` and `test` contexts; production gets roles and base configuration (v1.4.0)
- **Current version:** v1.0.0 to v1.6.0, changeset 25 (28/10/2026): 35 tables in five schemas
- **Justification:** native Spring Boot integration, history of applied changes in the database, contexts to separate test data from production

### Alternative considerations

- **Flyway:** not chosen; the team already had the Liquibase changelogs from the start of the project
- **Manual SQL scripts:** rejected for migrations. The structure-only script `alerta_mujer_schema.sql` is generated from a database with all migrations applied and is used only as documentation

---

## Changelog structure

### Main changelog file

```yaml
# auth-service/src/main/resources/db/changelog/db.changelog-master.yaml
databaseChangeLog:
  - include: { file: changesets/000-create-schemas.yaml }        # identity, alert, resource, admin
  - include: { file: changesets/v1.0.0-tables.yaml }              # tables + dev/test data
  - include: { file: changesets/v1.1.0-constraints-audit.yaml }   # sequences, indexes, CHECK, updated_at, audit, app user
  - include: { file: changesets/v1.2.0-saved-route.yaml }         # saved_route and its limits
  - include: { file: changesets/v1.3.0-evidence-file.yaml }       # name, type, size, SHA-256
  - include: { file: changesets/v1.4.0-production-base.yaml }     # roles and base configuration
  # later changesets: contact notices, tracking (schema notification), chat,
  # account deletion, sessions, retention, trips and automatic protection
  - include: { file: changesets/025-fix-auto-protection.yaml }
```

The file names above summarize the content of each version; the exact names are in the backend repository.

### Individual changelog file naming

- **Rule:** one file per change, added at the end of the master
- **Never** edit a changeset that was already applied in any environment; create a new one
- **Author:** the developer who writes the change

### Changelog file template

```yaml
databaseChangeLog:
  - changeSet:
      id: 026-add-example-column
      author: team
      comment: Add example column to alert.safe_trip
      changes:
        - addColumn:
            schemaName: alert
            tableName: safe_trip
            columns:
              - column:
                  name: example_column
                  type: varchar(50)
      rollback:
        - dropColumn:
            schemaName: alert
            tableName: safe_trip
            columnName: example_column
```

---

## Migration lifecycle

### Development environment

1. **Create migration:** add a new changeset file and include it at the end of the master
2. **Apply locally:** `docker compose up --build` — Auth applies the pending changesets when it starts
3. **Check:** the three services start and `mvn clean test` passes in `microservicios/`
4. **Commit:** include the changeset in the same pull request as the code that needs it
5. **Review:** the pull request to `develop` must include the migration review

### Staging environment

There is no separate staging database. The integration branches (`develop` and `qa`) run the automated tests in GitHub Actions; the migrations are applied for real when they reach production.

### Production environment

1. **Merge to `main`:** Render rebuilds the services
2. **Apply migration:** Auth applies the pending changesets on startup against Render PostgreSQL 16
3. **Monitor:** watch the Auth logs in Render during startup
4. **Post-deployment check:** `/actuator/health` of the three services and a quick test of the affected feature
5. **Example:** on 27/10/2026 changeset 25 corrected in production an error that prevented closing automatic protection and creating its alerts

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

- [ ] New changeset file added at the end of the master (never edit an applied one)
- [ ] ChangeSet has descriptive comment
- [ ] Rollback tag is included (except in production where rollback is prohibited)
- [ ] Migration tested locally (apply + rollback)
- [ ] Migration applied locally with Docker Compose and tests passing in CI
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
