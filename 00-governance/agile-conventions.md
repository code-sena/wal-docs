# Agile Team Conventions

> Defines how the team works through its development cycles. Agree on and sign off
> with the entire team before the first sprint. Update when the team decides to change something.

---

## Sprint structure

| Field | Value |
|-------|-------|
| Duration | 2 weeks |
| Sprint start | Monday |
| Sprint end | Friday of week 2 |
| Current sprint | Sprint 1 — September 19, 2026 to October 2, 2026 |
| Estimated capacity | 20 story points per sprint (4 team members × 5 SP average) |

---

## Ceremonies

### Sprint Planning
- **When:** First day of the sprint — 9:00 AM
- **Duration:** Maximum 2 hours (1h per week of sprint)
- **Who:** Entire team (menticio, JoseSebastianSuarezCumaco, JustinDanielaBahamon)
- **Goal:** Select and commit to sprint user stories, break down into technical tasks
- **Output artifact:** Sprint Backlog updated in GitHub Projects

### Daily Stand-up
- **When:** Every day — 9:30 AM
- **Duration:** Maximum 15 minutes
- **Format:**
  1. What did I do yesterday?
  2. What will I do today?
  3. Is anything blocking me?
- **Rule:** Technical discussions happen after the daily, not during it

### Sprint Review
- **When:** Last day of the sprint — 3:00 PM
- **Duration:** Maximum 30 minutes
- **Who:** Team + Product Owner (menticio as PO proxy)
- **Goal:** Show what was built and collect feedback

### Sprint Retrospective
- **When:** Last day of the sprint — after the review (3:30 PM)
- **Duration:** Maximum 45 minutes
- **Format:** What went well / What to improve / Action commitments
- **Rule:** Each retro produces at least 1 improvement action with an owner and due date

### Backlog Refinement
- **When:** Wednesday of the second week — 2:00 PM
- **Duration:** Maximum 1 hour
- **Goal:** Detail and estimate user stories for the next sprint
- **Exit criterion:** The user story meets the Definition of Ready

---

## Estimation

### Scale
| Points | Meaning |
|--------|---------|
| 1 | Trivial — done in hours |
| 2 | Small — done in one day |
| 3 | Medium — takes 2–3 days |
| 5 | Large — takes almost a full sprint |
| 8 | Very large — should be split |
| 13 | Epic — MUST be split before the sprint |

**Technique:** Planning Poker
**Tool:** GitHub Projects (with estimation field)

### Estimation rule
- If there is disagreement of 2+ levels (e.g., someone says 3 and another says 8), discuss before voting again.
- If a story is estimated at 8 or 13, it must be split into smaller sub-tasks.

---

## Backlog tool

**Tool:** GitHub Projects
**Board URL:** To be created in GitHub organization

### Board columns
| Column | Meaning |
|--------|---------|
| Backlog | Pending refinement |
| Ready | Ready to enter the sprint (meets DoR) |
| In Progress | Someone is actively working on it |
| In Review | In Pull Request / code review |
| Done | Meets DoD and is closed |

---

## Team velocity

| Sprint | Story points completed | Notes |
|--------|----------------------|-------|
| Sprint 1 | TBD | First sprint (Sep 19 – Oct 2) |
| Sprint 2 | TBD | Second sprint (Oct 3 – Oct 16) |
| Sprint 3 | TBD | Third sprint (Oct 17 – Oct 30) |
| Average | TBD | Will be calculated after Sprint 3 |

---

## Related documents

- Definition of Ready → `00-governance/definition-of-ready.md`
- Definition of Done → `00-governance/definition-of-done.md`
- Risk management → `15-project-control/risks.md`
- Technical debt backlog → `15-project-control/tech-backlog.md`
