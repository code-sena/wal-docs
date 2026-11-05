# Wireframe: Moderator Management
 
**Route:** `/admin/moderator-management`  
**Component:** `ModeratorManagementComponent`  
**Layout:** `AdminLayout` (Admin sidebar + Admin topbar)  
**Required role:** ADMIN  
**Guards:** `authGuard` + `adminGuard`  
**Lazy loading:** yes  
 
> ⚠️ **Implementation note:** The route `/admin/moderator-management` and the component `ModeratorManagementComponent` exist in `navigation-map.md`. According to the services table, `UsersService` is used with a filter by the `'moderador'` role. The actual implementation level is **partial**: the route is registered and the component exists, but it shares the same `UsersService` as `UserComponent` — it is likely a filtered view of users with the moderator role. The wireframe documents the screen as implemented on that basis, with moderator-specific CRUD.
 
---
 
## ASCII Layout — Main View
 
```
┌─────────────────────────────────────────────────────────────────┐
│  ☰  AlertaMujer Admin        [🔔]  [☀️/🌙]  [ Admin ▼ ]        │
├──────────┬──────────────────────────────────────────────────────┤
│          │                                                        │
│  📊 Dash │   🛡️ Moderator Management                             │
│  👥 Users│                                                        │
│  🔔 Alert│   ┌──────────────┐  ┌──────────────┐  ┌────────────┐  │
│  🗺️ Zones│   │  TOTAL MOD.  │  │    ACTIVE    │  │  INACTIVE  │  │
│  🚨 Emerg│   │              │  │              │  │            │  │
│  📋 Moder│   │      8       │  │      6       │  │     2      │  │
│  📱 Dev. │   └──────────────┘  └──────────────┘  └────────────┘  │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  FILTERS AND SEARCH                             │ │
│          │   │  [ 🔍 Search by name or email... ]              │ │
│          │   │  [app-chip: All●] [app-chip: Active]            │ │
│          │   │  [app-chip: Inactive]                           │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  MODERATORS (8 records)   [➕ NEW MODERATOR]    │ │
│          │   │                                                  │ │
│          │   │  ┌───┬──────────────┬───────────┬──────┬──────┐ │ │
│          │   │  │ # │ Name         │ Email     │ Stat.│ Act. │ │ │
│          │   │  ├───┼──────────────┼───────────┼──────┼──────┤ │ │
│          │   │  │ 1 │ Carlos Vélez │ cv@g.com  │  ✅  │ ✏️ 🚫 │ │ │
│          │   │  │ 2 │ Diana Ríos   │ dr@g.com  │  ✅  │ ✏️ 🚫 │ │ │
│          │   │  │ 3 │ Fernando L.  │ fl@g.com  │  ✅  │ ✏️ 🚫 │ │ │
│          │   │  │ 4 │ Gloria Peña  │ gp@g.com  │  🔴  │ ✏️ ✅ │ │ │
│          │   │  │ 5 │ Hernán Mora  │ hm@g.com  │  ✅  │ ✏️ 🚫 │ │ │
│          │   │  └───┴──────────────┴───────────┴──────┴──────┘ │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
└──────────┴──────────────────────────────────────────────────────┘
```
 
---
 
## ASCII Layout — Create / Edit Moderator Modal
 
```
                  ┌──────────────────────────────────────┐
                  │  ➕ New moderator                     │  [✕]
                  │                                       │
                  │  ┌─────────────────┬───────────────┐  │
                  │  │ [👤] Name *     │ [👤] Surname *│  │
                  │  └─────────────────┴───────────────┘  │
                  │                                       │
                  │  [✉️] Email address *                  │
                  │  ──────────────────────────────────   │
                  │                                       │
                  │  [📱] Phone *                         │
                  │  ──────────────────────────────────   │
                  │                                       │
                  │  [🔒] Password *  (on create only)    │
                  │  ──────────────────────────────────   │
                  │                                       │
                  │  Status:   ● Active   ○ Inactive      │
                  │                                       │
                  │  Assigned role: 🛡️ moderador (fixed)  │
                  │                                       │
                  │  [ CANCEL ]   [ 💾 SAVE ]             │
                  └──────────────────────────────────────┘
```
 
---
 
## Form Fields
 
### Create / Edit moderator
 
| Field | Type | Required | Validation |
|-------|------|-----------|------------|
| Name | `text` | ✅ | Min. 2 characters |
| Surname | `text` | ✅ | Min. 2 characters |
| Email address | `email` | ✅ | Email format; unique in system |
| Phone | `tel` | ✅ | International format |
| Password | `password` | ✅ create only | Min. 8 chars, 1 uppercase, 1 number |
| Status | `radio` | ✅ | `Activo` / `Inactivo` |
| Role | — | — | Always `'moderador'` — not editable |
 
