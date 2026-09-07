# System Scope

---

## In Scope

What the system **does build and maintain** (per the "Alcance" section of the SRS):

### MVP functionality

| # | Feature | Description | Responsible service |
|---|---------|-------------|---------------------|
| 1 | Sign-up and login | Registration, authentication, and account recovery for users | Mobile app / Backend |
| 2 | Emergency contacts | Configuring trusted contacts for the support network | Mobile app |
| 3 | Panic button | Sending alerts with real-time location, triggerable via widget, sudden movement, physical buttons, or a discreet confirmation | Mobile app |
| 4 | Multi-channel notifications | Sending configurable alerts via SMS, email, and push notifications | Backend + external services (FCM, SMS) |
| 5 | Real-time tracking | Selected contacts follow the location while the alert stays active | Mobile app / Backend |
| 6 | Evidence | Recording and backing up audio and video tied to an alert | Mobile app / Backend |
| 7 | History | Historical record of events, alerts, and locations issued | Backend |
| 8 | Zones and directory | Map of safe/unsafe zones and directory of nearby support centers | Mobile app / Backend |
| 9 | Guidance resources | Guide on how to act in risk situations | Mobile app |
| 10 | Personalization | Changing the app's language and visual theme | Mobile app |
| 11 | Public informational site | Landing page with no authentication required | Web (Angular) |
| 12 | Authenticated user portal | Reviewing history, location, evidence, and device status | Web (Angular) |
| 13 | Admin panel | Managing users, alerts, zones, evidence, reports, system configuration, and moderation | Web (Angular) / Backend |

### Included integrations

| External system | Integration type | Purpose |
|----------------|-----------------|---------|
| Firebase Cloud Messaging (FCM) | SDK / API | Sending push notifications |
| SMS messaging service (e.g. Twilio) | API | Sending SMS alerts |
| Email service | API | Sending additional email notifications |

### Environments

The SRS defines a single target technology stack (React Native/Expo, Angular, Spring Boot, PostgreSQL) but doesn't lay out separate Local/Dev/Staging/Production environments — that split is an implementation decision left to the team, not something the requirements document scopes.

---

## Out of Scope

The SRS doesn't include an explicit "out of scope" section, but its own wording draws a few boundaries:

- **iOS** — the SRS repeatedly scopes device compatibility to Android only ("aplicación... compatible con dispositivos Android desde la versión 10 en adelante"), which implicitly leaves iOS out of the current scope.
- **Direct integration with authorities/emergency services** — the system provides a directory of emergency numbers and support centers (RF7.1) and lets the user place a call (RF1.2), but doesn't describe any automated dispatch or data exchange with police/emergency-service systems.
- **Persisted administrative report history** — the admin panel generates and exports reports with filters (RF13.4), but the SRS doesn't describe storing a historical log of previously generated reports.

### What another system/team handles (and why not us)

Not described in the SRS — the document scopes Alerta Mujer as a self-contained mobile + web + backend system, without naming any adjacent system it depends on or hands work off to.

---

## Scope assumptions

| # | Assumption | Consequence if false |
|---|-----------|---------------------|
| 1 | The requirements described will remain stable throughout the development cycle | The SRS would need to be revised and re-baselined mid-development, per the IEEE 830 standard it follows |
| 2 | Devices have the necessary permissions for sending messages and accessing location | The app couldn't send alerts or location |
| 3 | The emergency-resource directory will be kept up to date by administrative staff (RF13.1) | Support information would become outdated |
| 4 | Alerts will be responded to promptly by the relevant authorities | The system's core value proposition — reducing emergency response time — wouldn't actually be achieved, even though the alert itself was sent correctly |
| 5 | Users have consistent access to location and messaging services | The app couldn't notify contacts or locate the user |
| 6 | Internet connectivity is available to communicate with the REST API | The system couldn't send/receive real-time alerts |

---

## Constraints

| Type | Description |
|------|-------------|
| **Technology** | Mobile app in React Native + Expo (Android); web in Angular; backend in Spring Boot + Spring Security + Spring Data JPA; PostgreSQL database with Liquibase |
| **Compatibility** | The app must be compatible with Android devices (minimum version left pending in the SRS) |
| **Connectivity** | Requires internet access to send real-time alerts |
| **Security** | Must follow security protocols and personal-data-protection regulations |
| **Concurrency** | The architecture must support multiple concurrent connections and simultaneous alerts from multiple users, with fault tolerance |
| **Time / Budget / Team** | Not addressed in the SRS — it's a requirements document, not a project plan |

---

## External dependencies

| Dependency | Team / Provider | Required date | Status |
|-----------|----------------|--------------|--------|
| Firebase Cloud Messaging (FCM) | Google / Firebase | Not set — no delivery date is defined at the requirements stage | Provider fixed (Firebase), integration not yet built |
| SMS messaging service | Provider not yet finalized — the SRS names Twilio only as an example ("ej. Twilio") | Not set | Provider selection pending |
| Email service | Provider not named in the SRS | Not set | Provider selection pending |

---

## Related sections

- Vision and roadmap → `03-product/vision.md`
- Glossary of terms → `01-context/glossary.md`
- System description → `01-context/overview.md`
- Scope-related risks → `15-project-control/risks.md`
