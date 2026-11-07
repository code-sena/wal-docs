# Administrator Manual - AlertaMujer

**Version:** 2.1  
**Date:** October 28, 2026  
**Audience:** Administrators of the web platform  
**Scope:** Web administration panel (routes starting with `/admin`). The mobile app is for users only and is not covered here.

---

## Table of contents

1. Introduction
2. Accessing the panel
3. Panel structure
4. Dashboard
5. User management
6. Alert management (Monitoring and Response Center)
7. Zones and help points
8. Reports
9. Evidence management
10. Moderation
11. Device management
12. Audit
13. System settings
14. My profile
15. Responsible use of information
16. Common problems
17. Known limitations
18. Glossary
19. Appendix: list of screenshots

---

## 1. Introduction

The administration panel is the part of the web platform from which users, alerts, zones, reports, evidence, moderation, devices and the general system settings are supervised. It also keeps an audit log of everything done in the panel.

All panel actions are saved on the server. What is not yet complete is summarized in section 17.

---

## 2. Accessing the panel

### 2.1 Logging in

1. Open the web address of the platform and go to **Iniciar sesión** (Log in).
2. Enter your **email** and your **password**, or use **Continuar con Google** (Continue with Google) if your administration account is linked to Google.
3. Press **INGRESAR** (Sign in).

<p align="center"><img src="img/web/admin/login.png" width="700" alt="Login screen"></p>

If your account has the **Administrator** role, the system takes you to `/admin/dashboard`. If it is a user account, it takes you to her own dashboard.

**Important:**
- Each administrator logs in with **her own account**. There are no shared accounts.
- Credentials are not written in this manual. Ask the responsible team for them.
- The first administrator account is created from the database (see the **Installation Manual**). It cannot be done from the panel.
- After 5 failed attempts in one minute, the system temporarily blocks attempts from that connection. Failed attempts are recorded in the audit log.

### 2.2 Session duration

- The session lasts at most 24 hours from login.
- It also closes by itself if the panel is left open without use. The time is chosen in **Mi perfil > Cerrar sesión por inactividad** (My profile > Log out after inactivity; for example, 15 minutes) and applies to the browser where you set it.
- If the server responds "unauthorized", the system logs you out and returns to the login page.
- To log out yourself: use the button next to your name at the end of the side menu, the account menu in the top bar or **Cerrar sesión** (Log out) in **Mi perfil**.

Every login, logout, logout due to inactivity, password change and failed attempt is recorded in **Auditoría** (Audit) with the date, the browser and the IP address.

### 2.3 What happens if I am not an administrator

Administration screens only exist under `/admin` and are protected by role. If you log in with an account without the administrator role, the system sends you back to the login page. In addition, the server rejects any administration request that does not come from an account with that role.

---

## 3. Panel structure

```
+---------------------------------------------------------------------------+
| Menu  Hola, <name>              Bell  Theme  [Account v]  Language        |
+------------------------+--------------------------------------------------+
| ADMINISTRACIÓN         |                                                  |
|                        |                                                  |
| Dashboard              |                                                  |
| Gestión de usuarios    |          Screen content                          |
| Gestión de alertas     |                                                  |
| Zonas y puntos de ayuda|                                                  |
| Informes               |                                                  |
| Gestión de evidencias  |                                                  |
| Moderación             |                                                  |
| Gestión de dispositivos|                                                  |
| Auditoría              |                                                  |
| Configuración          |                                                  |
| Mi perfil              |                                                  |
|                        |                                                  |
| [Name and email] [Exit]                                                   |
+------------------------+--------------------------------------------------+
```

**Side menu (11 options):** Dashboard, Gestión de usuarios (User management), Gestión de alertas (Alert management), Zonas y puntos de ayuda (Zones and help points), Informes (Reports), Gestión de evidencias (Evidence management), Moderación (Moderation), Gestión de dispositivos (Device management), Auditoría (Audit), Configuración (Settings) and Mi perfil (My profile). At the bottom you see your name, your email and the log out button.

