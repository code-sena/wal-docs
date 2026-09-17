# User Stories — Woman Alert

> Formalization of the user stories (US) that were already included in `03-product/product-backlog.md`, using the Gherkin format of this folder. There are no sprint dates yet (see `03-product/roadmap.md`), so "Target Sprint" remains pending until the team defines it.

## Backlog Status

| Current | Total US | Refined | In Progress | Completed |
| ------- | -------- | ------- | ----------- | --------- |
| Current | 15       | 15      | 5           | 1         |

*(Updated with the actual status confirmed by the team: the mobile application has been under development for some time — HU-MOVIL-002 is already completed, while the other three existing mobile user stories are in progress with details still to be polished. The web panel is under reconstruction based on instructor feedback, so the two existing web user stories are considered in progress. The 9 new user stories (HU-MOVIL-005 to 009, HU-WEB-ADMIN-002 to 005) have just been formalized from SRS FRs that did not previously have a user story, so they remain in the Backlog — none of them is an invented decision; they are simply the "As a / I want / So that" form applied to an existing requirement.)*

## Epics

| Epic ID | Description           | Scope                                                                                         |
| ------- | --------------------- | --------------------------------------------------------------------------------------------- |
| EP-001  | Alerts and Activation | Everything related to activating an alert and ensuring it reaches trusted contacts (FR4, FR5) |
| EP-002  | User Management       | Emergency contacts, evidence, resources, and map (FR2.2, FR6, FR7, FR9)                       |
| EP-003  | User Web Portal       | Profile, history, and evidence consultation from the web (FR12)                               |
| EP-004  | Administrative Panel  | Management and supervision from the administrator role (FR13)                                 |

---

### HU-MOVIL-001 — Activate Emergency Alert {#HU-MOVIL-001}

**Epic:** EP-001

> **As** a mobile application user
> **I want** to activate an alert through at least two quick-access methods (widget, physical button, or touch pattern)
> **So that** my real-time location is sent and my trusted contacts are notified without needing to open the app

**Acceptance Criteria:**

```gherkin
Scenario 1: Successful activation through widget or physical button
  Given the user has emergency contacts configured
  When she activates the alert through a widget, sudden movement, or physical button (FR5.1-FR5.3)
  Then the system sends the location and notifies the configured contacts (FR4.1, FR4.2)
  And dispatch occurs within a maximum of 2 seconds up to delivery to the channel provider, in at least 95% of cases (RNF5)

Scenario 2: Notification channel failure
  Given an alert has been activated
  When the primary delivery channel fails
  Then the system automatically retries through another available channel (RNF9)

Scenario 3: Activation method registration
  Given an alert has been triggered
  When the system processes it
  Then it is labeled with the activation method used, only as historical/analytical data, without affecting the priority or urgency with which it is processed, which is always maximum (FR4.4); the system may also request discreet confirmation when applicable (FR5.4) and generate reminders or mini-alerts according to configured rules (FR4.5)
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | 8                                                           |
| Priority            | High                                                        |
| Target Sprint       | To Be Defined                                               |
| Status              | In Progress                                                 |
| Dependencies        | HU-MOVIL-002 (requires configured contacts)                 |
| Affected Service(s) | Alert module (backend), `features/BotonActivacion` (mobile) |

---

### HU-MOVIL-002 — Manage Emergency Contacts {#HU-MOVIL-002}

**Epic:** EP-002

> **As** a mobile application user
> **I want** to add, view, modify, and delete my emergency contacts
> **So that** the system knows whom to notify when I activate an alert

**Acceptance Criteria:**

```gherkin
Scenario 1: Add an emergency contact
  Given I am in the contacts module
  When I add a contact with a name and phone number
  Then it is saved in my trusted contacts list (FR2.2)

Scenario 2: Delete a contact
  Given I have saved contacts
  When I delete one
  Then it will no longer receive notifications for future alerts
```

| Field               | Value                                                    |
| ------------------- | -------------------------------------------------------- |
| Story Points        | 5                                                        |
| Priority            | High                                                     |
| Target Sprint       | To Be Defined                                            |
| Status              | Done                                                     |
| Dependencies        | None                                                     |
| Affected Service(s) | Contacts module (backend), `features/contacts` (mobile) |

---

### HU-MOVIL-003 — Record Evidence During an Alert {#HU-MOVIL-003}

**Epic:** EP-002

> **As** a mobile application user
> **I want** to capture audio and/or video as evidence during an alert
> **So that** a stored record of what happened is available

**Acceptance Criteria:**

```gherkin
Scenario 1: Record evidence during an active alert
  Given I have an active alert
  When I record audio or video
  Then it is stored and backed up, associated with that alert (FR6.1-FR6.3)
