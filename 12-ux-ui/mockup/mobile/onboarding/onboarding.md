## 1. Onboarding (carousel)

**Route:** `/onboarding`
**Access:** PUBLIC (first launch only)
**Implementation status:** Implemented — 7-slide horizontal pager with dot indicator; the location slide persists the selection locally before entering the app.

### Objective
Introduce the value proposition, teach the SOS button gestures, explain messaging/calls and the trust network, capture the user's department/municipality, and hand off to Login.

### Structure (top to bottom, per slide)
1. Full-bleed illustration area over a soft lavender/pink background with decorative blurred circles
2. White rounded card (`border-radius 22px`, top accent bar in `var(--accent)`) containing:
   - Two-line title, the second line in `var(--accent)` (e.g. "¡Tu seguridad es / nuestra prioridad!")
   - Uppercase section label (e.g. "GESTOS DISPONIBLES", "CÓMO TE PROTEGEMOS", "TU RED DE FAMILIA", "SIEMPRE INFORMADA")
   - List of feature rows: emoji tile + bold title + one-line description + optional right-aligned tag badge
3. Dot indicator (7 dots, active dot elongated in `var(--accent)`)

### Captured slides
| # | Title | Content |
|---|-------|---------|
| 1 | ¡Tu seguridad es nuestra prioridad! | 🤝 Red de apoyo digital · 📍 Ubicación en tiempo real · 🚨 Alerta inmediata |
| 2 | Cómo funciona el botón | 👆 Toque corto `Discreta` — silent SMS with exact location · ✌️ Doble toque `Urgente` — contacts get notification + call |
| 3 | Mensajes y Llamadas | 💬 SMS Automático `Instantáneo` · 📞 Llamada SOS `Urgente` — automatic emergency call |
| 4 | Tu Red de Apoyo | 🤝 Red de confianza `Familia` · 🔔 Aviso inmediato `Urgente` · Gestión flexible `Siempre` |
| 5 | Tu Ubicación | Department + Municipality selectors under a "TU SELECCIÓN" label, "💾 Guardar Ubicación" primary button, "✅ Datos protegidos" footnote |
| 6 | Mantente Informada | 🚨 Alertas SOS `Tiempo real` — critical notices even with the app closed · 🤝 Red de apoyo `Siempre activa` |

### Form fields (slide 5 only)
| Field | Type | Required | Validation |
|-------|------|-----------|------------|
| Departamento | `select` | ✅ | Colombian department list |
| Municipio | `select` | ✅ | Filtered by the selected department |

### Components used
- Horizontal `PageView` / carousel + dot indicator
- Feature row item (emoji tile + text + tag badge)
- Native `select` rows for the location step
- Primary pill button — "Guardar Ubicación"

### States
- **slide-idle:** Card visible, dot indicator reflects the index
- **swiping:** Horizontal slide transition
- **location-empty:** "Guardar Ubicación" disabled (opacity 0.6)
- **location-saved:** Selection persisted locally; advances to the next slide
- **finished:** Navigates to `/auth/login` and marks onboarding as seen

### Constraints
- **Small phones (<360dp):** Illustration height reduced; the card scrolls internally
- **Large phones / tablets:** Card capped at 420px wide, centered
- **Accessibility:** Carousel with `role="region"` and `aria-roledescription="carousel"`; dots as buttons with `aria-label="Slide X of 7"`; feature rows read as a list; all emoji decorative (`aria-hidden`) with the meaning carried by the text
- **Dark mode:** Card `#1c1c22`, body text `#e5e7eb`, accent preserved

### Prompt ready for Google Stitch
> Design the onboarding carousel of the AlertaMujer mobile app for Android. 7 swipeable slides on a soft lavender background `#f6f1fb` with large blurred decorative circles in pink and purple. Each slide has a flat vector illustration at the top and, below it, a white card with `border-radius 22px`, a 4px accent bar at the top in `#7c3aed` and `box-shadow 0 8px 24px rgba(76,29,149,0.12)`. Inside the card: a two-line centered title in `1.5rem 700` where the second line is `#7c3aed`, an uppercase section label in `0.68rem 700 #7c3aed` with a hairline divider, and a list of feature rows, each with a rounded emoji tile on a pale accent background, a bold title in `1rem 700`, a one-line description in `0.85rem #6b7280` and an optional pill tag on the right ("Discreta", "Urgente", "Tiempo real", "Familia"). One slide is interactive: it shows a "TU SELECCIÓN" label with two list rows (Departamento / Municipio) showing the picked values right-aligned, a full-width purple gradient pill button "💾 Guardar Ubicación" and a small "Datos protegidos" footnote with a shield check icon. At the bottom of every slide, a 7-dot indicator where the active dot is elongated and `#7c3aed`. Complies with WCAG AA.

---