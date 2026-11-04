# User Stories — Woman Alert

> User stories (US) from Software Analysis v3.4, written in the Gherkin format of this folder. They do not replace the formal requirements of SRS v2.6: they check that every use case answers a real need. The status is taken from the requirements linked to each story; "Done" means the feature exists in the code published in `main` and "pending" notes say which manual test is still missing. The team did not record sprint estimates, so story points are not filled in.

## Backlog Status

| Current | Total US | Refined | In Progress | Completed |
| ------- | -------- | ------- | ----------- | --------- |
| Current | 17       | 17      | 1           | 16        |

*(Updated on 28/10/2026 against SRS v2.6 and the code in `main`. Features were frozen on 24/10/2026.)*

## Epics

| Epic ID | Description           | Scope                                                                                         |
| ------- | --------------------- | --------------------------------------------------------------------------------------------- |
| EP-001  | Alerts and Activation | Activation, cancellation, notices to contacts, location during the alert and evidence (RF-ACT, RF-ALE, RF-EVI) |
| EP-002  | User Management       | Account, recovery, trusted contacts, map and assistance (RF-USU, RF-CON, RF-UBI, RF-REC)                  |
| EP-003  | User Web Portal       | History, evidence and account information from the web (RF-WEB, RF-HIS)                       |
| EP-004  | Administrative Panel  | Management, configuration, audit and session of the administrator role (RF-ADM, RF-AUD, RF-SES)          |

---

### HU-USU-001 — Create an Account and Log In {#HU-USU-001}

**Epic:** EP-002

> **As** an user
> **I want** to create an account and log in
> **So that** I can access my private features

```gherkin
Scenario 1: Valid registration
  Given I fill in the registration form with valid data and accept the terms
  When I submit it
  Then the account is created and the private area opens

Scenario 2: Invalid data
  Given a field does not pass the validation
  When I submit the form
  Then the account is not created and the reason is shown

Scenario 3: Login with Google
  Given I choose to log in with Google
  When the server verifies the token signature
  Then I enter with the same account in the app and on the web
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-USU-01 |
| Requirements        | RF-USU-001, RF-USU-003 |
| Status              | Done |
| Affected Service(s) | Auth (identity), `features/authentication` |

---

### HU-USU-002 — Recover Access to My Account {#HU-USU-002}

**Epic:** EP-002

> **As** an user
> **I want** to recover access if I forget my password
> **So that** I can use my account again

```gherkin
Scenario 1: Code sent
  Given I request the recovery with my email
  When the request is processed
  Then a 6-digit code is sent, valid for 15 minutes, and the answer is the same whether the account exists or not

Scenario 2: Too many attempts
  Given I enter a wrong code 5 times
  When I try again
  Then the code is no longer accepted and I must request a new one

Scenario 3: Reset
  Given the code is valid
  When I save a new password of 8 to 100 characters
  Then the previous password stops working
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-USU-02 |
| Requirements        | RF-USU-002 |
| Status              | Done |
| Affected Service(s) | Auth (identity) |

---

### HU-CON-001 — Keep My Trusted Contacts Updated {#HU-CON-001}

**Epic:** EP-002

> **As** an user
> **I want** to add, view, edit and remove my trusted contacts
> **So that** the notices reach the people I chose

```gherkin
Scenario 1: Add a contact
  Given I have fewer contacts than the configured limit
  When I save a contact with name, phone and email
  Then it appears in the list on the app and the web

Scenario 2: Limit reached
  Given I already have the maximum (5 by default)
  When I try to add another
  Then the server rejects it with 409 and the app shows the allowed maximum

Scenario 3: Change not saved
  Given the request fails
  When I edit a contact
  Then the change is not shown as saved
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-CON-01 |
| Requirements        | RF-CON-001, RF-CON-002 |
| Status              | Done |
| Affected Service(s) | Alert (alerts), `features/contacts` |

---

### HU-ACT-001 — Activate an Alert with an Accessible Method {#HU-ACT-001}

**Epic:** EP-001

> **As** an user
> **I want** to activate an alert with the SOS button, a contact, a shake or the widget
> **So that** I can ask for help when I am at risk

```gherkin
Scenario 1: Alert issued
  Given I start an activation
  When 5 seconds pass without cancelling
  Then the alert is registered with the activation method, date, time and available location

