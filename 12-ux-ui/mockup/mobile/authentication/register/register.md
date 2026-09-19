##  Register

**Route:** `/auth/register`
**Access:** PUBLIC
**Implementation status:** Entry point wired from Login ("Regístrate"); the form itself is not captured in the current screenshots — documented as the expected implementation, mirroring the web register form on the mobile shell.

### Objective
Let a new user create an account and land directly on the Home (SOS) tab.

### Structure (top to bottom)
1. Same gradient background and brand lockup as Login
2. White card with:
   - Title: "Crear cuenta" · Subtitle: "Completa tus datos para comenzar"
   - Nombre + Apellido (two columns)
   - Edad + Teléfono (two columns)
   - Correo electrónico (full width)
   - Contraseña + 👁 toggle
   - Confirmar contraseña + 👁 toggle
   - 4-level password strength bar (Débil / Regular / Buena / Fuerte)
   - Checkbox "Acepto los términos y condiciones"
   - Primary pill button "Crear cuenta"
3. Link: "¿Ya tienes cuenta? Inicia sesión" → `/auth/login`

### Form fields
| Field | Type | Required | Validation |
|-------|------|-----------|------------|
| Nombre | `text` | ✅ | Min. 2 characters |
| Apellido | `text` | ✅ | Min. 2 characters |
| Edad | `number` | ✅ | 14–99 |
| Teléfono | `tel` | ✅ | 10 digits (CO) |
| Correo electrónico | `email` | ✅ | Email format; unique |
| Contraseña | `password` | ✅ | Min. 8 chars, 1 uppercase, 1 number |
| Confirmar contraseña | `password` | ✅ | Must match |
| Términos | `checkbox` | ✅ | Must be checked |

### Components used
- Two-column input grid
- Strength bar (4 segments)
- Native checkbox + label with inline links
- Primary pill button + inline spinner

### States
- **default:** Empty form, button disabled
- **in progress:** Validation on blur, inline messages under each field
- **valid and complete:** All fields valid + checkbox checked → button enabled
- **submitting:** Button with spinner, disabled
- **server error:** Red banner "El correo ya está registrado"
- **success:** Account created, session stored, navigates to `/home`

### Constraints
- **Small phones (<360dp):** The two-column grid collapses to a single column
- **Keyboard open:** The card scrolls and keeps the focused field visible
- **Accessibility:** `autocomplete` `given-name`, `family-name`, `email`, `tel`, `new-password`; strength bar exposed as `role="progressbar"` with a text equivalent; errors with `role="alert"`
- **Dark mode:** Dark tokens active

### Prompt ready for Google Stitch
> Design the registration screen of the AlertaMujer mobile app for Android, matching the Login screen: purple gradient background, lion logo with the ALERTA MUJER wordmark, and a white card with `border-radius 22px` and padding 24px. The card contains the title "Crear cuenta" in `1.4rem 700 #1e1a3c`, the subtitle "Completa tus datos para comenzar" in `0.85rem #6b7280`, a two-column row for Nombre and Apellido, a two-column row for Edad and Teléfono, a full-width Correo electrónico field, Contraseña and Confirmar contraseña fields each with an eye visibility toggle, and a 4-segment password strength bar (Débil `#dc2626` → Regular `#d97706` → Buena `#84cc16` → Fuerte `#16a34a`) with the level name on the right. Inputs use background `#f7effb`, `border 1.5px solid #e9d5ff` and `border-radius 14px`. Below, a checkbox "Acepto los términos y condiciones" with the terms text as a purple link, and a full-width pill button "Crear cuenta" with the gradient `linear-gradient(90deg,#a855f7,#7c3aed)`. At the bottom, the link "¿Ya tienes cuenta? Inicia sesión" in `#c900ae`. On small screens the two-column rows collapse to one column. Complies with WCAG AA.
