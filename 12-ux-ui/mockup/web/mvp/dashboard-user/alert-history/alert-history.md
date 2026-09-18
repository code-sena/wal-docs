# Wireframe: Alert History

**Route:** `/dashboard/alert-history`
**Component:** `AlertHistory`
**Layout:** `DashboardLayout` (Sidebar + Topbar + BottomNav)
**Required Role:** USER (operator)
**Guards:** `authGuard` + `userGuard`
**Lazy loading:** yes
**Status:** ✅ Implemented

---

## Layout ASCII

```text
┌─────────────────────────────────────────────────────────────────┐
│  ☰  AlertaMujer              [🔔]  [☀️/🌙]  [ Ana García ▼ ]   │
├──────────┬──────────────────────────────────────────────────────┤
│          │                                                        │
│  🏠 Home │   🔔 Alert History                                    │
│  📍 Loc. │                                                        │
│  🔔 Alert│   ┌─────────────────────────────────────────────────┐ │
│  🎬 Evid.│   │  FILTERS                                        │ │
│  📱 Dev. │   │                                                  │ │
│          │   │  [ app-chip: All ● ] [ app-chip: Pending ]      │ │
│          │   │  [ app-chip: Handled ] [ app-chip: Cancelled ]  │ │
│          │   │                                    [app-period-badge ▼] │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  ALERTS (12 records)                            │ │
│          │   │                                                  │ │
│          │   │  ┌────┬──────────┬────────────┬──────┬────────┐ │ │
│          │   │  │ #  │ Type     │ Location   │ Date │ Status │ │ │
│          │   │  ├────┼──────────┼────────────┼──────┼────────┤ │ │
│          │   │  │  1 │ 🔴 SOS  │ Cra 7 #34  │Today │●Pend.  │ │ │
│          │   │  │  2 │ 🟡 Theft│ Calle 12   │Today │✅Hand. │ │ │
│          │   │  │  3 │ 🔴 SOS  │ Av. 68     │Yest. │✅Hand. │ │ │
│          │   │  │  4 │ 🟣 Harass│ Parq. Cntl │Yest. │✅Hand. │ │ │
│          │   │  │  5 │ 🩷 Med. │ Cra 15     │Yest. │●Pend.  │ │ │
│          │   │  └────┴──────────┴────────────┴──────┴────────┘ │ │
│          │   │                                                  │ │
│          │   │  [ < Previous ]              [ Next > ]          │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  📊 SUMMARY                                     │ │
│          │   │                                                  │ │
│          │   │  Total: 12   Pending: 2   Handled: 9            │ │
│          │   │  Cancelled: 1                                    │ │
│          │   └─────────────────────────────────────────────────┘ │
└──────────┴──────────────────────────────────────────────────────┘
```

---

## ASCII Layout — Alert Details Modal

```text
                   ┌──────────────────────────────────────┐
                   │  🔔 Alert Details #1                  │  [✕]
                   │                                       │
                   │  Type:       🔴 SOS                   │
                   │  Status:     ● Pending                │
                   │  Date:       Today, 10:32 am          │
                   │                                       │
                   │  📍 Location                          │
                   │  Cra 7 #34-12, Bogotá                 │
                   │  Lat: 4.7110  Lng: -74.0721           │
                   │                                       │
                   │  [ MINI MAP — alert location ]       │
                   │                                       │
                   │  📝 Description                        │
                   │  SOS alert activated from              │
                   │  mobile device.                        │
                   │                                       │
                   │              [ CLOSE ]                 │
                   └──────────────────────────────────────┘
```

---

## Form Fields

There is no input form. This is a read-only screen with filters.

### Displayed Data — Table

| Column   | Source Field       | Description                                                 |
| -------- | ------------------ | ----------------------------------------------------------- |
| #        | index              | Row number                                                  |
| Type     | `alerta.tipo`      | SOS / Theft / Harassment / Medical — with color badge       |
| Location | `alerta.ubicacion` | Abbreviated text address                                    |
| Date     | `alerta.tiempo`    | Formatted relative timestamp                                |
| Status   | `alerta.estado`    | Badge: Pending (amber) / Handled (green) / Cancelled (gray) |

### Filters

| Filter | Type                        | Values                              |
| ------ | --------------------------- | ----------------------------------- |
| Status | `app-chip` multi-select     | All · Pending · Handled · Cancelled |
| Period | `app-period-badge` dropdown | Today · Week · Month · Custom       |

---

## Validations

Not applicable (read-only screen with filters and no form validation).

---

## States

| State          | Description            | UI                                                 |
| -------------- | ---------------------- | -------------------------------------------------- |
| `loading`      | Fetching alerts        | Skeleton table (5 gray rows)                       |
| `ready`        | Alerts loaded          | Table with data and summary                        |
| `filtered`     | Active chips           | Updated table; active chip with `active: true`     |
| `no-results`   | Filter with no matches | Empty message: "No alerts match these filters"     |
| `no-data`      | User has no alerts     | Message: "You have no registered alerts" + 🔔 icon |
| `error`        | API failure            | Red banner + retry button                          |
| `details-open` | Row clicked            | `app-modal` size `md` with alert details           |

