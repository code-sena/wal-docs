# Project Glossary

---

## How to use this glossary

1. Before using a technical or business term in code, docs, or conversations: look it up here.
2. If it's missing: add it with its definition.
3. If there's disagreement about the definition: discuss it as a team and update this document.

---

## Domain terms

| Term | Definition | Notes / Synonyms |
|------|-----------|-----------------|
| User | Person who will use the Alerta Mujer application | |
| Panic button | Feature that sends automatic alerts with real-time location, triggerable via widget, sudden movement, physical buttons, or a discreet confirmation | |
| Alert | Event generated when the user triggers the panic button; includes real-time location and is sent to emergency contacts through multiple channels (SMS, email, push) | |
| Emergency contact | Trusted person configured by the user to receive alerts and follow real-time tracking while the alert is active | Support network |
| Safe / unsafe zone | Geographic area classified by risk level, tied to the frequent locations registered by the user | |
| Evidence | Audio and/or video recording captured and backed up during an alert | |
| Emergency resource directory | List of support institutions (hotlines, health centers, police stations, shelters, support organizations), kept up to date by administrative staff (RF13) | |
| Authenticated user portal | Web platform component where the user reviews her history, location, evidence, and device status | Web equivalent of the mobile app's features |
| Admin panel | Web platform component, restricted to administrators, for managing users, alerts, zones, evidence, reports, system configuration, and moderation | |
| Public informational site | The web platform's landing page, no authentication required | |

---

## Technical project terms

| Term | Definition |
|------|-----------|
| MVVM (organized by feature) | Architectural pattern used in the mobile app (React Native + Expo), where code is organized by feature rather than by technical layer |
| Feature-Based Architecture | Architectural pattern used in the web platform (Angular), separating the user modules (dashboard, contacts, alerts, users), the admin modules, and the public site (landing) |
| Adapted hexagonal architecture | Pattern used in the backend (Spring Boot), organized by business modules, with ports and adapters |
| REST API | Interface through which the mobile app, web platform, and backend communicate, fully decoupling the client from the server |

---

## Acronyms

| Acronym | Meaning |
|---------|---------|
| User | Person who will use the application |
| SRS | Software Requirements Specification |
| RF | Functional Requirement |
| RNF | Non-Functional Requirement |
| SENA | Servicio Nacional de Aprendizaje |
| CU | Use Case |
| FCM | Firebase Cloud Messaging |
