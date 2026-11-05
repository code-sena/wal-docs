# System Overview

---

## What is Woman Alert?

Woman Alert (ALERTA MUJER) is a personal safety solution made up of an Android mobile application, a complementary web platform, a backend of three Spring Boot microservices and a PostgreSQL 16 database. It is aimed primarily at women who may be in a risk situation, without restricting its use by gender.

Alerts are activated **only from the mobile app**, with as few steps as possible: SOS button, alert to a single contact, shaking the phone, the Android home screen widget, and server-side activation when a "Notify me if I don't arrive" trip is not confirmed or when automatic protection loses the signal. Once the alert is issued, every trusted contact receives an email with a link to a public tracking page and, if they have the app installed with an active session, a push notification with an alarm. The web platform does not activate alerts: it is used to consult and administer information and to host the public tracking page for contacts.

Woman Alert is a complementary support tool. It depends on connectivity, GPS, battery, device permissions and third-party services, so it does not guarantee that an alert reaches its recipient and it does not replace the National Police, the Attorney General's Office, Family Commissioners or the official emergency lines (123 and 155).

## Problem it solves

**Without the system:** asking for help during an emergency is not always possible. Lack of signal, difficulty sharing the location, fear of being discovered, not knowing the official support routes and the time it takes to reach trusted people can delay or prevent a request for help. In Colombia, the INS reported in 2024 (preliminary figures) 154,312 suspected cases of gender-based and domestic violence, 76.2 % of them affecting women; in Huila the rate was 509.5 cases per 100,000 inhabitants, above the national rate.

**With the system:** the user configures her trusted contacts in advance and can trigger an alert in seconds, with a 5-second window to cancel it. While the alert is active the app sends her location periodically, records video and then audio as evidence, and opens a chat with her contacts. Contacts follow the alert from a web page without needing an account. The server resends the notice if nobody opens it in 5 minutes, warns contacts if the location stops arriving, asks the user at minute 25 whether she is still in danger and closes the alert at the 2-hour limit. Discreet options are available: cancellation PIN, duress PIN (simulates cancelling and notifies contacts silently), cancelling with four presses of the volume-down button and a discreet mode with neutral notification texts.

## Primary users

| Role | Description | What do they do in the system |
|------|-------------|--------------------------|
| User | Person (mainly women) with a compatible Android phone and Internet access | Registers, configures trusted contacts, activation methods and PINs, activates alerts, starts trips, enables automatic protection, and checks her history, locations and evidence from the app or the user web portal |
| Trusted contact | Family member or friend registered by the user; does not need an account | Receives the email with the tracking link (and push if they have the app), follows the alert on the map, writes in the alert chat and marks the notice as received |
| Administrator | Authorized staff with the `admin` role (there is no separate moderator role) | Uses the admin panel: dashboard, users, alerts, zones and help points, reports, evidence, moderation, devices, read-only audit log and system parameters |

## Technology stack

| Layer | Technology | Justification |
|-------|-----------|---------------|
| Mobile interface | Expo SDK 54, React Native 0.81, React 19.1 and TypeScript 5.9 (strict), MVVM organized by feature; native Expo module in Kotlin (`shake-sensor`, 11 classes) | Access to sensors, background location, camera, microphone and foreground services that keep working with the app closed or the screen off |
| Web interface | Angular 21.2 with standalone components, signals and lazy loading; Bootstrap 5.3.8, Leaflet 1.9.4, Firebase 13 | Public landing, public tracking page, user portal and admin panel in one SPA with role guards |
| Server | Java 17, Spring Boot 3.2.0, three microservices (Auth, Alert, Evidence) with a shared kernel and hexagonal architecture in nine modules | Separates identity, alerts and evidence; domain rules are free of framework code and checked with ArchUnit |
| Database | PostgreSQL 16, five schemas, 35 tables, versioned with Liquibase (v1.0.0 to v1.6.0, changeset 25) | Relational integrity, CHECK constraints and audit triggers |
| External services | Firebase Cloud Messaging, Google Sign-In / Firebase Authentication, Resend (Brevo as alternative), Backblaze B2, MapLibre + OpenFreeMap (mobile), Leaflet + OpenStreetMap (web), Nominatim | Push, Google login, email by HTTP API (Render blocks SMTP), persistent evidence storage and maps without Google Maps billing |
| Hosting | Render (three Docker services + PostgreSQL, free plan), Vercel (web), GitHub Releases (APK) | Automatic publication from the `main` branch |

## Current status

- **Phase:** final delivery of the SENA training project. New features were frozen on 24/10/2026; after that date only automatic protection (approved before the freeze) and bug fixes were added.
- **Current version:** APK v1.0.1 published on GitHub Releases on 28/10/2026 (it replaced v1.0.0 the same day with the 15, 25 and 35-minute automatic protection times) (Android 7.0 / API 24 or higher, compiled for API 36). Render and Vercel publish from `main` since 28/10/2026.
- **Latest delivery:** SRS v2.6 (102 functional and 25 non-functional requirements), Software Analysis v3.4, Technical Proposal v4.5, Software Design v5.2, Database (script, data dictionary and ER diagrams), Test Plan v7.1, Quality and Improvements v8.1, Implementation v9.1 and the technical, installation, user and administrator manuals.
- **Verification:** 553 automated tests pass (backend 351, web 39, mobile 163) and GitHub Actions runs them on every pull request. Manual tests of the native features with the release APK (PM-04 to PM-09) and end-to-end evidence of the notices between two phones are still pending.
- **Next milestone:** run and record the pending manual tests, close the open security findings (web token in `localStorage`, no switch for daily location sharing, no own encryption of evidence files) and write the privacy policy, data treatment policy and terms and conditions.

## Project contacts

| Role | Name | Contact |
|------|------|---------|
| Project Leader / Backend | Maicol Stiven Sánchez Castañeda | Through the team channel |
| Database Leader | Justin Daniela Bahamón Baloco | Through the team channel |
| Mobile App Design / UI Lead | José Sebastián Suárez Cumaco | Through the team channel |
