# Wireframe: Device Management (Admin)

**Route:** `/admin/devices-management`  
**Component:** `DeviceManagementComponent`  
**Layout:** `AdminLayout` (Admin Sidebar + Admin Topbar)  
**Required Role:** ADMIN  
**Guards:** `authGuard` + `adminGuard`  
**Lazy loading:** Yes  
**Status:** ✅ Implemented  

---

## ASCII Layout — Main View

```
┌─────────────────────────────────────────────────────────────────┐
│  ☰  AlertaMujer Admin        [🔔]  [☀️/🌙]  [ Admin ▼ ]        │
├──────────┬──────────────────────────────────────────────────────┤
│          │                                                        │
│  📊 Dash │   📱 Device Management                          │
│  👥 Usuar│                                                        │
│  🔔 Alert│   ┌──────────────┐ ┌──────────────┐ ┌─────────────┐  │
│  🗺️ Zonas│   │  TOTAL       │ │  CONNECTED  │ │ DISCONNECTED│  │
│  🚨 Emerg│   │              │ │              │ │             │  │
│  📋 Moder│   │     342      │ │     298      │ │     44      │  │
│  📱 Disp.│   │ devices │ │  online    │ │  no signal  │  │
│          │   └──────────────┘ └──────────────┘ └─────────────┘  │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  FILTERS                                        │ │
│          │   │  [ 🔍 Search by UUID, model, user... ]     │ │
│          │   │                                                  │ │
│          │   │  [app-chip: All●] [app-chip: Connected]      │ │
│          │   │  [app-chip: Disconnected] [app-chip: Inactive]│ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  DEVICES  (342 records) [➕ REGISTER]   │ │
│          │   │                                                  │ │
│          │   │  ┌───┬──────────────┬──────────┬───────┬──────┐ │ │
│          │   │  │ # │ UUID / Model│ User  │ 🔋/GPS│ Status │ │ │
│          │   │  ├───┼──────────────┼──────────┼───────┼──────┤ │ │
│          │   │  │ 1 │ a1b2… S21    │ Ana G.   │ 85%💪 │ ✅Co.│ │ │
│          │   │  │ 2 │ c3d4… A52    │ María L. │ 72%💪 │ ✅Co.│ │ │
│          │   │  │ 3 │ e5f6… S20    │ Laura T. │ 15%⚠️ │ ✅Co.│ │ │
│          │   │  │ 4 │ 7890… P30    │ Paula M. │ —     │ 🔴De.│ │ │
│          │   │  │ 5 │ abcd… A12    │ Sofía R. │ —     │ ⚫In.│ │ │
│          │   │  └───┴──────────────┴──────────┴───────┴──────┘ │ │
│          │   │                                                  │ │
│          │   │  [ < Previous ]   Page 1 / 69  [ Next > ]  │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
└──────────┴──────────────────────────────────────────────────────┘
```

---

## ASCII Layout — Device Detail / Edit Modal

```
                  ┌──────────────────────────────────────┐
                  │  📱 Device a1b2c3d4              │  [✕]
                  │                                       │
                  │  UUID:        a1b2c3d4-e5f6-7890      │
                  │  Model:      Samsung Galaxy S21      │
                  │  OS:          Android 13              │
                  │  App Version: 2.1.0                   │
                  │                                       │
                  │  User:     Ana García              │
                  │  Email:      ana@gmail.com           │
                  │                                       │
                  │  Status:   ● Connected                │
                  │  Battery:  85%  ████████████░░        │
                  │  GPS:      Strong  ◉◉◉◉○              │
                  │  Last sync: Hoy, 10:32 am           │
                  │                                       │
                  │  ─── Update status ───────────    │
                  │  ● Connected  ○ Disconnected          │
                  │  ○ Inactivo                           │
                  │                                       │
                  │  [ CANCEL ]  [ 💾 UPDATE ]      │
                  └──────────────────────────────────────┘
```

---

## Layout ASCII — Modal Registrar Device

```
                  ┌──────────────────────────────────────┐
                  │  ➕ Register device             │  [✕]
                  │                                       │
                  │  Device UUID *               │
                  │  [ a1b2c3d4-e5f6-7890              ] │
                  │  Format: xxxxxxxx-xxxx-xxxx          │
                  │                                       │
                  │  User asignada *                   │
                  │  [ Search user...        ▼       ] │
                  │                                       │
                  │  Model (opcional)                    │
                  │  [ Samsung Galaxy S21               ] │
                  │                                       │
                  │  Status inicial:                      │
                  │  ● Activo   ○ Inactivo                │
                  │                                       │
                  │  [ CANCEL ]   [ 💾 REGISTER ]      │
                  └──────────────────────────────────────┘
```

---

## Form Fields

### Register device

| Field | Type | Required | Validation |
|-------|------|-----------|------------|
| UUID | `text` | ✅ | Format UUID v4 (`/^[0-9a-f]{8}-[0-9a-f]{4}-/i`) |
| User asignada | `select` with search | ✅ | Existing user in the system |
| Model | `text` | ❌ | Max. 100 characters |
| Initial status | `radio` | ✅ | `Activo` / `Inactivo` |

### Edit Device (Detail Modal)

| Field | Type | Required |
|-------|------|-----------|
| Status | `radio` | ✅ | `Connected` / `Disconnected` / `Inactivo` |

### Filters

