# Traceability Matrix — Woman Alert

> Real status (confirmed by the team): the mobile application has been under development for quite some time — only details remain to be polished/improved; it is not being developed from scratch. The web dashboard (user and admin) is being reconstructed based on instructor recommendations. The backend has not yet been started, but the team has already consulted and defined the structure that will be used. Therefore, there are still no real test files — the "Tests that confirm it" column remains as "Pending" instead of inventing names of files that do not exist.

## FR → US → Tests → Service

| FR ID       | Description (summary)                                                          | US(s)              | Tests that confirm it | Service                             | Status                  |
| ----------- | ------------------------------------------------------------------------------ | ------------------ | --------------------- | ----------------------------------- | ----------------------- |
| FR2.2       | Emergency contact management                                                   | HU-MOVIL-002       | Pending               | Contacts Module                     | ✅ Done                  |
| FR4.1       | Alert message and location delivery                                            | HU-MOVIL-001       | Pending               | Alerts Module                       | 🟡 In Progress          |
| FR4.2       | Notification to emergency contacts                                             | HU-MOVIL-001       | Pending               | Alerts Module                       | 🟡 In Progress          |
| FR4.3       | Location sharing during an active alert                                        | HU-MOVIL-001       | Pending               | Alerts Module                       | 🟡 In Progress          |
| FR5.1       | Widget activation                                                              | HU-MOVIL-001       | Pending               | features/BotonActivacion (mobile)   | 🟡 In Progress          |
| FR5.2       | Sudden movement activation                                                     | HU-MOVIL-001       | Pending               | features/BotonActivacion (mobile)   | 🟡 In Progress          |
| FR5.3       | Physical button activation                                                     | HU-MOVIL-001       | Pending               | features/BotonActivacion (mobile)   | 🟡 In Progress          |
| FR6.1       | Video recording as evidence                                                    | HU-MOVIL-003       | Pending               | Evidence Module                     | 🟡 In Progress          |
| FR6.2       | Audio recording as evidence                                                    | HU-MOVIL-003       | Pending               | Evidence Module                     | 🟡 In Progress          |
| FR6.3       | Evidence storage and backup                                                    | HU-MOVIL-003       | Pending               | Evidence Module                     | 🟡 In Progress          |
| FR7.1       | Emergency resources directory                                                  | HU-MOVIL-004       | Pending               | Zones Module / features/mapa        | 🟡 In Progress          |
| FR9.1       | Safe/risk zone visualization                                                   | HU-MOVIL-004       | Pending               | Zones Module                        | 🟡 In Progress          |
| FR9.3       | Nearby help centers                                                            | HU-MOVIL-004       | Pending               | Zones Module                        | 🟡 In Progress          |
| FR12.1      | Web authentication                                                             | HU-WEB-USUARIA-001 | Pending               | features/auth (web)                 | 🟡 Under Reconstruction |
| FR12.2      | Profile viewing (web)                                                          | HU-WEB-USUARIA-001 | Pending               | features/dashboard (web)            | 🟡 Under Reconstruction |
| FR12.3      | Alert viewing (web)                                                            | HU-WEB-USUARIA-001 | Pending               | features/dashboard (web)            | 🟡 Under Reconstruction |
| FR12.4      | History viewing (web)                                                          | HU-WEB-USUARIA-001 | Pending               | features/dashboard (web)            | 🟡 Under Reconstruction |
| FR12.6      | Evidence visualization (web)                                                   | HU-WEB-USUARIA-001 | Pending               | features/dashboard (web)            | 🟡 Under Reconstruction |
| FR13.1      | User management (admin)                                                        | HU-WEB-ADMIN-001   | Pending               | features/admin (web)                | 🟡 Under Reconstruction |
| FR13.2      | Administrative alert management                                                | HU-WEB-ADMIN-001   | Pending               | features/admin (web)                | 🟡 Under Reconstruction |
| FR13.4      | Report management (PDF/Excel/printing)                                         | HU-WEB-ADMIN-001   | Pending               | features/admin (web)                | 🟡 Under Reconstruction |
| FR13.9      | Administrative action auditing                                                 | HU-WEB-ADMIN-001   | Pending               | features/admin (web)                | 🟡 Under Reconstruction |
| FR1.1–FR1.6 | App permissions (contacts, calls, location, notifications, microphone, camera) | HU-MOVIL-005       | Pending               | Mobile operating system permissions | 🔴 Pending              |
| FR2.1       | Account creation                                                               | HU-MOVIL-006       | Pending               | Authentication Module               | 🔴 Pending              |
| FR2.3       | Account access recovery                                                        | HU-MOVIL-006       | Pending               | Authentication Module               | 🔴 Pending              |
| FR3.1       | Interactive guide                                                              | HU-MOVIL-007       | Pending               | features/inicio (mobile)            | 🔴 Pending              |
| FR3.2       | Current and registered locations on the home screen                            | HU-MOVIL-007       | Pending               | features/inicio (mobile)            | 🔴 Pending              |
| FR3.3       | Reminders and warnings on the home screen                                      | HU-MOVIL-007       | Pending               | features/inicio (mobile)            | 🔴 Pending              |
| FR4.4       | Record activation method as a label (not as priority)                          | HU-MOVIL-001 (AC3) | Pending               | Alerts Module                       | 🟡 In Progress          |
| FR4.5       | Reminders or mini-alerts                                                       | HU-MOVIL-001 (AC3) | Pending               | Alerts Module                       | 🟡 In Progress          |
| FR5.4       | Discreet activation confirmation                                               | HU-MOVIL-001 (AC3) | Pending               | features/BotonActivacion (mobile)   | 🟡 In Progress          |
| FR8.1       | High-priority notifications                                                    | HU-MOVIL-009       | Pending               | Notifications Module                | 🔴 Pending              |
| FR8.2       | Notifications visible during an active alert                                   | HU-MOVIL-009       | Pending               | Notifications Module                | 🔴 Pending              |
| FR9.2       | Zone registration                                                              | HU-MOVIL-004 (AC3) | Pending               | Zones Module                        | 🟡 In Progress          |
| FR10.1      | Language change                                                                | HU-MOVIL-008       | Pending               | features/configuracion (mobile)     | 🔴 Pending              |
| FR11.1      | Theme change                                                                   | HU-MOVIL-008       | Pending               | features/configuracion (mobile)     | 🔴 Pending              |
| FR13.3      | Zone and risk-level management (admin)                                         | HU-WEB-ADMIN-002   | Pending               | features/admin (web)                | 🔴 Pending              |
| FR13.5      | Viewing a user's contacts (admin)                                              | HU-WEB-ADMIN-003   | Pending               | features/admin (web)                | 🔴 Pending              |
| FR13.6      | Viewing a user's device (admin)                                                | HU-WEB-ADMIN-003   | Pending               | features/admin (web)                | 🔴 Pending              |
| FR13.7      | Evidence viewing (admin)                                                       | HU-WEB-ADMIN-003   | Pending               | features/admin (web)                | 🔴 Pending              |
| FR13.8      | General system parameters                                                      | HU-WEB-ADMIN-004   | Pending               | features/admin (web)                | 🔴 Pending              |
| FR13.10     | Reported user management                                                       | HU-WEB-ADMIN-005   | Pending               | features/admin (web)                | 🔴 Pending              |