**Top bar:**
- **Menu:** opens or closes the side menu.
- **Greeting:** "Hola, ..." with your name.
- **Bell:** shows panel notifications, such as new alerts.
- **Theme:** switches between light and dark mode.
- **Account:** your name and your role (Administración), with access to your profile and logout.
- **Idioma (Language):** language selector. The panel is translated into English, Portuguese and French.

**On a phone or narrow screen:** the menu is a drawer that starts closed and closes by itself when you choose an option.

---

## 4. Dashboard

**Menu:** Dashboard

It is called **Dashboard de un vistazo** (Dashboard at a glance) and shows a summary of the system. Below the title is the time of the last update (for example, "Actualizado a las 22:21:34", updated at 22:21:34).

**Period:** at the top right there are four buttons to choose the data range: **Hoy**, **7 días**, **30 días** and **Personalizado** (Today, 7 days, 30 days and Custom). The figures change according to the period.

<p align="center"><img src="img/web/admin/dashboard-1.png" width="700" alt="Dashboard, cards and alerts by hour"></p>

**Top cards**

| Card | What it shows |
|---|---|
| Pendientes ahora (Pending now) | Alerts not yet attended at this moment ("Sin pendientes" if there are none) |
| Usuarias activas (Active users) | Number and percentage of all accounts |
| Atendidas (Attended) | Attended alerts and their percentage of the total |
| Canceladas (Cancelled) | Cancelled alerts and their percentage, with the note "posibles falsas alarmas" (possible false alarms) |

**Shortcuts:** below the cards are the **Alertas**, **Bloquear una cuenta**, **Zonas de riesgo** and **Reportes** buttons (Alerts, Block an account, Risk zones and Reports), which go straight to those screens.

**Alerts by hour of the day**

Bar chart with how many alerts were registered in each hour (0h to 23h), grouped by time slot: **Madrugada** (early morning, 0h to 5h), **Mañana** (morning, 6h to 11h), **Tarde** (afternoon, 12h to 17h) and **Noche** (night, 18h to 23h). The hours with the most alerts are highlighted. Above the chart there are three figures:
- **Hora pico** (Peak hour): the hour with the most alerts and how many there were.
- **Franja más riesgosa** (Riskiest slot): the slot with the most alerts, with the number and the percentage.
- **Franja más tranquila** (Quietest slot): the slot with the fewest alerts.

**Zones with the most incidents**

Map with the points where most alerts are triggered and, next to it, the ranking of the five zones with the most alerts (number and percentage). When you tap a zone in the list or a point on the map, the **Punto seleccionado** (Selected point) appears below with the address, the number of alerts, the coordinates and, if it falls inside a registered risk zone, which one.

<p align="center"><img src="img/web/admin/dashboard-2.png" width="700" alt="Dashboard, zones with the most incidents"></p>

**More or fewer alerts than last week?**

Compares the alerts of each day this week with those of the same day the previous week. At the top it shows the totals of both weeks and a sentence with the difference. Hover over a day to see the details.

<p align="center"><img src="img/web/admin/dashboard-3.png" width="700" alt="Dashboard, weekly comparison"></p>

**Recent alerts**

Table with the latest alerts: time, user, status and location (**Ver en mapa**, view on map), with the **Detalle** (Detail) link. It can be filtered by **Todas**, **Pendientes**, **Atendidas** and **Canceladas** (All, Pending, Attended and Cancelled).

<p align="center"><img src="img/web/admin/dashboard-4.png" width="700" alt="Dashboard, recent alerts"></p>

**If something goes wrong:** if the data does not load, you will see "No se pudieron cargar los datos del dashboard" (The dashboard data could not be loaded). Wait one or two minutes and reload: the server services may be starting up (see section 16).

---

## 5. User management

**Menu:** Gestión de usuarios

<p align="center"><img src="img/web/admin/usuarios.png" width="700" alt="User management"></p>

### 5.1 Viewing and searching

At the top there are counters: **Total, Activas, Bloqueadas, Inactivas** (Total, Active, Blocked, Inactive).

