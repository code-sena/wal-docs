# Wireframe: Risk Zone Management
 
**Route:** `/admin/zone-management`  
**Component:** `ZoneManagementComponent`  
**Layout:** `AdminLayout` (Admin sidebar + Admin topbar)  
**Required role:** ADMIN  
**Guards:** `authGuard` + `adminGuard`  
**Lazy loading:** yes  
**Status:** ✅ Implemented  
 
---
 
## ASCII Layout — Main View
 
```
┌─────────────────────────────────────────────────────────────────┐
│  ☰  AlertaMujer Admin        [🔔]  [☀️/🌙]  [ Admin ▼ ]        │
├──────────┬──────────────────────────────────────────────────────┤
│          │                                                        │
│  📊 Dash │   🗺️ Risk Zone Management                             │
│  👥 Users│                                                        │
│  🔔 Alert│   ┌──────────────────────────┬────────────────────┐   │
│  🗺️ Zones│   │  INTERACTIVE MAP         │  ZONES PANEL       │   │
│  🚨 Emerg│   │                          │                    │   │
│  📋 Moder│   │  ┌────────────────────┐  │  [ 🔍 Search zone ]│   │
│  📱 Dev. │   │  │                    │  │                    │   │
│          │   │  │  [ LEAFLET MAP ]   │  │  [➕ NEW ZONE]     │   │
│          │   │  │                    │  │                    │   │
│          │   │  │  🔴 High zone      │  │  ┌──────────────┐  │   │
│          │   │  │  🟡 Medium zone    │  │  │ 🔴 Campoa.   │  │   │
│          │   │  │  🟢 Low zone       │  │  │ 24 alerts    │  │   │
│          │   │  │                    │  │  │ HIGH — 42%   │  │   │
│          │   │  │  [+ Add point]     │  │  │ [✏️][🗑️]      │  │   │
│          │   │  │  [✕ Cancel]        │  │  └──────────────┘  │   │
│          │   │  │                    │  │                    │   │
│          │   │  └────────────────────┘  │  ┌──────────────┐  │   │
│          │   │                          │  │ 🟡 Av. Sant. │  │   │
│          │   │  Legend:                 │  │ 15 alerts    │  │   │
│          │   │  ● High ● Medium ● Low   │  │ MEDIUM — 26% │  │   │
│          │   │                          │  │ [✏️][🗑️]      │  │   │
│          │   │                          │  └──────────────┘  │   │
│          │   │                          │                    │   │
│          │   │                          │  ┌──────────────┐  │   │
│          │   │                          │  │ 🟢 Parq. Gzn │  │   │
│          │   │                          │  │ 6 alerts     │  │   │
│          │   │                          │  │ LOW — 10%    │  │   │
│          │   │                          │  │ [✏️][🗑️]      │  │   │
│          │   │                          │  └──────────────┘  │   │
│          │   └──────────────────────────┴────────────────────┘   │
│          │                                                        │
└──────────┴──────────────────────────────────────────────────────┘
```
 
---
 
## ASCII Layout — Create / Edit Zone Modal
 
```
                  ┌──────────────────────────────────────┐
                  │  ➕ New risk zone                     │  [✕]
                  │                                       │
                  │  Zone name *                          │
                  │  [ Campoalegre Centro               ] │
                  │                                       │
                  │  Risk level *                         │
                  │  ● High   ○ Medium   ○ Low            │
                  │                                       │
                  │  Description                          │
                  │  [ Zone with a high concentration of ]│
                  │                                       │
                  │  Coordinates (center of the zone) *   │
                  │  Lat: [ 2.2964  ]  Lng: [ -75.0199 ] │
                  │                                       │
                  │  Radius (meters) *                    │
                  │  [ 500                              ] │
                  │                                       │
                  │  [ Or select on the map ↗️ ]           │
                  │                                       │
                  │  [ CANCEL ]   [ 💾 SAVE ZONE ]        │
                  └──────────────────────────────────────┘
```
 
---
 
## ASCII Layout — Delete Zone Modal (danger)
 
```
                  ┌──────────────────────────────────────┐
                  │  🗑️ Delete zone                       │  [✕]
                  │                                       │
                  │  Delete "Campoalegre Centro"?         │
                  │                                       │
                  │  This zone has 24 associated          │
                  │  alerts. The alerts will not be       │
                  │  deleted.                             │
                  │                                       │
                  │  [ CANCEL ]   [ 🗑️ DELETE ]           │
                  └──────────────────────────────────────┘
```
 
---
 
## Form Fields
 
### Create / Edit zone
 
| Field | Type | Required | Validation |
|-------|------|-----------|------------|
| Name | `text` | ✅ | Min. 3 characters |
| Risk level | `radio` | ✅ | `Alto` / `Medio` / `Bajo` |
| Description | `textarea` | ❌ | Max. 300 characters |
| Latitude | `number` | ✅ | Valid range for Colombia (-4 to 13) |
| Longitude | `number` | ✅ | Valid range for Colombia (-79 to -67) |
| Radius (meters) | `number` | ✅ | Min. 100m, max. 10,000m |
 
---
 
## Validations
 
```typescript
// Zone form
nombre      : required, minLength(3)
nivel       : required, enum['Alto','Medio','Bajo']
descripcion : optional, maxLength(300)
lat         : required, min(-4), max(13)
lng         : required, min(-79), max(-67)
radio       : required, min(100), max(10000)
```
 
---
 
## States
 
