## 11. Side drawer (profile, navigation and themes)

**Route:** overlay — opened with the ☰ icon from any tab
**Access:** USER
**Implementation status:** Implemented — profile header, shortcut navigation, custom-theme switch and preset theme swatches with live preview.

### Objective
Give access to the user's profile, the main sections and, above all, the theme system that re-skins the whole app instantly.

### Structure (top to bottom)
1. **Profile header** on the theme's solid background: circular avatar, username (`dannybahamon613`), email in a lighter tone, short underline accent
2. **Navigation cards** — white rounded rows, each with a tinted icon tile, a label and a chevron; the active row is highlighted with a tinted background:
   - 🏠 Inicio · 👥 Contactos · 🕘 Historial · ⚙️ Configuración
3. **Theme card:**
   - Row: ☀️ "Tema Personalizado" + switch
   - Divider + uppercase label "TEMAS PREDETERMINADOS"
   - Swatch grid of circular color chips; the selected one shows a white ✓ on a dark ring
4. The remaining content of the screen behind the drawer stays visible and dimmed

### Components used
- Profile header
- Navigation row card with an icon tile
- Switch (custom theme)
- Color swatch grid with a selected state
- Scrim overlay + slide-in animation

### States
- **closed:** Off-canvas
- **open:** Slides in from the left over a dimmed scrim
- **item-active:** The current section's row is tinted and shows a chevron
- **custom-theme-off:** Only the preset swatches are selectable
- **custom-theme-on:** A color picker row is revealed below the switch
- **theme-applied:** Every surface, the header gradient and the BottomNav re-tint immediately; the choice persists across launches
- **logged-out:** Tapping "Cerrar sesión" clears the session and returns to Login

### Constraints
- **Width:** 82% of the screen, max 320dp
- **Gestures:** Opens with an edge swipe, closes with a swipe or a tap on the scrim
- **Accessibility:** Drawer with `role="dialog"` and `aria-modal="true"`, trapped focus, back gesture closes it; swatches as `role="radio"` inside a `role="radiogroup"` with the theme name as the label, never color alone; the switch announces its state
- **Dark mode:** Handled as one more theme in the same system

### Prompt ready for Google Stitch
> Design the side navigation drawer of the AlertaMujer mobile app for Android, shown as an overlay covering about 82% of the screen with the rest of the app dimmed behind it. The drawer background is a solid theme color (show the purple `#6d28d9` variant). At the top, a centered circular avatar of about 80dp with a white border, the username "dannybahamon613" in `1.2rem 700 #ffffff`, the email "dannybahamon613@gmail.com" in `0.8rem rgba(255,255,255,0.8)` and a short white underline accent. Below, a stack of white cards with `border-radius 14px`, each a navigation row with a rounded square icon tile in pale purple, a label in `1rem 600 #1e1a3c` and a chevron: "Inicio" with a home icon, "Contactos" with a people icon, "Historial" with a clock icon and "Configuración" with a gear icon; the active row uses a tinted lavender background instead of white. At the bottom, a card containing a row with a sun icon, the label "Tema Personalizado" and a toggle switch on the right, then a hairline divider, the uppercase label "TEMAS PREDETERMINADOS" in `0.68rem 700 #6b7280`, and a grid of circular color swatches of about 44dp in magenta `#ec2c85`, wine `#9b1b44`, near-black, dark purple `#4c1d95` and blue `#2563eb`, where the selected swatch shows a white check mark and a dark ring. Complies with WCAG AA.

---