- **Search:** type a name, email or other data; the list filters as you type.
- **Filter by status:** with the **Filtrar por Estado** (Filter by status) selector or with the colored chips: **Todas**, **Activas**, **Inactivas** and **Bloqueadas por Fraude** (All, Active, Inactive and Blocked for fraud). Each chip shows how many accounts are in that status.
- **Pagination:** at the bottom right, with the total number of accounts ("Mostrando 1-6 de 16", showing 1-6 of 16).

The table shows: selection checkbox, Name (with its role: user or administrator), Email, Registration date, Alerts, Last activity, Status and Actions. Blocked rows are highlighted.

### 5.2 Viewing details and editing

1. In the row, press the eye icon (**Ver detalle**, view details).
2. The user's details open: name, email, status, phone, registration date, alerts sent, role and latest alerts.
3. Press **Editar** (Edit) to change her data and save. The change is recorded in the audit log.

### 5.3 Resetting the password

The circular-arrows button starts the user's password recovery. She receives a code by email to create a new password. You never see or type her password.

### 5.4 Blocking or unblocking

1. Press the padlock icon in the row.
2. In the **Bloquear cuenta** (Block account) window, write the **reason** and attach, if you have one, a **screenshot** to serve as evidence.
3. Confirm.

The account changes to **Bloqueada por Fraude** (Blocked for fraud), the user can no longer log in and the block is recorded as a case in **Moderación**, with its reason and evidence. To unblock, press the padlock again and confirm.

You can also check several rows with the checkboxes and block them together.

### 5.5 Deleting an account

Deleting an account erases the user's personal data (it is anonymized) and **cannot be undone**. That is why it needs two people:

1. One administrator creates the deletion request.
2. Another administrator reviews it and approves or rejects it in **Moderación > Solicitudes para eliminar cuentas** (Moderation > Account deletion requests) (see 10.2).

Whoever creates the request cannot approve it.

---

## 6. Alert management (Monitoring and Response Center)

**Menu:** Gestión de alertas

Split screen: left panel with the list and map on the right. At the top right you will see **EN VIVO** (Live) and how many alerts are active.

<p align="center"><img src="img/web/admin/alertas.png" width="700" alt="Monitoring and Response Center"></p>

### 6.1 Alerts tab

- **Search:** by name, place or alert number (for example `#63`).
- **Mode filter:** how the alert was triggered (panic button, from a contact or by shaking the phone).
- **Status filter.**
- Each card shows the user's name, how long ago it was triggered, the description (for example "Alerta SOS activada al agitar el teléfono", SOS alert triggered by shaking the phone, or "Alerta SOS activada desde la app, solo para: Mamá", SOS alert triggered from the app, only for: Mom) and the coordinates.
- When you tap a card, the map centers on that alert and opens a balloon with its data.

### 6.2 Attending an alert

1. Press **Atender** (Attend) on the card, or **Marcar como atendida** (Mark as attended) in the map balloon.
2. The alert changes to **Atendida** (Attended) and the card shows a check mark.

The change is saved on the server and recorded in the audit log. When the configured duration is reached (see section 13), the app asks the user whether she is still in danger (5 minutes before); if she does not answer, her contacts are notified and the alert stays active. It is marked as **Expirada** (Expired) when it reaches the 120-minute cap.

### 6.3 Map and layers

In the bottom right corner of the map is the **Capas** (Layers) panel, with switches to show or hide: Alertas, Zonas de Riesgo, Policía y CAI, Salud, Bomberos y apoyo, and Calor (Alerts, Risk zones, Police and CAI, Health, Firefighters and support, and Heat map). The **+** button in the top right corner is used to add elements to the map.

### 6.4 Zones tab

- **Zonas calientes** (Hot zones): calculated automatically from the number of alerts. **Marcar** (Mark) saves it as a zone.
- **Zonas marcadas** (Marked zones): the ones that already exist, with a level filter. It shows how many alerts fall inside and the radius.

To delete a marked zone use the trash icon on its card. Full zone management is in section 7.