Scenario 2: No connectivity
  Given the phone has no Internet
  When the alert is sent
  Then the app shows the alert as failed and offers to retry instead of presenting it as registered

Scenario 3: Shake with the screen off
  Given the shake method is enabled
  When I shake the phone with the app closed
  Then the native foreground service starts the countdown
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-ACT-01 |
| Requirements        | RF-ACT-005, RF-ALE-001 |
| Status              | Done (manual test with the release APK pending) |
| Affected Service(s) | Alert (alerts), `features/activation`, `modules/shake-sensor` |

---

### HU-ACT-002 — Cancel an Activation Made by Mistake {#HU-ACT-002}

**Epic:** EP-001

> **As** an user
> **I want** to cancel an activation during 5 seconds
> **So that** I avoid an unwanted alert

```gherkin
Scenario 1: Cancel in time
  Given the countdown is running
  When I press cancel or press volume-down four times
  Then no alert is created and I am told it was cancelled

Scenario 2: PIN configured
  Given I configured a cancellation PIN
  When I try to finish an issued alert
  Then the app asks for the PIN before closing it

Scenario 3: Duress PIN
  Given someone forces me to cancel
  When I enter the duress PIN
  Then the app looks cancelled and my contacts receive a silent notice
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-ACT-01 |
| Requirements        | RF-ACT-006 |
| Status              | Done |
| Affected Service(s) | Mobile app |

---

### HU-ALE-001 — Notify My Contacts with My Location {#HU-ALE-001}

**Epic:** EP-001

> **As** an user
> **I want** my contacts to receive a notice with my available location
> **So that** they know I need help

```gherkin
Scenario 1: Notice per contact
  Given the alert was registered
  When the Alert service sends the notices
  Then each contact receives an email with the tracking link and, if they have the app, a push; the result is recorded per contact and channel

Scenario 2: Nobody opens it
  Given no contact opens the notice
  When 5 minutes pass
  Then the notice is resent once with the latest location

Scenario 3: Channel failure
  Given the email or push provider fails
  When the send is processed
  Then the failure is recorded as `failed` and the notice is not shown as sent
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-ALE-01 |
| Requirements        | RF-ALE-002, RF-ALE-006 |
| Status              | Done (end-to-end evidence pending) |
| Affected Service(s) | Alert (notifications) |

---

### HU-ALE-002 — Link My Location to the Active Alert {#HU-ALE-002}

**Epic:** EP-001

> **As** an user
> **I want** my location to be linked to the active alert
> **So that** my contacts have context during the event

```gherkin
Scenario 1: Periodic location
  Given the alert is active and the permission is granted
  When time passes
  Then the app sends the location every 15 s for 5 minutes and then every 30 s, also with the app closed

Scenario 2: Lost signal
  Given the server stops receiving locations
  When 5 minutes pass
  Then the contacts are warned with the last recorded location

Scenario 3: No location
  Given the location could not be obtained
  When the screen is updated
  Then it is not presented as updated
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-ALE-02 |
| Requirements        | RF-ALE-003 |
| Status              | Done |
| Affected Service(s) | Alert (alerts), Kotlin foreground service |

---

### HU-HIS-001 — Check My Previous Alerts {#HU-HIS-001}

**Epic:** EP-003

> **As** an user
> **I want** to check my previous alerts
> **So that** I can review the registered events

```gherkin
Scenario 1: History
  Given I have previous alerts
  When I open the history
  Then I see only my alerts with totals by status, map, route, evidence and contacts notified

Scenario 2: Empty state
  Given I have no alerts
  When I open the history
  Then an empty state is shown

