# Risk Register

> Unmitigated risks become problems. This register allows the team to
> anticipate, not just react.
> Review and update at each retrospective or when there is a significant project change.

---

## Probability × impact matrix

```
IMPACT
  │
  │ HIGH  │  [Mitigate]    │  [Avoid]       │
  │       │  Low           │  High          │
  │       │  Probability   │  Probability   │
  │───────────────────────────────────────
  │ MEDIUM│  [Accept]      │  [Mitigate]    │
  │       │  with monitoring│              │
  │───────────────────────────────────────
  │ LOW   │  [Accept]      │  [Accept]      │
  │       │                │               │
  └───────────────────────────────────────
              LOW              HIGH
                      PROBABILITY
```

**Response strategies:**
- **Avoid:** Change the plan so the risk cannot materialize
- **Mitigate:** Reduce the probability or impact
- **Transfer:** Pass the risk to another party (insurance, provider, contract)
- **Accept:** Acknowledge the risk and have a contingency plan

---

## Active risk register

### R-001 — Service availability and cold start

| Field | Value |
|-------|-------|
| **ID** | R-001 |
| **Category** | Technical / External |
| **Description** | A sleeping service on the free Render plan can take up to about 90 seconds to answer, scheduled tasks stop while it sleeps, and the free database has an expiration date. An alert could be delayed at the worst moment. |
| **Probability** | Medium |
| **Impact** | High |
| **Risk level** | High |
| **Strategy** | Mitigate |
| **Mitigation plan** | UptimeRobot calls the three `/actuator/health` every 5 minutes; RPC timeout of 90 s; the app shows the status and allows retrying; wake the services before each presentation; evaluate a plan without sleep. |
| **Contingency plan** | Move the database before it expires; redeploy from `main`; tell users to call 123 or 155 directly. |
| **Trigger** | Health endpoint slow or down; database expiration notice from Render. |
| **Owner** | Backend team |
| **Review date** | 2026-11-04 |
| **Status** | Active |

---

### R-002 — Notice delivery not verified end to end

| Field | Value |
|-------|-------|
| **ID** | R-002 |
| **Category** | Technical |
| **Description** | Notices depend on Resend, Firebase Cloud Messaging and the contact's connectivity. The test between two phones on 23/10/2026 is not archived and PM-04 to PM-09 are pending. |
| **Probability** | Medium |
| **Impact** | High |
| **Risk level** | High |
| **Strategy** | Mitigate |
| **Mitigation plan** | Run and record the manual tests with the release APK; record each notice per contact and channel; resend once after 5 minutes; never present the feature as guaranteed. |
| **Contingency plan** | Show the failed status to the user and keep the assistance lines visible. |
| **Trigger** | `failed` records in `alert_contact`; complaints from test contacts. |
| **Owner** | Whole team |
| **Review date** | 2026-11-04 |
| **Status** | Active |

---

### R-003 — Privacy and security gaps

| Field | Value |
|-------|-------|
| **ID** | R-003 |
| **Category** | Security / Legal |
| **Description** | Location, contacts and evidence are personal data under Law 1581 of 2012. Open findings: daily location without a switch, web token in `localStorage`, no own encryption of evidence, 64 `console` calls in the mobile app, admin password in a commit message. Privacy policy, data treatment policy and terms are not written. |
| **Probability** | Medium |
| **Impact** | High |
| **Risk level** | High |
| **Strategy** | Mitigate |
| **Mitigation plan** | Close the open findings of the security matrix; write the policies and terms; keep the minimal admin view; rotate any exposed secret. |
| **Contingency plan** | Revoke sessions (rotate `JWT_SECRET`), remove the exposed data and inform the affected users. |
| **Trigger** | A secret found in the history; a privacy question from the instructor or a user. |
| **Owner** | Backend and mobile teams |
| **Review date** | 2026-11-04 |
| **Status** | Active |

---

### R-004 — Background location and native features

| Field | Value |
|-------|-------|
| **ID** | R-004 |
| **Category** | Technical |
| **Description** | Permissions, battery saving and manufacturer restrictions can stop the location, the shake detection, the trip or automatic protection with the screen off. |
| **Probability** | High |
| **Impact** | Medium |
| **Risk level** | High |
| **Strategy** | Mitigate |
| **Mitigation plan** | Kotlin foreground services; permissions and protection section; ask for background location only when enabling automatic protection; test on physical phones with the release APK. |
| **Contingency plan** | Document the real behavior per manufacturer in the user manual. |
| **Trigger** | Gaps in the location log of an alert; reports from testers. |
| **Owner** | Mobile team |
| **Review date** | 2026-11-04 |
| **Status** | Active |

---

### R-005 — Web and app parity

| Field | Value |
|-------|-------|
| **ID** | R-005 |
| **Category** | Product |
| **Description** | The web does not offer trips and does not let the user edit her personal data. |
| **Probability** | High |
| **Impact** | Low |
| **Risk level** | Medium |
| **Strategy** | Accept |
| **Mitigation plan** | Document in the manuals which features exist on each platform. |
| **Contingency plan** | Point users to the app for those features. |
| **Trigger** | Questions in the user manual review. |
| **Owner** | Web team |
| **Review date** | 2026-11-04 |
| **Status** | Active |