### 6.5 Contacting the user or coordinating with authorities

The system notifies only the user's trusted contacts, by email and by notification. The panel does **not** call the user or send messages. If coordination with authorities is needed, it is done outside the system with the alert data (user, location and time).

---

## 7. Zones and help points

**Menu:** Zonas y puntos de ayuda

**Gestión de Zonas** (Zone management) screen. Safe zones, risk zones and help points (CAI, police stations, care centers) are managed here. **This is how zones are loaded in production**: in a new database there are none, and the app map is empty until someone creates them.

<p align="center"><img src="img/web/admin/zonas.png" width="700" alt="Zone management"></p>

**Cards:** Total Zonas, Zonas Riesgo Alto, Zonas Seguras and En Revisión (Total zones, High-risk zones, Safe zones and Under review).

**Search and filters:** search box by name and the chips **Todas**, **Riesgo Alto**, **Riesgo Medio**, **Riesgo Bajo** and **Seguras** (All, High risk, Medium risk, Low risk and Safe).

**Table:** Zone, City, Type, Alerts, Status, Last update and Actions. It is paginated.

| Field | Options |
|---|---|
| Type | Riesgo Alto, Riesgo Medio, Riesgo Bajo, Segura (High risk, Medium risk, Low risk, Safe) |
| Status | Activa, Revisión, Inactiva (Active, Under review, Inactive) |

### 7.1 Creating a zone

1. Press **Nueva Zona** (New zone).
2. Fill in **Name, City, Zone type** and **Status**.
3. In **Ubicación en el mapa** (Location on the map) mark the center of the zone (a marker with the coordinates appears).
4. If you want, adjust the **Radio del área** (Area radius).
5. Press **Guardar** (Save).

### 7.2 Viewing, editing and deleting

- **View** (eye): opens the details with type, status, alerts, last update and location (center and radius).
- **Edit** (pencil): changes the same fields of the form.
- **Delete** (trash can): asks for confirmation.

### 7.3 Zones reported by users

Users can classify places from the app. Those reports arrive as **En Revisión** (Under review) and the administration approves or rejects them before they count on everyone's map.

---

## 8. Reports

**Menu:** Informes

**Gestión de Reportes** (Report management) screen. A report groups alerts in order to review them or hand them over, for example in a monthly review or to a partner organization.

<p align="center"><img src="img/web/admin/informes.png" width="700" alt="Report management"></p>

At the top there is a **¿Para qué sirven los informes?** (What are reports for?) box that explains it; it can be closed with the X.

**Cards:** Total Reportes and Exportaciones (Total reports and Exports).

**Filters:** search box, **Fecha inicial** (Start date), **Fecha final** (End date), **Lugar de alerta** (Alert location) and **Estado** (Status). **Limpiar filtros** (Clear filters) removes them.

**Table:** Report name, Date, City, User, Event, Status, Generated by and Actions.

### 8.1 Creating a report

1. Press **Nuevo Reporte** (New report).
2. Write a **name** that says what it contains.
3. Choose the alerts in one of two ways:
   - **One by one**, checking them in the list (you can use *Seleccionar todas*, select all).
   - **With filters** by dates and status. In this case, each download includes the alerts that match the filters at that moment.
4. Fill in the city, the user and the event if applicable. That data describes the report in the document.
5. Save.

### 8.2 Viewing, editing, downloading and deleting

- **Detail:** shows the report data and the alerts it includes.
- **Edit:** changes its data; the chosen alerts are kept.
- **PDF:** for reading or printing. It includes a summary by status and by activation method, and the list of alerts.
- **Excel (CSV):** for filtering, sorting or making charts. It opens directly in Excel with the correct accents. It is a CSV file, not an .xlsx.
- **Delete:** removes it from the list and from the statistics.

Each download adds one to the **Exportaciones** (Exports) counter.

Reports contain personal data: share them only with those who must see them and delete them when they are no longer needed.

---

## 9. Evidence management

**Menu:** Gestión de evidencias