### Filters
 
| Filter | Type | Values |
|--------|------|---------|
| Search | `text` | Name or email (300ms debounce) |
| Status | `app-chip` | All · Active · Inactive |
 
---
 
## Validations
 
```typescript
// Create/edit moderator form
nombre   : required, minLength(2)
apellido : required, minLength(2)
correo   : required, email, unique
telefono : required, pattern internacional
password : required (solo crear), minLength(8), pattern(/[A-Z]/, /[0-9]/)
estado   : required, enum['Activo','Inactivo']
// rol: always 'moderador' — set in the backend on create
```
 
---
 
## States
 
| State | Description | UI |
|--------|-------------|-----|
| `loading` | Fetching moderators | Skeleton stat cards + table |
| `ready` | Data loaded | Stat cards + table |
| `searching` | Input active | Table filters with debounce |
| `create-modal` | New modal | Empty form |
| `edit-modal` | Edit modal | Pre-filled form |
| `saving` | POST/PUT | Button with spinner |
| `activating` | PATCH updateEstado | Badge updates in table |
| `error` | API failure | Red toast |
 
---
 
## Flow
 
```
/admin/moderator-management
  ├── onInit → UsersService.getAll() filtered by rol === 'moderador'
  │             → Stat cards + table
  │
  ├── Search / Chips
  │     → Filters the list locally
  │
  ├── [➕ New moderator]
  │     → Empty form modal
  │     → [Save] → UsersService.create({ ...datos, rol: 'moderador' })
  │     → Success toast → table refreshes
  │
  ├── [✏️ Edit]
  │     → UsersService.getById(id) → pre-fills modal
  │     → [Save] → UsersService.update(id, datos)
  │
  ├── [🚫 Deactivate] (active moderator)
  │     → UsersService.updateEstado(id, 'Inactivo')
  │     → Badge turns red; action changes to [✅ Activate]
  │
  └── [✅ Activate] (inactive moderator)
        → UsersService.updateEstado(id, 'Activo')
        → Badge turns green
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
--estado-activo   : background var(--green-pale) / color var(--green);
--estado-inactivo : background var(--red-pale)   / color var(--red);
 
/* Row hover */
background: var(--bg-card-hover); cursor: pointer;
 
/* Moderator role icon */
color: var(--purple-deep); /* #7c3aed */
```
 
---
 
## Components Used
 
| Component | Variant/Config | Usage |
|------------|----------------|-----|
| `app-card` | — | Stat cards, filters, table |
| `app-chip` | `active: boolean` | Status filters |
| `app-button` | `primary`, `sm` | "➕ New moderator" |
| `app-button` | `danger`, `sm` | Deactivate button in row |
| `app-modal` | `md`, `default` | Create/edit modal |
| `app-spinner` | `sm`, `inline` | Loading states |
 
---
 
## Responsive
 
| Breakpoint | Behavior |
|------------|---------------|
| Desktop `>1024px` | Stat cards in 3 columns; full table |
| Tablet `760–1024px` | Stat cards in 2 columns; table without phone |
| Mobile `<760px` | Stacked stat cards; table with horizontal scroll |
 
---
 
## Accessibility
 
- Table with `<caption>` "List of system moderators"
- Action buttons with `aria-label` per moderator
- Fixed role in modal marked as `readonly` with `aria-readonly="true"`
- Modal with `role="dialog"`, `aria-modal="true"`, trapped focus
- "8 records" counter with `aria-live="polite"`
---
 
## Service Integration
 
```typescript
// UsersService — methods used (filtered by the 'moderador' role)
 
UsersService.getAll(): Observable<Usuario[]>
// GET /usuarios  → client-side filter by rol === 'moderador'
// or GET /usuarios?rol=moderador if the backend supports it
 
UsersService.create(datos: Omit<Usuario, 'id'>): Observable<Usuario>
// POST /usuarios  with { ...datos, rol: 'moderador' }
 
UsersService.update(id: number, datos: Partial<Usuario>): Observable<Usuario>
// PUT /usuarios/:id
 
UsersService.updateEstado(id: number, estado: string): Observable<Usuario>
// PATCH /usuarios/:id/estado
 
// Reused model
interface Usuario {
  id      : number;
  nombre  : string;
  apellido: string;
  correo  : string;
  telefono: string;
  rol     : 'moderador';   // always on this screen
  estado  : 'Activo' | 'Inactivo';
}
```