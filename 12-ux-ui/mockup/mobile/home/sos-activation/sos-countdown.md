##  SOS activation (countdown)

**Route:** `/sos/activating` (full-screen modal)
**Access:** USER
**Implementation status:** Implemented — 3-second countdown with a cancel action before the alert is dispatched.

### Objective
Give the user a short, reversible window before the alert is sent, while showing exactly what is about to happen.

### Structure (top to bottom)
1. Circular brand logo floating above the card
2. White card (`border-radius 24px`, large padding) with:
   - Countdown ring with the remaining seconds in the center (`3` → `2` → `1`), stroke in `var(--accent)`
   - Title "Activando Alerta SOS..." in `var(--accent)`
   - Three checklist rows with rounded icon tiles:
     - 📍 "Enviando ubicación en tiempo real"
     - 🎥 "Iniciando grabación de video"
     - 📞 "Llamando a tus contactos elegidos"
   - Full-width soft pill button "Cancelar Alerta"
3. No BottomNav (full-screen, blocking)

### Components used
- Animated countdown ring (`CircularProgressIndicator` style, 1s per step)
- Checklist row (icon tile + label), items lighting up as each task starts
- Soft secondary pill button

### States
- **counting:** Ring animates from 3 to 0; vibration on each tick
- **step-running:** The active checklist row is highlighted, completed rows get a ✅
- **cancelled:** Modal closes, returns to Home, toast "Alerta cancelada"
- **dispatched:** Countdown reaches 0 → navigates to the active-alert screen
- **no-permissions:** Rows that can't run are dimmed with the note "Permiso denegado"

### Constraints
- **Blocking:** The system back gesture is mapped to "Cancelar Alerta", never to a silent dismissal
- **Accessibility:** Countdown with `aria-live="assertive"` announcing the remaining seconds; the cancel button is the first focusable element and at least 56dp tall; vibration + sound cues alongside the visual countdown
- **Dark mode:** Card `#1c1c22`; ring keeps the accent color

### Prompt ready for Google Stitch
> Design the SOS activation screen of the AlertaMujer mobile app for Android. Full-screen very light lavender background `#f6f1fb`. At the top center, a circular dark purple badge with the gold lion logo and a glowing ring. Below it, a large white card with `border-radius 24px`, padding 28px and `box-shadow 0 12px 32px rgba(76,29,149,0.12)`, containing: a centered circular countdown ring of about 120dp with a `3px #a21caf` stroke and the digit "3" in the middle in `3rem 700 #1e1a3c`; the title "Activando Alerta SOS..." in `1.1rem 700 #a21caf`; and three rows, each with a rounded square icon tile in pale purple `#f3e8ff` (a location pin, a video camera and a phone) followed by the texts "Enviando ubicación en tiempo real", "Iniciando grabación de video" and "Llamando a tus contactos elegidos" in `0.95rem #1e1a3c`. At the bottom of the card, a full-width pill button "Cancelar Alerta" with a soft lavender background `#e9d5ff`, text `#4c1d95` and `border-radius 50px`. No bottom navigation. Complies with WCAG AA.

---