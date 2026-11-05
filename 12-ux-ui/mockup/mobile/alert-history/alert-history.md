## 10. Alert history

**Route:** `/history` — BottomNav tab **HISTORIAL**
**Access:** USER
**Implementation status:** Implemented — searchable alert log with a summary strip and status-coded entries.

### Objective
Let the user review every alert she has triggered, its status and where it happened, and open any entry for the full detail.

### Structure (top to bottom)
1. Hero header: "Historial de Alertas" + subtitle "Consulta el registro de tus alertas y su estado actual." + illustration on the right
2. Search field "Buscar por fecha, lugar o estado..."
3. **Summary card** — 4 inline metrics, each with a number over a label:
   - Total **5** alertas · Enviadas **2** alertas · Canceladas **2** alertas · En curso **1** alerta
   - "Enviadas" in `--sent`, "Canceladas" in `--info`, "Total" and "En curso" in `--text-primary`
4. **Alert list** — one card per alert, with a colored left rail matching the status:
   - Circular icon tile with a location pin, tinted by the alert type
   - Type title (e.g. "Emergencia", "Asistencia") + 🕐 time on the right
   - 📅 date · 📍 place
   - Status badge: `ENVIADA` · `CANCELADA` · `EN CURSO`
   - Chevron to open the detail
5. BottomNav with HISTORIAL active

### Displayed data
| Element | Source field | Description |
|---------|-------------|-------------|
| Type | `alerta.tipo` | Emergencia / Asistencia |
| Time | `alerta.hora` | HH:mm |
| Date | `alerta.fecha` | "30 Mar, 2026" |
| Place | `alerta.lugar` | Municipality or address |
| Status | `alerta.estado` | Enviada / Cancelada / En curso |

### Components used
- Hero header with illustration
- Search field
- Inline metric strip
- Alert list item with a status rail and badge
- Detail bottom sheet (map snapshot, timeline, contacts notified)

### States
- **loading:** Skeleton metric strip + three skeleton rows
- **ready:** Metrics and list rendered
- **searching:** The list filters by date, place or status
- **no-results:** "No hay alertas con esa búsqueda"
- **empty:** "Aún no has enviado alertas" with a bell icon
- **in-progress:** The "En curso" entry pulses and links to the active-alert screen
- **detail-open:** Bottom sheet with the full record
- **error:** Red banner with a retry button

### Constraints
- **Small phones (<360dp):** The metric strip scrolls horizontally; the place line truncates with an ellipsis
- **Long lists:** Infinite scroll in pages of 20
- **Accessibility:** The list uses `role="list"` with each item as a button labelled "Ver detalle de la alerta de [tipo] del [fecha]"; status conveyed by the badge text, not only by the rail color; metrics with `aria-live="polite"`
- **Dark mode:** Dark cards; status colors and rails preserved

### Prompt ready for Google Stitch
> Design the Alert History screen of the AlertaMujer mobile app for Android. At the top, a purple gradient hero block with rounded bottom corners containing the title "Historial de Alertas" in `1.5rem 700 #ffffff`, the subtitle "Consulta el registro de tus alertas y su estado actual." in `0.85rem rgba(255,255,255,0.85)` and a small illustration of documents and charts on the right. Below, on a `#f6f1fb` background: a white pill search field with a purple search icon and the placeholder "Buscar por fecha, lugar o estado..."; a white card with `border-radius 18px` split into four equal columns, each with a small uppercase label on top ("Total", "Enviadas", "Canceladas", "En curso") and a big number below in `1.6rem 700` ("5", "2", "2", "1") with the word "alertas" underneath in `0.72rem #6b7280`, where "Enviadas" is purple `#7c3aed` and "Canceladas" is blue `#2563eb`. Then a vertical list of white cards with `border-radius 18px` and a 4px colored left rail, each containing a circular pale-purple tile with a location pin, the type title in `1.05rem 700` (for example "Emergencia" in purple and "Asistencia" in blue), a clock icon with the time "14:32" on the right, a calendar icon with the date "30 Mar, 2026", a pin icon with the place "Neiva, Huila", a chevron on the right and a pill status badge at the bottom reading "ENVIADA" in pale purple or "CANCELADA" in pale blue. Below, the 5-tab bottom navigation with HISTORIAL active. Complies with WCAG AA.

---