| State | Description | UI |
|--------|-------------|-----|
| `loading` | Fetching zones | Spinner over the map + skeleton in the panel |
| `ready` | Zones loaded | Map with circles + list in the panel |
| `searching` | Text in the search box | Panel filters zones by name |
| `create-modal` | New zone modal | Empty form |
| `edit-modal` | Edit modal | Pre-filled form |
| `map-mode` | "select on map" click | Special cursor on the map; a click saves the coordinates into the form |
| `saving` | POST/PUT in progress | Button with spinner |
| `delete-modal` | Confirm delete | Danger modal |
| `error` | API failure | Red toast |
 
---
 
## Flow
 
```
/admin/zone-management
  ├── onInit
  │   ├── RiskZonesService.getZonas() → draws circles + fills the panel
  │   └── RiskZonesService.getPuntos() → draws CAIs and hospitals
  │
  ├── Click on a zone on the map or in the panel
  │     → Map flyTo(lat, lng) of the zone
  │     → Highlights the circle with a thicker border
  │
  ├── [➕ New zone]
  │     → Empty form modal
  │     → [Save] → RiskZonesService.createZona(datos)
  │     → A new circle appears on the map + a card in the panel
  │
  ├── [✏️ Edit]
  │     → Modal pre-filled with the zone's data
  │     → [Save] → RiskZonesService.updateZona(id, datos)
  │     → Circle and card updated
  │
  ├── [Select on map]
  │     → Activates selection mode in Leaflet
  │     → Click on the map → coordinates are copied into the form
  │
  └── [🗑️ Delete]
        → Danger modal warning about associated alerts
        → [Confirm] → RiskZonesService.deleteZona(id)
        → Circle removed from the map + card removed from the panel
```
 
---
 
## Applied Styles
 
### Colors
```css
background: var(--bg-base);
 
/* Split layout — map + panel */
display: grid;
grid-template-columns: 1fr 360px;
gap: 16px;
 
/* Cards in the panel */
background: var(--bg-card);
border: 1.5px solid var(--border);
border-radius: var(--dash-radius-card);
box-shadow: var(--dash-shadow-card);
 
/* Circles on the map by level */
--zona-alto  : fill #ef4444 / stroke #dc2626 / opacity 0.25;
--zona-medio : fill #f59e0b / stroke #d97706 / opacity 0.25;
--zona-bajo  : fill #22c55e / stroke #16a34a / opacity 0.25;
 
/* Selected circle */
stroke-width: 3; opacity: 0.4;
 
/* Card in the panel — level badge */
--badge-alto  : background var(--red-pale)   / color var(--red);
--badge-medio : background var(--amber-pale) / color var(--amber);
--badge-bajo  : background var(--green-pale) / color var(--green);
 
/* Dark mode — map filter */
filter: invert(1) hue-rotate(180deg) brightness(0.85) contrast(0.9) saturate(0.6);
```
 
### Typography
```css
font-size: 2rem; font-weight: 700;  /* page title */
font-size: 1rem; font-weight: 700;  /* zone name in card */
font-size: 0.875rem;                /* alerts count + percentage */
```
 
### Spacing
```css
padding: 24px;
gap: 16px;
 
/* Zones panel */
overflow-y: auto;
max-height: calc(100vh - 200px);
 
/* Map */
height: calc(100vh - 200px);
min-height: 500px;
border-radius: var(--dash-radius-card);
```
 
---
 
## Components Used
 
| Component | Variant/Config | Usage |
|------------|----------------|-----|
| `app-card` | — | Zone cards in the panel |
| `app-button` | `primary`, `sm` | "➕ New zone" |
| `app-button` | `secondary`, `sm` | "Select on map" |
| `app-modal` | `zona`, `default` | Create/edit zone modal |
| `app-modal` | `sm`, `danger` | Confirm delete zone |
| `app-spinner` | `lg`, `fullscreen` | Initial zone loading |
| Leaflet map | Google Maps tiles | Interactive map with circles |
 
---
 
## Responsive
 
| Breakpoint | Behavior |
|------------|---------------|
| Desktop `>1024px` | Split layout: map on the left + panel on the right (360px) |
| Tablet `760–1024px` | Panel above the map (vertically stacked); panel with scroll |
| Mobile `<760px` | Tabs: Map / List; no split layout; collapsed sidebar |
 
---
 
## Accessibility
 
- Map with `aria-label="Risk zones map"`
- Circles with `role="button"` and `aria-label="Zone [name], level [level], [n] alerts"`
- Zones panel with `role="list"` and cards as `role="listitem"`
- Modal with `role="dialog"`, `aria-modal="true"`, trapped focus
- Coordinate inputs with `aria-describedby` explaining the valid range
- Minimum contrast on level badges (text over a light/dark background)
---
 
## Service Integration
 
```typescript
// RiskZonesService — methods used
 
RiskZonesService.getZonas(): Observable<ZonaRiesgo[]>
// GET /zones
 
RiskZonesService.createZona(datos: Omit<ZonaRiesgo, 'id' | 'alertas'>): Observable<ZonaRiesgo>
// POST /zones
 
RiskZonesService.updateZona(id: number, datos: Partial<ZonaRiesgo>): Observable<ZonaRiesgo>
// PUT /zones/:id
 
RiskZonesService.deleteZona(id: number): Observable<void>
// DELETE /zones/:id
 
RiskZonesService.getPuntos(): Observable<PuntoInteres[]>
// GET /zones/puntos  (CAIs, hospitals)
 
// Data model
interface ZonaRiesgo {
  id          : number;
  nombre      : string;
  nivel       : 'Alto' | 'Medio' | 'Bajo';
  descripcion : string;
  lat         : number;
  lng         : number;
  radio       : number;        // meters
  alertas     : number;        // count of associated alerts
  porcentaje  : number;        // % of the total
}
 
interface PuntoInteres {
  id    : number;
  tipo  : 'CAI' | 'Hospital';
  nombre: string;
  lat   : number;
  lng   : number;
}
```
 
---
 