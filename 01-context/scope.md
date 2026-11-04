# System Scope

---

## In scope

What the system **builds and maintains** (per SRS v2.6 and the general project document):

### MVP functionality

| # | Feature | Description | Responsible service |
|---|---------|-------------|---------------------|
| 1 | Accounts and access | Registration, login with email/password or Google, recovery with a 6-digit code sent by email, profile, logout and account deletion. The same account works in the app and on the web | Mobile + Web / Auth |
| 2 | Trusted contacts | Add, view, edit and delete contacts (from the phone book or manually). Default limit of 5, configurable by the administrator between 1 and 20, validated on the server | Mobile + Web / Alert |
| 3 | Alert activation | SOS button, alert to a single contact, shake detection, home screen widget, trip not confirmed and automatic protection. 5-second cancellation window, cancellation PIN, duress PIN, cancel with volume-down ×4 and discreet mode | Mobile / Alert |
| 4 | Contact notification | Email (Resend) with a public tracking link and push (FCM) with an alarm until silenced. One resend if nobody opens it in 5 min; notice when the location is lost | Alert |
| 5 | Active alert follow-up | Periodic location (every 15 s for the first 5 min, then every 30 s, or 60 s in saving mode), chat between user and contacts, "Are you still in danger?" at minute 25, absolute limit of 2 hours | Mobile / Alert |
| 6 | "Notify me if I don't arrive" trips | Destination and arrival time (5 to 240 min), heartbeat every 2 minutes; the server creates the alert if arrival is not confirmed or the signal is lost | Mobile / Alert |
| 7 | Automatic protection | Starts when leaving a frequent place (150 m geofence); silent "Did you arrive OK?" questions at 15 and 25 min and alert at 35 min; "Is everything OK?" in a risk zone with alert at 5 min; alert if the signal is lost for 20 min | Mobile (Kotlin) / Alert |
| 8 | Evidence | Video and then audio recorded during the alert, stored in Backblaze B2 with SHA-256, kept 30 days with a notice 5 days before deletion; private copy on the device | Mobile / Evidence |
| 9 | History | Alerts sent, cancelled and in progress, with map, route, evidence and notice status per contact (sent, delivered or seen) | Mobile + Web / Alert |
| 10 | Map, zones and places | Current location, safe and risk zones, saved places with a safety level, saved routes (max. 4, 2 starred) | Mobile / Alert |
| 11 | Assistance | Lines 123, purple line and mental health line, opened in the dialer without automatic calls | Mobile + Web |
| 12 | Notifications inbox | Risk zone notices, end of a contact's alert, new phone signed in | Mobile / Auth + Alert |
| 13 | Personalization | Spanish, English, French and Portuguese; light and dark mode plus four color themes | Mobile + Web |
| 14 | Public landing | What Woman Alert is, how it works, FAQ and APK download | Web |
| 15 | Public tracking page | Live map refreshed every 10 s, route, chat, recordings, time active and access to 123, without an account | Web / Alert |
| 16 | User portal | Safety summary, emergencies, alerts from my contacts, locations, evidence (download and keep up to 5), contacts, assistance centers, device status and settings | Web |
| 17 | Admin panel | Dashboard, users (including blocking), alerts, zones and help points, reports with PDF/CSV export, evidence, moderation, devices, read-only audit log, parameters and system status | Web / Auth |

### Integrations included

| External system | Integration type | Purpose |
|----------------|-----------------|-----------------|
| Firebase Cloud Messaging (FCM) | Admin SDK (server) and `@react-native-firebase/messaging` (app) | Push notices to contacts and app notifications |
| Resend (Brevo as configurable alternative) | HTTP API | Alert email with tracking link, end-of-alert email and recovery code |
| Google Sign-In / Firebase Authentication | SDK + server-side signature verification | Login with Google on mobile and web |
| Backblaze B2 | S3-compatible API | Evidence storage |
| MapLibre + OpenFreeMap / Leaflet + OpenStreetMap | Map tiles | Maps in the app and on the web |
| Nominatim (OpenStreetMap) | HTTP API | Address search for trip destinations |
| Android (geofences, sensors, dialer) | Native APIs from the Kotlin module | Automatic protection, shake detection, opening the dialer |

### Environments

