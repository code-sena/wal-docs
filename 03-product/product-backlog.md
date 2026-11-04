# Product Backlog — Woman Alert

Prioritized list of user stories and technical tasks, ordered by user value and dependencies. Updated on 28/10/2026 against SRS v2.6 and the code in `main`. New features were frozen on 24/10/2026, so P0 and P1 are built and the open work is verification and the items in P2, P3 and the technical debt.

---

## Backlog priorities

### Priority Legend
- **P0** — Critical: the alert path and the accounts (delivered in APK v1.0.0)
- **P1** — Important: complements of the alert path (delivered)
- **P2** — Next: gaps found by the audit and the quality review
- **P3** — Future consideration

---

## P0 — Critical for MVP

### HU-USU-001: Account and Login
**As** a new user  
**I want** to register and log in with email or Google  
**So that** I can use Woman Alert in the app and on the web

**Acceptance Criteria:**
- Registration validates the data and requires accepting the terms
- Login with email/password or Google opens the private area
- Five failed attempts per minute from the same IP return 429

**Requirements:** RF-USU-001, RF-USU-003, RF-USU-005, RF-USU-007  
**Priority:** P0  
**Status:** Done

---

### HU-USU-002: Recover Access
**As** an user  
**I want** to recover my account with a code sent to my email  
**So that** I do not lose access to my contacts and history

**Acceptance Criteria:**
- A 6-digit code is sent, valid 15 minutes with 5 attempts
- The answer is the same whether the account exists or not

**Requirements:** RF-USU-002  
**Priority:** P0  
**Status:** Done

---

### HU-CON-001: Trusted Contacts
**As** an user  
**I want** to add, edit and remove my trusted contacts  
**So that** the notices reach the people I chose

**Acceptance Criteria:**
- Contacts from the phone book or manual, synchronized with the web
- The server rejects contacts above the configured limit (default 5)

**Requirements:** RF-CON-001, RF-CON-002, RF-WEB-008  
**Priority:** P0  
**Status:** Done

---

### HU-ACT-001: Activate an Alert
**As** an user  
**I want** to activate an alert with the SOS button, a contact, a shake or the widget  
**So that** I can ask for help in seconds

**Acceptance Criteria:**
- A 5-second countdown allows cancelling
- The alert is registered with method, time and available location
- Shake works with the app closed (native service)

**Requirements:** RF-ACT-001 to RF-ACT-006, RF-ALE-001  
**Priority:** P0  
**Status:** Done — manual test with the release APK pending

---

### HU-ACT-002: Cancel Safely
**As** an user  
**I want** to cancel by mistake or under pressure without being discovered  
**So that** an unwanted alert is avoided and a forced cancellation still warns my contacts

**Acceptance Criteria:**
- Volume-down ×4 cancels during the countdown
- The cancellation PIN is required to finish an issued alert
- The duress PIN looks like a cancellation and sends a silent notice

**Requirements:** RF-ACT-006 to RF-ACT-009  
**Priority:** P0  
**Status:** Done

---

### HU-ALE-001: Notify My Contacts
**As** an user  
**I want** my contacts to receive a notice with a tracking link  
**So that** they know I need help and can follow me

**Acceptance Criteria:**
- Email (Resend) to every contact; push (FCM) if they have the app
- Result recorded per contact and channel
- Resent once if nobody opens it in 5 minutes

**Requirements:** RF-ALE-002, RF-ALE-006, RF-ALE-011  
**Priority:** P0  
**Status:** Done — end-to-end evidence pending

---

### HU-ALE-002: Location During the Alert
**As** an user  
**I want** my location to be shared while the alert is active  
**So that** my contacts see where I am

**Acceptance Criteria:**
- Every 15 s for 5 minutes, then every 30 s (60 s in saving mode)
- Lost-signal notice after 5 minutes without location

**Requirements:** RF-ALE-003, RF-ALE-009  
**Priority:** P0  
**Status:** Done

