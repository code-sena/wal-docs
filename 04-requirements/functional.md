# Functional Requirements — Woman Alert

> All FRs come from the SRS (Section 3.2). The "Source (US)" column indicates whether a user story already exists in `03-producto/product-backlog.md`; where it says "Pending", it represents a real gap — an FR without a user story yet, not invented data. For the actual implementation status of each FR (done / in progress / under reconstruction / pending), see `04-requisitos/traceability-matrix.md` — this table does not repeat it to avoid having the same data in two locations that could become out of sync.

## Mobile (primary channel)

| ID     | Module               | Description                                                                                                                                                                                                | Source (US)        | Priority |
| ------ | -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------ | -------- |
| FR1.1  | Permissions          | The app must request access to the contact list to register emergency contacts                                                                                                                             | HU-MOVIL-005       | High     |
| FR1.2  | Permissions          | The app must request the necessary permissions to make emergency calls                                                                                                                                     | HU-MOVIL-005       | Medium   |
| FR1.3  | Permissions          | The app must request access to location services to obtain and share the user's location                                                                                                                   | HU-MOVIL-005       | High     |
| FR1.4  | Permissions          | The app must request permission to display alerts, status, and event notifications                                                                                                                         | HU-MOVIL-005       | High     |
| FR1.5  | Permissions          | The app must request microphone access to record audio/voice messages                                                                                                                                      | HU-MOVIL-005       | Medium   |
| FR1.6  | Permissions          | The app must request camera access to capture evidence                                                                                                                                                     | HU-MOVIL-005       | Medium   |
| FR2.1  | Users                | The system must allow users to create an account with the required information                                                                                                                             | HU-MOVIL-006       | High     |
| FR2.2  | Users                | The user must be able to add, view, modify, and delete their emergency contacts                                                                                                                            | HU-MOVIL-002       | High     |
| FR2.3  | Users                | The user must be able to recover access to their account                                                                                                                                                   | HU-MOVIL-006       | Medium   |
| FR3.1  | Home/Dashboard       | The app must display an interactive guide to its main features                                                                                                                                             | HU-MOVIL-007       | Low      |
| FR3.2  | Home/Dashboard       | The user must be able to view their current location and registered locations                                                                                                                              | HU-MOVIL-007       | Medium   |
| FR3.3  | Home/Dashboard       | The app must be able to display relevant reminders, warnings, and updates                                                                                                                                  | HU-MOVIL-007       | Low      |
| FR4.1  | Alerts               | The system must send alert information together with the user's location through the configured channels                                                                                                   | HU-MOVIL-001       | High     |
| FR4.2  | Alerts               | When an alert is activated, the system must notify the associated emergency contacts                                                                                                                       | HU-MOVIL-001       | High     |
| FR4.3  | Alerts               | The system must allow the user's location to be shared while an alert is active                                                                                                                            | HU-MOVIL-001       | High     |
| FR4.4  | Alerts               | The system must record which activation method triggered each alert (only as a label for history/analysis; it does not change the priority or urgency with which it is processed, which is always maximum) | HU-MOVIL-001 (AC3) | Medium   |
| FR4.5  | Alerts               | The system may generate reminders or mini-alerts according to configured rules                                                                                                                             | HU-MOVIL-001 (AC3) | Low      |
| FR5.1  | Activation           | The user must be able to activate an alert through a quick-access widget                                                                                                                                   | HU-MOVIL-001       | High     |
| FR5.2  | Activation           | The system must be able to detect sudden movement as an activation mechanism                                                                                                                               | HU-MOVIL-001       | High     |
| FR5.3  | Activation           | The system must be able to use physical device buttons as an activation mechanism                                                                                                                          | HU-MOVIL-001       | High     |
| FR5.4  | Activation           | The system must be able to request discreet confirmation of the activation when applicable                                                                                                                 | HU-MOVIL-001 (AC3) | Medium   |
| FR6.1  | Evidence             | The app must allow video to be captured as emergency evidence                                                                                                                                              | HU-MOVIL-003       | Medium   |
| FR6.2  | Evidence             | The app must allow audio to be recorded as evidence                                                                                                                                                        | HU-MOVIL-003       | Medium   |
| FR6.3  | Evidence             | The system must store and back up evidence associated with an alert                                                                                                                                        | HU-MOVIL-003       | Medium   |
| FR7.1  | Emergency Resources  | The system must allow users to consult available emergency numbers, organizations, and help centers                                                                                                        | HU-MOVIL-004       | Medium   |
| FR8.1  | Notifications        | The system must be able to generate high-priority notifications related to emergencies                                                                                                                     | HU-MOVIL-009       | Medium   |
| FR8.2  | Notifications        | The system must keep certain notifications visible while an alert is active                                                                                                                                | HU-MOVIL-009       | Medium   |
| FR9.1  | Smart Map            | The system must allow users to view safe and high-risk areas                                                                                                                                               | HU-MOVIL-004       | Medium   |
| FR9.2  | Smart Map            | The system must allow users to register zones when they have permission to do so                                                                                                                           | HU-MOVIL-004 (AC3) | Low      |
| FR9.3  | Smart Map            | The system must allow users to locate nearby help centers using geographic information                                                                                                                     | HU-MOVIL-004       | Medium   |
| FR10.1 | Internationalization | The app must allow the interface language to be changed                                                                                                                                                    | HU-MOVIL-008       | Low      |
| FR11.1 | Personalization      | The app must allow the visual theme to be changed (light/dark)                                                                                                                                             | HU-MOVIL-008       | Low      |

