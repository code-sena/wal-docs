# Functional Requirements — Woman Alert

> All FRs come from SRS v2.6 (28/10/2026), which renamed the requirements to the `RF-<MODULE>-<NNN>` scheme. The "Previous ID" column keeps the identifier used in earlier versions of the SRS (RF1.1 to RF20.2) so older documents can still be traced. The status is the one recorded in the SRS after comparing each requirement with the code published in `main`: **Implemented** means the feature exists in the code; it is only considered **verified** when there is a recorded test (see `04-requirements/traceability-matrix.md` and `11-quality/testing-strategy.md`). The full acceptance criteria are in Document 02.

Summary: 102 functional requirements — 99 implemented, 3 in development. RF-ACT-004 (lock screen and power button activation) was retired on 23/10/2026 and is not listed.

## Mobile (primary channel)

| ID | Module | Description | Previous ID | User story | Status |
|----|--------|-------------|-------------|------------|--------|
| RF-PER-001 | Permissions | Request permission to access the phone's contact list | RF1.1 | — | Implemented |
| RF-PER-002 | Permissions | Request the location permissions needed to see locations, record routes and update the location during an alert | RF1.3 | — | Implemented |
| RF-PER-003 | Permissions | Request permission to show system notifications about alerts, status changes and other events | RF1.4 | — | Implemented |
| RF-PER-004 | Permissions | Request microphone access before any feature that records audio | RF1.5 | — | Implemented |
| RF-PER-005 | Permissions | Request camera access before any feature that records video as evidence | RF1.6 | — | Implemented |
| RF-PER-006 | Permissions | Show a permissions and protection section with the status of every permission and phone setting used by the protection features | New | — | Implemented |
| RF-USU-001 | Account and access | Create an account with the registration form, validating the required data before completing it | RF2.1 | HU-USU-001 | Implemented |
| RF-USU-002 | Account and access | Recover access with prior verification (6-digit code by email, valid 15 min, 5 attempts) and an effective password reset | RF2.3 | HU-USU-002 | Implemented |
| RF-USU-003 | Account and access | Log in to the app with email and password or with a Google account; complete missing registration data when needed | RF2.4 | HU-USU-001 | Implemented |
| RF-USU-004 | Account and access | Log out from the app options, ending the authenticated session | RF2.5 | — | Implemented |
| RF-USU-006 | Account and access | Delete her own account from the app | — | — | Implemented |
| RF-USU-007 | Account and access | Accept the terms and conditions and the privacy policy to complete the registration in the app and on the web | New | — | In development |
| RF-USU-008 | Account and access | View and edit the profile: photo, name, email, phone, birth date, department and municipality | New | — | Implemented |
| RF-CON-001 | Trusted contacts | Register, view, edit and delete the trusted contacts that receive the alert notices | RF2.2 | HU-CON-001 | Implemented |
| RF-INI-001 | Start and guide | Provide a tutorial that explains the main features and the purpose of each permission | RF3.1 | — | Implemented |
| RF-ALE-001 | Alerts | Activate an alert with the activation methods and register it with its status, date, time and available location | RF4.1 | HU-ACT-001 | Implemented |
| RF-ALE-002 | Alerts | Notify every trusted contact automatically when the alert is activated, recording the result per recipient and channel (email and push) | RF4.2 | HU-ALE-001 | Implemented |
| RF-ALE-003 | Alerts | Obtain and update the user's location while the alert is active, when permissions and connectivity allow it | RF4.3 | HU-ALE-002 | Implemented |
| RF-ALE-004 | Alerts | Identify the alert modality according to the activation method used | RF4.4 | — | Implemented |
| RF-ALE-005 | Alerts | Ask follow-up questions in the defined situations and notify the contacts if the user does not answer in time | RF4.5 | — | Implemented |
| RF-ALE-006 | Alerts | Resend the notice once per alert if no contact opens it within 5 minutes, using the latest location | RF4.6 | HU-ALE-001 | Implemented |
| RF-ALE-007 | Alerts | Manage the alert duration: question at minute 25, 30-minute extension if she answers yes, saving mode and notice if she does not, absolute limit of 2 hours | RF16.1 | — | Implemented |
| RF-ALE-009 | Alerts | Warn the contacts when an active alert stops receiving the location for the configured time, with the last recorded location | — | — | Implemented |
| RF-ALE-010 | Alerts | Exchange text messages between the user and her contacts during the alert | — | — | Implemented |
| RF-ALE-011 | Alerts | Send each contact an email with a link that shows the map, route, messages, time active, number of locations and recordings | New | — | Implemented |
| RF-ALE-012 | Alerts | Show the alert timer and let the user finish the episode with "I'm OK" (cancellation PIN required if configured) | New | — | Implemented |
| RF-ALE-013 | Alerts | Let the user check the tracking of the notices sent to each contact from the alert history | New | — | Implemented |
| RF-ACT-001 | Activation methods | Activate an alert by detecting a shake of the phone through the accelerometer | RF5.1 | — | Implemented |
| RF-ACT-002 | Activation methods | Start an alert from a quick-access widget on the home screen | RF5.2 | — | Implemented |
| RF-ACT-003 | Activation methods | Start an alert addressed to a specific contact from the contacts section | RF5.3 | — | Implemented |
| RF-ACT-005 | Activation methods | Start an alert manually with the emergency button on the home screen, followed by a 5-second countdown | RF5.5 | HU-ACT-001 | Implemented |
| RF-ACT-006 | Activation methods | Open a 5-second window after any activation request during which the alert can be cancelled before it is issued (RN-08) | RF5.6 | HU-ACT-002 | Implemented |
| RF-ACT-007 | Activation methods | Configure a 4 to 6 digit duress PIN that simulates the cancellation while the contacts are notified silently | New | — | Implemented |
| RF-ACT-008 | Activation methods | Configure a 4 to 6 digit cancellation PIN required to cancel or finish an issued alert | New | — | Implemented |
| RF-ACT-009 | Activation methods | Cancel the activation during the 5-second window by pressing volume-down four times quickly | New | — | Implemented |
| RF-ACT-010 | Activation methods | Enable or disable each activation method from the settings (SOS button, single contact, shake, trip and widget) | New | — | Implemented |
| RF-ACT-011 | Activation methods | Treat the "Notify me if I don't arrive" trip as an activation method in which the server creates the alert | New | — | Implemented |
| RF-EVI-001 | Evidence | Record video with the camera as evidence, after the permission is granted | RF6.1 | HU-EVI-001 | Implemented |
| RF-EVI-002 | Evidence | Record audio with the microphone as evidence, after the permission is granted | RF6.2 | HU-EVI-001 | Implemented |
| RF-EVI-003 | Evidence | Store the audio and video evidence, link it to the alert and keep it for a defined period (30 days) | RF6.3 | HU-EVI-001 | Implemented |
| RF-EVI-004 | Evidence | Keep a copy of the evidence in the app's internal storage, outside the gallery, with options to download, share or delete it | — | — | Implemented |
| RF-REC-001 | Emergency resources | Provide an assistance module with the three assistance channels: 123, purple line and mental health line | RF7.1 | HU-REC-001 | Implemented |
| RF-REC-002 | Emergency resources | Open the phone dialer with the selected number; the user starts the call manually | RF7.2 y RF1.2 | HU-REC-001 | Implemented |
| RF-NOT-001 | Notifications | Show informative notices and messages about states, warnings and results of the actions | RF3.3 | — | Implemented |
| RF-NOT-002 | Notifications | Show emergency notifications according to the permissions granted | RF8.1 | — | Implemented |
| RF-NOT-003 | Notifications | Keep a visible notification while an alert is active, when the operating system allows it | RF8.2 | — | Implemented |
| RF-NOT-004 | Notifications | Show the alerts issued by people who registered the user as a trusted contact | — | — | Implemented |
| RF-NOT-005 | Notifications | Enable discreet mode (neutral texts in fixed notifications) and configure notification preferences | New | — | Implemented |
| RF-NOT-006 | Notifications | Notify the user's other phones when the account signs in from a new phone | New | — | Implemented |
| RF-NOT-007 | Notifications | Show the list of notifications received, such as risk zone notices and end of a contact's alert, with the elapsed time | New | — | Implemented |
| RF-UBI-001 | Map and location | Show the current location on the map when location services and permissions are available, including the last recorded location | RF9.1 y RF3.2 | HU-UBI-001 | Implemented |
| RF-UBI-002 | Map and location | Consult the recorded routes from the map module | RF9.2 | — | Implemented |
| RF-UBI-003 | Map and location | Consult the frequent or saved places | RF9.3 | — | Implemented |
| RF-UBI-004 | Map and location | Consult the geographic information linked to the user's alerts | RF9.4 | — | Implemented |
| RF-UBI-005 | Map and location | Show auxiliary zones on the mobile map as circles, together with the current location | RF18.2 | — | Implemented |
| RF-UBI-006 | Map and location | Save a zone or place with name, address, city, neighborhood, description, notes and safety level, and edit or delete it | New | — | Implemented |
| RF-UBI-007 | Map and location | Create a route by choosing origin and destination on the map or from saved places, and save it | New | — | Implemented |
| RF-UBI-008 | Map and location | Share the current or a saved location with the phone's share options and open navigation in an external maps app | New | — | Implemented |
| RF-HIS-001 | History | Show in the mobile history the contacts notified for each alert and the notice status per channel | RF18.1 | HU-HIS-001 | Implemented |
| RF-HIS-002 | History | Show the alert history with totals by status (sent, cancelled, in progress) and, per alert, date, reason, status and location | New | — | Implemented |
| RF-IDI-001 | Language | Select the interface language between Spanish, English, French and Portuguese (app and web) | RF10.1 | — | Implemented |
| RF-TEM-001 | Visual theme | Customize the appearance: six themes in the app (light, dark and four color themes) and a theme service on the web | RF11.1 y RNF009 | — | Implemented |
| RF-TRA-001 | Trips | Start a trip with destination and estimated arrival time from the current location; only one active trip per user | — | — | Implemented |
| RF-TRA-002 | Trips | Create an alert from the server, without depending on the phone's signal, when arrival is not confirmed or the signal is lost during the trip | — | — | Implemented |
| RF-TRA-003 | Trips | Accompany the user automatically when she leaves a frequent place and notify her contacts with the last location when needed | New | — | Implemented |

