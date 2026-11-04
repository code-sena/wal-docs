# C4 Container Diagram — Woman Alert

> C4 Level 2 (Container) diagram of the system deployed from `main` on 28/10/2026. It uses Mermaid,
> which renders natively on GitHub, GitLab and most modern editors (VS Code with an extension,
> Obsidian, etc.).

---

## System: Woman Alert

```mermaid
C4Container
    title Container Diagram — Woman Alert

    Person(usuaria, "User", "Activates alerts from the Android app; consults her information on the web")
    Person(contacto, "Trusted contact", "Receives the notice by email (and push if they have the app); follows the alert without an account")
    Person(admin, "Administrator", "Manages users, alerts, zones, reports, evidence, moderation and configuration")

    System_Boundary(sistema, "Woman Alert") {

        Container(movil, "Mobile app", "Expo 54, React Native 0.81, TypeScript, Kotlin module", "Only channel that activates alerts. MVVM by feature. Foreground services for shake, recording, trips and automatic protection.")

        Container(web, "Web", "Angular 21", "Landing with APK download, public tracking page, user portal and admin panel under /admin.")

        Container(auth, "Auth Service", "Java 17, Spring Boot 3.2", "identity, devices, settings, admin. Issues JWT. Applies the Liquibase migrations.")

        Container(alert, "Alert Service", "Java 17, Spring Boot 3.2", "alerts, notifications, zones, resources. Notices, tracking, trips, automatic protection, scheduled tasks.")

        Container(evidence, "Evidence Service", "Java 17, Spring Boot 3.2", "Evidence upload, SHA-256, download, review and 30-day retention.")

        ContainerDb(db, "PostgreSQL 16", "Render PostgreSQL", "Schemas identity, alert, resource, admin, notification. 35 tables. Each service owns its schemas.")
    }

    System_Ext(fcm, "Firebase", "Cloud Messaging (push) and Google login verification")
    System_Ext(mail, "Resend", "Email by HTTP API (Brevo as alternative)")
    System_Ext(b2, "Backblaze B2", "S3-compatible storage for evidence")
    System_Ext(maps, "Map tiles", "MapLibre + OpenFreeMap, Leaflet + OpenStreetMap, Nominatim")

    %% People → system
    Rel(usuaria, movil, "Activates alerts, configures contacts", "Android")
    Rel(usuaria, web, "Consults her information", "HTTPS")
    Rel(contacto, web, "Opens the tracking link", "HTTPS")
    Rel(admin, web, "Administers", "HTTPS")

    %% Clients → services (no gateway: routing by path)
    Rel(movil, auth, "Login, profile, devices", "HTTPS / REST + JWT")
    Rel(movil, alert, "Alerts, location, contacts, trips", "HTTPS / REST + JWT")
    Rel(movil, evidence, "Uploads evidence", "HTTPS / multipart")
    Rel(web, auth, "Login, admin panel", "HTTPS / REST + JWT")
    Rel(web, alert, "Portal data, public tracking", "HTTPS / REST")
    Rel(web, evidence, "Views and downloads evidence", "HTTPS / REST + JWT")

    %% Internal calls
    Rel(auth, alert, "AlertsApi, ZonesApi", "RPC /internal (X-Internal-Key)")
    Rel(alert, auth, "IdentityApi, DevicesApi, SettingsApi", "RPC /internal")
    Rel(alert, evidence, "EvidenceApi", "RPC /internal")
    Rel(evidence, alert, "AlertsApi, IdentityApi", "RPC /internal")

    %% Database
    Rel(auth, db, "Reads / writes identity, admin; migrates", "SQL")
    Rel(alert, db, "Reads / writes alert, resource, notification", "SQL")
    Rel(evidence, db, "Reads / writes alert.evidence", "SQL")

    %% External systems
    Rel(alert, fcm, "Push notices", "HTTPS")
    Rel(auth, fcm, "Verifies Google tokens", "HTTPS")
    Rel(alert, mail, "Alert, end and recovery emails", "HTTPS")
    Rel(evidence, b2, "Stores files", "HTTPS / S3")
    Rel(movil, maps, "Map tiles and address search", "HTTPS")
    Rel(web, maps, "Map tiles", "HTTPS")
```

---

## How to use this diagram

1. **Render on GitHub:** the diagram renders automatically on push. No extra tool is needed.

2. **Render in VS Code:** install the "Markdown Preview Mermaid Support" extension or the official Mermaid extension.

3. **Export as an image:** use the [Mermaid CLI](https://github.com/mermaid-js/mermaid-cli):
```bash
mmdc -i c4-container-example.md -o ../exports/c4-container.svg
```

4. **Update the diagram:** the diagram lives in Git next to the documentation. When the architecture changes (new service, gateway, new storage), update it in the same pull request.

---

## Conventions for this project

| Element | Color / Style | When to use it |
|----------|---------------|---------------|
| `Container` | Blue | Deployable service or client application |
| `ContainerDb` | Blue cylinder | Database |
| `Person` | Person icon | Human actor (user, contact, administrator) |
| `System_Ext` | Gray | Third-party system we do not control |

---

## Correlations

- Service catalog → `09-microservices/service-catalog.md`
- Details of each service → `09-microservices/services/`
- Index of all diagrams → `08-uml/diagram-index.md`
- Architecture overview → `05-architecture/overview.md`
