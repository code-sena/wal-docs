# 00-governance — Team Rules

> This section defines the agreements the team commits to follow throughout the project.
> Every team member must read all documents in this section before making their first commit.

---

## Documents in this section

| File | Purpose | Status |
|------|---------|--------|
| [git-conventions.md](./git-conventions.md) | Branch strategy, commit format, PR policy, merge rules | ✅ Customized for Woman Alert |
| [agile-conventions.md](./agile-conventions.md) | Sprint structure, ceremonies, estimation, backlog tool | ✅ Customized for Woman Alert (2-week sprints, GitHub Projects) |
| [definition-of-done.md](./definition-of-done.md) | Checklist that every completed user story must satisfy | ✅ Ready to use |
| [definition-of-ready.md](./definition-of-ready.md) | Checklist for a user story to enter a sprint | ✅ Ready to use |
| [documentation-rules.md](./documentation-rules.md) | How to write, update, and delete documentation | ✅ Ready to use |
| [microservices-documentation.md](./microservices-documentation.md) | Required documents per microservice | ✅ Ready to use |
| [security-policy.md](./security-policy.md) | How the team handles vulnerabilities and security incidents | ✅ Customized for Woman Alert (RBAC roles defined) |
| [security-rules.md](./security-rules.md) | Code-level security rules: secrets, auth, input validation | ✅ Ready to use |

---

## How governance applies

Governance rules apply to **the entire project** — all sections, all services, all team members.
If a rule conflicts with a local convention, governance wins unless a new ADR overrides it.

> Change a governance rule only through team agreement.
> Document the change and the reason. Announce it before the next sprint.

---

## Woman Alert team specifics

### Team members
- **menticio** — Tech Lead / Backend (Project Leader)
- **JoseSebastianSuarezCumaco** — Mobile App Design / UI Lead
- **JustinDanielaBahamon** — Database Leader

### Project context
Woman Alert is a gender violence prevention system with privacy-critical requirements (evidence, location data). Governance rules emphasize:
- Privacy-first approach for all data handling
- Security review for any feature involving PII or evidence
- Accessibility considerations (stress-mode UI, discreet activation)

### Governance status
- ✅ Sprint structure defined (2-week sprints starting Sep 19, 2026)
- ✅ Ceremony schedule established (daily standups, planning, review, retro)
- ✅ RBAC roles customized for Woman Alert context
- ✅ Git conventions adapted for current workflow (main + feature branches)