## NFR Validation Matrix

| NFR ID | Description                        | How It Is Validated                  | Tool                     | Status     |
| ------ | ---------------------------------- | ------------------------------------ | ------------------------ | ---------- |
| RNF5   | Alert dispatch ≤2s in 95% of cases | Load testing                         | To be defined (e.g., k6) | 🔴 Pending |
| RNF7   | Location accuracy of 10–20m        | Comparison against GPS reference     | To be defined            | 🔴 Pending |
| RNF8   | HTTPS/TLS + AES-256 encryption     | Security test / configuration review | To be defined            | 🔴 Pending |
| RNF14  | Role-based access control          | Authorization test (user vs. admin)  | To be defined            | 🔴 Pending |

## Reverse Traceability: US → FR

| US                 | Title                                         | FR(s) Implemented                                             | Sprint        |
| ------------------ | --------------------------------------------- | ------------------------------------------------------------- | ------------- |
| HU-MOVIL-001       | Activate Emergency Alert                      | FR4.1, FR4.2, FR4.3, FR4.4, FR4.5, FR5.1, FR5.2, FR5.3, FR5.4 | To be defined |
| HU-MOVIL-002       | Manage Emergency Contacts                     | FR2.2                                                         | To be defined |
| HU-MOVIL-003       | Record Evidence During an Alert               | FR6.1, FR6.2, FR6.3                                           | To be defined |
| HU-MOVIL-004       | View Map and Emergency Resources              | FR7.1, FR9.1, FR9.2, FR9.3                                    | To be defined |
| HU-MOVIL-005       | Grant Required App Permissions                | FR1.1, FR1.2, FR1.3, FR1.4, FR1.5, FR1.6                      | To be defined |
| HU-MOVIL-006       | Register and Recover My Account               | FR2.1, FR2.3                                                  | To be defined |
| HU-MOVIL-007       | View the Home Guide and System Notifications  | FR3.1, FR3.2, FR3.3                                           | To be defined |
| HU-MOVIL-008       | Customize App Language and Theme              | FR10.1, FR11.1                                                | To be defined |
| HU-MOVIL-009       | Receive High-Priority Emergency Notifications | FR8.1, FR8.2                                                  | To be defined |
| HU-WEB-USUARIA-001 | View History and Status from the Web Portal   | FR12.1, FR12.2, FR12.3, FR12.4, FR12.6                        | To be defined |
| HU-WEB-ADMIN-001   | Manage Users, Alerts, and Reports             | FR13.1, FR13.2, FR13.4, FR13.9                                | To be defined |
| HU-WEB-ADMIN-002   | Manage Zones and Their Risk Level             | FR13.3                                                        | To be defined |
| HU-WEB-ADMIN-003   | View a User's Contacts, Devices, and Evidence | FR13.5, FR13.6, FR13.7                                        | To be defined |
| HU-WEB-ADMIN-004   | Configure General System Parameters           | FR13.8                                                        | To be defined |
| HU-WEB-ADMIN-005   | Manage Reported Users                         | FR13.10                                                       | To be defined |

