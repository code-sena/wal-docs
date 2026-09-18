# System Overview

---

## What is Woman Alert?

Woman Alert is a system in development that includes a mobile application (Android) and a web platform, designed to provide quick, safe and discreet care to women at risk or gender violence. Allows the user to activate a panic button that sends alerts with real-time location to trusted contacts configured by the user, through multiple channels (SMS, email, push notifications).

## Problem it solves

**In the face of the system:** in a situation of violence, women lack accessible and safe tools to quickly reach their support networks; Knowledge of care routes is limited and available channels are poorly connected, so many victims do not receive timely support and remain at risk.

**With the system:** the user can activate an emergency alert in seconds (via a widget, a sudden movement, physical buttons or a discreet confirmation), share their location in real time with trusted contacts, make backup copies of evidence (audio/video) and consult a directory of support institutions, safe/unsafe zones and action guides, reducing emergency response times.

## Primary users

| Role | Description | What do they do in the system |
|------|-------------|--------------------------|
| User | A person with basic knowledge of mobile applications and network use (mobile data or Wi-Fi) | Activate alerts, manage emergency contacts, log frequent locations, and check your history, location, and evidence (from the mobile app or authenticated user web portal) |
| Administrator | Authorized personnel for operational management and supervision of the system, with basic knowledge of web administration platforms | Manage users, alerts, zones, reports, devices, system settings, and moderation, based on your role permissions |

## Technology stack

| Layer | Technology | Justification (according to SRS) |
|-------|-----------|---------------|
| Mobile interface | React Native + Expo (Android), MVVM architecture organized by feature | Allows access to hardware features and necessary permissions (GPS in the background, camera, microphone, sensors) |
| Web interface | Angular Feature-Based Architecture | Separates the user modules (panel, contacts, alerts, users), the administration modules and the public site (landing) |
| Server | Spring Boot + Spring Security + Spring Data JPA, adapted hexagonal architecture (ports and adapters) organized by business modules | Create server logic, APIs, and database connection; supports multiple simultaneous connections and fault tolerance |
| Database | PostgreSQL, with Liquibase for schema versioning and change control | Relational engine to securely log events, alerts and users |
| External services | Firebase Cloud Messaging (push notifications), SMS messaging service (e.g. Twilio), email service | Sending alerts through multiple channels to increase delivery chances |

## Current status

- **Phase:** Requirements Specification: The current deliverable is the SRS itself (IEEE 830 standard, as mentioned in the document's own reference table), which defines what will be built before design and development begins.
- **Current version:** No follow-up yet: the project is still in the SRS stage, before a first release.
- **Latest delivery:** The SRS document, covering functional requirements (RF1–RF13), non-functional requirements and use cases 1–4.
- **Next Milestone:** Move from requirements to design/development: Convert the entities and functional requirements of the SRS into the architecture, data model, and UI mockups referenced throughout the document (e.g., "User Profile Mockup", "Consistent Localization Flow").

## Project contacts

| Role | Name | Contact |
|------|------|---------|
| Project Leader / Backend | Maicol Sánchez Castañeda | samaicol777@gmail.com |
| Database Leader | Justin Daniela Bahamón Baloco | dannybahamon613@gmail.com |
| Mobile App Design / UI Lead | José Sebastián Suárez Cumaco | masquebugs1@gmail.com |