---

### HU-ALE-003: Alert Duration
**As** an user  
**I want** the app to ask me if I am still in danger  
**So that** the alert does not end while I still need help

**Acceptance Criteria:**
- Question at minute 25; yes extends 30 minutes
- No answer: saving mode and notice to contacts
- Absolute limit of 2 hours

**Requirements:** RF-ALE-005, RF-ALE-007  
**Priority:** P0  
**Status:** Done

---

### HU-EVI-001: Record Evidence
**As** an user  
**I want** video and audio to be recorded during the alert  
**So that** a record of what happened is kept

**Acceptance Criteria:**
- Recording starts automatically if permissions were granted
- Upload queue when there is no connection
- Kept 30 days with a notice 5 days before deletion

**Requirements:** RF-EVI-001 to RF-EVI-004  
**Priority:** P0  
**Status:** Done — field test pending

---

### HU-TRA-001: Notify Me If I Don't Arrive
**As** an user  
**I want** to start a trip with a destination and arrival time  
**So that** the server alerts my contacts if I do not confirm my arrival

**Acceptance Criteria:**
- Arrival time 5 to 240 minutes, heartbeat every 2 minutes
- The server creates the alert with the last location

**Requirements:** RF-TRA-001, RF-TRA-002, RF-ACT-011  
**Priority:** P0  
**Status:** Done — device test pending

---

### HU-TRA-002: Automatic Protection
**As** an user  
**I want** protection to start by itself when I leave a frequent place  
**So that** I am accompanied without starting a trip

**Acceptance Criteria:**
- Silent questions at 15 and 25 minutes, alert at 35
- Alert if the signal is lost for 20 minutes

**Requirements:** RF-TRA-003  
**Priority:** P0  
**Status:** Done — device test pending

---

### HU-WEB-001: User Portal
**As** an user  
**I want** to consult my alerts, locations, evidence, contacts and device from the web  
**So that** I can review my information from a computer

**Acceptance Criteria:**
- Shows only my data
- Never claims to locate a phone that is off

**Requirements:** RF-WEB-001 to RF-WEB-013  
**Priority:** P0  
**Status:** Done — no trips and no personal data editing on the web

---

### HU-ADM-001: Admin Panel
**As** an administrator  
**I want** to manage users, alerts, zones, reports, evidence, moderation, devices and configuration  
**So that** I can operate the system

**Acceptance Criteria:**
- Only the admin role accesses `/api/admin/**`
- Every change is audited
- Contacts' personal data is never shown

**Requirements:** RF-ADM-001 to RF-ADM-012, RF-AUD, RF-SES  
**Priority:** P0  
**Status:** Done

---

## P1 — Important for MVP

### HU-NOT-001: Contact Alarm and Inbox
**As** an user  
**I want** contacts with the app to hear an alarm and to have an inbox of notices  
**So that** no notice goes unnoticed

**Acceptance Criteria:**
- Alarm until silenced (max. 10 minutes)
- Inbox with risk zone notices and end of a contact's alert

**Requirements:** RF-NOT-002, RF-NOT-004, RF-NOT-007  
**Priority:** P1  
**Status:** Done — test pending

---

### HU-UBI-001: Map, Places and Routes
**As** an user  
**I want** to see zones, save places and routes, and share a location  
**So that** I can plan safer trips

**Acceptance Criteria:**
- Places with safety level
- Max. 4 routes, 2 starred

**Requirements:** RF-UBI-001 to RF-UBI-008  
**Priority:** P1  
**Status:** Done

---

### HU-REC-001: Assistance Lines
**As** an user  
**I want** to open the dialer with 123, the purple line or the mental health line  
**So that** I decide whether to call

**Acceptance Criteria:**
- No automatic call

**Requirements:** RF-REC-001, RF-REC-002  
**Priority:** P1  
**Status:** Done

---

