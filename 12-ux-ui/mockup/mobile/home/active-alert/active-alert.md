## Active alert

**Route:** `/sos/active`
**Access:** USER
**Implementation status:** Implemented — live alert screen with remaining-time ring, live location sharing status and the "Estoy bien" de-escalation action.

### Objective
Keep the user informed while the alert is live, let her tell her network she is safe, and keep an emergency call one tap away.

### Structure (top to bottom)
1. Header row: ☰ hamburger + a circular "center on my location" button (◎)
2. Title "Alerta activa" in `var(--accent)` + subtitle "Tu red fue notificada"
3. Large countdown ring with `3:02` and the label "Tiempo restante"
4. **Emergency services card:** shield icon, "Servicios de emergencia activos", body "Cuando estés a salvo, presiona 'Estoy bien' para notificar."
5. **Location sharing card:** "Compartiendo ubicación" title, row with 📍 "En tiempo real" and 👁 "0 vistas"
6. **"Estoy bien" button:** wide soft-lavender card-button with a heart icon, title "Estoy bien" and caption "Notifica a tu red que estás a salvo"
7. Bottom link: 📞 "Llamar al 911"

### Components used
- Countdown ring (mm:ss)
- Informational cards
- Card-style primary action ("Estoy bien")
- Emergency call link
- Locate-me floating button

### States
- **active:** Ring counting down; the view counter updates live
- **viewed:** "N vistas" increases as contacts open the shared location
- **extending:** When the timer runs out, the alert auto-extends and the ring restarts
- **im-ok:** Confirmation sheet → notifies the network, stops sharing, returns to Home with a toast
- **call-911:** Opens the dialer with the emergency number pre-filled
- **connection-lost:** Amber banner "Sin conexión — reintentando enviar ubicación"

### Constraints
- **Always on:** Screen wake lock while the alert is active; a persistent notification mirrors the state
- **Accessibility:** Ring with `aria-live="polite"` announcing the remaining minutes (not every second); "Estoy bien" as a single large target of at least 72dp; the emergency call link is never hidden behind a scroll
- **Dark mode:** Dark surfaces, accent and green/red semantics preserved

### Prompt ready for Google Stitch
> Design the active-alert screen of the AlertaMujer mobile app for Android. Background is a soft lavender `#f6f1fb` with no header bar — only a floating white rounded hamburger button on the top left and a floating white circular "center location" button on the top right. Below, the left-aligned title "Alerta activa" in `2rem 700 #a21caf` and the subtitle "Tu red fue notificada" in `0.95rem #6b7280`. In the center, a large circular ring of about 200dp with a `4px #a21caf` stroke on a white face, containing a small bell icon, the time "3:02" in `2.6rem 700 #1e1a3c` and the caption "Tiempo restante" in `0.85rem #6b7280`. Under it, a white card with `border-radius 18px` containing a purple shield-check icon, the title "Servicios de emergencia activos" in `1rem 700 #7c3aed` and the body "Cuando estés a salvo, presiona 'Estoy bien' para notificar." in `0.85rem #6b7280`; then a white card titled "Compartiendo ubicación" with a two-column row: a location pin with "En tiempo real" and an eye icon with "0 vistas", separated by a vertical divider. Then a wide soft-lavender card-button `#e9d5ff` with `border-radius 18px` containing a heart icon, the title "Estoy bien" in `1.1rem 700 #4c1d95` and the caption "Notifica a tu red que estás a salvo". At the very bottom, a centered link with a phone icon "Llamar al 911" in `#a21caf`. Complies with WCAG AA.

---