| Environment | Where | Branch |
|-------------|-------|--------|
| Local | Docker Compose: PostgreSQL, SeaweedFS (local S3) and the three services on ports 8081, 8082 and 8083; web on `localhost:4200`; app with a development APK | Feature branches |
| Integration | Pull request to `develop`, promoted to `qa`; GitHub Actions runs the tests | `develop`, `qa` |
| Production | Render (Auth, Alert, Evidence and PostgreSQL), Vercel (web), GitHub Releases (APK v1.0.0) | `main` |

---

## Out of scope

- **iOS.** The app targets Android 7.0 (API 24) or higher only.
- **SMS, WhatsApp and RCS notices.** Discarded by the team; the approved channels are email and push.
- **Automatic calls.** The app opens the dialer with the number and the user decides whether to call.
- **Locating a phone that is off or offline.** Only the last recorded location is shown, with its date and time.
- **Direct integration with the authorities.** There is no dispatch or data exchange with the police or the 123 line.
- **Lock screen and power button activation.** Removed from the app on 23/10/2026; recorded as "Retired" in the SRS.
- **Creating an alert without connection.** The app shows the status and allows retrying, but there is no local queue for the initial creation.
- **Own encryption of evidence files.** Files travel over HTTPS but are not encrypted with an application key.

### What another system/team handles (and why not us)

| Responsibility | Who handles it | Why not us |
|----------------|----------------|------------|
| Emergency response, physical protection, legal and psychosocial care | National Police, Attorney General's Office, Family Commissioners, lines 123 and 155 | Woman Alert is a training project and a complementary tool, not an official reporting channel |
| Email and push delivery | Resend / Brevo and Firebase Cloud Messaging | Delivery depends on third-party services and on the recipient's connectivity |
| File storage | Backblaze B2 | Persistent storage outside the free hosting plan |

---

## Scope assumptions

| # | Assumption | Consequence if false |
|---|-----------|---------------------|
| 1 | The user grants the location, notification, camera, microphone and contacts permissions, plus the special Android accesses (overlay, full-screen notifications, exact alarms, unrestricted battery) | Protection features stop working with the screen off or the app closed |
| 2 | The phone has Internet access when the alert is created | The alert is not registered; evidence and trip heartbeats are queued until the connection returns |
| 3 | Contacts have a valid email address | The notice only reaches contacts who have the app with an active session (push) |
| 4 | The Render services are awake or can wake up in time | The first request after inactivity can take up to about 90 seconds; UptimeRobot pings every 5 minutes to keep them awake |
| 5 | The administrators keep zones, help points and parameters updated | The map and assistance information become outdated |
| 6 | Contacts open and understand the notice | The system resends once and warns when the signal is lost, but it cannot guarantee a response |

---

## Restrictions

| Type | Description |
|------|-------------|
| **Technology** | React Native + Expo + TypeScript with a Kotlin module (Android); Angular 21 (web); Java 17 + Spring Boot 3.2 in three microservices; PostgreSQL 16 with Liquibase |
| **Compatibility** | Android 7.0 (API 24) or higher; manual tests on a Samsung SM-A125M (Android 12) and an Infinix X678B. Web for recent Chrome, Edge and Firefox, desktop first |
| **Connectivity** | Creating an alert, sending the location and notifying contacts require Internet |
| **Security** | JWT (24 h access, 15-day refresh), BCrypt, role control, CORS restricted to the web domain, `X-Internal-Key` for internal calls, 5 login attempts per minute per IP |
| **Privacy** | Location, contacts and evidence are personal data under Law 1581 of 2012. The privacy policy, data treatment policy and terms and conditions are not written yet |
| **Hosting** | Free Render plan: services sleep after 15 minutes, cold start of 1 to 3 minutes, scheduled tasks only run while the service is awake, and the free database has an expiration date |
| **Time / Team** | Three members and the academic deadline; feature freeze since 24/10/2026 |

---

## External dependencies

| Dependency | Team / Supplier | Required date | State |
|--------|----------------|--------------|--------|
| Firebase Cloud Messaging | Google / Firebase | In use | Implemented; tested between two phones in production on 23/10/2026 (record pending in the test plan) |
| Email provider | Resend (Brevo alternative) | In use | Implemented through HTTP API |
| Google Sign-In / Firebase Authentication | Google | In use | Implemented on mobile and web |
| Backblaze B2 | Backblaze | In use | Implemented |
| Render | Render | In use | Free plan, `main` branch |
| Vercel | Vercel | In use | `main` branch |

---

## Related sections

- Vision and roadmap → `03-product/vision.md`
- Glossary of terms → `01-context/glossary.md`
- System Overview → `01-context/overview.md`
- Scope related risks → `15-project-control/risks.md`