### HU-CFG-001: Minimum Version
**As** an user  
**I want** to be told when my app is too old  
**So that** I install the version that works

**Acceptance Criteria:**
- Non-blocking notice with download link

**Requirements:** RF-CFG-001  
**Priority:** P1  
**Status:** Done

---

## P2 — Enhancements (Horizon 2)

### HU-WEB-002: Web Parity
**As** an user  
**I want** to manage trips and edit my personal data from the web  
**So that** I do not depend on the phone for those tasks

**Acceptance Criteria:**
- Same rules as the app

**Requirements:** RF-WEB-002  
**Priority:** P2  
**Status:** Pending

---

### HU-ALE-004: Alert Without Connection
**As** an user  
**I want** the alert to be queued and sent when the connection returns  
**So that** an alert is not lost because of a short signal loss

**Acceptance Criteria:**
- Single record, no duplicates

**Requirements:** RF-ALE-001, RNF-DIS-002  
**Priority:** P2  
**Status:** Pending

---

### HU-PRI-001: Daily Location Switch
**As** an user  
**I want** to turn off the daily location sharing  
**So that** I control what is recorded about me

**Acceptance Criteria:**
- Explicit consent

**Requirements:** RNF-PRI-002  
**Priority:** P2  
**Status:** Pending

---

## P3 — Future Considerations (Horizon 3)

### HU-ACC-001: Accessibility Review
**As** an user who uses a screen reader  
**I want** the app and the web to be usable with TalkBack and the keyboard  
**So that** I can use Woman Alert without seeing the screen

**Acceptance Criteria:**
- Labels on icon-only buttons, starting with the alert button

**Requirements:** RNF-ACC-001  
**Priority:** P3  
**Status:** Pending

---

### HU-EVI-002: Evidence Encryption
**As** an user  
**I want** my evidence files to be encrypted with an own key  
**So that** nobody with access to the storage can watch them

**Acceptance Criteria:**
- Or remove the text "Encrypted recording" from the tutorial

**Requirements:** RNF-SEG-006  
**Priority:** P3  
**Status:** Pending

---

### HU-VAL-001: Validation with Users from Huila
**As** a team  
**I want** to test the activation methods with real users  
**So that** we know the design works under stress

**Acceptance Criteria:**
- Recorded instrument and results

**Requirements:** RNF-USB-001  
**Priority:** P3  
**Status:** Pending

---

## Technical Debt Items

### TECH-001: Run and record manual tests with the release APK
**Description:** PM-04 to PM-09: shake, widget, trip, automatic protection, background recording, notices between two phones  
**Priority:** P0

---

### TECH-002: Move the map views to ViewModels
**Description:** `MapScreen.tsx` and other map views mix view and logic  
**Priority:** P1

---

### TECH-003: Remove the demo mode of the mobile app
**Description:** Index screen and temporary button in the contact tracking  
**Priority:** P1

---

### TECH-004: Global timeout in the web interceptor
**Description:** Requests hang while a service is asleep  
**Priority:** P1

---

### TECH-005: Unify duplicated zone and evidence services in the web
**Description:** Three zone services and two evidence services  
**Priority:** P2

---

### TECH-006: Save the smoke tests in the repository
**Description:** `scripts/smoke/` to reuse them  
**Priority:** P2

---

### TECH-007: Try a production build (AAB)
**Description:** Never executed  
**Priority:** P2

---

### TECH-008: Remove `console` calls outside `__DEV__`
**Description:** 64 calls remain in the mobile app  
**Priority:** P1

---

### TECH-009: Measure RNF-REN-001 to RNF-REN-003
**Description:** Dispatch time, battery and GPS accuracy  
**Priority:** P2

---

## Correlations

* User Stories with acceptance criteria → `04-requirements/user-stories.md`
* Requirements and traceability → `04-requirements/functional.md`, `04-requirements/traceability-matrix.md`
* Risks → `15-project-control/risks.md`