## Status Legend

| Status                  | Meaning                                                            |
| ----------------------- | ------------------------------------------------------------------ |
| ✅ Done                  | Implemented, tested, and in production                             |
| 🟡 In Progress          | Currently under development                                        |
| 🟡 Under Reconstruction | A previous version existed and is being rebuilt; not yet completed |
| 🔴 Pending              | In the backlog; development has not started                        |
| ⏸ Blocked               | Has an external blocker                                            |

## Identified Gaps (Requirements Without Coverage)

| Gap Type               | Description                                                                                                                                                                                                                                                                                                                                                   | Required Action                                                                            |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| ~~FR without US~~      | Closed: the FRs that previously had no US (permissions, account, home guide, notifications, language/theme, and the remaining administrative FR13 requirements) now have their own user stories (HU-MOVIL-005 to 009, HU-WEB-ADMIN-002 to 005); FR4.4, FR4.5, FR5.4, and FR9.2 were added as additional acceptance criteria for HU-MOVIL-001 and HU-MOVIL-004 | None — the team only needs to prioritize them in the backlog                               |
| US without tests       | None of the 15 USs currently has a written acceptance test — including HU-MOVIL-002, which is already implemented in code but has no confirmed automated test                                                                                                                                                                                                 | Add acceptance tests for what is already implemented, and as the remaining work progresses |
| NFR without validation | RNF5, RNF7, RNF8, RNF14 (and the remaining NFRs) do not yet have a defined tool or test                                                                                                                                                                                                                                                                       | Define them with the technical team once the testing stack is selected                     |

## How to Maintain This Matrix

* When a new US is created: add its row to "FR → US → Tests → Service".
* When a test is written: record the actual file in the corresponding column (stop using "Pending").
* When a US is completed: change its status to ✅.
* During each planning session: review the gaps in this table and assign them to someone.

## Correlations

* User Stories → `04-requirements/user-stories.md`
* Non-Functional Requirements → `04-requirements/non-functional.md`
* Functional Requirements → `04-requirements/functional.md`
