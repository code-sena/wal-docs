# Data Model — Auth Service

> Tables owned by the Auth service. The full column list, checks and indexes are in
> `06-data/models.md`; this document summarizes what Auth is responsible for.

---

## Database engine

PostgreSQL 16, shared with alert-service and evidence-service. Auth owns the `identity` and `admin`
schemas and is the only service that runs Liquibase (v1.0.0 to v1.6.0, changeset 25). The application
database user can read and write data but cannot create or drop tables or modify the audit log.

---

## PostgreSQL schema

### Table: `identity.users`

Registered people: first and last name, email (unique, format checked), phone (unique, format
checked), document, birth date, `role_id`, `terms_accepted_at`. `users.id` is the `sub` of the JWT.

### Table: `identity.role`

Two rows: `user` (id 1) and `administrator` (id 2). There is no separate moderator role.

### Table: `identity.account` / `identity.user_profile` / `identity.admin_profile`

`account` (1 to 1 with `users`) stores the BCrypt hash, status (`active`, `inactive`, `blocked`,
`pending`) and last access. `user_profile` (1 to 1 with `users`) is the id that alerts, contacts and
locations reference. `admin_profile` marks administration accounts.

### Table: `identity.device_session`

One session per device with the hash of its refresh token (15 days), status (`active`, `expired`,
`revoked`), expiration and last use.

### Other `identity` tables

`device` (UUID, push token, brand, model, OS, app version, GPS status, status), `device_permission`,
`alert_activation_setting`, `recovery_request` (code hash, attempts, expiration, requesting IP) and
`user_preference` (language, theme, notifications).

### `admin` tables

`audit_log` (immutable), `user_report`, `moderation_action`, `system_configuration` (single row),
`alert_report` (reports exported to PDF or CSV) and `account_deletion_request` (needs a second
administrator).

---

## Redis schema

Not used. Sessions are stored in `identity.device_session` and the rate limit counters live in memory.

---

## Migrations

- File `db/changelog/db.changelog-master.yaml` with one file per change in `changesets/`.
- Applied automatically when Auth starts, locally (Docker) and in Render.
- An applied changeset is never edited. Details → `06-data/migration-strategy.md`.

---

## Correlations

- Full table structure → `06-data/models.md`
- Field meanings → `06-data/data-dictionary.md`
- Entities and rules → `02-domain/entities-and-rules.md`
