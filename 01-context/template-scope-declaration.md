# Project scope statement template

> **Document type:** Scope and limits statement  
> **Target audience:** Project leaders, stakeholders, academic instructors, development team  
> **Language Convention:** English (according to ADR-001)  

---

## 1. General description and objective of the project

* **Project name:** Woman Alert (ALERTA MUJER)
* **Release/Target Phase:** Final academic delivery - SENA, ADSO program, group 3145556. APK v1.0.1 (28/10/2026)
* **Project Purpose:** Provide an integrated solution, made up of an Android app from which emergency alerts are activated and a complementary web platform for consultation and administration, so that women can ask for help quickly, share their location with their trusted contacts and access information related to risk situations.

---

## 2. Features in scope (what will be built)

List the functions, modules, and explicit user flows included in this scope. Group them logically by platform or domain.

### 2.1 Mobile application (React Native / Expo)
* [x] **User authentication and profile:** registration with acceptance of terms, login with email or Google, JWT session (access 24 h, refresh 15 days) stored in `expo-secure-store`, recovery with a 6-digit email code, profile editing and account deletion.
* [x] **Emergency activation:** SOS button with a 5-second countdown, alert to a single contact, shake detection (4.5 g threshold, native foreground service), home screen widget, cancellation PIN, duress PIN, cancel with volume-down ×4 and discreet mode.
* [x] **Location during the alert:** periodic location sent to the server (every 15 s for the first 5 min, then 30 s, or 60 s in saving mode), also with the app closed. Last recorded location shown with its date and time.
* [x] **Emergency Contact Management:** add from the phone book or manually, edit and delete trusted contacts (`emergency_contact`); default limit 5, validated on the server.
* [x] **Evidence Collection:** automatic video and then audio recording during the alert, chunked uploads with a retry queue, private copy outside the gallery (`evidence`).
* [x] **Trips and automatic protection:** "Notify me if I don't arrive" (`safe_trip`) and automatic protection when leaving a frequent place (`protection_device`, geofences).
* [x] **Map, places and zones:** MapLibre map with safe and risk zones (`zone`), saved places with safety level (`frequent_location`), saved routes (`saved_route`) and assistance lines.
* [x] **Notifications:** contact alarm until silenced, inbox with a bell, alerts received from contacts.
* [x] **Internationalization and customization:** `es`, `en`, `fr`, `pt` and six themes (light, dark and four colors) (`user_preference`).

### 2.2 Web Administration Panel (Angular)
* [x] **Executive Dashboard:** number of users, attended and cancelled alerts, alerts by time of day, zones with more incidents and comparisons.
* [x] **User and Device Management:** paginated user list, editing and blocking with reason; devices and their status.
* [x] **Alert Inspection:** list of alerts, status change (active, attended, cancelled, expired) and route of active or attended alerts only.
* [x] **Zone and Resource Management:** CRUD of zones and help points; approval or rejection of zone reports.
* [x] **Reports:** creation, editing and deletion of alert reports with PDF and CSV export.
* [x] **Moderation and auditing:** user reports, moderation actions, account deletion approved by two administrators and read-only audit log with filters and export (`audit_log`).
* [x] **System Configuration:** alert duration (30 to 1440 min), emergency number, contact limit and minimum app version (`system_configuration`), with confirmation before saving and a system status page.
* [x] **User portal and public pages:** landing with APK download, public tracking page for contacts and authenticated user portal.

### 2.3 Backend and infrastructure (Java/Spring Boot/PostgreSQL)
* [x] **REST API:** three microservices (Auth 51 endpoints, Alert 93, Evidence 14 in `main`) with hexagonal architecture and a shared kernel; internal calls through `/internal/rpc` and `/internal/events` protected with `X-Internal-Key`.
* [x] **Shared PostgreSQL Database:** PostgreSQL 16 with 35 tables in five schemas (`identity`, `alert`, `resource`, `admin`, `notification`), 42 relationships, versioned with Liquibase.
* [x] **Notification delivery:** email (Resend) and push (FCM) per contact, result recorded per contact and channel in `alert_contact` (`sent`, `failed`, `expired`), one resend after 5 minutes and lost-signal notice.
* [x] **Scheduled tasks:** alert expiration, resend, trips and automatic protection, evidence retention (30 days) and location retention (7 and 30 days).

---

## 3. Out-of-scope features (what will NOT be built)

