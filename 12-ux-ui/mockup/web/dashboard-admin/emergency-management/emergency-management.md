# Wireframe: Emergency Management
 
**Route:** `/admin/emergency-management`  
**Component:** `EmergencyManagementComponent`  
**Layout:** `AdminLayout` (Admin sidebar + Admin topbar)  
**Required role:** ADMIN  
**Guards:** `authGuard` + `adminGuard`  
**Lazy loading:** yes  
 
> ⚠️ **Implementation note:** The route `/admin/emergency-management` exists in `navigation-map.md` and there is a registered `EmergencyManagementComponent`. However, according to the API services table in `navigation-map.md`, the `EmergencyService` service (dispatch, updateStatus) is **not listed** in the "API Services by Route" section of the web navigation map — it only appears in the original request. The screen is documented as **partially implemented**: the route and the component exist, but the service may still be in an integration state. The wireframe reflects the expected functionality based on the `dispatch` and `updateStatus` methods of `EmergencyService`.
 
---
 
## ASCII Layout — Main View
 
```
┌─────────────────────────────────────────────────────────────────┐
│  ☰  AlertaMujer Admin        [🔔]  [☀️/🌙]  [ Admin ▼ ]        │
├──────────┬──────────────────────────────────────────────────────┤
│          │                                                        │
│  📊 Dash │   🚨 Emergency Management                             │
│  👥 Users│                                                        │
│  🔔 Alert│   ┌──────────────┐ ┌──────────────┐ ┌─────────────┐  │
│  🗺️ Zones│   │  ACTIVE      │ │  DISPATCHED  │ │  RESOLVED   │  │
│  🚨 Emerg│   │              │ │              │ │             │  │
│  📋 Moder│   │     3        │ │      7       │ │     42      │  │
│  📱 Dev. │   │ 🔴 urgent    │ │  en route    │ │  this month │  │
│          │   └──────────────┘ └──────────────┘ └─────────────┘  │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  FILTERS                                        │ │
│          │   │  [ 🔍 Search by location, user... ]             │ │
│          │   │  [app-chip: All●] [app-chip: Active]            │ │
│          │   │  [app-chip: Dispatched] [app-chip: Resolved]    │ │
│          │   │                            [app-period-badge ▼] │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  EMERGENCIES  (52 records)                      │ │
│          │   │                                                  │ │
│          │   │  ┌───┬──────────────┬───────────┬──────┬──────┐ │ │
│          │   │  │ # │ User         │ Location  │ Time │ Stat.│ │ │
│          │   │  ├───┼──────────────┼───────────┼──────┼──────┤ │ │
│          │   │  │ 1 │ Ana García   │ Cra 7 #34 │10:32 │🔴Act.│ │ │
│          │   │  │ 2 │ María López  │ Calle 12  │10:15 │🟡Disp│ │ │
│          │   │  │ 3 │ Laura Torres │ Centr.Pk  │09:50 │🟡Disp│ │ │
│          │   │  │ 4 │ Sofía Ruíz   │ Av. 68    │09:30 │✅Res.│ │ │
│          │   │  │ 5 │ Paula Moreno │ Cra 15    │09:00 │✅Res.│ │ │
│          │   │  └───┴──────────────┴───────────┴──────┴──────┘ │ │
│          │   │                                                  │ │
│          │   │  [ < Previous ]    Page 1 / 11    [ Next > ]    │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
└──────────┴──────────────────────────────────────────────────────┘
```
 
---
 
## ASCII Layout — Manage Emergency Modal
 
```
                  ┌──────────────────────────────────────┐
                  │  🚨 Emergency #1 — Ana García         │  [✕]
                  │                                       │
                  │  Current status: 🔴 Active            │
                  │  Start time:     Today, 10:32 am      │
                  │                                       │
                  │  📍 Location                          │
                  │  Cra 7 #34-12, Bogotá                 │
                  │  [ MINI MAP ]                         │
                  │                                       │
                  │  Related alert: #1 — SOS              │
                  │                                       │
                  │  ─────────────────────────────────    │
                  │  Actions:                             │
                  │                                       │
                  │  [ 🚑 DISPATCH UNIT ]                 │
                  │  Sends a response signal              │
                  │                                       │
                  │  Update status:                       │
                  │  ○ Active  ● Dispatched  ○ Resolved   │
                  │                                       │
                  │  Response notes:                      │
                  │  [ Patrol sent to the sector... ]     │
                  │                                       │
                  │  [ CANCEL ]  [ 💾 UPDATE ]            │
                  └──────────────────────────────────────┘
```
 
---
 
## Form Fields
 
