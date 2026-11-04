# Project Glossary

---

## How to use this glossary

1. Before using a technical or business term in code, documents, or conversations: look it up here.
2. If missing: add it with your definition.
3. If there is disagreement on the definition: discuss it as a team and update this document.

---

## Domain Terms

| Term | Definition | Notes / Synonyms |
|------|-----------|-----------------|
| User | Person registered in Woman Alert who activates alerts from the mobile app. The product prioritizes women but does not exclude anyone | Usuaria; role `user` |
| Trusted contact | Person registered by the user who receives the alert notice by email and, if they have the app, by push | Emergency contact; table `alert.emergency_contact` |
| Alert | Emergency episode started from the app or by the server. States: `active`, `resolved` (attended), `cancelled`, `expired` | Only one active alert per user |
| Activation method | How the alert was started: `panic_button`, `contact`, `suspicious_movement` (shake); trips and automatic protection are created by the server | Recorded as a label; it does not change the priority |
| Cancellation window | 5 seconds after requesting the activation during which the alert can be cancelled without being registered (RN-08) | Countdown |
| Cancellation PIN | 4 to 6 digit PIN required to cancel or finish an issued alert | RF-ACT-008 |
| Duress PIN | 4 to 6 digit PIN, different from the cancellation PIN, that simulates cancelling while contacts receive a silent notice | Emergency PIN; RF-ACT-007 |
| Discreet mode | Fixed notifications show neutral texts ("Service active") without revealing protection or recording | RF-NOT-005 |
| Tracking link | 256-bit random code sent by email that opens the public tracking page; only its hash is stored and it expires 2 hours after the alert ends | `notification.tracking_link` |
| Public tracking page | Web page for contacts, without account: live map, route, chat, recordings and access to 123 | `/seguimiento/` |
| Resend of the notice | Second notice sent once if no contact opens the first one within 5 minutes | RF-ALE-006 |
| Lost signal | The active alert or trip stops receiving the user's location for the configured time; contacts are warned with the last location | RF-ALE-009 |
| "Notify me if I don't arrive" | Trip with destination and estimated arrival time; the server creates the alert if arrival is not confirmed or the heartbeat stops | Safe trip; `alert.safe_trip` |
| Automatic protection | Surveillance that starts by itself when leaving a frequent place and asks silent questions if the user stops in an unknown place | `alert.protection_device`; RF-TRA-003 |
| Frequent place | Place saved by the user with a safety level `muy_segura`, `moderada` or `muy_insegura` | `alert.frequent_location` |
| Zone | Geographic area defined by the administration: `risk`, `safe`, `help`, `policia`, `asistencia` | `resource.zone` |
| Evidence | Video, audio or photo recorded during an alert, stored in Backblaze B2 with its SHA-256 hash | Kept 30 days; up to 5 alerts can be kept |
| Main phone | The only device that reports the daily location (every 50 m or 5 min) | `identity.device` |
| Assistance module | Lines 123, purple line and mental health line, opened in the dialer | RF-REC-001 |
| Moderation | Review of reports about users and the actions taken; executed by the administrator | `admin.user_report`, `admin.moderation_action` |

---

## Technical terms of the project

| Term | Definition |
|------|-----------|
| Hexagonal architecture | Each of the nine backend modules separates `domain` (pure models), `application` (use cases and ports) and `adapter` (REST, schedulers, events, persistence and external services). Checked with `HexagonalArchitectureTest` (ArchUnit) |
| Shared kernel | Maven module shared by the three services: JWT security, error handling, `*Api` contracts and the RPC client |
| `*Api` contract | Interface published by a module (`IdentityApi`, `AlertsApi`, `DevicesApi`, `SettingsApi`, `ZonesApi`, `EvidenceApi`). In the same service it is a local call; in another service it becomes an HTTP call |
| RPC client | `RpcClientFactory`: generic HTTP client that calls `POST /internal/rpc/{Contract}/{method}` with a 90-second timeout |
| Internal key | `X-Internal-Key` header required by `/internal/**`; without it the service answers 403 |
| Client-side routing | There is no API Gateway: each client chooses the service by the start of the path (`serviceRoutes.ts` in mobile, `service-routes.ts` in web) |
| MVVM | Mobile pattern: each feature has `view/`, `viewModel/` (hooks `useXxxViewModel`) and `style/` |
| Foreground service | Android service of the Kotlin module that keeps the alert, the trip and automatic protection running with the app closed |
| Cold start | Delay of the free Render plan when waking a sleeping service (up to about 90 seconds) |
| Changeset | Liquibase migration unit; an applied changeset is never edited |

---

## Acronyms

| Acronym | Meaning |
|---------|---------|
| SRS | Software Requirements Specification (Document 02) |
| RF / RNF | Functional / non-functional requirement (Spanish: requisito funcional / no funcional) |
| RN | Business rule (regla de negocio) |
| CU | Use case (caso de uso) |
| HU | User story (historia de usuario) |
| CP | Test case (caso de prueba) |
| PM | Manual test (prueba manual) with the release APK |
| CAL | Quality finding in Document 08 |
| JWT | JSON Web Token |
| FCM | Firebase Cloud Messaging |
| APK / AAB | Android application package / Android App Bundle |
| EAS | Expo Application Services (APK build) |
| CI | Continuous integration (GitHub Actions) |
| SENA | Servicio Nacional de Aprendizaje |
| ADSO | Análisis y Desarrollo de Software (training program) |
| INS / INMLCF | National Health Institute / National Institute of Legal Medicine and Forensic Sciences |