## Web Platform — Authenticated User

| ID | Module | Description | Previous ID | User story | Status |
|----|--------|-------------|-------------|------------|--------|
| RF-USU-005 | Account and access | Log in to the web with a Google account through Firebase Authentication, with a valid configuration per domain | RF19.1 | — | Implemented |
| RF-USU-009 | Account and access | Change the account password from the web settings | New | — | Implemented |
| RF-WEB-001 | User web portal | Log in to the web with email and password or with Google and access the features authorized for the account | RF12.1 | HU-WEB-001 | Implemented |
| RF-WEB-002 | User web portal | View and edit the profile on the web; today the settings only allow changing password, theme and notifications and requesting deletion | RF12.2 | — | In development |
| RF-WEB-003 | User web portal | Consult the alerts registered for the account | RF12.3 | HU-WEB-001 | Implemented |
| RF-WEB-004 | User web portal | Consult the history of alerts and events of the account | RF12.4 | HU-HIS-001 | Implemented |
| RF-WEB-005 | User web portal | View the location information linked to the alerts and records | RF12.5 | HU-WEB-001 | Implemented |
| RF-WEB-006 | User web portal | Consult the evidence of her own alerts | RF12.6 | HU-EVI-002 | Implemented |
| RF-WEB-007 | User web portal | Offer a landing page that explains Woman Alert, answers frequent questions and offers the APK download | New | — | Implemented |
| RF-WEB-008 | User web portal | Consult and add emergency contacts, synchronized with the app | New | — | Implemented |
| RF-WEB-009 | User web portal | Show the assistance lines, the care centers with directions and the history of manual calls | New | — | Implemented |
| RF-WEB-010 | User web portal | Show the status of the linked phone: permissions, remaining session time, alert reception, last location, connectivity and linked devices | New | — | Implemented |
| RF-WEB-011 | User web portal | Show the alerts of the people who registered the user as an emergency contact | New | — | Implemented |
| RF-WEB-012 | User web portal | Download the audio and video evidence and keep up to five; the rest are deleted after 30 days | New | — | Implemented |
| RF-WEB-013 | User web portal | Show a home page with a summary of the account and the protection status | New | — | Implemented |