## Web Platform — Authenticated User

| ID     | Module   | Description                                                                             | Source (US)        | Priority |
| ------ | -------- | --------------------------------------------------------------------------------------- | ------------------ | -------- |
| FR12.1 | User Web | The web platform must allow the user to authenticate                                    | HU-WEB-USUARIA-001 | High     |
| FR12.2 | User Web | The user must be able to view and manage their profile information from the web         | HU-WEB-USUARIA-001 | Medium   |
| FR12.3 | User Web | The user must be able to view the alerts associated with their account from the web     | HU-WEB-USUARIA-001 | Medium   |
| FR12.4 | User Web | The user must be able to view their alert, location, and event history from the web     | HU-WEB-USUARIA-001 | Medium   |
| FR12.5 | User Web | The user must be able to view information about their locations and alerts from the web | HU-WEB-USUARIA-001 | Medium   |
| FR12.6 | User Web | The user must be able to view evidence associated with their own alerts from the web    | HU-WEB-USUARIA-001 | Medium   |

## Web Platform — Administrator

| ID      | Module    | Description                                                                                                                                                     | Source (US)      | Priority |
| ------- | --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- | -------- |
| FR13.1  | Admin Web | The administrator must be able to view and manage registered users                                                                                              | HU-WEB-ADMIN-001 | High     |
| FR13.2  | Admin Web | The administrator must be able to view alerts generated by users                                                                                                | HU-WEB-ADMIN-001 | High     |
| FR13.3  | Admin Web | The administrator must be able to create, view, modify, and delete zones and their risk level                                                                   | HU-WEB-ADMIN-002 | Medium   |
| FR13.4  | Admin Web | The administrator must be able to generate filtered reports (date/type/status/city) and download them in PDF/Excel format or print them                         | HU-WEB-ADMIN-001 | Medium   |
| FR13.5  | Admin Web | The administrator must be able to view the emergency contacts of a specific user                                                                                | HU-WEB-ADMIN-003 | Medium   |
| FR13.6  | Admin Web | The administrator must be able to view information about users' devices (brand, model, version, GPS status, permission status)                                  | HU-WEB-ADMIN-003 | Low      |
| FR13.7  | Admin Web | The administrator must be able to view evidence related to alerts                                                                                               | HU-WEB-ADMIN-003 | Medium   |
| FR13.8  | Admin Web | The administrator must be able to manage general system parameters (maximum SOS time, emergency number, administrative email, notifications, visual parameters) | HU-WEB-ADMIN-004 | Medium   |
| FR13.9  | Admin Web | The system must record administrator actions (administrator, action, date, time, IP)                                                                            | HU-WEB-ADMIN-001 | Medium   |
| FR13.10 | Admin Web | The administrator must be able to view reported users, review the reasons, and execute administrative actions                                                   | HU-WEB-ADMIN-005 | Medium   |

## Note on Anonymity

The SRS explicitly clarifies that there is no FR dedicated to "anonymity": the possibility of sending an alert without identifying oneself should be included as an acceptance criterion for FR4/FR5, rather than as a separate module. This is noted here so that this detail is not lost when transferring the information to `traceability-matrix.md`.

## Correlations

* Formalized user stories → `04-requisitos/user-stories.md`
* Non-functional requirements → `04-requisitos/non-functional.md`
* Traceability matrix → `04-requisitos/traceability-matrix.md`