Audio and video recordings that the app uploads during alerts, and files that users upload from the web. The files are stored in cloud storage and are not lost when the server restarts.

<p align="center"><img src="img/web/admin/evidencias.png" width="700" alt="Evidence management"></p>

**Cards:** Total Evidencias, Evidencias Verificadas, Pendientes de Revisión, Rechazadas and Descargas (Total evidence, Verified evidence, Pending review, Rejected and Downloads).

**Filters:** search box (name, description, ID or user), type, status, **Limpiar filtros** and **Buscar** (Clear filters and Search).

**Table:** Preview, Evidence name (with its ID and size), User, Related alert, Type (Video or Audio), Date, Status and Actions.

### 9.1 Viewing details

The eye opens the **Detalle de Evidencia** (Evidence detail) with the player, the date, the time and the size; the user's data (name, email); the related alert and, if available, the location and the activation method.

### 9.2 Verifying, rejecting, downloading and deleting

- **Verificar** (Verify) and **Rechazar** (Reject) change the status of the evidence. The change is saved with the name of the person who made it.
- **Descargar** (Download) downloads the original file and adds one to the downloads counter.
- **Eliminar** (Delete) erases the record and the file. Use it carefully: it cannot be recovered.

### 9.3 Retention

Evidence is deleted automatically after 30 days, with a notice to the user 5 days before. The exception is the evidence of the alerts the user decided to keep from the web (up to 5 alerts per user).

---

## 10. Moderation

**Menu:** Moderación

Cases of reported users, blocks made from **Gestión de usuarios** (with their reason and evidence) and account deletion requests. Everything comes from data stored on the server.

<p align="center"><img src="img/web/admin/moderacion.png" width="700" alt="Moderation"></p>

### 10.1 Cards and reports

**Cards:** Reportes Pendientes, Casos Resueltos and Usuarios Sancionados (Pending reports, Resolved cases and Sanctioned users).

**Search and filters:** search box (report ID, reported user, reason, severity or status) with the **Buscar** (Search) button, and the **Motivo de reporte**, **Gravedad** and **Estado** filters (Report reason, Severity and Status). **Limpiar filtros** removes them.

**Table:** Report, Reported user, Reason, Severity, Date, Status and Actions.

| Action | What it does |
|---|---|
| Agregar Reporte (Add report) | Creates a case: reported user, reason, severity and description |
| View (eye) | Opens the case with its history |
| Edit (pencil) | Changes the reason, severity, status or description |
| Delete (trash can) | Deletes the case, with confirmation |

Each moderation action is recorded in the audit log.

### 10.2 Account deletion requests

Pending requests appear in this box. Remember:

- Deleting an account erases its personal data and cannot be undone.
- One administrator requests it and another approves it.
- If there are no pending requests, it says "No hay solicitudes pendientes" (There are no pending requests).
- Under **Últimas decididas** (Latest decisions) you see the requests already approved or rejected.

---

## 11. Device management

**Menu:** Gestión de dispositivos

Phones and browsers linked to users' accounts.

<p align="center"><img src="img/web/admin/dispositivos.png" width="700" alt="Device management"></p>

**Cards:** Total Dispositivos, Activos, Inactivos, Bloqueados and Sincronizados hoy (Total devices, Active, Inactive, Blocked and Synced today, last 24 hours).

**Filters:** search box (ID, name, IMEI, phone or user) with the **Buscar** button, status selector and **Limpiar filtros**.

**Table:** Device (model and Android version), User, Type, IMEI/ID, Phone, Status, Last sync and Actions.

### Actions

- **View details** (eye): device and user data.
- **Edit** (pencil): changes the data and the status (Activo, Inactivo, Bloqueado; Active, Inactive, Blocked).
- **Delete** (trash can): asks for confirmation.

Each status change is recorded in the audit log.

Each user's main phone is the one that reports the day-to-day location. The other phones only report it during an alert.

---

## 12. Audit

**Menu:** Auditoría

Shows who entered the panel, from where and what changed. It is a **read-only log**: nobody can edit or delete its records, not even an administrator.

