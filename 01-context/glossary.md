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
| User | Person who will use the Woman Alert application | |
| Panic button | Function that sends automatic alerts with real-time location, activated by widget, sudden movement, physical buttons or a discreet confirmation | |
| Alert | Event generated when the user activates the panic button; includes real-time location and is sent to emergency contacts through multiple channels (SMS, email, push) | |
| Emergency contact | Trusted person configured by the user to receive alerts and follow real-time monitoring while the alert is active | Support network |
| Safe/unsafe zone | Geographic area classified by risk level, linked to frequent locations registered by the user | |
| Evidence | Audio and/or video recording captured and backed up during an alert | |
| Emergency Resource Directory | List of support institutions (hotlines, health centers, police stations, shelters, support organizations), kept up to date by administrative staff (RF13) | |
| Authenticated User Portal | Web platform component where the user reviews their history, location, evidence and device status | Web equivalent of mobile app features |
| Administration panel | Web platform component, restricted to administrators, for user management, alerts, zones, evidence, reports, system configuration and moderation | |
| Public information site | The home page of the web platform does not require authentication | |

---

## Technical terms of the project

| Term | Definition |
|------|-----------|
| MVVM (organized by function) | Architectural pattern used in mobile app (React Native + Expo), where code is organized by feature instead of technical layer |
| Function-based architecture | Architectural pattern used in the web platform (Angular), separating the user modules (dashboard, contacts, alerts, users), the administration modules and the public site (landing) |
| Adapted hexagonal architecture | Pattern used in the backend (Spring Boot), organized by business modules, with ports and adapters |
| REST API | Interface through which the mobile application, the web platform and the backend communicate, completely decoupling the client from the server |

---

## Acronyms

| Acronym | Meaning |
|---------|---------|
| User | Person who will use the application |
| SRS | Software Requirements Specification |
| RF | Functional requirement |
| RNF | Non-functional requirement |
| SENA | National Apprenticeship Service |
| CU | Use case |
| FCM | Firebase Cloud Messaging |