#System scope

---

## In scope

What the system **builds and maintains** (per the "Scope" section of the SRS):

### MVP functionality

| # | Feature | Description | Responsible service |
|---|---------|-------------|---------------------|
| 1 | Register and log in | User account registration, authentication and recovery | Mobile/backend application |
| 2 | Emergency contacts | Set up trusted contacts for the support network | Mobile application |
| 3 | Panic button | Sending alerts with real-time location, activated by widget, sudden movement, physical buttons or a discreet confirmation | Mobile application |
| 4 | Multichannel notifications | Sending configurable alerts via SMS, email and push notifications | Backend + external services (FCM, SMS) |
| 5 | Real-time tracking | Selected contacts track location while alert remains active | Mobile/backend application |
| 6 | Evidence | Recording and backup of audio and video linked to an alert | Mobile/backend application |
| 7 | History | Historical record of events, alerts and locations issued | Server |
| 8 | Zones and directory | Map of safe/unsafe areas and directory of nearby support centers | Mobile/backend application |
| 9 | Guidance Resources | Guide on how to act in risk situations | Mobile application |
| 10 | Personalization | Change the language and visual theme of the application | Mobile application |
| 11 | Public information site | No Authentication Required Landing Page | Web (angular) |
| 12 | Authenticated User Portal | Review device history, location, evidence and status | Web (angular) |
| 13 | Administration panel | User Management, Alerts, Zones, Testing, Reporting, System Configuration and Moderation | Web (angular)/backend |

### Integrations included

| External system | Integration type | Purpose |
|----------------|-----------------|-----------------|
| Firebase Cloud Messaging (FCM) | SDK/API | Sending push notifications |
| SMS messaging service (e.g. Twilio) | API | Sending alerts by SMS |
| Email service | API | Sending additional notifications by email |

### Environments

The SRS defines a single-target technology stack (React Native/Expo, Angular, Spring Boot, PostgreSQL) but does not establish separate local/development/staging/production environments; that division is an implementation decision left up to the team, not something the requirements documents achieve.

---

## Out of scope

The SRS does not include an explicit "out of scope" section, but its wording itself sets some limits:

- **iOS** — SRS repeatedly scans for device compatibility with Android only ("application... compatible with Android devices from version 10 onwards"), implicitly leaving iOS out of current scope.
- **Direct integration with authorities/emergency services**: The system provides a directory of emergency numbers and support centers (RF7.1) and allows the user to make a call (RF1.2), but does not describe any automated dispatch or data exchange with police/emergency services systems.
- **Persistent administrative report history**: The administration panel generates and exports reports with filters (RF13.4), but the SRS does not describe storing a historical record of previously generated reports.

### What another system/team handles (and why not us)

Not described in the SRS: the document discusses Woman Alert as a standalone mobile + web + backend system, without naming any adjacent systems it depends on or works with.

---

## Scope assumptions

| # | Assumption | Consequence if false |
|---|-----------|---------------------|
| 1 | The described requirements will remain stable throughout the development cycle | The SRS should be revised and rebased mid-development, based on the IEEE 830 standard that follows |
| 2 | Devices have the necessary permissions to send messages and access location | The app was unable to send alerts or location |
| 3 | Administrative staff will keep the emergency resource directory updated (RF13.1) | Support information would be obsolete |
| 4 | Alerts will be responded to promptly by the relevant authorities | The core value proposition of the system (reducing emergency response time) would not actually be achieved, even though the alert itself was successfully sent.
| 5 | Users have constant access to location and messaging services | The application could not notify contacts or locate the user |
| 6 | Internet connectivity is available to communicate with the REST API | The system was unable to send/receive real-time alerts |

---

## Restrictions

| Type | Description |
|------|-------------|
| **Technology** | Mobile application in React Native + Expo (Android); web in Angular; backend in Spring Boot + Spring Security + Spring Data JPA; PostgreSQL database with Liquibase |
| **Compatibility** | The app must be compatible with Android devices (minimum version is pending in the SRS) |
| **Connectivity** | Requires Internet access to send real-time alerts |
| **Security** | You must follow security protocols and personal data protection regulations |
| **Simultaneity** | The architecture must support multiple simultaneous connections and simultaneous alerts from multiple users, with fault tolerance |
| **Time / Budget / Team** | Not addressed in the SRS: it is a requirements document, not a project plan |

---

## External dependencies

| Dependency | Team / Supplier | Required date | State |
|--------|----------------|--------------|--------|
| Firebase Cloud Messaging (FCM) | Google/Firebase | Not established: no delivery date is defined in the requirements stage | Fixed provider (Firebase), integration not created yet |
| SMS messaging service | Vendor not yet finalized: SRS names Twilio only as example ("ex. Twilio") | Not established | Pending supplier selection |
| Email service | Provider not named on SRS | Not established | Pending supplier selection |

---

## Related sections

- Vision and roadmap → `03-product/vision.md`
- Glossary of terms → `01-context/glossary.md`
- System Overview → `01-context/overview.md`
- Scope related risks → `15-project-control/risks.md`