<p align="center"><img src="img/web/admin/auditoria.png" width="700" alt="Audit"></p>

**Tabs:** **Todo** (All), **Sesiones** (Sessions: logins and logouts) and **Intentos fallidos** (Failed attempts).

**Filters:** Responsible (includes the option for changes with no person), Type, Action, From, To and **Limpiar** (Clear).

**Table:**

| Column | What it shows |
|---|---|
| Date | Day and time of the event |
| Responsible | Who did it, with their role. "No registrado" (Not recorded) when the change was made by the system, for example when an alert expires or is updated |
| Origin | Where from: web panel, mobile app or automatic task, with browser and operating system |
| Action | Modified, Logged in, Logged out, etc. |
| About | What was changed (for example "Alerta #63" or "Panel de administración") |
| Detail | Which columns changed or how the session was started. For system settings it shows the previous value and the new one |
| IP | IP address of the connection |

**Exportar CSV** (Export CSV): downloads what you are viewing, with the applied filters, in a file that opens in Excel. The file contains personal data: store it in a safe place.

Records never include passwords or other secrets.

---

## 13. System settings

**Menu:** Configuración

Parameters that affect the whole platform. Your personal account is managed in **Mi perfil**.

<p align="center"><img src="img/web/admin/configuracion.png" width="700" alt="System settings"></p>

### 13.1 App parameters

These values apply to **all users**:

| Parameter | What it controls | Range |
|---|---|---|
| Maximum duration of an SOS alert (minutes) | How often the user is asked whether she is still in danger; without an answer her contacts are notified and the alert stays active. The absolute cap is 120 minutes, or this value if it is higher | 30 to 1440 (30 by default) |
| Emergency contacts per user | Maximum number of contacts each user can register. The server enforces it even if the app does not block it | 1 to 20 (5 by default) |
| Emergency number | Line offered to users | In Colombia, 123 |
| Minimum mobile app version | Anyone with an earlier version will see a notice to download the new one when opening the app. It does not block it: the alert button keeps working. Raise it only when you publish a new APK | Empty = no minimum |
| Support email | Receives the help requests from the panel | |

**To save:**
1. Change the values.
2. Press **Revisar y guardar** (Review and save). You will see a summary of the changes to confirm.
3. Confirm.

**Descartar** (Discard) undoes the changes you have not saved. Below it shows the date of the last change. Every change is recorded in **Auditoría** with the previous value and the new one.

### 13.2 System status

Shows whether the database is connected (and how long it takes to respond), whether push notifications are configured, how many alerts are active now, the time of the last check for expired alerts and since when the server has been online. The reload button refreshes it. It does not show passwords or keys.

### 13.3 Exporting data

- **Lista de usuarios** (User list): CSV file that opens in Excel.
- **Historial de alertas** (Alert history): alerts from the last 30 days, in CSV.

The files include users' personal data. Store them in a safe place and do not share them.

### 13.4 Help and support

**Pedir ayuda** (Ask for help) opens an email addressed to the configured support address.

---

## 14. My profile

**Menu:** Mi perfil

<p align="center"><img src="img/web/admin/mi-perfil.png" width="700" alt="My profile"></p>

| Block | What it has |
|---|---|
| Personal data | First name, last name and phone, with **Guardar datos** (Save data). The email identifies the account and cannot be changed here |
| Password | Current password, new password and confirmation, with **Cambiar contraseña** (Change password). At least 8 characters; use uppercase letters, numbers and symbols to make it stronger. If the current password is wrong, it is not changed |
| Session and appearance | **Cerrar sesión por inactividad** (Log out after inactivity, for example 15 minutes) and **Claro** or **Oscuro** (Light or Dark) theme. The session also expires 24 hours after logging in |
| My recent activity | Your latest logins: how you logged in (email and password or Google), browser, operating system, date and IP address |

If in **Mi actividad reciente** (My recent activity) you see a login you do not recognize, change your password immediately and notify the team.