```

| Field               | Value                                                                   |
| ------------------- | ----------------------------------------------------------------------- |
| Story Points        | 5                                                                       |
| Priority            | Medium                                                                  |
| Target Sprint       | To Be Defined                                                           |
| Status              | In Progress                                                             |
| Dependencies        | HU-MOVIL-001                                                            |
| Affected Service(s) | Evidence module (backend), camera/microphone permissions (FR1.5, FR1.6) |

---

### HU-MOVIL-004 — View Zone Map and Emergency Resources {#HU-MOVIL-004}

**Epic:** EP-002

> **As** a mobile application user
> **I want** to view safe/risk zones and a directory of nearby help centers
> **So that** I can make informed decisions about my safety

**Acceptance Criteria:**

```gherkin
Scenario 1: View current zone
  Given I open the map
  When I check my zone
  Then I can see whether it is marked as safe or risky (FR9.1)

Scenario 2: View help directory
  Given I need help
  When I access the directory
  Then I can see available phone numbers, organizations, and help centers (FR7.1, FR9.3)
```

| Field               | Value                                            |
| ------------------- | ------------------------------------------------ |
| Story Points        | 5                                                |
| Priority            | Medium                                           |
| Target Sprint       | To Be Defined                                    |
| Status              | In Progress                                      |
| Dependencies        | None                                             |
| Affected Service(s) | Zones module (backend), `features/mapa` (mobile) |

---

### HU-WEB-USUARIA-001 — View History and Status from the Web Portal {#HU-WEB-USUARIA-001}

**Epic:** EP-003

> **As** a user authenticated on the web platform
> **I want** to view my profile, alerts, history, location, and evidence from a computer
> **So that** I can review my information when I do not have my phone available, as a complement to the mobile app

**Acceptance Criteria:**

```gherkin
Scenario 1: View information from the web
  Given I log in to the web platform (FR12.1)
  When I access the portal
  Then I can see my profile, alerts, and history (FR12.2, FR12.3, FR12.4)

Scenario 2: Evidence visible only to its owner
  Given I have evidence recorded from the mobile app
  When I view it on the web
  Then I can only see the evidence associated with my own account (FR12.6, RNF16)
```

| Field               | Value                                                                   |
| ------------------- | ----------------------------------------------------------------------- |
| Story Points        | 5                                                                       |
| Priority            | Medium                                                                  |
| Target Sprint       | To Be Defined                                                           |
| Status              | Under Reconstruction                                                    |
| Dependencies        | HU-MOVIL-001, HU-MOVIL-003 (alerts/evidence must exist to be consulted) |
| Affected Service(s) | `features/dashboard` (web)                                              |

---

### HU-WEB-ADMIN-001 — Manage Users, Alerts, and Reports {#HU-WEB-ADMIN-001}

**Epic:** EP-004

> **As** a web platform administrator
> **I want** to manage users, alerts, and reports
> **So that** I can supervise the system and perform operational follow-up

**Acceptance Criteria:**

```gherkin
Scenario 1: View users and alerts
  Given I am an authenticated administrator
  When I access the panel
  Then I can view and manage users (FR13.1) and generated alerts (FR13.2)

Scenario 2: Generate a filtered report
  Given I need to generate a report for a period
  When I generate a report using date/type/status/city filters
  Then I can download it as PDF or Excel, or print it (FR13.4)

Scenario 3: Action auditing
  Given an administrator performs an action in the panel
  Then the system records it (administrator, action, date, time, IP) (FR13.9)
