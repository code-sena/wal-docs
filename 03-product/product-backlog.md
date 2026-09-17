# Product Backlog — Woman Alert

Prioritized list of user stories and technical tasks to be implemented, ordered by user value and dependencies.

---

## Backlog priorities

### Priority Legend
- **P0** — Critical for MVP (Horizon 1, Sprint 1-2)
- **P1** — Important for MVP (Horizon 1, Sprint 3-4)
- **P2** — Enhancement (Horizon 2)
- **P3** — Future consideration (Horizon 3)

---

## P0 — Critical for MVP

### HU-IDM-001: User Registration
**As** a new user  
**I want** to register with my phone number or email  
**So that** I can access the Woman Alert system

**Acceptance Criteria:**
- Given a valid phone number, when the user submits registration, then receive SMS verification code
- Given a valid email, when the user submits registration, then receive email verification link
- Given an invalid phone/email format, when the user submits, then show validation error
- Given a verified phone/email, when user sets password, then account is created with status `active`

**Estimation:** 3 SP  
**Priority:** P0  
**Dependencies:** None

---

### HU-IDM-002: User Login
**As** a registered user  
**I want** to log in with my credentials  
**So that** I can access my account and use the app

**Acceptance Criteria:**
- Given valid credentials, when user logs in, then receive JWT access token
- Given invalid credentials, when user attempts login, then show error message
- Given 5 failed login attempts, when user tries again, then temporarily block account (5 minutes)
- Given successful login, when JWT expires, then refresh token automatically

**Estimation:** 2 SP  
**Priority:** P0  
**Dependencies:** HU-IDM-001

---

### HU-IDM-003: Emergency Contacts Management
**As** a user  
**I want** to add, edit, and delete emergency contacts  
**So that** my alerts reach the right people

**Acceptance Criteria:**
- Given a contact form, when user adds contact, then save with name, phone, email, relationship
- Given an existing contact, when user edits details, then update record
- Given a contact, when user deletes it, then remove from emergency contacts list
- Given no contacts configured, when user triggers alert, then show error "Configure emergency contacts first"

**Estimation:** 3 SP  
**Priority:** P0  
**Dependencies:** HU-IDM-002

---

### HU-ALT-001: Main Panic Button
**As** a user in danger  
**I want** to activate a panic button from the app interface  
**So that** I can quickly send an alert to my contacts

**Acceptance Criteria:**
- Given the main screen, when user taps panic button, then immediately trigger alert
- Given an active alert, when user taps cancel, then prompt for confirmation
- Given confirmation, when user confirms cancellation, then send "alert cancelled" notification to contacts
- Given an alert with no active contacts, when user taps panic button, then show error

**Estimation:** 5 SP  
**Priority:** P0  
**Dependencies:** HU-IDM-003

---

### HU-ALT-002: Widget Activation
**As** a user  
**I want** to add a panic button widget to my home screen  
**So that** I can activate alerts without opening the app

**Acceptance Criteria:**
- Given the widget configuration screen, when user adds widget, then widget appears on home screen
- Given the widget, when user taps it, then trigger alert without opening app
- Given an active alert, when user taps widget again, then show cancellation confirmation
- Given device without widget support, when user tries to add widget, then show "not supported" message

**Estimation:** 5 SP  
**Priority:** P0  
**Dependencies:** HU-ALT-001

---

### HU-ALT-003: Sudden Movement Detection
**As** a user in danger  
**I want** the app to detect sudden movements and trigger an alert  
**So that** I can get help even if I cannot manually activate the alert

**Acceptance Criteria:**
- Given movement detection enabled, when accelerometer detects pattern matching danger, then trigger alert
- Given detection triggered, when user does not cancel within 10 seconds, then alert is sent
- Given a false positive, when user cancels within grace period, then alert is not sent
- Given movement detection settings, when user adjusts sensitivity, then detection threshold changes accordingly

**Estimation:** 8 SP  
**Priority:** P0  
**Dependencies:** HU-ALT-001

---

### HU-ALT-004: Multi-channel Notifications
**As** a user  
**I want** my emergency contacts to receive alerts via SMS, email, and push notifications  
**So that** at least one channel reaches them

