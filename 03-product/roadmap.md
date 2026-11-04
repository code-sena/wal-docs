# Product Roadmap — Woman Alert

This roadmap shows what was delivered and what comes next, over three horizons. Dates are the verifiable milestones recorded in the technical audit and the general project document; the team's Gantt chart (January to July, year not stated) is used only as a planning reference.

---

## Horizon 1 — MVP (Sprint 1-2)

**Objective:** Deliver the core system: activation from the app, notices to contacts, accompaniment during the alert, web portal and admin panel.

**Timeline:** built iteratively until the feature freeze of 24/10/2026; APK v1.0.0 and publication from `main` on 28/10/2026.

### Modules and features

#### Identity and Access Management
- [x] Registration with acceptance of terms, login with email or Google, JWT with refresh
- [x] Recovery with a 6-digit code by email
- [x] Profile, account deletion, new-phone notice
- [x] Rate limit on authentication

#### Alert Activation
- [x] SOS button with a 5-second countdown, alert to a single contact, shake (native service), widget
- [x] Cancellation PIN, duress PIN, cancel with volume-down ×4, discreet mode
- [x] Enable or disable each method
- [x] Lock screen and power button activation — **removed on 23/10/2026**

#### Alert Delivery
- [x] Email with tracking link (Resend) and push with alarm (FCM)
- [x] Result per contact and channel, resend after 5 minutes, lost-signal notice
- [x] Public tracking page for contacts, chat

#### Evidence Recording
- [x] Automatic video and then audio during the alert, upload queue
- [x] Backblaze B2 with SHA-256, 30-day retention, keep up to 5 alerts
- [x] Private copy on the device

#### Location Tracking
- [x] Periodic location during the alert, also with the app closed
- [x] "Notify me if I don't arrive" trips
- [x] Automatic protection when leaving a frequent place (added after the freeze, approved before it)

#### Notifications
- [x] Inbox with a bell, alerts received from contacts
- [x] Minimum version notice

#### Web Platform
- [x] Landing with APK download, public tracking page, user portal, admin panel under `/admin`
- [x] Translated to English, Portuguese and French on 25/10/2026

#### Cross-cutting
- [x] Three microservices with hexagonal architecture and ArchUnit tests (25/10/2026)
- [x] 553 automated tests and GitHub Actions on every pull request (28/10/2026)
- [x] Render, Vercel and GitHub Releases publish from `main` (28/10/2026)

---

## Horizon 2 — Iteration (Sprint 3-4)

**Objective:** Verify what was built and close the gaps found by the audit and the quality review.

### Enhancements

#### Evidence Expansion
- Field test of recording with the app in the background
- Encryption of evidence files with an own key, or correction of the tutorial text "Encrypted recording"

#### Zone Management
- Review of zones and help points with real data of Neiva and Huila

#### Advanced Activation
- Manual tests of shake, widget, trip and automatic protection with the release APK (PM-04 to PM-09) on several manufacturers
- Local queue to create the alert without connection

#### Reporting and Analytics
- Measurement of alert dispatch time, battery and GPS accuracy (RNF-REN-001 to 003)

#### Administrative Features
- Record previous and new values in the audit of configuration changes (RF-AUD-002)
- Change the administrative password that appears in a commit message

---

## Horizon 3 — Long-term Evolution (Sprint 5+)

**Objective:** Improve with real use, if the project continues after the academic delivery.

### Future considerations

#### UX and Usability
- Validation with users from Huila, with a preserved instrument and results
- Accessibility review with TalkBack, large text and keyboard

#### Integration
- Decide whether to add a gateway (DIS-ARQ-04)
- Production build (AAB) and distribution beyond GitHub Releases

#### Privacy and Security
- Privacy policy, data treatment policy and terms and conditions (Law 1581 of 2012)
- Switch to turn off the daily location; web token outside `localStorage`; encryption at rest

#### AI and Automation
- Not planned. Any automatic risk classification would need an explicit ethical and privacy review first.

#### Community Features
- Not planned beyond the existing zone reports moderated by the administration.

---

## Dependencies between horizons

| Item | Depends on |
|------|------------|
| Declaring the alert path as verified | Manual tests PM-04 to PM-09 and archived evidence of the notices between two phones |
| Opening the app to more users | Privacy policy and terms; closing the open security findings |
| Removing the cold start | A hosting plan without sleep |
| Web parity | Trips and personal data endpoints already exist in the backend; only the web screens are missing |

---

## Risk-based scope adjustments

| Risk | Scope decision |
|------|----------------|
| Academic deadline | Feature freeze on 24/10/2026; only automatic protection and bug fixes after it |
| Google Maps billing | Migrated to MapLibre and Leaflet with OpenStreetMap tiles |
| SMTP blocked by Render | Email through an HTTP API |
| Lock screen activation unreliable | Removed and recorded as Retired |
| SMS cost and complexity | Discarded; email with tracking link plus push |

---

## Success criteria per horizon

### Horizon 1 (MVP)
- [x] All core features implemented in `main`
- [x] 553 automated tests green in CI
- [x] APK v1.0.0 published and downloadable from the landing page
- [ ] Notices verified end to end between two phones with recorded evidence

### Horizon 2 (Iteration)
- [ ] PM-04 to PM-09 recorded
- [ ] Open security findings closed
- [ ] Performance targets measured

### Horizon 3 (Long-term)
- [ ] Design validated with users from Huila
- [ ] Policies and terms published
- [ ] Hosting without sleep

---

## Correlations

* Product vision → `03-product/vision.md`
* Product backlog → `03-product/product-backlog.md`
* Risks → `15-project-control/risks.md`