```

| Field               | Value                                          |
| ------------------- | ---------------------------------------------- |
| Story Points        | 8                                              |
| Priority            | Medium                                         |
| Target Sprint       | To Be Defined                                  |
| Status              | Under Reconstruction                           |
| Dependencies        | HU-MOVIL-001 (alerts must exist to be managed) |
| Affected Service(s) | `features/admin` (web)                         |

---

### HU-MOVIL-005 — Grant Required App Permissions {#HU-MOVIL-005}

**Epic:** EP-002

> **As** a mobile application user
> **I want** the app to request the necessary permissions (contacts, calls, location, notifications, microphone, camera)
> **So that** I can use all emergency functions without failures caused by missing permissions

```gherkin
Scenario 1: Permission request by function
  Given I open the app for the first time
  When I access each function that requires permission
  Then the app requests the corresponding permission (contacts FR1.1, calls FR1.2, location FR1.3, notifications FR1.4, microphone FR1.5, camera FR1.6)

Scenario 2: Critical permission denied
  Given I deny a critical permission (location or notifications)
  When I try to activate an alert
  Then the app explains which function will not work without that permission
```

| Field               | Value                                 |
| ------------------- | ------------------------------------- |
| Story Points        | 3                                     |
| Priority            | High                                  |
| Target Sprint       | To Be Defined                         |
| Status              | Backlog                               |
| Dependencies        | None                                  |
| Affected Service(s) | Operating system permissions (mobile) |

---

### HU-MOVIL-006 — Register and Recover My Account {#HU-MOVIL-006}

**Epic:** EP-002

> **As** a mobile application user
> **I want** to create an account and recover access if I lose it
> **So that** I can use the app securely and not lose access to my contacts and history

```gherkin
Scenario 1: Account registration
  Given I am a new user
  When I complete the registration with the required information
  Then my account is created (FR2.1)

Scenario 2: Access recovery
  Given I forgot my password or lost access
  When I use the recovery option
  Then I can reset access to my account (FR2.3)
```

| Field               | Value                           |
| ------------------- | ------------------------------- |
| Story Points        | 5                               |
| Priority            | High                            |
| Target Sprint       | To Be Defined                   |
| Status              | Backlog                         |
| Dependencies        | None                            |
| Affected Service(s) | Authentication module (backend) |

---

### HU-MOVIL-007 — View Start Guide and System Notices {#HU-MOVIL-007}

**Epic:** EP-002

> **As** a mobile application user
> **I want** to see a guide to the main functions, my current location, and relevant notices from the home screen
> **So that** I understand how to use the app and stay informed about important updates

```gherkin
Scenario 1: Interactive guide
  Given I enter the app for the first time
  When I reach the home screen
  Then I see an interactive guide to the main functions (FR3.1)

Scenario 2: Home screen panel
  Given I am on the home screen
  When I view it
  Then I see my current location and registered locations (FR3.2), as well as relevant reminders or warnings (FR3.3)
```

| Field               | Value                      |
| ------------------- | -------------------------- |
| Story Points        | 3                          |
| Priority            | Low                        |
| Target Sprint       | To Be Defined              |
| Status              | Backlog                    |
| Dependencies        | None                       |
| Affected Service(s) | `features/inicio` (mobile) |

---

### HU-MOVIL-008 — Customize App Language and Theme {#HU-MOVIL-008}

**Epic:** EP-002

> **As** a mobile application user
> **I want** to change the interface language and visual theme (light/dark)
> **So that** the app adapts to my preferences and is more comfortable to use

```gherkin
Scenario 1: Language change
  Given I am in settings
  When I change the language
  Then the entire interface is updated to that language (FR10.1)

Scenario 2: Theme change
  Given I am in settings
  When I change the theme
  Then the app is displayed in light or dark mode according to my selection (FR11.1)
```

| Field               | Value                             |
| ------------------- | --------------------------------- |
| Story Points        | 2                                 |
| Priority            | Low                               |
| Target Sprint       | To Be Defined                     |
| Status              | Backlog                           |
| Dependencies        | None                              |
| Affected Service(s) | `features/configuracion` (mobile) |

---

### HU-MOVIL-009 — Receive High-Priority Emergency Notifications {#HU-MOVIL-009}

**Epic:** EP-001

> **As** a mobile application user
> **I want** emergency-related notifications to have high priority and remain visible
> **So that** I do not miss them while an alert is active

```gherkin
Scenario 1: High-priority notification
  Given an event related to an emergency occurs
  When the system generates the notification
  Then it is displayed with high priority (FR8.1)

