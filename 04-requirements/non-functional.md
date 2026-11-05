# Non-Functional Requirements — Woman Alert

> SRS v2.6 groups the non-functional requirements under the `RNF-<CATEGORY>-<NNN>` scheme (25 requirements). The previous identifiers (RNF1–RNF18 and RNF001–RNF010) are kept in the "Previous ID" column. Where the SRS defines a numerical metric it is copied exactly; where the metric has not been measured, the evidence column says so instead of assuming a result. Status: 11 implemented, 7 in development and 7 proposed.

## Performance

No performance figure has been measured yet. The values below are the targets defined in the SRS; until they are measured they stay as **Proposed**.

| ID | Category | Requirement | Previous ID | Current evidence | Test | Status |
|----|----------|-------------|-------------|------------------|------|--------|
| RNF-REN-001 | Performance | The dispatch of an alert to the notice channels must take at most 2 seconds from the activation, measured until the request is delivered to the provider | RNF5 | Not measured | CP-REN-001 | Proposed |
| RNF-REN-002 | Performance | GPS and sensor use must not exceed 10 % of battery per hour of continuous use of the foreground service | RNF6 | Not measured | — | Proposed |

## Availability and Fault Tolerance

The free Render plan puts each service to sleep after 15 minutes without traffic; the first request afterwards can take up to about 90 seconds. UptimeRobot calls the three `/actuator/health` endpoints every 5 minutes to keep them awake, and internal RPC calls wait up to 90 seconds. Scheduled tasks (expiration, resend, trips, retention) only run while the service is awake.

| ID | Category | Requirement | Previous ID | Current evidence | Test | Status |
|----|----------|-------------|-------------|------------------|------|--------|
| RNF-DIS-002 | Availability / communications | Features that send alerts or locations depend on connectivity and on the delivery mechanism; Internet is necessary but does not guarantee delivery | RNF4 y RNF004 | The app shows the status (sending, sent, failed) and allows retrying; no local queue for creating the alert | CP-SOS-003 | In development |
| RNF-DIS-003 | Availability / communications | If a notice channel (email or FCM) fails, the failure is recorded per contact and channel | RNF9 | `alert.alert_contact` with `failed` status, attempts and reason | — | Implemented |
| RNF-API-001 | Robustness | Non-existent API routes are rejected with HTTP 404, not with a 500 internal error | RF20.2 | Failed in the old monolith (15/10/2026); to be revalidated in the microservices | CP-SEG-003 | Implemented |
| RNF-MAN-001 | Maintainability | Database changes use reproducible migrations and the technical documentation stays synchronized with the system version | RNF-MAN-01 | Liquibase v1.0.0 to v1.6.0 (changeset 25); ArchUnit tests for the hexagonal structure | — | Implemented |

## Security

Passwords are hashed with BCrypt. The access token lasts 24 hours and the refresh token 15 days, both signed with `JWT_SECRET`; the services refuse to start with an empty secret or with the value that was once published. `RateLimitFilter` allows 5 attempts per minute per IP on login, Google, registration and recovery, and then answers 429. `/internal/**` requires `X-Internal-Key`.

| ID | Category | Requirement | Previous ID | Current evidence | Test | Status |
|----|----------|-------------|-------------|------------------|------|--------|
| RNF-SEG-001 | Security | The server accepts browser requests only from the authorized origins of each environment; the * wildcard is not used in production | RNF-SEG-01 | CORS restricted to the Vercel domain with `CORS_ORIGIN`; another origin receives 403 (checked in production) | CP-SEG-005 | Implemented |
| RNF-SEG-002 | Security | The mobile app stores session tokens in the device's secure storage | RNF-SEG-02 | `expo-secure-store`, with AsyncStorage fallback if the module is not available | — | Implemented |
| RNF-SEG-003 | Security | No administrative credential is stored in the source code or in the repository history; each administrator uses an individual account | RNF-SEG-03 | Branch with passwords deleted and JWT secret rotated on 28/10/2026; the admin password that appears in a commit message still has to be changed | — | In development |
| RNF-SEG-004 | Security / authorization | Functions and data are restricted by role (user or administrator) in queries, exports and administrative operations | RNF14, RNF005 y RNF-SEG-04 | `@PreAuthorize("hasRole('ADMIN')")`; the owner is taken from the JWT; web guards only guide navigation | CP-AUT-003, CP-SEG-001, CP-SEG-004, CP-SEG-006 | Implemented |
| RNF-SEG-005 | Security / authentication | The web portal controls access to authenticated features and prevents unauthorized sessions from reaching protected resources | RNF15 y RNF008 | Logout on 401 and on inactivity; the web token is still stored in `localStorage` | CP-SEG-002 | In development |
| RNF-SEG-006 | Security | Personal information, contacts and locations travel encrypted over HTTPS/TLS; encryption at rest is applied to stored data | RNF8 | HTTPS in production; the database does not encrypt columns at rest and evidence files are not encrypted with an own key | — | In development |
| RNF-PRI-001 | Security / privacy | Personal data, photos, contacts, locations, alerts and evidence are not public nor accessible by other users without authorization | RNF1, RNF16 y RNF001 | Ownership checks in the use cases; privacy policy and terms still to be written | — | In development |
| RNF-PRI-002 | Privacy | Exposure of location, IP, history, contacts and evidence is minimized according to the role and purpose of each access | RNF-PRI-01 | The administration only sees the route of active or attended alerts and, per contact, the send status and relationship; daily location has no switch to disable it | — | In development |
| RNF-OBS-001 | Observability / security | Diagnostic logs do not expose credentials or unnecessary sensitive data | RNF-OBS-01 | Client HTTP logs only in `__DEV__`; 64 `console` calls remain in the mobile app | — | In development |