At the top right is the **Cerrar sesión** (Log out) button.

---

## 15. Responsible use of information

From the panel you can reach very sensitive information: locations, trusted contacts, recordings and personal data. Keep in mind:

- Consult a user's information only when you need it to handle an alert, a report or a request.
- Everything you do is recorded in the audit log with your name and your IP.
- Do not download evidence or export data unless necessary, and delete the files when you no longer need them.
- Data processing must comply with Colombian Law 1581 of 2012 on personal data protection.

---

## 16. Common problems

**I enter the panel and it sends me to the login page**
- Your session expired (24 hours or inactivity): log in again.
- Your account does not have the administrator role.

**"Credenciales incorrectas" (Incorrect credentials) or too many attempts**
Check the email and the password. If you made more than 5 attempts in one minute, wait a moment before trying again.

**The Dashboard says "No se pudieron cargar los datos" or everything takes a long time**
The server uses a free plan in which each service goes to sleep after 15 minutes without use. The first request may take between 1 and 3 minutes while it wakes up. Wait and reload. Before a presentation, open the panel a few minutes in advance.

**The map, the zones or the assistance section are empty**
It is a new database without zones or resources. Load them from **Zonas y puntos de ayuda**.

**A user says her contacts did not receive the notice**
1. Check that her contacts have an email registered.
2. In **Configuración > Estado del sistema** (Settings > System status), check that push notifications are configured.
3. Check with the technical team that the email provider is configured on the server.

**The user cannot trigger the alert**
She may have an old version of the app. Ask her to install the most recent APK.

**Notifications do not arrive in the bell**
Reload the page. If nothing changes, check your connection.

---

## 17. Known limitations

| # | Limitation | Screen |
|---|---|---|
| 1 | The alert detail shows, for each contact, the notice status and the relationship, but not yet the address or the last location | Alert management |
| 2 | Few zone types and no special danger marker | Zones and help points |
| 3 | Each device record shows basic information | Device management |
| 4 | The minimum app version only shows a notice, it does not force an update | Settings |
| 5 | The panel is translated into English, Portuguese and French since 25/10/2026; some new texts may remain in Spanish | Top bar |
| 6 | Users can delete their account from the app (My profile); deletion from the panel needs the approval of a second administrator | Moderation |
| 7 | Fixed: the administration only sees the route of active or attended alerts and, for each contact, the delivery status and the relationship, without their personal data | Several |
| 8 | On the server's free plan, the first request after a while without use may take up to about 90 seconds; an UptimeRobot monitor keeps the services awake | All |

---

## 18. Glossary

| Term | Meaning |
|---|---|
| Alert | Emergency notice that a user triggers from the app |
| Alert status | Active, Attended, Cancelled or Expired |
| Zone | Area marked as risky or safe |
| CAI | Comando de Atención Inmediata (police post) |
| Hot zone | Area with many alerts, calculated automatically |
| Evidence | Audio, video or other file linked to an alert |
| SHA-256 fingerprint | Code that makes it possible to check that a file was not modified |
| Report | Set of alerts exported as PDF or CSV |
| Audit | Read-only log of the panel's changes and sessions |
| Token | Temporary credential that identifies your session |
| Administrator role | Account with access to `/admin` |

---

## 19. Appendix: list of screenshots

Screenshots included in the `img/web/admin/` folder:

| File | Screen |
|---|---|
| login.png | Login |
| dashboard-1.png | Dashboard: cards and alerts by hour |
| dashboard-2.png | Dashboard: zones with the most incidents |
| dashboard-3.png | Dashboard: weekly comparison |
| dashboard-4.png | Dashboard: recent alerts |
| usuarios.png | User management |
| alertas.png | Monitoring and Response Center |
| zonas.png | Zones and help points |
| informes.png | Reports |
| evidencias.png | Evidence management |
| moderacion.png | Moderation |
| dispositivos.png | Device management |
| auditoria.png | Audit |
| configuracion.png | System settings |
| mi-perfil.png | My profile |

---

**End of the Administrator Manual**