---

### R-006 — Time and academic scope

| Field | Value |
|-------|-------|
| **ID** | R-006 |
| **Category** | Team |
| **Description** | Three members and the academic deadline limit implementation and test coverage. |
| **Probability** | High |
| **Impact** | Medium |
| **Risk level** | High |
| **Strategy** | Mitigate |
| **Mitigation plan** | Feature freeze since 24/10/2026; prioritize the alert path; record pending items instead of hiding them. |
| **Contingency plan** | Deliver with the pending items declared in the documents. |
| **Trigger** | Missed checkpoints in the delivery plan. |
| **Owner** | Project leader |
| **Review date** | 2026-11-04 |
| **Status** | Active |

---

### R-007 — Communication barriers in the territory

| Field | Value |
|-------|-------|
| **ID** | R-007 |
| **Category** | External |
| **Description** | Lack of coverage, fear of asking for help and not knowing the support routes can prevent the timely use of the app; there are no measurements for Huila. |
| **Probability** | Medium |
| **Impact** | High |
| **Risk level** | High |
| **Strategy** | Mitigate |
| **Mitigation plan** | Tell users which features need Internet; keep the 123, purple line and mental health line visible; validate the design with users from Huila. |
| **Contingency plan** | The app never replaces the official lines; the manual explains it. |
| **Trigger** | Feedback from user validation. |
| **Owner** | Whole team |
| **Review date** | 2026-11-04 |
| **Status** | Active |

---

### R-008 — Safety of the user if the app is discovered

| Field | Value |
|-------|-------|
| **ID** | R-008 |
| **Category** | Product / Security |
| **Description** | Someone watching the phone could discover the app or a notice. |
| **Probability** | Medium |
| **Impact** | High |
| **Risk level** | High |
| **Strategy** | Mitigate |
| **Mitigation plan** | Discreet mode, duress PIN, cancel with volume-down ×4, recording without preview; recommend privacy practices in the user manual; test with users. |
| **Contingency plan** | Duress PIN sends a silent notice to the contacts. |
| **Trigger** | User feedback; contacts reporting unexpected notices. |
| **Owner** | Mobile team |
| **Review date** | 2026-11-04 |
| **Status** | Active |

---

## Common technical risks in microservices

These risks apply to almost all microservices projects. Evaluate which ones apply:

| Risk | Typical probability | Standard mitigation |
|------|--------------------|--------------------|
| Failure cascade (one service down brings everything down) | Medium | Circuit Breaker, timeout, fallback |
| Data inconsistency between services | High | Saga, Outbox, idempotent events |
| Network latency in synchronous calls | High | gRPC, cache, async where possible |
| Lost messages in the broker | Medium | At-least-once + idempotent consumers |
| Schema evolution breaks consumers | High | Version events, compatible changes first |
| Technical debt accumulation | High | DoD with minimum coverage, regular reviews |
| Premature over-engineering | Medium | Start simple, YAGNI, measure before optimizing |
| Sensitive data exposure in logs | Medium | Logging policy, PII masking, SAST |
| Configuration drift between environments | High | Infrastructure as Code, environment variables |

---

## Closed risks / Lessons learned

| ID | Risk | Outcome | Lesson |
|----|------|---------|--------|
| R-C01 | Contacts not notified when an alert is created (CAL-01) | Occurred; corrected with email (Resend) and push with tracking link | The core path must have an end-to-end test from the start |
| R-C02 | Map broken in the APK of 14/10/2026 because of Google Maps authorization (CAL-22) | Occurred; migrated to MapLibre with OpenFreeMap | Avoid services that depend on billing for a training project |
| R-C03 | Secrets in a branch and in the history (CAL-17) | Occurred; branch deleted, old Firebase key invalid, JWT secret rotated | Never commit `.env`; a key that was ever committed must be changed |
| R-C04 | No continuous integration and the deployed branch not merged into `qa` or `main` (CAL-24) | Corrected on 28/10/2026 with GitHub Actions and publication from `main` | Agree on the branch flow before the first deployment |
| R-C05 | Duplicated admin routes under `/dashboard` (CAL-18) | Corrected; admin routes only under `/admin` | Keep the access decision on the server; guards only guide |

---

## Template for adding a new risk

```markdown
### R-00X — [Name]

| Field | Value |
|-------|-------|
| **ID** | R-00X |
| **Category** | |
| **Description** | |
| **Probability** | |
| **Impact** | |
| **Risk level** | |
| **Strategy** | |
| **Mitigation plan** | |
| **Contingency plan** | |
| **Trigger** | |
| **Owner** | |
| **Review date** | |
| **Status** | Active |
```

---

## Correlations

- External dependencies → `15-project-control/dependencies.md`
- Related open questions → `15-project-control/open-questions.md`
- Architectural decisions that mitigated risks → `05-architecture/decisions/`