The following features and technical capabilities are **explicitly excluded** from the current version of the project to maintain project focus, avoid scope changes, and comply with architectural guidelines:

* [ ] **Lock screen and power button activation:** removed from the app on 23/10/2026 and recorded as "Retired" in the SRS.
* [ ] **Offline alert creation / SMS backup:** creating an alert requires Internet. SMS, WhatsApp and RCS are not notice channels by team decision.
* [ ] **Automatic calls:** the app only opens the dialer; the user starts the call.
* [ ] **Locating a phone that is off or offline:** only the last recorded location is available.
* [ ] **API Gateway:** there is no gateway in production; each client routes requests to the right service by path.
* [ ] **Physical separation of databases by microservice:** the three services share one PostgreSQL instance; each service owns its schemas and asks for other data through `*Api` contracts.
* [ ] **iOS and mobile web client:** the app is distributed only as an Android APK.
* [ ] **Own encryption of evidence files:** evidence relies on HTTPS/TLS in transit; files are not encrypted with an application key.
* [ ] **Separate moderator role:** moderation is performed by the administrator.

---

## 4. Deliverables and technical artifacts

The final result of this phase of the project includes the following artifacts:

| Deliverable | Description | Format / Repository |
|---|---|---|
| **Mobile client** | React Native Expo Android app, v1.0.1 | `.apk` on GitHub Releases / `AlertaMujer` repository |
| **Web client** | Angular SPA (landing, tracking, user portal, admin panel) | Vercel / `Frond-end-Web` repository |
| **Backend Services** | Three Spring Boot microservices + shared kernel | Maven multi-module / `Backend-ALERTA-MUJER` repository |
| **Database schema** | Structure-only script, data dictionary and ER diagrams (35 tables) | `.sql`, `.xlsx`, `.mmd`, `.png`, `.svg` |
| **Technical documentation**| SRS, analysis, technical proposal, design, test plan, quality, implementation and manuals | `.docx` / `.pdf` and this Markdown repository |


### Minimum Technical Requirements (System Constraints)

#### Mobile Client (Android Native App)
* **Operating System:** Android 7.0 (API 24) minimum, compiled for Android 16 (API 36)
* **Memory (RAM):** not measured; tested on a Samsung SM-A125M (Android 12) and an Infinix X678B
* **Hardware Sensors:** GPS, accelerometer, camera and microphone
* **Storage:** space for the app and the local copy of the evidence
* **Network Connectivity:** mobile data or Wi-Fi to create the alert and send the location

#### Web Administration Client (Angular Dashboard)
* **Web Browser:** recent versions of Google Chrome, Microsoft Edge and Mozilla Firefox
* **Display Resolution:** desktop resolutions first; mobile view available but not prioritized
* **Memory (RAM):** not defined
* **Network Connectivity:** Internet connection

---

## 5. Assumptions, dependencies and scope restrictions

### 5.1 Key assumptions
* Users own Android 7.0+ smartphones with GPS, Internet and the permissions required by the protection features.
* External services (Firebase Cloud Messaging, Resend, Backblaze B2, Render, Vercel) keep their standard availability during testing and evaluation.

### 5.2 Technical restrictions
* **Language Convention (ADR-001):** All technical artifacts, code, database schemas, and documentation are in **English**. User-facing text is localized via i18n.
* **Architecture:** Hexagonal architecture for the nine backend modules (checked with ArchUnit), MVVM pattern for the mobile app and core/features/shared structure with standalone components for the web.
* **Database:** Shared PostgreSQL 16 instance for the three microservices; only Auth applies the migrations.
* **Hosting:** free Render plan with cold start; free database with an expiration date.

---

## 6. Acceptance and approval of the project scope

By signing below, project stakeholders acknowledge and agree that this document accurately defines the scope, boundaries, and expected outcomes for the specified phase.

| Role | Name | Signature / Approval | Date |
|---|---|---|---|
| **Project Leader / Backend Leader** | Maicol Stiven Sánchez Castañeda | `[Approved/Pending]` | `[YYYY-MM-DD]` |
| **Database Leader** | Justin Daniela Bahamón Baloco | `[Approved/Pending]` | `[YYYY-MM-DD]` |
| **Design Lead** | José Sebastián Suárez Cumaco | `[Approved/Pending]` | `[YYYY-MM-DD]` |
| **Academic instructor** | Motta Vargas José de Jesús | `[Approved/Pending]` | `[YYYY-MM-DD]` |
