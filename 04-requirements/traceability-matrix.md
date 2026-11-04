# Traceability Matrix — Woman Alert

> Built from SRS v2.6 (102 FR and 25 NFR), Software Analysis v3.4 (32 use cases and 17 user stories) and Test Plan v7.1 (47 test cases), all dated 28/10/2026. The **Status** column is the implementation status recorded in the SRS after comparing it with the code in `main`. The **Verification** column is different: a requirement is only verified when a test case linked to it has a recorded result. On 27/10/2026 the 553 automated tests passed (backend 351, web 39, mobile 163), but those suites are not yet linked one by one to the test cases of the plan, so most requirements still appear as "Not verified".

## FR → US → Tests → Service

| FR ID | Description (summary) | Use case(s) | US(s) | Tests that confirm it | Service | Status | Verification |
| ----- | --------------------- | ----------- | ----- | --------------------- | ------- | ------ | ------------ |
| RF-PER-001 | Request permission to access the phone's contact list | CU-INI-01 | — | — | Mobile app | ✅ Implemented | No test case |
| RF-PER-002 | Request the location permissions needed to see locations, record routes and… | CU-INI-01, CU-TRA-02, CU-UBI-01 | — | CP-UBI-003 | Mobile app | ✅ Implemented | Not verified |
| RF-PER-003 | Request permission to show system notifications about alerts, status changes… | CU-INI-01, CU-NOT-01 | — | — | Mobile app | ✅ Implemented | No test case |
| RF-PER-004 | Request microphone access before any feature that records audio | CU-INI-01 | — | — | Mobile app | ✅ Implemented | No test case |
| RF-PER-005 | Request camera access before any feature that records video as evidence | CU-INI-01 | — | — | Mobile app | ✅ Implemented | No test case |
| RF-PER-006 | Show a permissions and protection section with the status of every permission… | CU-INI-01, CU-TRA-02 | — | CP-TRA-002 | Mobile app | ✅ Implemented | Not verified |
| RF-USU-001 | Create an account with the registration form, validating the required data… | CU-USU-01 | HU-USU-001 | — | Mobile + Auth (identity) | ✅ Implemented | No test case |
| RF-USU-002 | Recover access with prior verification (6-digit code by email, valid 15 min, 5… | CU-USU-02 | HU-USU-002 | CP-USU-001 | Mobile + Auth (identity) | ✅ Implemented | Not verified |
| RF-USU-003 | Log in to the app with email and password or with a Google account; complete… | CU-USU-01 | HU-USU-001 | CP-AUT-001, CP-AUT-002 | Mobile + Auth (identity) | ✅ Implemented | Partly verified |
| RF-USU-004 | Log out from the app options, ending the authenticated session | CU-USU-01 | — | — | Mobile + Auth (identity) | ✅ Implemented | No test case |
| RF-USU-005 | Log in to the web with a Google account through Firebase Authentication, with a… | CU-USU-03 | — | CP-AUT-004 | Web + Auth (identity) | ✅ Implemented | Partly verified |
| RF-USU-006 | Delete her own account from the app | CU-USU-01 | — | — | Mobile + Auth (identity) | ✅ Implemented | No test case |
| RF-USU-007 | Accept the terms and conditions and the privacy policy to complete the… | CU-USU-01 | — | — | Mobile + Auth (identity) | 🟡 In development | No test case |
| RF-USU-008 | View and edit the profile: photo, name, email, phone, birth date, department… | CU-USU-01 | — | — | Mobile + Auth (identity) | ✅ Implemented | No test case |
| RF-USU-009 | Change the account password from the web settings | CU-USU-01 | — | — | Web + Auth (identity) | ✅ Implemented | No test case |
| RF-CON-001 | Register, view, edit and delete the trusted contacts that receive the alert… | CU-CON-01 | HU-CON-001 | CP-CON-001 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-CON-002 | Configure a limit of trusted contacts per account and validate it on the server… | CU-CON-01 | HU-CON-001 | CP-ADM-007 | Auth (settings) + Alert | ✅ Implemented | Not verified |
| RF-INI-001 | Provide a tutorial that explains the main features and the purpose of each… | CU-INI-01 | — | — | Mobile app | ✅ Implemented | No test case |
| RF-ALE-001 | Activate an alert with the activation methods and register it with its status,… | CU-ACT-01 | HU-ACT-001 | CP-SOS-002, CP-SOS-003, CP-BD-001 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-ALE-002 | Notify every trusted contact automatically when the alert is activated,… | CU-ALE-01 | HU-ALE-001 | CP-SOS-004, CP-ALE-001 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-ALE-003 | Obtain and update the user's location while the alert is active, when… | CU-ALE-02 | HU-ALE-002 | CP-UBI-002 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-ALE-004 | Identify the alert modality according to the activation method used | CU-ACT-01 | — | CP-ACT-001 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-ALE-005 | Ask follow-up questions in the defined situations and notify the contacts if… | CU-ALE-03, CU-TRA-01, CU-TRA-02 | — | — | Mobile + Alert (alerts) | ✅ Implemented | No test case |
| RF-ALE-006 | Resend the notice once per alert if no contact opens it within 5 minutes, using… | CU-ALE-01 | HU-ALE-001 | — | Mobile + Alert (alerts) | ✅ Implemented | No test case |
| RF-ALE-007 | Manage the alert duration: question at minute 25, 30-minute extension if she… | CU-ALE-03 | — | CP-ADM-006 | Mobile + Alert (alerts) | ✅ Implemented | Partly verified |
| RF-ALE-008 | Allow authorized administrators to change the alert status between active,… | CU-ADM-02 | — | CP-WEB-002 | Mobile + Alert (alerts) | ✅ Implemented | Partly verified |
| RF-ALE-009 | Warn the contacts when an active alert stops receiving the location for the… | CU-ALE-01 | — | — | Mobile + Alert (alerts) | ✅ Implemented | No test case |
| RF-ALE-010 | Exchange text messages between the user and her contacts during the alert | CU-ALE-01 | — | — | Mobile + Alert (alerts) | ✅ Implemented | No test case |
| RF-ALE-011 | Send each contact an email with a link that shows the map, route, messages,… | CU-ALE-01 | — | — | Mobile + Alert (alerts) | ✅ Implemented | No test case |
| RF-ALE-012 | Show the alert timer and let the user finish the episode with "I'm OK"… | CU-ACT-01 | — | — | Mobile + Alert (alerts) | ✅ Implemented | No test case |
| RF-ALE-013 | Let the user check the tracking of the notices sent to each contact from the… | CU-ALE-01, CU-HIS-01 | — | — | Mobile + Alert (alerts) | ✅ Implemented | No test case |
| RF-ACT-001 | Activate an alert by detecting a shake of the phone through the accelerometer | CU-ACT-01 | — | CP-ACT-001 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-ACT-002 | Start an alert from a quick-access widget on the home screen | CU-ACT-01 | — | CP-ACT-003 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-ACT-003 | Start an alert addressed to a specific contact from the contacts section | CU-ACT-01 | — | — | Mobile + Alert (alerts) | ✅ Implemented | No test case |
| RF-ACT-005 | Start an alert manually with the emergency button on the home screen, followed… | CU-ACT-01 | HU-ACT-001 | CP-SOS-002 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-ACT-006 | Open a 5-second window after any activation request during which the alert can… | CU-ACT-01 | HU-ACT-002 | CP-SOS-001 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-ACT-007 | Configure a 4 to 6 digit duress PIN that simulates the cancellation while the… | CU-ACT-01, CU-TRA-02 | — | CP-TRA-003 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-ACT-008 | Configure a 4 to 6 digit cancellation PIN required to cancel or finish an… | CU-ACT-01, CU-TRA-02 | — | CP-TRA-003, CP-ACT-002 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-ACT-009 | Cancel the activation during the 5-second window by pressing volume-down four… | CU-ACT-01 | — | CP-ACT-002 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-ACT-010 | Enable or disable each activation method from the settings (SOS button, single… | CU-ACT-01 | — | — | Mobile + Alert (alerts) | ✅ Implemented | No test case |
| RF-ACT-011 | Treat the "Notify me if I don't arrive" trip as an activation method in which… | CU-TRA-01 | — | CP-TRA-001 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-EVI-001 | Record video with the camera as evidence, after the permission is granted | CU-EVI-01 | HU-EVI-001 | CP-EVI-001 | Mobile + Evidence | ✅ Implemented | Not verified |
| RF-EVI-002 | Record audio with the microphone as evidence, after the permission is granted | CU-EVI-01 | HU-EVI-001 | CP-EVI-001 | Mobile + Evidence | ✅ Implemented | Not verified |
| RF-EVI-003 | Store the audio and video evidence, link it to the alert and keep it for a… | CU-EVI-01 | HU-EVI-001 | CP-EVI-001 | Mobile + Evidence | ✅ Implemented | Not verified |
| RF-EVI-004 | Keep a copy of the evidence in the app's internal storage, outside the gallery,… | CU-EVI-01 | — | — | Mobile app | ✅ Implemented | No test case |
| RF-REC-001 | Provide an assistance module with the three assistance channels: 123, purple… | CU-REC-01 | HU-REC-001 | — | Mobile app | ✅ Implemented | No test case |
| RF-REC-002 | Open the phone dialer with the selected number; the user starts the call… | CU-REC-01 | HU-REC-001 | — | Mobile app | ✅ Implemented | No test case |
| RF-NOT-001 | Show informative notices and messages about states, warnings and results of the… | CU-NOT-01 | — | — | Mobile + Alert (notifications) | ✅ Implemented | No test case |
| RF-NOT-002 | Show emergency notifications according to the permissions granted | CU-NOT-01 | — | CP-NOT-001, CP-NOT-002 | Mobile + Alert (notifications) | ✅ Implemented | Not verified |
| RF-NOT-003 | Keep a visible notification while an alert is active, when the operating system… | CU-NOT-01 | — | — | Mobile + Alert (notifications) | ✅ Implemented | No test case |
| RF-NOT-004 | Show the alerts issued by people who registered the user as a trusted contact | CU-NOT-02 | — | — | Mobile + Alert (notifications) | ✅ Implemented | No test case |
| RF-NOT-005 | Enable discreet mode (neutral texts in fixed notifications) and configure… | CU-NOT-01 | — | — | Mobile + Alert (notifications) | ✅ Implemented | No test case |
| RF-NOT-006 | Notify the user's other phones when the account signs in from a new phone | CU-NOT-01 | — | — | Auth (devices) | ✅ Implemented | No test case |
| RF-NOT-007 | Show the list of notifications received, such as risk zone notices and end of a… | CU-NOT-01 | — | CP-NOT-003 | Mobile + Alert (notifications) | ✅ Implemented | Not verified |
| RF-UBI-001 | Show the current location on the map when location services and permissions are… | CU-UBI-01 | HU-UBI-001 | CP-UBI-001, CP-UBI-003 | Mobile + Alert (zones) | ✅ Implemented | Not verified |
| RF-UBI-002 | Consult the recorded routes from the map module | CU-UBI-01 | — | — | Mobile + Alert (alerts) | ✅ Implemented | No test case |
| RF-UBI-003 | Consult the frequent or saved places | CU-UBI-01 | — | — | Mobile + Alert (alerts) | ✅ Implemented | No test case |
| RF-UBI-004 | Consult the geographic information linked to the user's alerts | CU-UBI-01 | — | CP-ALE-001 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-UBI-005 | Show auxiliary zones on the mobile map as circles, together with the current… | CU-UBI-01 | — | — | Mobile + Alert (zones) | ✅ Implemented | No test case |
| RF-UBI-006 | Save a zone or place with name, address, city, neighborhood, description, notes… | CU-UBI-01 | — | — | Mobile + Alert (alerts) | ✅ Implemented | No test case |
| RF-UBI-007 | Create a route by choosing origin and destination on the map or from saved… | CU-UBI-01 | — | — | Mobile + Alert (alerts) | ✅ Implemented | No test case |
| RF-UBI-008 | Share the current or a saved location with the phone's share options and open… | CU-UBI-01 | — | — | Mobile + Alert (zones) | ✅ Implemented | No test case |
| RF-HIS-001 | Show in the mobile history the contacts notified for each alert and the notice… | CU-ALE-01, CU-HIS-01 | HU-HIS-001 | CP-HIS-001 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-HIS-002 | Show the alert history with totals by status (sent, cancelled, in progress)… | CU-HIS-01 | — | — | Mobile + Alert (alerts) | ✅ Implemented | No test case |
| RF-IDI-001 | Select the interface language between Spanish, English, French and Portuguese… | CU-IDI-01 | — | — | Mobile app | ✅ Implemented | No test case |
| RF-TEM-001 | Customize the appearance: six themes in the app (light, dark and four color… | CU-IDI-01 | — | — | Mobile app | ✅ Implemented | No test case |
| RF-WEB-001 | Log in to the web with email and password or with Google and access the… | CU-USU-03, CU-WEB-01 | HU-WEB-001 | CP-AUT-001 | Web + Auth | ✅ Implemented | Not verified |
| RF-WEB-002 | View and edit the profile on the web; today the settings only allow changing… | CU-WEB-01 | — | — | Web + Auth | 🟡 In development | No test case |
| RF-WEB-003 | Consult the alerts registered for the account | CU-WEB-01 | HU-WEB-001 | — | Web + Alert | ✅ Implemented | No test case |
| RF-WEB-004 | Consult the history of alerts and events of the account | CU-HIS-01 | HU-HIS-001 | CP-HIS-001 | Web + Alert | ✅ Implemented | Not verified |
| RF-WEB-005 | View the location information linked to the alerts and records | CU-UBI-01, CU-WEB-01 | HU-WEB-001 | — | Web + Alert | ✅ Implemented | No test case |
| RF-WEB-006 | Consult the evidence of her own alerts | CU-EVI-02 | HU-EVI-002 | — | Web + Evidence | ✅ Implemented | No test case |
| RF-WEB-007 | Offer a landing page that explains Woman Alert, answers frequent questions and… | CU-WEB-01 | — | — | Web | ✅ Implemented | No test case |
| RF-WEB-008 | Consult and add emergency contacts, synchronized with the app | CU-CON-01 | — | — | Web + Alert | ✅ Implemented | No test case |
| RF-WEB-009 | Show the assistance lines, the care centers with directions and the history of… | CU-REC-01 | — | — | Web + Alert | ✅ Implemented | No test case |
| RF-WEB-010 | Show the status of the linked phone: permissions, remaining session time, alert… | CU-WEB-01 | — | — | Web + Auth (devices) | ✅ Implemented | No test case |
| RF-WEB-011 | Show the alerts of the people who registered the user as an emergency contact | CU-NOT-02 | — | — | Web + Alert | ✅ Implemented | No test case |
| RF-WEB-012 | Download the audio and video evidence and keep up to five; the rest are deleted… | CU-EVI-02 | — | — | Web + Evidence | ✅ Implemented | No test case |
| RF-WEB-013 | Show a home page with a summary of the account and the protection status | CU-WEB-01 | — | — | Web + Auth + Alert | ✅ Implemented | No test case |
| RF-ADM-001 | Consult and manage registered users with paginated results, according to the… | CU-ADM-01 | HU-ADM-001 | CP-ADM-001, CP-ADM-010 | Web admin + Auth (admin) | ✅ Implemented | Partly verified |
| RF-ADM-002 | Consult the registered alerts and the information of each event | CU-ADM-02 | HU-ADM-001 | — | Web admin + Auth (admin) | ✅ Implemented | No test case |
| RF-ADM-003 | Create, view, edit and delete geographic zones and manage their risk… | CU-ADM-03 | — | — | Web admin + Alert (zones) | ✅ Implemented | No test case |
| RF-ADM-004 | Consult and generate reports with filters by date, type, status and city;… | CU-ADM-04 | HU-ADM-001 | CP-ADM-002, CP-ADM-003 | Web admin + Auth (admin) | ✅ Implemented | Partly verified |
| RF-ADM-005 | Consult, per trusted contact, the notice status and the relationship, without… | CU-ADM-09 | — | — | Web admin + Alert | ✅ Implemented | No test case |
| RF-ADM-006 | Consult the devices linked to the users and change their status | CU-ADM-01 | — | CP-WEB-002 | Web admin + Auth (admin) | ✅ Implemented | Partly verified |
| RF-ADM-007 | Consult the evidence linked to the alerts, see statistics and record the review | CU-ADM-05 | — | CP-WEB-002 | Web admin + Evidence | ✅ Implemented | Partly verified |
| RF-ADM-008 | Consult and modify the enabled configuration parameters, with confirmation… | CU-ADM-07 | HU-ADM-002 | CP-ADM-005 | Web admin + Auth (admin) | ✅ Implemented | Partly verified |
| RF-ADM-009 | Consult the reports made about users and execute the enabled moderation actions | CU-ADM-06 | HU-ADM-001 | CP-ADM-004 | Web admin + Auth (admin) | ✅ Implemented | Partly verified |
| RF-ADM-010 | Show the technical status of the system through a restricted service and screen | CU-ADM-08 | — | — | Web admin + Auth (admin) | ✅ Implemented | No test case |
| RF-ADM-011 | Show a dashboard with users, attended and cancelled alerts, alerts by time of… | CU-ADM-08 | — | — | Web admin + Auth (admin) | ✅ Implemented | No test case |
| RF-ADM-012 | Create, view, edit and delete the help points shown by the app | CU-ADM-03 | — | — | Web admin + Alert (resources) | ✅ Implemented | No test case |
| RF-AUD-001 | Record in the audit log the tracked administrative operations with actor, role,… | CU-AUD-01 | HU-ADM-003 | CP-ADM-008 | Web admin + Auth (admin) | ✅ Implemented | Partly verified |
| RF-AUD-002 | Record the previous and new value when system parameters change, without… | CU-AUD-01 | HU-ADM-002 | CP-ADM-008 | Web admin + Auth (admin) | 🟡 In development | Partly verified |
| RF-AUD-003 | Prevent editing or deleting audit records through the normal operations of the… | CU-AUD-01 | — | — | Web admin + Auth (admin) | ✅ Implemented | No test case |
| RF-AUD-004 | Consult, filter and export the audit log and list the actors available for the… | CU-AUD-01 | HU-ADM-003 | CP-ADM-008 | Web admin + Auth (admin) | ✅ Implemented | Partly verified |
| RF-SES-001 | Record the administrative session events: successful login, failed attempt,… | CU-SES-01 | — | CP-AUT-002, CP-SEG-002 | Web admin + Auth (admin) | ✅ Implemented | Partly verified |
| RF-SES-002 | Close administrative sessions automatically after the configured inactivity… | CU-SES-01 | HU-SES-001 | CP-SEG-002 | Web admin + Auth (admin) | ✅ Implemented | Partly verified |
| RF-SES-003 | Let the administrator change the password from "My profile", requiring the… | CU-SES-01 | — | CP-ADM-009 | Web admin + Auth (admin) | ✅ Implemented | Not verified |
| RF-CFG-001 | Publish to the apps the public configuration, including the minimum allowed app… | CU-CFG-01 | — | CP-CFG-001 | Auth (settings) | ✅ Implemented | Not verified |
| RF-TRA-001 | Start a trip with destination and estimated arrival time from the current… | CU-TRA-01 | — | CP-TRA-001 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-TRA-002 | Create an alert from the server, without depending on the phone's signal, when… | CU-TRA-01 | — | CP-TRA-001 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |
| RF-TRA-003 | Accompany the user automatically when she leaves a frequent place and notify… | CU-TRA-02 | — | CP-TRA-002 | Mobile + Alert (alerts) | ✅ Implemented | Not verified |

## NFR Validation Matrix

| NFR ID | Category | Requirement (summary) | Test case | How it is validated today | Status |
| ------ | -------- | --------------------- | --------- | ------------------------- | ------ |
| RNF-SEG-001 | Security | The server accepts browser requests only from the authorized origins… | CP-SEG-005 | CORS restricted to the Vercel domain with `CORS_ORIGIN`; another origin receives 403 (checked in production) | ✅ Implemented |
| RNF-SEG-002 | Security | The mobile app stores session tokens in the device's secure storage | — | `expo-secure-store`, with AsyncStorage fallback if the module is not available | ✅ Implemented |
| RNF-SEG-003 | Security | No administrative credential is stored in the source code or in the… | — | Branch with passwords deleted and JWT secret rotated on 28/10/2026; the admin password that appears in a commit message still has to be changed | 🟡 In development |
| RNF-SEG-004 | Security / authorization | Functions and data are restricted by role (user or administrator) in… | CP-AUT-003, CP-SEG-001, CP-SEG-004, CP-SEG-006 | `@PreAuthorize("hasRole('ADMIN')")`; the owner is taken from the JWT; web guards only guide navigation | ✅ Implemented |
| RNF-SEG-005 | Security / authentication | The web portal controls access to authenticated features and prevents… | CP-SEG-002 | Logout on 401 and on inactivity; the web token is still stored in `localStorage` | 🟡 In development |
| RNF-SEG-006 | Security | Personal information, contacts and locations travel encrypted over… | — | HTTPS in production; the database does not encrypt columns at rest and evidence files are not encrypted with an own key | 🟡 In development |
| RNF-PRI-001 | Security / privacy | Personal data, photos, contacts, locations, alerts and evidence are… | — | Ownership checks in the use cases; privacy policy and terms still to be written | 🟡 In development |
| RNF-PRI-002 | Privacy | Exposure of location, IP, history, contacts and evidence is minimized… | — | The administration only sees the route of active or attended alerts and, per contact, the send status and relationship; daily location has no switch to disable it | 🟡 In development |
| RNF-DIS-001 | Availability / external dependency | Geolocation checks the permission and the location service before… | — | Implemented | ✅ Implemented |
| RNF-DIS-002 | Availability / communications | Features that send alerts or locations depend on connectivity and on… | CP-SOS-003 | The app shows the status (sending, sent, failed) and allows retrying; no local queue for creating the alert | 🟡 In development |
| RNF-DIS-003 | Availability / communications | If a notice channel (email or FCM) fails, the failure is recorded per… | — | `alert.alert_contact` with `failed` status, attempts and reason | ✅ Implemented |
| RNF-DIS-004 | Availability / operating system | While an alert is active the app keeps recording and sending the… | CP-TRA-002, CP-ACT-001 | Kotlin foreground services; manual tests with the release APK pending | ✅ Implemented |
| RNF-REN-001 | Performance | The dispatch of an alert to the notice channels must take at most 2… | CP-REN-001 | Not measured | ⚪ Proposed |
| RNF-REN-002 | Performance | GPS and sensor use must not exceed 10 % of battery per hour of… | — | Not measured | ⚪ Proposed |
| RNF-REN-003 | Performance / reliability | The app tries to update the location with 10 to 20 m accuracy and… | — | Intervals defined in RN-09; accuracy not measured | ⚪ Proposed |
| RNF-COM-001 | Compatibility | The mobile app targets Android 7.0 (API 24) or higher; compatibility… | — | APK v1.0.0: minSdk 24, targetSdk 36; manual tests on Samsung SM-A125M and Infinix X678B | ✅ Implemented |
| RNF-COM-002 | Compatibility | The web platform works on modern browsers: recent Chrome, Edge and… | — | Not formally tested | ⚪ Proposed |
| RNF-COM-003 | Compatibility / usability | The web platform is oriented to desktop resolutions, especially the… | — | Responsive with desktop priority; breakpoints not documented | ⚪ Proposed |
| RNF-USB-001 | Usability / accessibility | Emergency actions use visible controls and clear messages with as few… | — | Not evaluated with users | ⚪ Proposed |
| RNF-USB-002 | Usability | The app offers the approved activation methods to start the SOS flow… | — | Implemented | ✅ Implemented |
| RNF-USB-003 | Usability | The user can configure the frequency and type of notifications to… | — | Implemented | ✅ Implemented |
| RNF-ACC-001 | Accessibility | Forms, maps, states and keyboard or screen reader navigation are… | CP-ACC-001 | Mobile accessibility is poor (few labels for screen readers); web has `lang="es"` and 118 `aria-label`; contrast not measured | ⚪ Proposed |
| RNF-MAN-001 | Maintainability | Database changes use reproducible migrations and the technical… | — | Liquibase v1.0.0 to v1.6.0 (changeset 25); ArchUnit tests for the hexagonal structure | ✅ Implemented |
| RNF-OBS-001 | Observability / security | Diagnostic logs do not expose credentials or unnecessary sensitive… | — | Client HTTP logs only in `__DEV__`; 64 `console` calls remain in the mobile app | 🟡 In development |
| RNF-API-001 | Robustness | Non-existent API routes are rejected with HTTP 404, not with a 500… | CP-SEG-003 | Failed in the old monolith (15/10/2026); to be revalidated in the microservices | ✅ Implemented |

## Reverse Traceability: US → FR

| US | Requirements | Use cases |
| -- | ------------ | --------- |
| HU-USU-001 | RF-USU-001, RF-USU-003 | CU-USU-01 |
| HU-USU-002 | RF-USU-002 | CU-USU-02 |
| HU-CON-001 | RF-CON-001, RF-CON-002 | CU-CON-01 |
| HU-ACT-001 | RF-ACT-005, RF-ALE-001 | CU-ACT-01 |
| HU-ACT-002 | RF-ACT-006 | CU-ACT-01 |
| HU-ALE-001 | RF-ALE-002, RF-ALE-006 | CU-ALE-01 |
| HU-ALE-002 | RF-ALE-003 | CU-ALE-02 |
| HU-HIS-001 | RF-HIS-001, RF-WEB-004 | CU-HIS-01 |
| HU-UBI-001 | RF-UBI-001 | CU-UBI-01 |
| HU-REC-001 | RF-REC-001, RF-REC-002 | CU-REC-01 |
| HU-EVI-001 | RF-EVI-001, RF-EVI-002, RF-EVI-003 | CU-EVI-01 |
| HU-EVI-002 | RF-WEB-006 | CU-EVI-02 |
| HU-WEB-001 | RF-WEB-001, RF-WEB-003, RF-WEB-005 | CU-WEB-01, CU-USU-03 |
| HU-ADM-001 | RF-ADM-001, RF-ADM-002, RF-ADM-004, RF-ADM-009 | CU-ADM-01, CU-ADM-02, CU-ADM-04, CU-ADM-06 |
| HU-ADM-002 | RF-ADM-008, RF-AUD-002 | CU-ADM-07 |
| HU-ADM-003 | RF-AUD-001, RF-AUD-004 | CU-AUD-01 |
| HU-SES-001 | RF-SES-002 | CU-SES-01 |

## Status Legend

| Symbol | Meaning |
| ------ | ------- |
| ✅ Implemented | The feature exists in the code published in `main` (SRS v2.6) |
| 🟡 In development | Partially built or with a known gap still open |
| ⚪ Proposed | Defined target that has not been built or measured |
| Partly verified | At least one linked test case is approved (locally, in the API or only the build) |
| Not verified | The linked test cases are pending, observed by the audit or failed |
| No test case | The test plan has no case linked to this requirement yet |

Test plan summary (47 cases): 12 approved (local or build only), 2 partial, 9 observed by the audit but not executed, 1 failed in the old monolith and to be revalidated, 23 pending.

## Identified Gaps (Requirements Without Coverage)

- **55 functional requirements have no linked test case:** RF-PER-001, RF-PER-003, RF-PER-004, RF-PER-005, RF-USU-001, RF-USU-004, RF-USU-006, RF-USU-007, RF-USU-008, RF-USU-009, RF-INI-001, RF-ALE-005, RF-ALE-006, RF-ALE-009, RF-ALE-010, RF-ALE-011, RF-ALE-012, RF-ALE-013, RF-ACT-003, RF-ACT-010, RF-EVI-004, RF-REC-001, RF-REC-002, RF-NOT-001, RF-NOT-003, RF-NOT-004, RF-NOT-005, RF-NOT-006, RF-UBI-002, RF-UBI-003, RF-UBI-005, RF-UBI-006, RF-UBI-007, RF-UBI-008, RF-HIS-002, RF-IDI-001, RF-TEM-001, RF-WEB-002, RF-WEB-003, RF-WEB-005, RF-WEB-006, RF-WEB-007, RF-WEB-008, RF-WEB-009, RF-WEB-010, RF-WEB-011, RF-WEB-012, RF-WEB-013, RF-ADM-002, RF-ADM-003, RF-ADM-005, RF-ADM-010, RF-ADM-011, RF-ADM-012, RF-AUD-003.
- **Native features without device evidence.** Shake, widget, trip, automatic protection, recording in the background and the contact alarm depend on the manual tests PM-04 to PM-09 with the release APK, still pending.
- **Notices between two phones.** Tested manually on 23/10/2026, but the evidence is not archived in the test plan (CP-SOS-004, CP-NOT-001).
- **Performance NFRs.** RNF-REN-001 to RNF-REN-003 have no measurement.
- **Accessibility.** RNF-ACC-001 has no evaluation; the audit reports insufficient labels in the mobile app.
- **Numbering of business rules.** Document 03 uses the codes RN-01 to RN-08 with different content from Document 02; the numbering must be unified.

## How to Maintain This Matrix

1. When a requirement changes in Document 02, update its row here in the same pull request.
2. When a test case is executed, record the result, date, environment and commit in the test plan and change the Verification column.
3. Link automated tests to test cases by name or ID so that the CI results count as evidence.
4. Do not mark a requirement as verified only because the code exists.
5. Review the matrix before every delivery.

## Correlations

- Functional requirements → `04-requirements/functional.md`
- Non-functional requirements → `04-requirements/non-functional.md`
- User stories → `04-requirements/user-stories.md`
- Testing strategy → `11-quality/testing-strategy.md`
- Service catalog → `09-microservices/service-catalog.md`