### Modal — Manage emergency
 
| Field | Type | Required | Validation |
|-------|------|-----------|------------|
| Status | `radio` | ✅ | `Activa` / `Despachada` / `Resuelta` |
| Response notes | `textarea` | ❌ | Max. 500 characters |
 
### Filters
 
| Filter | Type | Values |
|--------|------|---------|
| Free-text search | `text` | Location, user name |
| Status | `app-chip` | All · Active · Dispatched · Resolved |
| Period | `app-period-badge` | Today · Week · Month |
 
---
 
## Validations
 
```typescript
// Update emergency modal
estado: required, enum['Activa','Despachada','Resuelta']
notas : optional, maxLength(500)
```
 
---
 
## States
 
| State | Description | UI |
|--------|-------------|-----|
| `loading` | Fetching emergencies | Skeleton stat cards + table |
| `ready` | Data loaded | Stat cards + paginated table |
| `filtered` | Chips / search | Table updated |
| `detail-modal` | Row click | Modal with detail + map |
| `dispatching` | "Dispatch" click | Spinner + status changes to Dispatched |
| `updating` | PATCH in progress | Button with spinner |
| `urgent` | Active emergency > 10 min | Row in pulsing red |
| `error` | API failure | Red toast |
 
---
 
## Flow
 
```
/admin/emergency-management
  ├── onInit → EmergencyService (fetch emergencies)
  │             → Stat cards + paginated table
  │
  ├── Filters / search
  │     → Updates table
  │
  ├── Row click
  │     → Opens modal with detail
  │
  ├── [🚑 Dispatch unit] in modal
  │     → EmergencyService.dispatch(id)
  │     → Status changes to "Despachada"
  │     → Confirmation toast
  │
  └── Update status + notes
        → EmergencyService.updateStatus(id, { estado, notas })
        → Success toast → table refreshes
```
 
---
 
## Applied Styles
 
```css
background: var(--bg-base);
 
/* Stat cards */
background: var(--bg-card);
border: 1.5px solid var(--border);
border-radius: var(--dash-radius-card);
box-shadow: var(--dash-shadow-card);
 
/* Status badges */
--estado-activa     : background var(--red-pale)   / color var(--red);
--estado-despachada : background var(--amber-pale) / color var(--amber);
--estado-resuelta   : background var(--green-pale)  / color var(--green);
 
/* "Active" stat card — number */
color: var(--red); font-size: 2rem; font-weight: 700;
 
/* Urgent row */
background: var(--red-pale); animation: pulse 2s infinite;
 
/* Row hover */
background: var(--bg-card-hover); cursor: pointer;
```
 
---
 
## Components Used
 
| Component | Variant/Config | Usage |
|------------|----------------|-----|
| `app-card` | — | Stat cards, filters, table |
| `app-chip` | `active: boolean` | Status filters |
| `app-period-badge` | `conDropdown: true` | Time period |
| `app-button` | `primary`, `md` | "Dispatch unit" in modal |
| `app-modal` | `lg`, `default` | Detail + update modal |
| `app-spinner` | `sm`, `inline` | Loading states |
 
---
 
## Responsive
 
| Breakpoint | Behavior |
|------------|---------------|
| Desktop `>1024px` | Stat cards in 3 columns; full table |
| Tablet `760–1024px` | Stat cards in 2 columns; compact table |
| Mobile `<760px` | Stacked stat cards; table with horizontal scroll |
 
---
 
## Accessibility
 
- Stat cards with `role="status"` and `aria-live="polite"`
- Table with `<caption>` "List of emergencies"
- Urgent rows with `aria-label="Urgent emergency not yet dispatched"`
- Modal with `role="dialog"`, `aria-modal="true"`, trapped focus
- Dispatch button with a descriptive `aria-label`
---
 
## Service Integration
 
```typescript
// EmergencyService — methods used
 
EmergencyService.dispatch(id: number): Observable<Emergencia>
// POST /emergencies/:id/dispatch
// Changes status to 'Despachada' and records the dispatch timestamp
 
EmergencyService.updateStatus(id: number, datos: { estado: string; notas?: string }): Observable<Emergencia>
// PATCH /emergencies/:id/status
 
// Data model (inferred from the service methods)
interface Emergencia {
  id            : number;
  alertaId      : number;     // SOS alert that generated the emergency
  usuarioId     : number;
  usuarioNombre : string;
  ubicacion     : string;
  lat           : number;
  lng           : number;
  estado        : 'Activa' | 'Despachada' | 'Resuelta';
  horaInicio    : string;     // ISO timestamp
  horaDespacho  : string | null;
  notas         : string | null;
}
```