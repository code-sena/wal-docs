# Project scope statement template

> **Document type:** Scope and limits statement  
> **Target audience:** Project leaders, stakeholders, academic instructors, development team  
> **Language Convention:** English (according to ADR-001)  

---

## 1. General description and objective of the project

* **Project name:** Woman Alert
* **Release/Target Phase:** MVP Academic Delivery - SENA File 3145556
* **Project Purpose:** Provide a mobile and web security ecosystem that allows women in vulnerable situations to activate emergency alerts and share real-time location with trusted contacts.

---

## 2. Features in scope (what will be built)

List the functions, modules, and explicit user flows included in this scope. Group them logically by platform or domain.

### 2.1 Mobile application (React Native / Expo)
* [ ] **User authentication and profile:** User registration, login, JWT session management, account recovery flow and profile configuration.
* [ ] **Emergency activation:** Panic button activation, widget activation, physical button assignment and discrete confirmation mechanisms.
* [ ] **Real-time location sharing:** GPS tracking during active alerts (`location_log`), streaming coordinates in real-time via WebSockets, and viewing location history.
* [ ] **Emergency Contact Management:** Add, edit and delete trusted contacts (`emergency_contact`) with required phone numbers.
* [ ] **Evidence Collection:** Capturing photographs, audio and video recordings associated with an active emergency alert ("evidence").
* [ ] **Smart map and safe zones:** Interactive map showing safe/risk zones (`zone`) and nearby emergency assistance centers (`emergency_resource`).
* [ ] **Internationalization and customization:** Localized user interface (`es`, `en`, `fr`, `pt`) and theme customization (`user_preference`).

### 2.2 Web Administration Panel (Angular)
* [ ] **Executive Dashboard:** High-level metrics, active alert counters, and system-wide user statistics.
* [ ] **User and Device Management:** View and manage user accounts, inspect device permissions, and manage user status.
* [ ] **Alert and Test Inspection:** Real-time alert monitoring, location history tracking, and test review for administrators.
* [ ] **Zone and Resource Management:** CRUD operations for risk/safe geographic zones and emergency directory resources.
* [ ] **Moderation and auditing:** Review reported users, perform administrative actions, and maintain audit logs (`audit_log`).
* [ ] **System Configuration:** Manage global platform parameters through single-instance configuration settings (`system_configuration`).

### 2.3 Backend and infrastructure (Java/Spring Boot/PostgreSQL)
* [ ] **RESTful API and WebSockets:** Business logic endpoints and real-time streaming connections.
* [ ] **Shared PostgreSQL Database:** Relational data persistence supporting 27 core domain tables across 4 logical microservices.
* [ ] **Notification resilience and delivery:** Asynchronous notification sending strategy (Push, SMS, Email) with exponential retry rollback policies (`alert_contact`).

---

## 3. Out-of-scope features (what will NOT be built)

The following features and technical capabilities are **explicitly excluded** from the current version of the project to maintain project focus, avoid scope changes, and comply with architectural guidelines:

* [ ] **Biometric/Fingerprint Emergency Activation:** Biometric or fingerprint gestures will not be used to activate panic alerts; Activation is based on UI buttons, widgets, or physical button shortcuts designated in the app.
* [ ] **Completely Offline Emergency Mode / Native SMS Backup:** The app requires an active Wi-Fi or mobile data connection to transmit live GPS coordinates via WebSockets and communicate with the backend. Pure offline SMS emergency activation is out of reach.
* [ ] **User-driven zone reclassification:** End users cannot automatically create or reclassify risk level zones. Management of geographic risks and safe zones ("zone") is strictly restricted to administrative users via the Web Dashboard.
* [ ] **Customized contact ringtones and direct calls to operator:** Customized emergency audio tones per contact and automated direct phone calls over telecom operator networks are excluded.
* [ ] **Physical separation of databases by microservice:** Although the backend follows a logical architecture of 4 microservices (`identity`, `alert`, `resource`, `admin`), implementing physical database engines separated by service is out of scope. The platform uses a single shared PostgreSQL database instance.
* [ ] **Native Mobile Web Client:** The mobile UI is delivered exclusively as a native Android/iOS app built with React Native/Expo, not as a mobile browser web app.
* [ ] **Native Hardware Local Audio/Video Encryption:** Evidence uploaded to the system relies on standard HTTPS/TLS encryption in transit rather than the integration of a custom cryptographic chip into the device.

---

## 4. Deliverables and technical artifacts

The final result of this phase of the project includes the following artifacts:

| Deliverable | Description | Format / Repository |
|---|---|---|
| **Mobile client** | React Native Expo Android App Package | `.apk` / Mobile Repository |
| **Web administration client** | Angular Web App | Web/web repository creation |
| **Backend Services** | Spring Boot Microservices Codebase | Java Project / Backend Repository |
| **Database schema** | DDL Migration Scripts and DBML Documentation | `.sql` / `.dbml` files |
| **Technical documentation**| IEEE 830 SRS, ADR, Domain Maps and API Specifications | Markdown Repository (`.md`) |


### Minimum Technical Requirements (System Constraints)

#### Mobile Client (Android Native App)
* **Operating System:** Android 10.0 (API Level 29) minimum
* **Memory (RAM):** 3 GB RAM minimum
* **Hardware Sensors:** Integrated GPS (A-GPS), primary camera, and microphone
* **Storage:** 200 MB available internal storage
* **Network Connectivity:** Activate 4G/5G mobile data or Wi-Fi connection (required for real-time WebSocket coordinate streaming)

#### Web Administration Client (Angular Dashboard)
* **Web Browser:** Modern HTML5/ES6 compliant browser (Google Chrome 110+, Mozilla Firefox 110+, or Microsoft Edge 110+)
* **Display Resolution:** Minimum 1280x720 pixels
* **Memory (RAM):** 4 GB RAM minimum
* **Network Connectivity:** Broadband internet connection (minimum 5 Mbps)

---

## 5. Assumptions, dependencies and scope restrictions

### 5.1 Key assumptions
* End users own **Android 10+** smartphones with active GPS and Internet connectivity.
* External services (e.g. Firebase Cloud Messaging, Twilio, Google Maps) maintain standard service availability during testing and evaluation.

### 5.2 Technical restrictions
* **Language Convention (ADR-001):** All technical artifacts, code, database schemas, and documentation are in **English**. User-facing text is localized via i18n.
* **Architecture:** Hexagonal architecture for backend modules, MVVM pattern for mobile frontend and component/function-based structure for web.
* **Database:** Shared PostgreSQL instance for all microservices[cite: 1, 2].

---

## 6. Acceptance and approval of the project scope

By signing below, project stakeholders acknowledge and agree that this document accurately defines the scope, boundaries, and expected outcomes for the specified phase.

| Role | Name | Signature / Approval | Date |
|---|---|---|---|
| **Project Leader / Backend Leader** | Michael Stiven Sánchez Castañeda | `[Approved/Pending]` | `[YYYY-MM-DD]` |
| **Database Leader** | Justin Daniela Bahamón Baloco | `[Approved/Pending]` | `[YYYY-MM-DD]` |
| **Design Lead** | José Sebastián Suárez Cumaco | `[Approved/Pending]` | `[YYYY-MM-DD]` |
| **Academic instructor** | Motta Vargas José de Jesús | `[Approved/Pending]` | `[YYYY-MM-DD]` |