## Map

**Route:** `/map` — BottomNav tab **MAPA**
**Access:** USER
**Implementation status:** Tab registered in the BottomNav; the screen is not present in the current screenshot set — documented as the expected implementation.

### Objective
Show the user's live position, the risk zones around her and the nearby assistance points (police CAI, hospitals), and let her share the location from the map itself.

### Structure (top to bottom)
1. Full-screen map under a translucent header (☰ + logo)
2. Floating search field "Buscar dirección o lugar..."
3. Risk-zone circles: 🔴 high, 🟡 medium, 🟢 low, at 25% opacity
4. Assistance point markers: CAI and hospitals
5. Floating "center on my location" button (◎)
6. Bottom sheet (collapsed): current zone name, risk level badge and the actions "Compartir ubicación" and "Ver canales de asistencia"
7. BottomNav with MAPA active

### Components used
- Map view with tinted circles and custom markers
- Floating search field and locate-me button
- Draggable bottom sheet
- Level badges (high/medium/low)

### States
- **loading:** Spinner over the map while zones load
- **ready:** Map centered on the user with the zones drawn
- **permission-denied:** Blurred map + card "Activa el GPS para ver tu ubicación" with a settings button
- **in-risk-zone:** Red banner with the zone name and a shortcut to the SOS button
- **marker-selected:** Bottom sheet expands with the point's details and a "Cómo llegar" action
- **offline:** Cached tiles + amber "Modo sin conexión" badge

### Constraints
- **Battery:** Location updates throttled when the app is in the background
- **Accessibility:** Map with `aria-label="Mapa de zonas de riesgo y puntos de asistencia"`; a list alternative to the map for screen readers; markers with a descriptive label including name, type and distance
- **Dark mode:** Dark map style, circles keep their semantic colors

### Prompt ready for Google Stitch
> Design the Map screen of the AlertaMujer mobile app for Android. A full-screen map fills the view, with a translucent purple gradient header on top showing a white hamburger icon and the ALERTA MUJER lion logo. Floating over the map near the top, a white pill search field with `border-radius 50px`, a purple search icon and the placeholder "Buscar dirección o lugar..." in `0.9rem #6b7280`. On the map, translucent risk circles at 25% opacity in red `#ef4444`, amber `#f59e0b` and green `#22c55e`, plus rounded square markers for police CAIs and hospitals, and a purple pulsing dot for the user's position. On the bottom right, a white circular floating button with a purple crosshair icon. At the bottom, a white bottom sheet with `border-top-left-radius 22px` and `border-top-right-radius 22px` showing a drag handle, the current zone name in `1.05rem 700`, a risk-level pill badge, and two pill buttons: "Compartir ubicación" in purple gradient and "Ver canales de asistencia" in soft lavender. Below it, the 5-tab bottom navigation with MAPA active. Complies with WCAG AA.

---