# Wireframe: Alert Management (Admin)

**Route:** `/admin/alertas`  
**Component:** `AlertAdminComponent`  
**Layout:** `AdminLayout` (Admin sidebar + Admin topbar)  
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
│  📊 Dash │   🔔 Alert Management                                 │
│  👥 Users│                                                        │
│  🔔 Alert│   ┌───────────────┐ ┌───────────────┐ ┌────────────┐ │
│  🗺️ Zones│   │ TOTAL ALERTS │ │    PENDING    │ │  ATTENDED  │ │
│  🚨 Emerg│   │               │ │               │ │            │ │
│  📋 Moder│   │     247       │ │      12       │ │    218     │ │
│  📱 Dev. │   │  this month   │ │   🔴 urgent   │ │  this month│ │
│          │   └───────────────┘ └───────────────┘ └────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  FILTERS                                        │ │
│          │   │                                                  │ │
│          │   │  [ 🔍 Search by location, user... ]             │ │
│          │   │                                                  │ │
│          │   │  [app-chip: All●] [app-chip: Pending]            │ │
│          │   │  [app-chip: Attended] [app-chip: Cancelled]      │ │
│          │   │                                                  │ │
│          │   │  Type: [app-chip: SOS] [app-chip: Theft]        │ │
│          │   │        [app-chip: Harassment] [app-chip: Medical]│ │
│          │   │                              [app-period-badge ▼]│ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  ALERTS (247 records)                            │ │
│          │   │                                                  │ │
│          │   │  ┌───┬──────┬──────────┬───────────┬──────┬───┐ │ │
│          │   │  │ # │ Type │   User   │  Location │ Time │Sta│ │ │
│          │   │  ├───┼──────┼──────────┼───────────┼──────┼───┤ │ │
│          │   │  │ 1 │🔴SOS │ Ana G.   │ Cra 7 #34 │10:32 │●Pe│ │ │
│          │   │  │ 2 │🟡Thef│ María L. │ Calle 12  │10:28 │●Pe│ │ │
│          │   │  │ 3 │🟣Har.│ Laura T. │ Parq.Cntl │10:25 │✅At│ │ │
│          │   │  │ 4 │🩷Med.│ Paula M. │ Av. 68    │10:20 │✅At│ │ │
│          │   │  │ 5 │🔴SOS │ Sofía R. │ Cra 15    │10:15 │●Pe│ │ │
│          │   │  └───┴──────┴──────────┴───────────┴──────┴───┘ │ │
│          │   │                                                  │ │
│          │   │  [ < Previous ]    Page 1 / 50  [ Next > ]       │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
└──────────┴──────────────────────────────────────────────────────┘
```

---

## ASCII Layout — Alert Detail Modal

```
                  ┌──────────────────────────────────────┐
                  │  🔔 Alert #1 — SOS                   │  [✕]
                  │                                       │
                  │  User:       Ana García               │
                  │  Phone:      +57 300 123 4567         │
                  │  Type:       🔴 SOS                   │
                  │  Status:     ● Pending                │
                  │  Date:       Today, 10:32 am          │
                  │                                       │
                  │  📍 Location                          │
                  │  Cra 7 #34-12, Bogotá                  │
                  │  Lat: 4.7110  Lng: -74.0721            │
                  │  [ MINI MAP ]                         │
                  │                                       │
                  │  📝 Description / Notes                │
                  │  [ Text area for admin notes ]        │
                  │                                       │
                  │  Change status:                        │
                  │  ○ Pending  ● Attended  ○ Cancelled   │
                  │                                       │
                  │  [ CANCEL ]  [ 💾 SAVE CHANGES ]      │
                  └───────────────────────────────────────┘
