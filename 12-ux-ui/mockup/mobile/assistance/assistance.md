##  Assistance channels

**Route:** `/assistance` — BottomNav tab **ASISTENCIA**
**Access:** USER
**Implementation status:** Implemented — national emergency and support lines with one-tap dialing.

### Objective
Put the official emergency and support phone lines one tap away, grouped by the kind of help needed.

### Structure (top to bottom)
1. Hero header: "Canales de Asistencia" + subtitle "Canales a los que podrás acudir en caso de emergencia." + illustration on the right
2. **Primary line card** (full-width gradient card): "Emergencia inmediata", the number `123` in `2.6rem 700`, the line "Policía · Ambulancia · Bomberos", and a soft pill button "📞 Llamar ahora"
3. **Secondary line grid** (2 columns), each card with an icon tile, an uppercase category, the number in `2rem 700`, a one-line description and a "Llamar" pill button:
   - 👤 **VIOLENCIA — 156** · "Orientación jurídica y psicológica"
   - 💡 **SALUD MENTAL — 106** · "Línea de crisis emocional"
4. Additional channel cards continue below the fold
5. BottomNav with ASISTENCIA active

### Components used
- Hero header with illustration
- Gradient feature card for the primary line
- 2-column grid of channel cards
- Pill call buttons (open the native dialer)

### States
- **ready:** All channels listed
- **calling:** The tapped button shows a spinner while the dialer opens
- **no-dialer:** Fallback sheet offering to copy the number, with the toast "Número copiado"
- **no-permission:** Prompts for the call permission before dialing

### Constraints
- **Small phones (<360dp):** The 2-column grid collapses to a single column
- **Accessibility:** Each button with `aria-label="Llamar a la línea 123, emergencia inmediata"`; numbers read digit by digit; 48dp minimum targets; category conveyed by text, not only by the card color
- **Dark mode:** Cards switch to dark surfaces with the gradient preserved on the primary card

### Prompt ready for Google Stitch
> Design the Assistance Channels screen of the AlertaMujer mobile app for Android. At the top, a purple gradient hero block with `border-bottom-left-radius 24px` and `border-bottom-right-radius 24px` containing the title "Canales de Asistencia" in `1.5rem 700 #ffffff`, the subtitle "Canales a los que podrás acudir en caso de emergencia." in `0.85rem rgba(255,255,255,0.85)` and a small colorful illustration of support icons on the right. Below, on a `#f6f1fb` background, a full-width card with `border-radius 18px` and a purple-to-magenta gradient containing "Emergencia inmediata" in `1.15rem 700 #ffffff`, the number "123" in `2.6rem 700 #ffffff`, the line "Policía · Ambulancia · Bomberos" in `0.9rem rgba(255,255,255,0.85)` and a translucent white pill button with a phone icon "Llamar ahora". Under it, a two-column grid of gradient cards: one in pink-to-purple with a person icon tile, the uppercase label "VIOLENCIA", the number "156" in `2rem 700` and the description "Orientación jurídica y psicológica"; another in blue-to-purple with a lightbulb icon tile, "SALUD MENTAL", "106" and "Línea de crisis emocional". Each card ends with a translucent white pill button "Llamar". Below, the 5-tab bottom navigation with ASISTENCIA active. Complies with WCAG AA.

---