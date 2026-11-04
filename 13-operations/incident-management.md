# Incident Management

> An incident is any unplanned event that interrupts or degrades service.
> Effective incident response is trained before incidents occur, not during.
> Follow this playbook in order — in an incident, stress blocks free thinking.

---

## Severity classification

| Level | Name | Definition | Example | Response time |
|-------|------|------------|---------|---------------|
| **P0** | Critical | The alert path does not work: alerts cannot be created or contacts are not notified. | Alert service down, database unreachable or expired, email and push failing at the same time, `JWT_SECRET` mismatch | **Immediate** (<5 min) |
| **P1** | High | Core functionality degraded for many users. | Login or Google login not working, tracking link not opening, evidence uploads failing | **15 minutes** |
| **P2** | Medium | Secondary functionality degraded. Workaround available. | PDF/CSV export failing, admin dashboard slow, map tiles not loading | **1 hour** |
| **P3** | Low | Minor nuisance. No impact on the alert path. | Wrong label or translation, a wrong text in the tutorial | **Next delivery** |

During an incident the users are reminded that the app does not replace the official lines 123 and 155.

---

## Roles during an incident

| Role | Responsibility | When activated |
|------|----------------|----------------|
| **Incident Commander (IC)** | Coordinates response, makes decisions and communicates — the project leader | P0 and P1 always |
| **Tech Lead** | In-depth technical diagnosis of the affected service (backend, web or mobile) | P0 and P1 always |
| **On-Call Engineer** | First responder: checks the health endpoints and the Render logs | All incidents |
| **Comms Lead** | Informs the instructor and the test users | P0 and P1 |

---

## Response playbook

### Step 1: DETECT (0-5 min)

```
How do you know there is an incident?
  → UptimeRobot reports a health endpoint down
  → User or tester report
  → Admin panel: Configuration > System status
  → GitHub Actions failing on main

Immediate action:
  1. Check the three /actuator/health endpoints
  2. Write in the team group: "Investigating [brief description] — [your name]"
  3. Open the runbook for the affected service: `09-microservices/services/[service]/runbook.md`
```

### Step 2: CLASSIFY (5-10 min)

```
Determine severity:
  → How many users are affected?
  → What features are down?
  → Is there a workaround available?

If P0 or P1:
  → Activate the Incident Commander
  → Create the incident record (GitHub issue): INC-XXX
  → Open a call with the team
```

### Step 3: COMMUNICATE (10-15 min)

Initial update in the team channel and to stakeholders:

```
Initial communication template:
─────────────────────────────────
🔴 INCIDENT P[N] — [INC-XXX]
Affected service: [name]
Impact: [What is not working for whom]
Status: Investigating
Next update: in 30 minutes
IC: [Incident Commander Name]
─────────────────────────────────
```

### Step 4: DIAGNOSE (10-30 min)

```
Diagnostic tools (in order):
  1. Health checks → curl https://alerta-mujer-{auth,alert,evidence}.onrender.com/actuator/health
  2. Render → Logs tab of the affected service; last deploy and its startup
  3. Admin panel → Configuration > System status (database, push, active alerts, last expiration review)
  4. alert.alert_contact → are the notices `failed`? which channel?
  5. Installed APK → adb logcat -v time ReactNativeJS:V AndroidRuntime:E *:S
  6. Providers → Resend, Firebase and Backblaze status pages; Render database expiration

Key question: What changed in the last 2 hours?
  → Last deploy
  → Configuration change
  → Traffic spike
  → Change in an external system
```

**See specific decision tree:** `09-microservices/services/[service]/runbook.md`

### Step 5: MITIGATE (15-60 min)

```
Mitigation options (from safest to riskiest):
  1. Wake the sleeping services (call the three health endpoints)
  2. Rollback: redeploy the previous commit of main in Render / Vercel, or revert the pull request
  3. Fix configuration: JWT_SECRET, INTERNAL_API_KEY, CORS_ORIGIN, email or storage keys
  4. Database problem caused by a migration: write a new changeset that corrects it (never edit the applied one)
  5. Switch the email provider to the alternative (Brevo) if Resend is down
  6. Tell the users to rely on the official lines 123 and 155 until the service is back (last resort)
```

**Before each change during an incident:**
- Announce in the call what you are going to do
- Wait for the IC's confirmation
- Execute the change
- Report the result within 2 minutes

### Step 6: RESOLVE AND COMMUNICATE

```
The incident is resolved when:
  - Health checks respond UP on the three services
  - A test alert reaches a test contact by email and push
  - The project leader confirms users can operate normally

Resolution communication:
─────────────────────────
✅ RESOLVED — [INC-XXX]
Root cause: [brief description]
Resolution: [what was done]
Total duration: [X] minutes
Post-mortem: [meeting date]
─────────────────────────
```

---

## Post-Mortem (Incident Retrospective)

The post-mortem is not to blame anyone — it is to understand and prevent.
Held within 2 business days after resolving the incident.

### Post-mortem structure

**INC-XXX — [Incident title]**

**Key data:**
- Start date and time: 
- Resolution date and time: 
- Total duration: 
- Severity: P[N]
- Affected users: [N]

**Timeline:**

| Time | Event | Action taken |
|------|-------|-------------|
| HH:MM | [What happened] | [Who did what] |

**Root cause:**
> Why did it happen? (5 Why's — get to the real cause, not the symptom)

**Contributing factors:**
> Factors that facilitated the incident (without blaming people)

**What went well:**
> [What part of the incident response was effective]

**What did not work:**
> [What part failed or could be improved]

**Action items:**

| Action | Owner | Deadline | Status |
|--------|-------|---------|--------|
| [Concrete action to prevent recurrence] | [Name] | [date] | Pending |

---

## Communication channels

| Channel | Purpose |
|---------|---------|
| Team group | Main channel for all incidents |
| Call with the team | Temporary space for P0/P1 incidents |
| UptimeRobot | Health monitoring of the three services every 5 minutes |
| GitHub issues | Official record: INC-XXX |
| Landing page / release notes | Communication to users (if applicable) |

---

## Correlations

- Observability alerts → `13-operations/observability.md`
- Service runbooks → `09-microservices/services/XX/runbook.md`
- SLOs and Error Budget → `13-operations/README.md`