Scenario 3: Error
  Given the service does not answer
  When I open the history
  Then an error message is shown
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-HIS-01 |
| Requirements        | RF-HIS-001, RF-WEB-004 |
| Status              | Done |
| Affected Service(s) | Alert (alerts), app and web |

---

### HU-UBI-001 — See My Last Recorded Location {#HU-UBI-001}

**Epic:** EP-002

> **As** an user
> **I want** to see my last recorded location with its date and time
> **So that** I know where it was recorded for the last time

```gherkin
Scenario 1: Last location
  Given there are recorded points
  When I open the map
  Then the last recorded location is shown with its date and time, separate from the current location

Scenario 2: No data
  Given there are no points
  When I open the map
  Then I am told there is no information and no location is invented
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-UBI-01 |
| Requirements        | RF-UBI-001 |
| Status              | Done |
| Affected Service(s) | Alert (alerts), MapLibre |

---

### HU-REC-001 — Open the Dialer with an Emergency Number {#HU-REC-001}

**Epic:** EP-002

> **As** an user
> **I want** to choose an emergency number and open the dialer
> **So that** I decide whether to make the call

```gherkin
Scenario 1: Dialer
  Given I am in the assistance module
  When I choose 123, the purple line or the mental health line
  Then the dialer opens with the number

Scenario 2: No automatic call
  Given the dialer is open
  When I do nothing
  Then no call is started
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-REC-01 |
| Requirements        | RF-REC-001, RF-REC-002 |
| Status              | Done |
| Affected Service(s) | Mobile app |

---

### HU-EVI-001 — Record Evidence During an Alert {#HU-EVI-001}

**Epic:** EP-001

> **As** an user
> **I want** audio and video to be recorded as evidence of an alert
> **So that** information about the event is kept

```gherkin
Scenario 1: Automatic recording
  Given I granted camera and microphone
  When the alert starts
  Then the app records video and then audio automatically (RN-14)

Scenario 2: Upload queue
  Given there is no connection
  When a chunk finishes
  Then it stays in the queue and is uploaded when the connection returns

Scenario 3: Storage failure
  Given the upload fails
  When the result is processed
  Then the evidence is not shown as saved
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-EVI-01 |
| Requirements        | RF-EVI-001, RF-EVI-002, RF-EVI-003 |
| Status              | Done (field test pending) |
| Affected Service(s) | Evidence, `features/recording` |

---

### HU-EVI-002 — Check My Evidence from the Web {#HU-EVI-002}

**Epic:** EP-003

> **As** an authenticated user
> **I want** to check my evidence from the web
> **So that** I can review the recorded information

```gherkin
Scenario 1: Evidence list
  Given I have evidence
  When I open Evidence
  Then only my authorized evidence is shown and I can download it

Scenario 2: Keep evidence
  Given I mark an alert to keep
  When the 30 days pass
  Then its evidence is not deleted (up to 5 alerts)

Scenario 3: Empty
  Given I have no evidence
  When I open Evidence
  Then I am told there is none
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-EVI-02 |
| Requirements        | RF-WEB-006 |
| Status              | Done |
| Affected Service(s) | Evidence, web `features/dashboard` |

---

### HU-WEB-001 — Consult My Information from the Web Portal {#HU-WEB-001}

**Epic:** EP-003

> **As** an user
> **I want** to consult the information of my account from the web portal
> **So that** I can review it when I am not using the app

```gherkin
Scenario 1: Portal
  Given I log in on the web
  When I open the portal
  Then I see my alerts, locations, contacts, alerts received and device status with their date and time

Scenario 2: Phone off
  Given my phone is off
  When I check the location
  Then the portal shows the last recorded location and does not claim to locate the phone
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-WEB-01, CU-USU-03 |
| Requirements        | RF-WEB-001, RF-WEB-003, RF-WEB-005 |
| Status              | Done (no trips and no personal data editing on the web) |
| Affected Service(s) | Web + Auth + Alert |

---

### HU-ADM-001 — Manage Users, Alerts, Reports and Moderation {#HU-ADM-001}

**Epic:** EP-004

> **As** an administrator
> **I want** to manage users, alerts, reports and user reports according to my role
> **So that** I can attend the operation of the system

```gherkin
Scenario 1: Role check
  Given I do not have the admin role
  When I call `/api/admin/**`
  Then the server answers 403