## Location and Sensors

Location intervals (RN-09): during an active alert the app sends the location every 15 seconds for the first 5 minutes and then every 30 seconds, or every 60 seconds in saving mode; it keeps sending with the app closed. In daily use only the main phone reports its location, every 50 m or 5 minutes. Trips and automatic protection send a heartbeat every 2 minutes. The shake detector uses a 4.5 g threshold adjusted to the accelerometer.

| ID | Category | Requirement | Previous ID | Current evidence | Test | Status |
|----|----------|-------------|-------------|------------------|------|--------|
| RNF-DIS-001 | Availability / external dependency | Geolocation checks the permission and the location service before asking for coordinates; with signal loss, the last location is kept and labeled as such | RNF2 y RNF002 | Implemented | — | Implemented |
| RNF-DIS-004 | Availability / operating system | While an alert is active the app keeps recording and sending the location in the background, within the limits of the OS and the manufacturer | RNF13 | Kotlin foreground services; manual tests with the release APK pending | CP-TRA-002, CP-ACT-001 | Implemented |
| RNF-REN-003 | Performance / reliability | The app tries to update the location with 10 to 20 m accuracy and refreshes it at regular intervals during an active alert | RNF7 | Intervals defined in RN-09; accuracy not measured | — | Proposed |

## Usability and Accessibility

| ID | Category | Requirement | Previous ID | Current evidence | Test | Status |
|----|----------|-------------|-------------|------------------|------|--------|
| RNF-USB-001 | Usability / accessibility | Emergency actions use visible controls and clear messages with as few steps as possible, so they can be used under stress | RNF11 y RNF007 | Not evaluated with users | — | Proposed |
| RNF-USB-002 | Usability | The app offers the approved activation methods to start the SOS flow quickly, plus server activation for trips and automatic protection | RNF12 | Implemented | — | Implemented |
| RNF-USB-003 | Usability | The user can configure the frequency and type of notifications to avoid excessive alerts | RNF3 | Implemented | — | Implemented |
| RNF-ACC-001 | Accessibility | Forms, maps, states and keyboard or screen reader navigation are verified in the app and on the web | RNF-ACC-01 | Mobile accessibility is poor (few labels for screen readers); web has `lang="es"` and 118 `aria-label`; contrast not measured | CP-ACC-001 | Proposed |

## Compatibility

| ID | Category | Requirement | Previous ID | Current evidence | Test | Status |
|----|----------|-------------|-------------|------------------|------|--------|
| RNF-COM-001 | Compatibility | The mobile app targets Android 7.0 (API 24) or higher; compatibility is checked on the target devices | RNF10 y RNF006 | APK v1.0.1: minSdk 24, targetSdk 36; manual tests on Samsung SM-A125M and Infinix X678B | — | Implemented |
| RNF-COM-002 | Compatibility | The web platform works on modern browsers: recent Chrome, Edge and Firefox | RNF17 y RNF010 | Not formally tested | — | Proposed |
| RNF-COM-003 | Compatibility / usability | The web platform is oriented to desktop resolutions, especially the admin panel; the mobile view is available but not prioritized | RNF18 | Responsive with desktop priority; breakpoints not documented | — | Proposed |

## Identified Gaps (Not Invented, Only Flagged)

- **No measurements.** Alert dispatch time (RNF-REN-001), battery use (RNF-REN-002) and GPS accuracy (RNF-REN-003) have no recorded measurement. There is no load test and no coverage measurement.
- **Encryption at rest.** The database does not encrypt columns and evidence files are not encrypted with an own key (RNF-SEG-006). The tutorial text "Encrypted recording" must be corrected or the files must be encrypted.
- **Web token.** The web session token is stored in `localStorage` (RNF-SEG-005).
- **Daily location.** The location is sent while the app is open without a switch to disable it (RNF-PRI-002).
- **Accessibility.** The mobile app has 13 accessibility labels and 18 roles for 197 touchable elements; contrast and keyboard navigation are not measured on the web (RNF-ACC-001).
- **404 for unknown routes.** It failed in the old monolith and has to be revalidated in the microservices (RNF-API-001, CP-SEG-003).
- **Diagnostic logs.** 64 `console` calls remain in the mobile app outside `__DEV__` (RNF-OBS-001).

## Correlations

- Functional requirements → `04-requirements/functional.md`
- Traceability and validation → `04-requirements/traceability-matrix.md`
- Security threat model → `05-architecture/security-threat-model.md`
- Testing strategy → `11-quality/testing-strategy.md`
- Observability → `13-operations/observability.md`
- Risks → `15-project-control/risks.md`