---

## Flow

```text
/dashboard/alert-history
  ├── onInit → AlertsService.getByUsuario(usuarioId)
  │             → Populates table + calculates summary
  │
  ├── Click status chip
  │     → Locally filters table by selected status
  │     → Updates "X records" counter
  │
  ├── Change app-period-badge
  │     → AlertsService.getByUsuario(usuarioId, period)
  │     → Updates table and summary
  │
  ├── Click row
  │     → Opens app-modal with alert details
  │     → Mini map with marker at alert lat/lng
  │
  └── Click "Close" in modal
        → Closes modal, focus returns to the row
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

/* Alert type badges */
--tipo-sos     : background #fee2e2 / color #dc2626;   /* var(--red-pale / --red) */
--tipo-robo    : background #fef3c7 / color #d97706;   /* var(--amber-pale / --amber) */
--tipo-acoso   : background #ede9fe / color #7c3aed;   /* var(--purple-pale / --purple-deep) */
--tipo-medical : background #fce7f3 / color #ec4899;   /* pink / var(--pink) */

/* Status badges */
--estado-pendiente : background var(--amber-pale) / color var(--amber);
--estado-atendida  : background var(--green-pale)  / color var(--green);
--estado-cancelada : background #f3f4f6 / color var(--text-dim); /* #9ca3af */

/* Filter chips (app-chip active) */
background: var(--purple-pale);
border: 1.5px solid var(--purple-deep);
color: var(--purple-deep);

/* Row hover */
background: var(--bg-card-hover); /* #f0eeff */
cursor: pointer;
```

### Typography

```css
/* Page title */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Table header */
font-size: 0.875rem; font-weight: 600; color: var(--text-muted);

/* Table cells */
font-size: 0.875rem; color: var(--text-primary);

/* Summary — numbers */
font-size: 1.5rem; font-weight: 700; color: var(--text-primary);
font-size: 0.875rem; color: var(--text-muted); /* labels */
```

### Spacing

```css
padding: 24px;           /* page */
gap: 16px;               /* between cards */
padding: 12px 16px;      /* table rows */
gap: 8px;                /* filter chips */
```

---

## Components Used

| Component          | Variant/Config      | Usage                                             |
| ------------------ | ------------------- | ------------------------------------------------- |
| `app-card`         | —                   | Filter, table, and summary containers             |
| `app-chip`         | `active: boolean`   | Status filters (All, Pending, Handled, Cancelled) |
| `app-period-badge` | `conDropdown: true` | Time period selector                              |
| `app-modal`        | `md`, `default`     | Alert details modal                               |
| `app-spinner`      | `md`, `inline`      | Table loading state                               |

---

## Responsive

| Breakpoint          | Behavior                                                                   |
| ------------------- | -------------------------------------------------------------------------- |
| Desktop `>1024px`   | Full table; summary in horizontal row                                      |
| Tablet `760–1024px` | Table without full "Location" column (truncated); summary in 2 columns     |
| Mobile `<760px`     | Table with horizontal scroll; chips stacked in 2 rows; sidebar → BottomNav |

---

## Accessibility

* Table with `<caption>` "User alert history"
* Chips with `role="checkbox"` and `aria-checked` for filters
* Table rows with `role="button"` and `aria-label="View alert details #N"`
* Modal with `role="dialog"`, `aria-modal="true"`, focus trapped
* Status badges with text alternatives (not color only)
* Loading state: `aria-live="polite"` on table container

---

## Service Integration

```typescript
// AlertsService — methods used

// Get alerts for the authenticated user
AlertsService.getByUsuario(usuarioId: number): Observable<Alerta[]>
// GET /alertas?usuarioId=:id

// Data model
interface Alerta {
  id         : number;
  nombre     : string;
  descripcion: string;
  tipo       : 'SOS' | 'Robo' | 'Acoso' | 'Medical';
  tiempo     : string;       // ISO timestamp
  ubicacion  : string;       // Formatted address
  lat        : number;
  lng        : number;
  estado     : 'Pendiente' | 'Atendida' | 'Cancelada';
  usuarioId  : number;
}
```

---

## Use Cases

| # | Actor | Action                                  | Result                                           |
| - | ----- | --------------------------------------- | ------------------------------------------------ |
| 1 | User  | Navigates to `/dashboard/alert-history` | Sees table with all their alerts + summary       |
| 2 | User  | Clicks "Pending" chip                   | Table filters only pending alerts                |
| 3 | User  | Changes period to "Last month"          | Table updates with alerts from the current month |
| 4 | User  | Clicks a row                            | Opens modal with complete details and mini map   |
| 5 | User  | No registered alerts                    | Empty message with descriptive icon              |
| 6 | User  | Active filter with no results           | Message "No alerts match these filters"          |

```
```
