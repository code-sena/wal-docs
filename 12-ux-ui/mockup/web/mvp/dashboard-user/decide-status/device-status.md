# Wireframe: Device Status

**Route:** `/dashboard/device-status`
**Component:** `DeviceStatusComponent`
**Layout:** `DashboardLayout` (Sidebar + Topbar + BottomNav)
**Required Role:** USER (operator)
**Guards:** `authGuard` + `userGuard`
**Lazy loading:** yes
**Status:** ✅ Implemented

---

## ASCII Layout

```text
┌─────────────────────────────────────────────────────────────────┐
│  ☰  AlertaMujer              [🔔]  [☀️/🌙]  [ Ana García ▼ ]   │
├──────────┬──────────────────────────────────────────────────────┤
│          │                                                        │
│  🏠 Home │   📱 Device Status                                    │
│  📍 Loc. │                                                        │
│  🔔 Alert│   ┌─────────────────────────────────────────────────┐ │
│  🎬 Evid.│   │  LINKED DEVICE                                  │ │
│  📱 Dev. │   │                                                  │ │
│          │   │  ┌──────────────────────────────────────────┐   │ │
│          │   │  │                                          │   │ │
│          │   │  │   📱  Samsung Galaxy S21                 │   │ │
│          │   │  │                                          │   │ │
│          │   │  │   Status:     ● Connected                │   │ │
│          │   │  │   Model:      Samsung Galaxy S21         │   │ │
│          │   │  │   OS:         Android 13                 │   │ │
│          │   │  │   App Version: 2.1.0                     │   │ │
│          │   │  │   UUID:       a1b2c3d4-e5f6-7890         │   │ │
│          │   │  │                                          │   │ │
│          │   │  └──────────────────────────────────────────┘   │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   ┌───────────────────┐  ┌───────────────────────┐   │
│          │   │  🔋 BATTERY       │  │  📶 GPS SIGNAL        │   │
│          │   │                   │  │                        │   │
│          │   │      85%          │  │    Strong              │   │
│          │   │  ██████████░░     │  │    ◉◉◉◉○               │   │
│          │   │  Level: High      │  │    Excellent coverage  │   │
│          │   └───────────────────┘  └───────────────────────┘   │
│          │                                                        │
│          │   ┌───────────────────┐  ┌───────────────────────┐   │
│          │   │  🕐 LAST SYNC     │  │  🔴 LAST ALERT        │   │
│          │   │                   │  │                        │   │
│          │   │  Today, 10:32 am │  │  Today, 10:32 am      │   │
│          │   │  5 min ago       │  │  SOS — Pending         │   │
│          │   └───────────────────┘  └───────────────────────┘   │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  ⚠️ DEVICE PERMISSIONS                          │ │
│          │   │                                                  │ │
│          │   │  ✅ GPS Location       ✅ Microphone             │ │
│          │   │  ✅ Camera              ✅ Contacts              │ │
│          │   │  ✅ Notifications       ⚠️ Storage               │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   [ 🔄 UPDATE STATUS ]                                │
│          │                                                        │
└──────────┴──────────────────────────────────────────────────────┘
```

---

## ASCII Layout — No Linked Device

```text
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │                                                  │ │
│          │   │         📱                                       │ │
│          │   │   You don't have any linked device              │ │
│          │   │                                                  │ │
│          │   │   [ ➕ LINK DEVICE ]                             │ │
│          │   │                                                  │ │
│          │   └─────────────────────────────────────────────────┘ │
```

---

## Form Fields

There is no input form. This is a fully read-only screen.

### Displayed Data — Device

| Field       | Source              | Description                                     |
| ----------- | ------------------- | ----------------------------------------------- |
| Status      | `device.estado`     | `'Connected'` / `'Disconnected'` / `'Inactive'` |
| Model       | `device.modelo`     | Device model name                               |
| OS          | `device.os`         | Operating system and version                    |
| App Version | `device.versionApp` | Installed mobile app version                    |
| UUID        | `device.uuid`       | Unique device identifier                        |
| Battery     | `device.battery`    | Percentage 0–100                                |
| GPS Signal  | `device.gpsSignal`  | `'Strong'` / `'Moderate'` / `'Weak'`            |
| Last sync   | `device.lastSync`   | ISO timestamp of the last synchronization       |
| Last alert  | `device.lastAlerta` | Timestamp + type + status of the last alert     |

### Device Permissions

| Permission    | Field                            | Possible State                     |
| ------------- | -------------------------------- | ---------------------------------- |
| GPS Location  | `device.permisos.gps`            | ✅ Granted / ⚠️ Denied / ❌ Required |
| Microphone    | `device.permisos.microfono`      | ✅ / ⚠️ / ❌                         |
| Camera        | `device.permisos.camara`         | ✅ / ⚠️ / ❌                         |
| Contacts      | `device.permisos.contactos`      | ✅ / ⚠️ / ❌                         |
| Notifications | `device.permisos.notificaciones` | ✅ / ⚠️ / ❌                         |
| Storage       | `device.permisos.almacenamiento` | ✅ / ⚠️ / ❌                         |

---

## Validations

Not applicable. Read-only screen with an update button.

---

## States

