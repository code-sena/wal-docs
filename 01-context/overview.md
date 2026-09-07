# System Overview

---

## What is Alerta Mujer?

Alerta Mujer is a system made up of a mobile application (Android) and a web platform, designed to provide fast, secure, and discreet assistance to women in situations of risk or gender-based violence. It lets a user trigger a panic button that sends alerts with real-time location to trusted contacts configured by the user, through multiple channels (SMS, email, push notifications).

## Problem it solves

**Before the system:** in a situation of gender-based violence, women lack accessible and secure tools to quickly reach their support networks; awareness of care routes is limited and the available channels are poorly connected, so many victims don't receive timely support and remain at risk.

**With the system:** the user can trigger an emergency alert within seconds (via widget, sudden movement, physical buttons, or a discreet confirmation), share her real-time location with trusted contacts, back up evidence (audio/video), and check a directory of support institutions, safe/unsafe zones, and action guides — reducing emergency response times.

## Primary users

| Role | Description | What they do in the system |
|------|-------------|--------------------------|
| User | A person with basic knowledge of mobile apps and network usage (mobile data or wifi) | Triggers alerts, manages emergency contacts, registers frequent locations, and checks her history, location, and evidence (from the mobile app or from the authenticated-user web portal) |
| Administrator | Authorized staff for operational management and oversight of the system, with basic knowledge of admin web platforms | Manages users, alerts, zones, reports, devices, system configuration, and moderation, according to their role's permissions |

## Tech stack

| Layer | Technology | Rationale (per the SRS) |
|-------|-----------|---------------|
| Mobile frontend | React Native + Expo (Android), MVVM architecture organized by feature | Enables access to hardware features and permissions required (background GPS, camera, microphone, sensors) |
| Web frontend | Angular, Feature-Based Architecture | Separates the user modules (dashboard, contacts, alerts, users), the admin modules, and the public site (landing) |
| Backend | Spring Boot + Spring Security + Spring Data JPA, adapted hexagonal architecture (ports and adapters) organized by business modules | Builds the server logic, APIs, and database connection; supports multiple concurrent connections and fault tolerance |
| Database | PostgreSQL, with Liquibase for schema versioning and change control | Relational engine for securely recording events, alerts, and users |
| External services | Firebase Cloud Messaging (push notifications), SMS messaging service (e.g. Twilio), email service | Sending alerts through multiple channels to increase the chance of delivery |

## Current status

- **Phase:** Requirements specification — the current deliverable is the SRS itself (IEEE 830 standard, as referenced in the document's own reference table), which defines what will be built before design and development begin.
- **Current version:** Not tracked yet — the project is still at the SRS stage, before a first release.
- **Latest delivery:** The SRS document, covering functional requirements (RF1–RF13), non-functional requirements, and use cases 1–4.
- **Next milestone:** Moving from requirements to design/development — turning the SRS's functional requirements and entities into the architecture, data model, and UI mockups referenced throughout the document (e.g. "Mockup de perfil de usuaria", "Flujo de localización constante").

## Project contacts

| Role | Name | Contact |
|------|------|---------|
| Project Lead / Backend | Michael Sanchez Castañeda | samaicol777@gmail.com |
| Database Lead | Justin Daniela Bahamon Baloco | dannybahamon613@gmail.com |
| Mobile App Design / UI Lead | Jose Sebastian Suarez Cumaco | masquebugs1@gmail.com |
