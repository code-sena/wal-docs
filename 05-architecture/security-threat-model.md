# Security Threat Model

> STRIDE analysis of the three backend services (Auth, Alert, Evidence) and their two clients.
> This section is more sensitive than most for this project: the system stores location, audio
> and video evidence and trusted-contact data of women who may be at risk. A data leak here is not
> only a privacy incident — it can expose a victim's location to an aggressor. Status taken from
> the security matrix of the technical audit (24/10/2026) and the changes made afterwards.

---

## 1. Spoofing (impersonating a user or the system)

| Threat | Mitigation | Status |
|--------|-----------|--------|
| An attacker impersonates a victim's account to see her location or history or to cancel a real alert | JWT signed with `JWT_SECRET` (24 h access, 15-day refresh), BCrypt, 5 login attempts per minute per IP, cancellation PIN for issued alerts | Implemented |
| An attacker forges a Google login | Firebase / Google token verified with its signature on the server | Implemented |
| Someone forces the user to cancel the alert | Duress PIN: the app looks cancelled while the contacts receive a silent notice | Implemented |
| An attacker impersonates a service in an internal call | `/internal/**` requires `X-Internal-Key`; without it the answer is 403 | Implemented |
| A man-in-the-middle reads the traffic | HTTPS in production; cleartext only allowed in local development | Implemented |
| An attacker triggers fake alerts to spam contacts | Only one active alert per user; 5-second cancellation window | Partial — no specific rate limit on alert creation |

---

## 2. Tampering (unauthorized modification of data)

| Threat | Mitigation | Status |
|--------|-----------|--------|
| Modifying an alert's status or location after the fact | Owner taken from the JWT; only the owner or an administrator can change the status; changes recorded by audit triggers | Implemented |
| Tampering with uploaded evidence | SHA-256 hash stored with each file; safe file names; accepted file types checked in the domain | Implemented |
| Editing or deleting audit records | Trigger `trg_audit_log_no_update_delete`; the application database user cannot modify the audit log or create or drop tables | Implemented |
| Invalid data written directly through the API | Validation in DTOs and CHECK constraints in the database (email and phone format, states, coordinate range) | Implemented |

---

## 3. Repudiation (denying an action was taken)

| Threat | Mitigation | Status |
|--------|-----------|--------|
| An administrator denies having modified a record | `admin.log_row_change` records changes on the main tables; admin session events with IP and origin (web, mobile or scheduled task) | Implemented — the trigger stores the names of the changed columns, not the previous and new values (RF-AUD-002 in development) |
| A user denies having cancelled a real alert | State transitions with timestamps (`started_at`, `ended_at`, `cancelled_at`) | Implemented |
| A contact denies having received the notice | `alert_contact` stores channel, status, attempts and reason; the tracking page records visits (`tracking_view`) and the "Received" mark | Implemented |
| An account is deleted by a single administrator | Deletion requires approval from a second administrator and anonymizes the personal data | Implemented |

---

## 4. Information Disclosure (the highest-impact category for this project)

| Threat | Mitigation | Status |
|--------|-----------|--------|
| A victim's location reaches someone other than her contacts | Tracking link with 256 random bits; only its hash is stored and it expires 2 hours after the alert ends | Implemented |
| The administration sees too much | Only the route of active or attended alerts and, per contact, the send status and relationship; never the contact's personal data | Corrected (S2) |
| Evidence stored or transmitted unencrypted | HTTPS in transit; Backblaze B2 storage | Partial — files are not encrypted with an own key; the tutorial text "Encrypted recording" must be corrected |
| Sensitive columns readable in a database copy | Not mitigated | Pending — no encryption at rest |
| Web session token stolen by injected scripts | Logout on 401 and on inactivity | Pending — the token is stored in `localStorage` |
| Daily location shared without consent | Only the main phone reports, every 50 m or 5 min; retention of 7 days | Pending — no switch to disable it while the app is open |
| Secrets in the repository history | Branch with passwords deleted, old Firebase key no longer valid, JWT secret rotated, web Firebase key restricted to the project domains, default secret values removed | Corrected — the admin password that appears in a commit message still has to be changed |
| Personal data in logs | Mobile HTTP logs only in `__DEV__` | Pending — 64 `console` calls remain |
| Old data kept forever | Evidence deleted after 30 days (except kept alerts, up to 5), daily location after 7 days and alert locations after 30 days | Implemented |

---

## 5. Denial of Service

| Threat | Mitigation | Status |
|--------|-----------|--------|
| Brute force on login or recovery | `RateLimitFilter`: 5 attempts per minute per IP, then 429; recovery code valid 15 minutes with 5 attempts | Implemented |
| Very large uploads | Evidence limited to 50 MB (`413`) | Implemented |
| A sleeping service delays a real alert | UptimeRobot every 5 minutes; the app shows the status and allows retrying | Partial — cold start of up to about 90 seconds on the free plan |
| An external provider outage blocks delivery | Email and push are independent channels; each failure is recorded per contact | Partial — no automatic fallback between providers |

---

## 6. Elevation of Privilege

| Threat | Mitigation | Status |
|--------|-----------|--------|
| A regular user reaches administrator features | `@PreAuthorize("hasRole('ADMIN')")` on `/api/admin/**`; admin routes only under `/admin` in the web (duplicated routes under `/dashboard` removed) | Implemented |
| A user reads or changes another user's data | Ownership check in the use cases; trying to access another user's alert answers 403 | Implemented |
| A compromised client calls the API directly | Server-side validation independent of the client; guards only guide the UI | Implemented |
| The application database user is abused | Limited user: it reads and writes data but cannot create or drop tables or modify the audit log | Implemented |

---

## Priority follow-ups

Given the profile of this system, these are the gaps most worth closing first, in order:

1. **Daily location without a switch** (§4) — add consent and a switch to stop it.
2. **Web token in `localStorage`** (§4) — move to a safer storage strategy.
3. **Encryption of evidence and sensitive columns** (§4) — or remove any text that promises it.
4. **Change the administrative password** that appears in a commit message (§4).
5. **Privacy policy, data treatment policy and terms and conditions** required by Law 1581 of 2012.
6. **Remove `console` calls** outside `__DEV__` in the mobile app (§4).

---

## Correlations

- Data model (`account`, `role`, `alert`, `emergency_contact`, `audit_log`) → `06-data/models.md`
- Domain rules for `Alert` state transitions → `02-domain/entities-and-rules.md`
- Cross-cutting security configuration → `05-architecture/cross-cutting.md`
- Non-functional security requirements → `04-requirements/non-functional.md`
- RBAC roles, JWT policy, and security event definitions → `00-governance/security-policy.md`