Scenario 2: Audit
  Given I change the status of an alert
  When the change is saved
  Then it is recorded in the audit log

Scenario 3: Export
  Given I filter the reports
  When I export to PDF or CSV
  Then the file respects the filters and my permissions
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-ADM-01, CU-ADM-02, CU-ADM-04, CU-ADM-06 |
| Requirements        | RF-ADM-001, RF-ADM-002, RF-ADM-004, RF-ADM-009 |
| Status              | Done |
| Affected Service(s) | Auth (admin), web `features/admin` |

---

### HU-ADM-002 — Change System Parameters with Confirmation {#HU-ADM-002}

**Epic:** EP-004

> **As** an administrator
> **I want** to modify the system parameters with prior confirmation
> **So that** I avoid accidental changes

```gherkin
Scenario 1: Confirmation
  Given I change the alert duration
  When I press save
  Then the panel shows a summary and asks for confirmation

Scenario 2: Out of range
  Given I enter a duration outside 30 to 1440 minutes
  When I save
  Then the value is rejected

Scenario 3: Audit of values
  Given the change is saved
  When I check the audit log
  Then today only the names of the modified columns are recorded; recording previous and new values is in development
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-ADM-07 |
| Requirements        | RF-ADM-008, RF-AUD-002 |
| Status              | In Progress |
| Affected Service(s) | Auth (settings, admin) |

---

### HU-ADM-003 — See Who Did Each Operation {#HU-ADM-003}

**Epic:** EP-004

> **As** an administrator
> **I want** to see who did each operation, when and from where
> **So that** I can follow up the changes

```gherkin
Scenario 1: Record
  Given an administrative operation is executed
  When I open the audit log
  Then I see date, actor, role, origin, action and IP

Scenario 2: Filter and export
  Given there are many records
  When I filter by actor and date
  Then I can export the result

Scenario 3: Immutability
  Given someone tries to edit a record
  When the operation reaches the database
  Then the trigger prevents it
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-AUD-01 |
| Requirements        | RF-AUD-001, RF-AUD-004 |
| Status              | Done |
| Affected Service(s) | Auth (admin), database triggers |

---

### HU-SES-001 — Close My Admin Session after Inactivity {#HU-SES-001}

**Epic:** EP-004

> **As** an administrator
> **I want** my session to close after a period of inactivity
> **So that** the panel is protected if I leave

```gherkin
Scenario 1: Inactivity
  Given I stop using the panel
  When the threshold passes
  Then the session closes and the event is recorded

Scenario 2: Return
  Given my session was closed
  When I come back
  Then I must log in again
```

| Field               | Value                                                       |
| ------------------- | ----------------------------------------------------------- |
| Story Points        | Not estimated                                               |
| Use cases           | CU-SES-01 |
| Requirements        | RF-SES-002 |
| Status              | Done |
| Affected Service(s) | Web + Auth (admin) |

---

## Formalized Stories — Gaps Closed

Software Analysis v3.4 has 17 stories for 32 use cases, so not every requirement has its own story. The features added after the first SRS (trips, automatic protection, duress PIN, lost signal notice, chat, public tracking page, notifications inbox and minimum version) were incorporated into SRS v2.6 with proposed identifiers and are traced directly from the requirement to the use case and the test case in `04-requirements/traceability-matrix.md`. The previous stories HU-MOVIL-001 to 009 and HU-WEB-ADMIN-001 to 005 were replaced by these.

## Correlations

* Complete template with DoD checklist → `04-requirements/_template-hu.md`
* Non-functional requirements → `04-requirements/non-functional.md`
* Traceability matrix → `04-requirements/traceability-matrix.md`