| State                | Description                     | UI                                               |
| -------------------- | ------------------------------- | ------------------------------------------------ |
| `loading`            | Fetching device data            | `app-spinner` size `md` centered                 |
| `connected`          | Active device                   | Green "Connected" badge; battery and GPS visible |
| `disconnected`       | Device not synchronized         | Red "Disconnected" badge; last sync timestamp    |
| `no-device`          | No linked device                | Empty state with "Link device" button            |
| `error`              | API failure                     | Red banner + retry button                        |
| `updating`           | "Update" clicked                | Spinner inside button, subtle overlay            |
| `missing-permission` | Permission denied in mobile app | ⚠️ icon with explanatory tooltip                 |

---

## Flow

```text
/dashboard/device-status
  ├── onInit → DevicesService.getByUsuario(usuarioId)
  │     ├── device found → displays complete information
  │     └── no device → displays empty state
  │
  ├── [Update status]
  │     → DevicesService.getByUsuario(usuarioId) (new fetch)
  │     → Updates all metrics without reloading the page
  │
  └── [Link device] (no-device state)
        → Navigates to device linking screen
          (proposed: connect-device)
```

---

## Applied Styles

### Colors

```css
/* Page background */
background: var(--bg-base); /* #f5f4ff */

/* Cards */
background: var(--bg-card);
border: 1.5px solid var(--border);
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);

/* Device status */
--estado-conectado    : background var(--green-pale)  / color var(--green);
--estado-desconectado : background var(--red-pale)    / color var(--red);
--estado-inactivo     : background #f3f4f6 / color var(--text-dim);

/* Battery bar */
--bat-high    : var(--green);   /* > 50% */
--bat-medium  : var(--amber);   /* 20–50% */
--bat-low     : var(--red);     /* < 20% */

/* GPS signal — dots */
--gps-activo  : var(--purple-deep); /* #7c3aed */
--gps-inactivo: var(--purple-light); /* #c4b5fd */

/* Permissions */
--permiso-ok      : var(--green);   /* #16a34a */
--permiso-warning : var(--amber);   /* #d97706 */
--permiso-error   : var(--red);     /* #dc2626 */
```

### Typography

```css
/* Page title */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Device name */
font-size: 1.5rem; font-weight: 700; color: var(--text-primary);

/* Numeric metrics (battery %) */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Field labels */
font-size: 0.875rem; color: var(--text-muted);

/* UUID */
font-size: 0.8rem; font-family: monospace; color: var(--text-dim);
```

### Spacing

```css
/* Stat card grid (battery, GPS, sync, alert) */
display: grid;
grid-template-columns: repeat(2, 1fr);
gap: 16px;
padding: 24px;
```

---

## Components Used

| Component     | Variant/Config       | Usage                                               |
| ------------- | -------------------- | --------------------------------------------------- |
| `app-card`    | —                    | Device info, stat cards, and permissions containers |
| `app-button`  | `primary`, `md`      | "Update status"                                     |
| `app-button`  | `secondary`, `md`    | "Link device" (empty state)                         |
| `app-chip`    | `dotColor` by status | Status badge (Connected/Disconnected)               |
| `app-spinner` | `md`, `inline`       | Loading state                                       |

---

## Responsive

| Breakpoint          | Behavior                                                |
| ------------------- | ------------------------------------------------------- |
| Desktop `>1024px`   | Stat cards in a 2×2 grid; device information full width |
| Tablet `760–1024px` | Stat cards in 2 columns; reduced device information     |
| Mobile `<760px`     | Stat cards stacked in 1 column; sidebar → BottomNav     |

---

## Accessibility

* Battery bar: `role="progressbar"`, `aria-valuenow`, `aria-valuemin="0"`, `aria-valuemax="100"`
* GPS signal with dots: `aria-label="GPS signal: Strong — 4 out of 5 bars"`
* Permissions with descriptive `aria-label` for each permission
* Status badge: visible text (not color only)
* Empty state with `aria-live="polite"` when loading

---

## Service Integration

```typescript
// DevicesService — methods used

// Get the authenticated user's device
DevicesService.getByUsuario(usuarioId: number): Observable<Dispositivo>
// GET /devices?usuarioId=:id

// Data model
interface Dispositivo {
  id           : number;
  usuarioId    : number;
  uuid         : string;
  modelo       : string;
  os           : string;
  versionApp   : string;
  battery      : number;        // 0–100
  gpsSignal    : 'Strong' | 'Moderate' | 'Weak';
  estado       : 'Connected' | 'Disconnected' | 'Inactive';
  lastSync     : string;        // ISO timestamp
  lastAlerta   : {
    tiempo : string;
    tipo   : string;
    estado : string;
  } | null;
  permisos     : {
    gps            : boolean;
    microfono      : boolean;
    camara         : boolean;
    contactos      : boolean;
    notificaciones : boolean;
    almacenamiento : boolean;
  };
}
```

---

## Use Cases

| # | Actor | Action                                  | Result                                                     |
| - | ----- | --------------------------------------- | ---------------------------------------------------------- |
| 1 | User  | Navigates to `/dashboard/device-status` | Sees the complete status of their linked device            |
| 2 | User  | Device disconnected                     | Red badge + last connection timestamp                      |
| 3 | User  | Storage permission denied               | ⚠️ icon with tooltip "Permission denied in the mobile app" |
| 4 | User  | Clicks "Update status"                  | Data refreshes with the latest information                 |
| 5 | User  | No linked device                        | Empty state + "Link device" button                         |
| 6 | User  | Low battery (< 20%)                     | Battery bar turns red + "Critical level" text              |

```
```
