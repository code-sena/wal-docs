## 12. Settings

**Route:** `/settings`
**Access:** USER
**Implementation status:** Entry point present in the drawer ("Configuración"); the screen is not in the current screenshot set — documented as the expected implementation.

### Objective
Centralize permissions, alert behavior, notifications, privacy and the account actions.

### Structure (top to bottom)
1. Hero header: "Configuración"
2. **Permissions section:** Location, Camera, Microphone, Contacts — each a row with its state ("Concedido" / "Denegado") and a shortcut to the system settings
3. **Alert section:** countdown duration (3s / 5s / 10s), automatic video recording, automatic SOS call, silent mode
4. **Notifications section:** push alerts, sound, vibration
5. **Account section:** edit profile, change password, saved location (department/municipality)
6. **Privacy section:** terms, privacy notice, delete my data
7. Danger action: "Cerrar sesión"

### Components used
- Grouped setting rows with a switch, a state label or a chevron
- Section headers
- Selector sheet (countdown duration)
- Danger confirmation dialog

### States
- **ready:** All sections rendered with their current values
- **permission-denied:** The row shows "Denegado" in `--danger` with a "Permitir" action
- **saving:** The switch shows an inline spinner until confirmed
- **error:** Toast "No se pudo guardar el cambio"
- **logout-confirm:** Danger dialog before clearing the session

### Constraints
- **Accessibility:** Every switch announces its label and state; the permission state is text, not color alone; the destructive action requires confirmation
- **Dark mode:** Dark tokens active

### Prompt ready for Google Stitch
> Design the Settings screen of the AlertaMujer mobile app for Android. A purple gradient hero block with rounded bottom corners carries a back arrow and the title "Configuración" in `1.5rem 700 #ffffff`. On a `#f6f1fb` background, grouped white cards with `border-radius 18px`, each preceded by an uppercase section label in `0.68rem 700 #7c3aed`: "PERMISOS" with rows for Ubicación, Cámara, Micrófono and Contactos, each with a rounded icon tile, the name in `0.95rem 600` and a state pill on the right reading "Concedido" in green `#dcfce7`/`#16a34a` or "Denegado" in red `#fee2e2`/`#dc2626`; "ALERTA" with a row "Tiempo de cuenta regresiva" showing "3 segundos" and a chevron, plus switch rows for "Grabar video automáticamente", "Llamada SOS automática" and "Modo silencioso"; "NOTIFICACIONES" with switch rows for push, sound and vibration; "CUENTA" with rows for editing the profile, changing the password and the saved location showing "Huila · La Plata"; and "PRIVACIDAD" with rows for terms, privacy notice and "Eliminar mis datos" in red. At the bottom, a full-width outlined pill button "Cerrar sesión" in red `#dc2626`. Complies with WCAG AA.

---
