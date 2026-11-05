# Wireframe: Location History

**Route:** `/dashboard/location-history`  
**Component:** `LocationHistoryComponent`  
**Layout:** `DashboardLayout` (Sidebar + Topbar + BottomNav)  
**Required role:** USER (operator)  
**Guards:** `authGuard` + `userGuard`  
**Lazy loading:** yes  
**Status:** ✅ Implemented  

---

## ASCII layout

The labels inside the layout are the Spanish texts of the interface.

```
┌─────────────────────────────────────────────────────────────────┐
│  ☰  AlertaMujer              [🔔]  [☀️/🌙]  [ Ana García ▼ ]   │
├──────────┬──────────────────────────────────────────────────────┤
│          │                                                        │
│  🏠 Home │   🗺️ Historial de Ubicaciones                         │
│  📍 Ubic.│                                                        │
│  🔔 Alert│   ┌─────────────────────────────────────────────────┐ │
│  🎬 Evid.│   │  MAPA CON TRAYECTORIA                           │ │
│  📱 Disp.│   │                                                  │ │
│          │   │  ┌──────────────────────────────────────────┐   │ │
│          │   │  │                                          │   │ │
│          │   │  │         [ MAPA LEAFLET ]                 │   │ │
│          │   │  │                                          │   │ │
│          │   │  │  📍 Punto inicio (verde)                 │   │ │
│          │   │  │  ·····················                   │   │ │
│          │   │  │  📍 Punto final (rojo)                   │   │ │
│          │   │  │                                          │   │ │
│          │   │  │  ── Trayectoria polilínea punteada        │   │ │
│          │   │  └──────────────────────────────────────────┘   │ │
│          │   │                                                  │ │
│          │   │  [ 🗑️ LIMPIAR SELECCIÓN ]   [app-period-badge ▼] │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  HISTORIAL DE PUNTOS  (18 registros)            │ │
│          │   │                                                  │ │
│          │   │  [ 🔍 Buscar por dirección...           ]       │ │
│          │   │                                                  │ │
│          │   │  ┌────┬─────────────┬──────────────┬──────┬───┐ │ │
│          │   │  │ #  │ Fecha/Hora  │ Dirección    │ GPS  │ 🔋│ │ │
│          │   │  ├────┼─────────────┼──────────────┼──────┼───┤ │ │
│          │   │  │  1 │ Hoy 10:32  │ Cra 7 #34-12 │ 💪   │85%│ │ │
│          │   │  │  2 │ Hoy 10:27  │ Av. El Dorado│ 💪   │84%│ │ │
│          │   │  │  3 │ Hoy 10:15  │ Calle 26     │ ⚡   │82%│ │ │
│          │   │  │  4 │ Hoy 09:50  │ Cra 30       │ 💪   │80%│ │ │
│          │   │  │  5 │ Hoy 09:30  │ Parque Simón │ 💪   │79%│ │ │
│          │   │  └────┴─────────────┴──────────────┴──────┴───┘ │ │
│          │   │                                                  │ │
│          │   │  [ < Anterior ]              [ Siguiente > ]    │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
└──────────┴──────────────────────────────────────────────────────┘
```

---

## Form fields

There is no creation form. The screen is for viewing the history.

### Filter controls

| Control | Type | Description |
|---------|------|-------------|
| Address search | `text` input | Filters the table by a match in the `address` field |
| Period | `app-period-badge` | «Hoy» · «Última semana» · «Último mes» · «Personalizado» (today, last week, last month, custom) |

### Data shown — Table

| Column | Source | Description |
|--------|--------|-------------|
| # | index | Row number |
| Date/Time | `UbicacionEntry.time` | Formatted timestamp |
| Address | `UbicacionEntry.address` | Full formatted address |
| GPS | `UbicacionEntry.gpsSignal` | Signal level icon |
| Battery | `UbicacionEntry.battery` | Percentage with a small colored bar |

### Map — Route

| Element | Description |
|---------|-------------|
| Polyline | Dashed line that joins all the history points in time order |
| Start marker | Green pin → oldest point of the selected period |
| End marker | Red pin → most recent point |
| Intermediate markers | Small purple circles shown when a row is clicked |
| Popup | Clicking a marker shows: address + time + battery + GPS |

---

## Validations

```typescript
// Search filter (client-side)
search: not required, filters in real time with a 300 ms debounce
```

---

## States

| State | Description | UI |
|-------|-------------|-----|
| `loading` | Fetching the history | Spinner over the map + table skeleton |
| `ready` | Data loaded | Map with polyline + table with records |
| `search-filtered` | Text in the search box | Table updated; the map shows only the filtered points |
| `period-filtered` | Period changed | New fetch; map and table updated |
| `row-selected` | Row clicked | The map flies to the point and opens its popup |
| `no-data` | No history | Empty map + message «No hay registros de ubicación» |
| `error` | API failure | Red banner + retry button |