| Filtro | Type | Valores |
|--------|------|---------|
| Search | `text` | UUID, modelo, user name |
| Status | `app-chip` | All · Connected · Disconnected · Inactive |

---

## Validations

```typescript
// Register device form
uuid     : required, pattern UUID v4
usuaria  : required, must exist in UsersService
modelo   : optional, maxLength(100)
estado   : required, enum['Activo','Inactivo']

// Edit status
estado   : required, enum['Connected','Disconnected','Inactivo']
```

---

## Statuss

| Status | Description | UI |
|--------|-------------|-----|
| `loading` | Fetching devices | Skeleton stat cards + table |
| `ready` | Data loaded | Stat cards + paginated table |
| `filtrado` | Chips / búsqueda | Updated table |
| `modal-registrar` | New device modal | Empty form |
| `modal-detalle` | Row click | Modal with details + status editing |
| `guardando` | POST/PUT in progress | Button with spinner |
| `bateria-critica` | Battery < 20% | Red icon and text in table |
| `error` | API failure | Red toast |

---

## Flow

```
/admin/devices-management
  ├── onInit → DevicesService.getAll()
  │             → Stat cards (total / connected / disconnected)
  │             → Paginated table
  │
  ├── Search / Chips
  │     → DevicesService.getAll(filters)
  │     → Updates table
  │
  ├── [➕ Registrar]
  │     → Empty form modal
  │     → [Registrar] → DevicesService.create(datos)
  │     → Success toast → table refreshes
  │
  ├── Row click
  │     → DevicesService.getByUsuario(usuarioId) → preloads modal
  │     → Modal with complete details (read-only + edit status)
  │
  ├── [💾 Actualizar] status in modal
  │     → DevicesService.updateStatus(id, newStatus)
  │     → Badge updated in table; success toast
  │
  └── [🗑️ Delete] (inside the detail modal)
        → Modal danger: "¿Eliminar dispositivo UUID?"
        → DevicesService.delete(id)
        → Row removed from table
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

/* Device status badges */
--estado-conectado    : background var(--green-pale)  / color var(--green);
--estado-desconectado : background var(--red-pale)    / color var(--red);
--estado-inactivo     : background #f3f4f6 / color var(--text-dim);

/* Battery crítica (< 20%) */
color: var(--red); font-weight: 600;

/* GPS */
--gps-fuerte   : color var(--green);
--gps-moderada : color var(--amber);
--gps-debil    : color var(--red);

/* UUID in table */
font-family: monospace; font-size: 0.8rem; color: var(--text-dim);

/* Row hover */
background: var(--bg-card-hover); cursor: pointer;
```

---

## Components Used

| Component | Variant/Config | Usage |
|------------|----------------|-----|
| `app-card` | — | Stat cards, filters, table |
| `app-chip` | `active: boolean` | Status filters |
| `app-button` | `primary`, `sm` | "➕ Registrar" |
| `app-modal` | `md`, `default` | Register + detail/edit modal |
| `app-modal` | `sm`, `danger` | Confirm device deletion |
| `app-spinner` | `sm`, `inline` | Statuss loading |

---

## Responsive

| Breakpoint | Behavior |
|------------|---------------|
| Desktop `>1024px` | Stat cards 3 columns; full table with UUID |
| Tablet `760–1024px` | Stat cards 2 columns; truncated UUID |
| Mobile `<760px` | Stacked stat cards; horizontal table scrolling; UUID hidden |

---

## Accessibility

- Table with `<caption>` "Lista de dispositivos registrados"
- UUID with `aria-label="Unique device identifier"` and full `title`
- Critical battery with `aria-label="Batería crítica: 15%"`
- Stat card "Disconnected" with `aria-live="polite"` for updates
- Modal with `role="dialog"`, `aria-modal="true"`, focus trapped

---

## Service Integration

```typescript
// DevicesService — methods used

DevicesService.getAll(): Observable<Device[]>
// GET /devices

DevicesService.getByUsuario(usuarioId: number): Observable<Device>
// GET /devices?usuarioId=:id

DevicesService.create(datos: Partial<Device>): Observable<Device>
// POST /devices

DevicesService.update(id: number, datos: Partial<Device>): Observable<Device>
// PUT /devices/:id

DevicesService.updateStatus(id: number, estado: string): Observable<Device>
// PATCH /devices/:id/estado

DevicesService.delete(id: number): Observable<void>
// DELETE /devices/:id

// Model de datos
interface Device {
  id          : number;
  usuarioId   : number;
  uuid        : string;
  modelo      : string;
  os          : string;
  versionApp  : string;
  battery     : number;
  gpsSignal   : 'Strong' | 'Moderada' | 'Débil';
  estado      : 'Connected' | 'Disconnected' | 'Inactivo';
  lastSync    : string;
}
```

---

## Casos de Usage

| # | Actor | Action | Result |
|---|-------|--------|-----------|
| 1 | Admin | Navigates to `/admin/devices-management` | Sees the stat cards + the table of all devices |
| 2 | Admin | Filtra por "Disconnected" | Solo devices no signal activa |
| 3 | Admin | Searches by partial UUID | Table filters the specific device |
| 4 | Admin | Clicks "➕ Register" | Modal with form to link a new device |
| 5 | Admin | Row click | Modal with complete device + user details |
| 6 | Admin | Changes status to "Inactive" in modal | Badge actualizado; device will not receive notifications |
| 7 | Admin | Device with battery < 20% | Battery in red with an alert indicator |
