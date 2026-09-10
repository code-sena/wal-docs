# Security Threat Model

> STRIDE analysis of `alerta-mujer-backend` and its two clients. This section is more sensitive
> than most for this project specifically: the system stores real-time location, audio/video
> evidence, and emergency-contact data for women in active risk situations (SRS §1, §1.2). A data
> leak here isn't just a privacy incident — it can expose a victim's location to an aggressor.

---

## 1. Spoofing (impersonating a user or the system)

| Threat | Mitigation | Status |
|--------|-----------|--------|
| An attacker impersonates a victim's account to see her location/history or to cancel a real alert | Authentication via `account` + `role` (Spring Security) | Adopted, token strategy (JWT vs. session) still not defined — see `cross-cutting.md` §1 |
| An attacker impersonates the backend to a client app (man-in-the-middle) | HTTPS/TLS for all mobile-backend and web-backend traffic | Not yet stated explicitly in the SRS or architecture docs — should be a hard requirement given the data involved, not just a default |
| An attacker triggers a fake panic-button alert to spam an emergency contact | Rate limiting on alert activation (see `cross-cutting.md` §6, currently "not applicable") | Not mitigated yet — worth revisiting specifically for this reason |

---

## 2. Tampering (unauthorized modification of data)

| Threat | Mitigation | Status |
|--------|-----------|--------|
| Modifying an `alert`'s status or location after the fact (e.g. to hide evidence) | `audit_log`, already in the data model (`06-data/models.md`) | Adopted for traceability; access control on who can write to `alert` records still depends on the auth strategy in §1 |
| Tampering with uploaded evidence (audio/video) after submission | Not yet defined — no integrity check (hash, signature) on evidence files is mentioned in the SRS | Gap — flagged for follow-up, since evidence integrity matters if it's ever used outside the app |

---

## 3. Repudiation (denying an action was taken)

| Threat | Mitigation | Status |
|--------|-----------|--------|
| An administrator denies having modified or deleted a user's alert/zone record | `audit_log` records administrative actions | Adopted — depends on the audit log actually being populated on every write path, not just alert creation |
| A user denies having cancelled a real alert | `Alert` state transitions (`activar()`, cancellation) are timestamped | Adopted, per `02-domain/entities-and-rules.md` |

---

## 4. Information Disclosure (the highest-impact category for this project)

| Threat | Mitigation | Status |
|--------|-----------|--------|
| A victim's real-time location is exposed to someone other than her configured emergency contacts | Location sharing scoped to `EmergencyContact` records the user herself configured (SRS §Alcance) | Adopted at the domain-model level; enforcement depends on the still-undefined authorization detail in §1 |
| Evidence (audio/video) is stored or transmitted unencrypted | Encryption at rest and in transit for evidence storage | Not yet defined — the SRS objectives mention "protecting security and privacy of information through encryption" (§1.3) as a goal, but no specific mechanism is documented yet |
| Emergency contact list or alert history leaked through an unprotected endpoint or admin panel | Role-based access control (`user` vs. `administrator`) | Adopted at the model level; needs the token/session strategy from §1 finalized to be enforceable end-to-end |
| Database backup or logs inadvertently containing location/evidence data in plaintext | Not yet defined | Gap — worth an explicit statement once `deployment.md` §1 (environments) and logging (`cross-cutting.md` §2) are defined |

---

## 5. Denial of Service

| Threat | Mitigation | Status |
|--------|-----------|--------|
| A flood of requests prevents a real panic-button alert from being processed in time | Retry with Exponential Backoff on notification dispatch (`pattern-guide.md`); Circuit Breaker on external providers | Adopted for the notification path; nothing yet at the network/ingress layer, since there's no API Gateway (ADR-002) |
| An external provider (FCM/SMS/email) outage blocks alert delivery entirely | Circuit Breaker + multi-channel delivery (SMS, email, push are independent channels per `alert_contact.channel`) | Adopted — this is one of the strongest existing mitigations, since it was designed into the domain model directly |

---

## 6. Elevation of Privilege

| Threat | Mitigation | Status |
|--------|-----------|--------|
| A regular user accesses administrator-only functionality (user management, moderation, system configuration — RF13) | Role check (`user` vs. `administrator`) on every administrative endpoint | Adopted at the model level; enforcement mechanism (middleware/guard on every route) not yet documented |
| A compromised client (mobile or web) is used to call backend endpoints directly, bypassing intended UI flows | Server-side validation independent of client — the backend must not trust client-side checks | Principle, not yet backed by a documented validation strategy per endpoint |

---

## Priority follow-ups

Given the profile of this system, these are the gaps most worth closing first, in order:

1. **Encryption at rest for evidence and location data** (§4) — directly tied to the SRS's own
   stated objective of protecting user security and privacy
2. **Token/session strategy** (§1) — blocks enforcing every access-control mitigation listed above
3. **TLS as an explicit, documented requirement** (§1) — currently implicit, should be explicit
   given what's transmitted
4. **Rate limiting on alert activation** (§1, §5) — prevents both abuse and accidental DoS of the
   one path that cannot fail

---

## Correlations

- Data model (`account`, `role`, `alert`, `EmergencyContact`, `audit_log`) → `06-data/models.md`
- Domain rules for `Alert` state transitions → `02-domain/entities-and-rules.md`
- Circuit Breaker and Retry patterns → `05-architecture/pattern-guide.md`
- Open authentication/authorization decision → `05-architecture/cross-cutting.md` §1