Scenario 2: Persistence during an active alert
  Given I have an active alert
  When I check my notifications
  Then notifications related to that alert remain visible for the duration of the alert (FR8.2)
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | 3                                                           |
| Priority            | Medium                                                      |
| Target Sprint       | To Be Defined                                               |
| Status              | Backlog                                                     |
| Dependencies        | HU-MOVIL-001                                                |
| Affected Service(s) | Notification module (backend), notification system (mobile) |

---

### HU-WEB-ADMIN-002 — Manage Zones and Their Risk Level {#HU-WEB-ADMIN-002}

**Epic:** EP-004

> **As** a web platform administrator
> **I want** to create, view, modify, and delete zones together with their risk level
> **So that** the safe/risk zone information displayed to users remains up to date

```gherkin
Scenario 1: Manage a zone
  Given I am an administrator
  When I create or edit a zone
  Then I can assign a risk level to it and it becomes available to users (FR13.3)
```

| Field               | Value                                          |
| ------------------- | ---------------------------------------------- |
| Story Points        | 5                                              |
| Priority            | Medium                                         |
| Target Sprint       | To Be Defined                                  |
| Status              | Backlog                                        |
| Dependencies        | None                                           |
| Affected Service(s) | Zones module (backend), `features/admin` (web) |

---

### HU-WEB-ADMIN-003 — View a User's Contacts, Devices, and Evidence {#HU-WEB-ADMIN-003}

**Epic:** EP-004

> **As** a web platform administrator
> **I want** to view a specific user's emergency contacts, device information, and evidence
> **So that** I can perform complete follow-up of an alert when necessary

```gherkin
Scenario 1: Complete user profile
  Given I am reviewing a user
  When I view their profile
  Then I can see their emergency contacts (FR13.5), device information (brand, model, version, GPS status, and permission status) (FR13.6), and evidence related to their alerts (FR13.7)
```

| Field               | Value                                                                     |
| ------------------- | ------------------------------------------------------------------------- |
| Story Points        | 5                                                                         |
| Priority            | Medium                                                                    |
| Target Sprint       | To Be Defined                                                             |
| Status              | Backlog                                                                   |
| Dependencies        | HU-MOVIL-002, HU-MOVIL-003 (contacts/evidence must exist to be consulted) |
| Affected Service(s) | `features/admin` (web)                                                    |

---

### HU-WEB-ADMIN-004 — Configure General System Parameters {#HU-WEB-ADMIN-004}

**Epic:** EP-004

> **As** a web platform administrator
> **I want** to manage general system parameters (maximum SOS time, emergency number, administrative email, notifications, visual parameters)
> **So that** the system can be adjusted without depending on a code change

```gherkin
Scenario 1: Modify a general parameter
  Given I am an administrator
  When I modify a general parameter
  Then the change is applied to the system without requiring a new deployment (FR13.8)
```

| Field               | Value                  |
| ------------------- | ---------------------- |
| Story Points        | 3                      |
| Priority            | Medium                 |
| Target Sprint       | To Be Defined          |
| Status              | Backlog                |
| Dependencies        | None                   |
| Affected Service(s) | `features/admin` (web) |

---

### HU-WEB-ADMIN-005 — Manage Reported Users {#HU-WEB-ADMIN-005}

**Epic:** EP-004

> **As** a web platform administrator
> **I want** to view reported users, review the reason for the report, and perform administrative actions
> **So that** the system has a moderation mechanism for misuse

```gherkin
Scenario 1: Review a report
  Given there is a report about a user
  When I review it
  Then I can see the reason and perform the corresponding administrative action (FR13.10)
```

| Field               | Value                  |
| ------------------- | ---------------------- |
| Story Points        | 3                      |
| Priority            | Medium                 |
| Target Sprint       | To Be Defined          |
| Status              | Backlog                |
| Dependencies        | None                   |
| Affected Service(s) | `features/admin` (web) |

## Formalized Stories — Gaps Closed

FR4.4, FR4.5, and FR5.4 did not generate a separate user story: they were added as AC3 within HU-MOVIL-001 because they are refinements of the same alert activation story. The same applies to FR9.2, which was added as AC3 to HU-MOVIL-004. The remaining FRs that did not have a user story now have their own story above (HU-MOVIL-005 to 009, HU-WEB-ADMIN-002 to 005).

## Correlations

* Complete template with DoD checklist → `04-requirements/_template-hu.md`
* Non-functional requirements → `04-requirements/non-functional.md`
* Traceability matrix → `04-requirements/traceability-matrix.md`
