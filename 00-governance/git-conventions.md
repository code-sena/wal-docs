# Git Conventions

> **Read this document before making your first commit on the project.**

## Branch strategy

```
main          ← Production (Render and Vercel publish from it). Merge from qa only.
  └── qa      ← Promotion before production. Merge from develop.
        └── develop   ← Integration. Every pull request targets this branch.
              └── feature/[description]   ← One branch per feature
              └── fix/[description]       ← One branch per bugfix
              └── refactor/[description]  ← Refactoring without behavior change
              └── docs/[description]      ← Documentation (wal-docs)
```

**Woman Alert team note:** since 28/10/2026 the three code repositories keep only `develop`, `qa` and `main`. Changes enter by pull request to `develop`, are promoted to `qa` and then to `main`. GitHub Actions runs the tests on every push and pull request, and a pull request is merged only with the pipeline in green. In `wal-docs` the work branches follow the `docs/[section]` pattern (for example `docs/architecture`, `docs/data`).

**Rules:**
- Nobody commits directly to `main`, `qa` or `develop`
- Every task = one branch + one Pull Request
- One branch = one task (do not mix different features)
- Branches are deleted after merge

---

## Branch naming format

```
[type]/[description-in-kebab-case]

Examples:
feature/auto-protection-shorter-times
fix/alert-contact-method
refactor/map-screen-viewmodel
docs/final-delivery
```

---

## Commit format (Conventional Commits)

```
[type]([scope]): [lowercase description, imperative mood, no trailing period]

[optional body — explain WHY, not what]

[optional footer — issue/user story references]
```

**Types:**
| Type | When to use |
|------|-------------|
| `feat` | New functionality |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, whitespace (no logic change) |
| `refactor` | Code refactoring without behavior change |
| `test` | Add or modify tests |
| `chore` | Tooling, dependencies, CI |
| `perf` | Performance improvement |

**Examples:**
```
feat(alertas): agregar reintento al crear la alerta

fix(alerts): register alerts started from a contact as `contact`

docs(api): update auth-service OpenAPI contract

test(trip): cover the heartbeat of the safe trip
```

---

## Pull Request policy

- **Size:** maximum 400 lines of code (excluding tests). If larger, split it.
- **Reviewers:** minimum 1 approval before merging
- **Review time:** reviewer has a maximum of 24 business hours
- **Template:** use the template at `.github/pull_request_template.md`
- **Green CI:** merge only proceeds if all pipeline checks pass

---

## Merge policy

- Use **Squash and Merge** for features (keeps `dev` history clean)
- Use **Merge Commit** for releases to `main` (preserves full history)
- **Do not** use Rebase & Merge (creates confusion in shared history)

---

## Tags and versioning

Follow [SemVer](https://semver.org/): `MAJOR.MINOR.PATCH`

```bash
# When releasing to production
git tag -a v1.2.0 -m "Release v1.2.0: add reports module"
git push origin v1.2.0
```