**Acceptance Criteria:**
- Given an active alert, when notification is sent, then attempt SMS first
- Given SMS failure, when retry is attempted, then try email
- Given email failure, when retry is attempted, then try push notification
- Given all channels fail, when retries exhausted, then log error and alert user

**Estimation:** 5 SP  
**Priority:** P0  
**Dependencies:** HU-ALT-001

---

### HU-ALT-005: Real-time Location Sharing
**As** a user  
**I want** my contacts to see my real-time location during an active alert  
**So that** they can find me quickly

**Acceptance Criteria:**
- Given an active alert, when location is updated, then send coordinates to contacts every 30 seconds
- Given GPS signal loss, when location cannot be obtained, then send last known location with "stale" flag
- Given alert resolution, when alert ends, then stop location sharing
- Given location accuracy < 10m, when coordinates are sent, then mark as "high accuracy"

**Estimation:** 5 SP  
**Priority:** P0  
**Dependencies:** HU-ALT-004

---

### HU-EVI-001: Audio Evidence Recording
**As** a user  
**I want** the app to record audio during an active alert  
**So that** I have evidence of the situation

**Acceptance Criteria:**
- Given an active alert, when microphone permission is granted, then start audio recording
- Given recording in progress, when alert ends, then upload file to cloud storage
- Given upload failure, when retry is attempted, then keep local copy and retry later
- Given successful upload, when file is stored, then delete local copy to save space

**Estimation:** 5 SP  
**Priority:** P0  
**Dependencies:** HU-ALT-001

---

### HU-EVI-002: Photo Evidence Capture
**As** a user  
**I want** to take photos during an active alert  
**So that** I can document the situation visually

**Acceptance Criteria:**
- Given an active alert, when user taps camera button, then capture photo immediately
- Given photo captured, when upload succeeds, then add to evidence history
- Given camera permission denied, when user tries to capture, then show permission request
- Given dark environment, when photo is taken, then use flash if available

**Estimation:** 3 SP  
**Priority:** P0  
**Dependencies:** HU-ALT-001

---

### HU-EVI-003: Video Evidence Recording
**As** a user  
**I want** to record video during an active alert  
**So that** I can capture the full context of the situation

**Acceptance Criteria:**
- Given an active alert, when user taps video button, then start video recording
- Given recording in progress, when user taps stop, then end recording and upload
- Given storage full, when recording is attempted, then show error and suggest deleting old files
- Given video > 60 seconds, when recording continues, then compress automatically

**Estimation:** 5 SP  
**Priority:** P0  
**Dependencies:** HU-EVI-001

---

### HU-LOC-001: Frequent Locations
**As** a user  
**I want** to save my frequent locations (home, work)  
**So that** the system can identify my usual patterns

**Acceptance Criteria:**
- Given the locations screen, when user adds location, then save name, address, coordinates
- Given a location, when user marks as "home" or "work", then set as primary location
- Given user near a frequent location, when alert is triggered, then include location name in notification
- Given a location, when user deletes it, then remove from frequent locations

**Estimation:** 3 SP  
**Priority:** P0  
**Dependencies:** HU-ALT-005

---

### HU-WEB-001: User Portal
**As** a user  
**I want** to access a web portal to view my alert history  
**So that** I can review past incidents

**Acceptance Criteria:**
- Given a logged-in user, when user visits portal, then show list of past alerts
- Given an alert in the list, when user clicks it, then show details (time, location, contacts notified)
- Given evidence attached, when user views alert details, then show links to audio/photo/video files
- Given no alerts, when user visits portal, then show "No alerts yet" message

**Estimation:** 5 SP  
**Priority:** P0  
**Dependencies:** HU-ALT-001

---

### HU-WEB-002: Administrative Panel
**As** an administrator  
**I want** to view all active alerts in the system  
**So that** I can monitor emergency situations

**Acceptance Criteria:**
- Given an administrator, when accessing admin panel, then show list of active alerts
- Given an active alert, when admin clicks it, then show user details, location, contacts
- Given an alert, when admin adds a note, then save note with timestamp and admin ID
- Given an alert, when admin manually resolves it, then change status to `resolved` and notify user

**Estimation:** 8 SP  
**Priority:** P0  
**Dependencies:** HU-WEB-001

---

## P1 — Important for MVP

