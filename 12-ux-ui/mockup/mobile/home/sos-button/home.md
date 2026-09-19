## Home / SOS button

**Route:** `/home` — BottomNav tab **INICIO**
**Access:** USER
**Implementation status:** Implemented — safety status card, camera/microphone permission indicators, main SOS button and last-alert summary.

### Objective
Give the user a single, always-reachable screen to trigger an SOS alert, while showing at a glance whether the app has the permissions and location access it needs.

### Structure (top to bottom)
1. Header bar: ☰ hamburger (opens the drawer) + brand logo on the right
2. **Safety status card** — green outlined card: shield-check icon, "Zona segura" title in `--ok`, subtitle "Sin incidentes • GPS inactivo", bold line "Permiso denegado", chevron on the right (opens permission settings)
3. **Permission strip** — two inline indicators, each with a state dot: 🔴 📷 "Cámara" · 🔴 🎤 "Micrófono" (red dot = denied, green dot = granted)
4. **SOS button** — large circular metallic button (≈62% of screen width) with a brushed-metal bezel, a radial accent-colored face, the lion mark and the word "ALERTA"; tinted by the active theme
5. **Last alert card** — history icon, "Última alerta: Hace 168 días", secondary line "Última ubicación enviada: 14:32 — Neiva, Huila", chevron to the history detail
6. BottomNav with INICIO active

### Gestures on the SOS button
| Gesture | Action |
|---------|--------|
| Short tap | Discreet alert — silent SMS with the exact location |
| Double tap | Urgent alert — contacts receive a notification **and** a call |
| Long press | Starts the 3-second countdown to the full SOS activation |

### Components used
- Status card (outlined, semantic color)
- Permission indicator chip with a state dot
- Circular SOS button (metallic bezel + radial face + haptic feedback)
- Summary card with chevron
- BottomNav

### States
- **safe:** Green card "Zona segura"
- **permissions-missing:** Red dots on Cámara/Micrófono + "Permiso denegado" line; tapping the card opens the system settings
- **gps-off:** Subtitle shows "GPS inactivo"; a tap prompts to enable location
- **risk-zone:** Card turns amber/red with the zone name instead of "Zona segura"
- **pressing:** Button scales down with haptic feedback and a glowing ring
- **activating:** Navigates to the SOS countdown screen
- **alert-active:** The button is replaced by a shortcut to the active-alert screen
- **no-history:** Last-alert card shows "Aún no has enviado alertas"

### Constraints
- **Small phones (<360dp):** SOS button capped at 220dp; the last-alert card collapses to one line
- **Large phones:** Button capped at 280dp, vertically centered
- **Accessibility:** SOS button with `aria-label="Botón de alerta SOS, mantén presionado para activar"` and a minimum 200dp target; permission indicators announce their state as text, not color alone; status card with `role="status"` and `aria-live="polite"`; haptic + audible feedback on activation
- **Dark mode:** Near-black background `#101014`; the button keeps its metallic bezel with the accent face

### Prompt ready for Google Stitch
> Design the Home screen of the AlertaMujer mobile app for Android. Background is a very soft lavender `#f6f1fb` (near-black `#101014` in dark mode) with a slim header showing a white hamburger icon on the left and the circular gold-and-purple lion logo with the ALERTA MUJER wordmark on the right, over a purple gradient bar. Below the header, a white card outlined in green `1.5px #16a34a` with `border-radius 16px` containing a green shield-check icon, the title "Zona segura" in `1.05rem 700 #16a34a`, the line "Sin incidentes • GPS inactivo" in `0.85rem #6b7280`, the bold line "Permiso denegado" and a chevron on the right. Under it, a centered row with two indicators: a red dot with a camera icon and the label "Cámara", and a red dot with a microphone icon and the label "Micrófono", both in `#dc2626`. In the center of the screen, a very large circular emergency button (about 62% of the screen width) with a brushed dark-metal bezel, a radial purple face `#7c3aed → #3b0764`, a subtle glow, an embossed lion head and the word "ALERTA" in white `1.8rem 700` letterspaced. At the bottom, a white card with `border-radius 16px` containing a history clock icon, the title "Última alerta: Hace 168 días" in `0.95rem 600` and the secondary line "Última ubicación enviada: 14:32 — Neiva, Huila" in `0.8rem #6b7280`, with a chevron. A 5-tab bottom navigation bar (INICIO, MAPA, ASISTENCIA, CONTACTOS, HISTORIAL) in the purple gradient, with INICIO active in white and a filled icon. Complies with WCAG AA.

---