## Web Platform — Administrator

| ID | Module | Description | Previous ID | User story | Status |
|----|--------|-------------|-------------|------------|--------|
| RF-CON-002 | Trusted contacts | Configure a limit of trusted contacts per account and validate it on the server (default 5, range 1 to 20) | RF17.1 | HU-CON-001 | Implemented |
| RF-ALE-008 | Alerts | Allow authorized administrators to change the alert status between active, attended, cancelled and expired | RF16.2 | — | Implemented |
| RF-ADM-001 | Administration | Consult and manage registered users with paginated results, according to the role permissions | RF13.1 | HU-ADM-001 | Implemented |
| RF-ADM-002 | Administration | Consult the registered alerts and the information of each event | RF13.2 | HU-ADM-001 | Implemented |
| RF-ADM-003 | Administration | Create, view, edit and delete geographic zones and manage their risk classification | RF13.3 | — | Implemented |
| RF-ADM-004 | Administration | Consult and generate reports with filters by date, type, status and city; create, edit and delete reports and export them to PDF and CSV | RF13.4 | HU-ADM-001 | Implemented |
| RF-ADM-005 | Administration | Consult, per trusted contact, the notice status and the relationship, without showing the contact's personal data | RF13.5 | — | Implemented |
| RF-ADM-006 | Administration | Consult the devices linked to the users and change their status | RF13.6 | — | Implemented |
| RF-ADM-007 | Administration | Consult the evidence linked to the alerts, see statistics and record the review | RF13.7 | — | Implemented |
| RF-ADM-008 | Administration | Consult and modify the enabled configuration parameters, with confirmation before saving and effective application | RF13.8 | HU-ADM-002 | Implemented |
| RF-ADM-009 | Administration | Consult the reports made about users and execute the enabled moderation actions | RF13.10 | HU-ADM-001 | Implemented |
| RF-ADM-010 | Administration | Show the technical status of the system through a restricted service and screen | RF20.1 | — | Implemented |
| RF-ADM-011 | Administration | Show a dashboard with users, attended and cancelled alerts, alerts by time of day, zones with more incidents and comparisons | New | — | Implemented |
| RF-ADM-012 | Administration | Create, view, edit and delete the help points shown by the app | New | — | Implemented |
| RF-AUD-001 | Audit | Record in the audit log the tracked administrative operations with actor, role, IP, date, time, operation and origin (web, mobile or scheduled task) | RF13.9 y RF14.1 | HU-ADM-003 | Implemented |
| RF-AUD-002 | Audit | Record the previous and new value when system parameters change, without exposing secrets | RF14.2 | HU-ADM-002 | In development |
| RF-AUD-003 | Audit | Prevent editing or deleting audit records through the normal operations of the application (RN-04) | RF14.3 | — | Implemented |
| RF-AUD-004 | Audit | Consult, filter and export the audit log and list the actors available for the filters | RF14.4 | HU-ADM-003 | Implemented |
| RF-SES-001 | Admin session | Record the administrative session events: successful login, failed attempt, password change and logout (voluntary or by inactivity) | RF15.1 | — | Implemented |
| RF-SES-002 | Admin session | Close administrative sessions automatically after the configured inactivity threshold | RF15.2 | HU-SES-001 | Implemented |
| RF-SES-003 | Admin session | Let the administrator change the password from "My profile", requiring the current password and at least eight characters | RF15.3 | — | Implemented |
| RF-CFG-001 | Public configuration | Publish to the apps the public configuration, including the minimum allowed app version, without exposing secret parameters | RF17.2 | — | Implemented |

## Note on Anonymity

Woman Alert does not offer anonymous use: every alert belongs to an authenticated account, and the owner of alerts, contacts, locations, routes and zone reports is always taken from the JWT, never from the request body. What the system does protect is the privacy of third parties and the discretion of the user:

- Trusted contacts do not need an account. They follow the alert through a tracking link with 256 random bits; only its hash is stored and it stops working 2 hours after the alert ends.
- The administration never sees the personal data of the contacts: only, per contact, the notice status and the relationship (RF-ADM-005). It only sees the route of active or attended alerts.
- Discreet mode, the duress PIN and cancelling with the volume button exist so that a person watching the phone does not discover the alert.
- Deleting an account requires two administrators and anonymizes the personal data.

## Correlations

- Non-functional requirements → `04-requirements/non-functional.md`
- User stories → `04-requirements/user-stories.md`
- Traceability with use cases and tests → `04-requirements/traceability-matrix.md`
- Business rules and entities → `02-domain/entities-and-rules.md`
- Product backlog → `03-product/product-backlog.md`
- API that implements each module → `07-api/api-contract.md`