### HU-ALT-006: Physical Button Activation
**As** a user  
**I want** to activate alerts by long-pressing physical buttons  
**So that** I can trigger alerts discreetly

**Estimation:** 5 SP  
**Priority:** P1  
**Dependencies:** HU-ALT-001

---

### HU-ALT-007: Discreet Confirmation Pattern
**As** a user  
**I want** to confirm alerts with a discreet gesture pattern  
**So that** attackers don't notice I'm activating the app

**Estimation:** 5 SP  
**Priority:** P1  
**Dependencies:** HU-ALT-001

---

### HU-NOT-001: In-app Notifications
**As** a user  
**I want** to receive notifications within the app  
**So that** I stay informed about alerts and updates

**Estimation:** 3 SP  
**Priority:** P1  
**Dependencies:** HU-ALT-001

---

### HU-ZON-001: Safe/Risk Zone Map
**As** a user  
**I want** to view a map of safe and risk zones  
**So that** I can plan safer routes

**Estimation:** 5 SP  
**Priority:** P1  
**Dependencies:** HU-LOC-001

---

### HU-RES-001: Emergency Resource Directory
**As** a user  
**I want** to browse a directory of support resources  
**So that** I can find help near me

**Estimation:** 3 SP  
**Priority:** P1  
**Dependencies:** HU-ZON-001

---

## P2 — Enhancements (Horizon 2)

### HU-EVI-004: Evidence Metadata Tagging
**As** a user  
**I want** evidence files to include metadata (timestamp, location)  
**So that** context is preserved

**Estimation:** 3 SP  
**Priority:** P2  
**Dependencies:** HU-EVI-001

---

### HU-ZON-002: User-reported Zone Classifications
**As** a user  
**I want** to report zones as safe or dangerous  
**So that** other users benefit from my experience

**Estimation:** 5 SP  
**Priority:** P2  
**Dependencies:** HU-ZON-001

---

### HU-ALT-008: Voice Activation
**As** a user  
**I want** to activate alerts by saying a keyword  
**So that** I can trigger alerts hands-free

**Estimation:** 8 SP  
**Priority:** P2  
**Dependencies:** HU-ALT-001

---

### HU-REP-001: Alert Statistics Dashboard
**As** an administrator  
**I want** to view statistics about alerts in the system  
**So that** I can identify patterns and improve response

**Estimation:** 5 SP  
**Priority:** P2  
**Dependencies:** HU-WEB-002

---

## P3 — Future Considerations (Horizon 3)

### HU-INT-001: Integration with 123 Emergency Line
**As** a user  
**I want** alerts to optionally reach the national emergency line  
**So that** I can get official help

**Estimation:** 8 SP  
**Priority:** P3  
**Dependencies:** HU-ALT-004

---

### HU-COM-001: Community Support Features
**As** a user  
**I want** to connect with other verified users for support  
**So that** I can build a safety network

**Estimation:** 8 SP  
**Priority:** P3  
**Dependencies:** HU-IDM-003

---

### HU-AI-001: Predictive Risk Assessment
**As** a user  
**I want** the app to warn me when entering high-risk areas  
**So that** I can take precautions

**Estimation:** 13 SP  
**Priority:** P3  
**Dependencies:** HU-ZON-002

---

## Technical Debt Items

### TECH-001: Implement Comprehensive Test Coverage
**Description:** Add unit tests, integration tests, and E2E tests for all critical paths  
**Priority:** P0  
**Estimation:** 13 SP

---

### TECH-002: Performance Optimization for GPS Tracking
**Description:** Optimize GPS polling frequency to meet RNF6 (≤10% battery/hour)  
**Priority:** P0  
**Estimation:** 5 SP

---

### TECH-003: Implement Audit Logging
**Description:** Add comprehensive audit logging for all administrative actions  
**Priority:** P1  
**Estimation:** 3 SP

---

### TECH-004: Add Rate Limiting to API Endpoints
**Description:** Prevent abuse and protect against DDoS attacks  
**Priority:** P1  
**Estimation:** 3 SP

---

## Correlations

* User Stories with acceptance criteria → `04-requirements/user-stories.md`
* Technical backlog → `15-project-control/technical-backlog.md` (to be created)
* Sprint planning based on this backlog → `00-governance/agile-conventions.md`