---

## Flow

```
/dashboard/location-history
  ├── onInit → PhoneLocationService.getHistory(userId)
  │             → Fills the table
  │             → Draws the polyline and markers on the map
  │
  ├── Search input (300 ms debounce)
  │     → Filters the table locally by `address`
  │     → The map updates the visible points
  │
  ├── Change in app-period-badge
  │     → PhoneLocationService.getHistory(userId, period)
  │     → Updates the table and redraws the polyline
  │
  ├── Click on a table row
  │     → Map flyTo(lat, lng) of that entry
  │     → Popup with the data of that location
  │     → Row highlighted with background var(--purple-pale)
  │
  └── [Clear selection]
        → Removes the row highlight and closes the map popup
        → Goes back to the full polyline view
```

---

## Applied styles

### Colors
```css
/* Page background */
background: var(--bg-base); /* #f5f4ff */

/* Cards */
background: var(--bg-card);
border: 1.5px solid var(--border);
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);

/* Map polyline */
color: var(--purple-deep); /* #7c3aed */
stroke-dasharray: 6, 4;
stroke-width: 3;

/* Start marker */
color: var(--green); /* #16a34a */

/* End marker */
color: var(--red); /* #dc2626 */

/* Intermediate markers */
color: var(--purple-mid); /* #a78bfa */
radius: 5px;

/* GPS signal */
--gps-strong   : var(--green);
--gps-moderate : var(--amber);
--gps-weak     : var(--red);

/* Inline battery */
--bat-high   : var(--green);
--bat-medium : var(--amber);
--bat-low    : var(--red);

/* Selected row */
background: var(--purple-pale);   /* #ede9fe */
border-left: 3px solid var(--purple-deep);

/* Dark mode — map filter */
filter: invert(1) hue-rotate(180deg) brightness(0.85) contrast(0.9) saturate(0.6);
```

### Typography
```css
/* Page title */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Table header */
font-size: 0.875rem; font-weight: 600; color: var(--text-muted);

/* Table cells */
font-size: 0.875rem; color: var(--text-primary);

/* Search box */
font-size: 0.9rem;
```

### Spacing
```css
padding: 24px;          /* page */
gap: 24px;              /* between the map card and the table card */

/* Map */
height: 380px;
border-radius: var(--dash-radius-card);

/* Table */
padding: 12px 16px;     /* rows */
```

---

## Components used

| Component | Variant/Config | Use |
|-----------|----------------|-----|
| `app-card` | — | Map container and table container |
| `app-period-badge` | `conDropdown: true` | Time period selector |
| `app-button` | `secondary`, `sm` | «Limpiar selección» (clear selection) |
| `app-spinner` | `md`, `inline` | Loading state |
| Leaflet map | OpenStreetMap tiles | Route display |
| Native search input | `text` with 🔍 icon | Address filter |

---

## Responsive

| Breakpoint | Behavior |
|------------|----------|
| Desktop `>1024px` | 380 px map; table with all columns |
| Tablet `760–1024px` | 280 px map; table without the detailed GPS column |
| Mobile `<760px` | 220 px map; table with horizontal scroll; full-width search box; sidebar → BottomNav |

---

## Accessibility

- Map with `aria-label="Mapa de trayectoria de ubicaciones"`
- Table with `<caption>` «Historial de ubicaciones del dispositivo»
- Search box with `aria-label="Filtrar por dirección"` and `role="searchbox"`
- Table rows as `role="button"` + `aria-label="Ver en mapa: [dirección]"`
- Polyline with a text description in the `aria-description` of the map container
- Accessible map popup with the full text (not only visual)

---

## Service integration

```typescript
// PhoneLocationService — method used

// Get the location history
PhoneLocationService.getHistory(userId: number): Observable<UbicacionEntry[]>
// GET /phone-location/:userId/history

// With a period filter
PhoneLocationService.getHistory(userId: number, from: string, to: string): Observable<UbicacionEntry[]>
// GET /phone-location/:userId/history?desde=...&hasta=...

// Data model
interface UbicacionEntry {
  id        : number;
  usuarioId : number;
  time      : string;
  address   : string;
  gpsSignal : 'Fuerte' | 'Moderada' | 'Débil';
  battery   : number;
  lat       : number;
  lng       : number;
}
```

---

## Use cases

| # | Actor | Action | Result |
|---|-------|--------|--------|
| 1 | User | Opens `/dashboard/location-history` | Sees the map with today's route + the full table |
| 2 | User | Changes the period to «Última semana» | The map redraws the weekly polyline; the table updates |
| 3 | User | Types "Cra 7" in the search box | The table filters; the map shows only those points |
| 4 | User | Clicks a row | The map zooms to the point; popup with the detail |
| 5 | User | Clicks «Limpiar selección» | The map goes back to the full polyline view |
| 6 | User | No history in the period | Empty map + descriptive message |