```

---

## Form Fields

### Filters (no validation, read-only)

| Filter | Type | Values |
|--------|------|--------|
| Free search | `text` | Location, user name |
| Status | `app-chip` | All · Pending · Attended · Cancelled |
| Type | `app-chip` | SOS · Theft · Harassment · Medical |
| Period | `app-period-badge` | Today · Week · Month · Custom |

### Modal — Update Alert

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| Status | `radio` | ✅ | `Pending` / `Attended` / `Cancelled` |
| Notes | `textarea` | ❌ | Max. 500 characters |

---

## Validations

```typescript
// Update alert modal
estado: required, enum['Pendiente','Atendida','Cancelada']
notas : optional, maxLength(500)
```

---

## States

| State | Description | UI |
|-------|-------------|-----|
| `cargando` | Fetching alerts | Stat card skeletons + table skeleton |
| `listo` | Data loaded | Stat cards with numbers + paginated table |
| `filtrado` | Chips / search active | Updated table; counter changes |
| `modal-detalle` | Row clicked | Modal with complete details + mini map |
| `guardando` | PATCH in progress | Button with spinner |
| `urgente` | SOS alert unattended > 5 min | Row with `var(--red-pale)` background + pulse |
| `error` | API failure | Red toast |

---

## Flow

```
/admin/alertas
  ├── onInit → AlertsService.getAll()
  │             → Populates stat cards + paginated table
  │
  ├── Search / Chips / Period badge
  │     → AlertsService.getAll(filters)
  │     → Updates table and counters
  │
  ├── Row click
  │     → Opens modal with complete details
  │     → Mini map with marker at the alert's lat/lng
  │
  ├── Change status in modal
  │     → AlertsService.updateEstado(id, newStatus)
  │     → Success toast → badge updated in table
  │
  └── Save notes
        → AlertsService.update(id, { notas })
        → Success toast → modal closes
```

---

## Applied Styles

### Colors

```css
background: var(--bg-base);

/* Stat cards */
background: var(--bg-card);
border: 1.5px solid var(--border);
border-radius: var(--dash-radius-card);

/* Stat card numbers */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Alert type badges */
--tipo-sos     : background var(--red-pale)    / color var(--red);
--tipo-robo    : background var(--amber-pale)  / color var(--amber);
--tipo-acoso   : background var(--purple-pale) / color var(--purple-deep);
--tipo-medical : background #fce7f3 / color var(--pink);

/* Status badges */
--estado-pendiente : background var(--amber-pale) / color var(--amber);
--estado-atendida  : background var(--green-pale)  / color var(--green);
--estado-cancelada : background #f3f4f6 / color var(--text-dim);

/* Urgent row (SOS > 5 min unattended) */
background: var(--red-pale);
animation: pulse 2s infinite; /* opacity 1 ↔ 0.7 */

/* Row hover */
background: var(--bg-card-hover);
cursor: pointer;
```

### Typography

```css
font-size: 2rem; font-weight: 700; /* page title */
font-size: 0.875rem; font-weight: 600; /* table header */
font-size: 0.875rem; /* cells */
```

### Spacing

```css
/* Stat cards — 3-column grid */
display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px;
padding: 24px;
```

---

## Components Used

| Component | Variant/Config | Usage |
|-----------|----------------|-------|
| `app-card` | — | Stat cards, filters, table |
| `app-chip` | `active: boolean` | Status and type filters |
| `app-period-badge` | `conDropdown: true` | Time period |
| `app-modal` | `lg`, `default` | Alert detail + status editing modal |
| `app-spinner` | `sm`, `inline` | Saving status |

---

## Responsive

| Breakpoint | Behavior |
|------------|----------|
| Desktop `>1024px` | Stat cards in 3 columns; full table |
| Tablet `760–1024px` | Stat cards in 2 columns; compact table |
| Mobile `<760px` | Stacked stat cards; table with horizontal scrolling |

---

## Accessibility

- Stat cards with `role="status"` and `aria-live="polite"` for updates
- Table with `<caption>` "System alerts list"
- Urgent rows with `aria-label="Urgent unattended alert"` + visual indicator
- Modal with `role="dialog"`, `aria-modal="true"`, focus trapped
- Notes textarea with `aria-label` and remaining character counter
- Chips with `role="checkbox"` and `aria-checked`

---

## Service Integration

```typescript
// AlertsService — methods used

AlertsService.getAll(): Observable<Alerta[]>
// GET /alertas

AlertsService.update(id: number, datos: Partial<Alerta>): Observable<Alerta>
// PUT /alertas/:id

AlertsService.updateEstado(id: number, estado: string): Observable<Alerta>
// PATCH /alertas/:id/estado

// Data model
interface Alerta {
  id         : number;
  nombre     : string;
  descripcion: string;
  tipo       : 'SOS' | 'Robo' | 'Acoso' | 'Medical';
  tiempo     : string;
  ubicacion  : string;
  lat        : number;
  lng        : number;
  estado     : 'Pendiente' | 'Atendida' | 'Cancelada';
  usuarioId  : number;
  notas      : string | null;
}
```

