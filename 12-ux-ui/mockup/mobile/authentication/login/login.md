## Login
 
**Route:** `/auth/login`
**Access:** PUBLIC
**Implementation status:** Implemented — email/password form with frontend validation, Google sign-in entry point and links to password recovery and registration.
 
### Objective
Authenticate the user, store token + role locally, and open the Home (SOS) tab.
 
### Structure (top to bottom)
1. Purple gradient background with the brand lockup (lion logo + "ALERTA MUJER" + "SEGURIDAD PARA TODAS") at the top
2. White card (`border-radius 22px`, padding 24px) with:
   - Title: "Bienvenida a tu espacio de seguridad" (centered, two lines)
   - Email input with ✉️ prefix icon
   - Password input with 👁 visibility toggle
   - Right-aligned link "¿Olvidaste tu contraseña?"
   - Primary gradient pill button "Iniciar sesión"
3. White pill button "Continuar con Google" with the Google G mark
4. "¿No tienes cuenta?" caption
5. Secondary solid purple pill button "Regístrate"
6. Legal footnote: Terms of Service and Privacy Notice
### Form fields
| Field | Type | Required | Validation |
|-------|------|-----------|------------|
| Correo electrónico | `email` | ✅ | Email format |
| Contraseña | `password` | ✅ | Min. 8 characters |
 
### Components used
- Brand lockup (logo + wordmark + tagline)
- Rounded inputs with prefix icon and trailing visibility toggle
- Primary gradient pill button + inline spinner
- Google OAuth button
- Text links
### States
- **initial:** Empty form, "Iniciar sesión" disabled (opacity 0.6)
- **valid:** Both fields valid → button enabled
- **submitting:** Button shows an inline spinner and is disabled
- **credential error:** Red banner "Correo o contraseña incorrectos" above the button with `role="alert"`; inputs get a `#dc2626` border
- **server error:** "Error al iniciar sesión. Intenta de nuevo."
- **success:** Saves `token`, `role`, `userId`; navigates to `/home`
### Constraints
- **Small phones (<360dp):** Card padding reduced to 20px; logo scaled down
- **Landscape:** Whole screen scrolls; logo collapses
- **Accessibility:** Labels associated with every input; `autocomplete="email"` / `autocomplete="current-password"`; toggle with `aria-label="Mostrar/Ocultar contraseña"`; errors with `role="alert"`; 48dp minimum touch targets
- **Dark mode:** Card `#1c1c22`; inputs `#26262e`; accent preserved
### Prompt ready for Google Stitch
> Design the Login screen of the AlertaMujer mobile app for Android. Full-screen purple gradient background `linear-gradient(160deg,#c9a4ec,#8b2fc9)` with a dark purple bottom band. At the top, a circular gold-and-purple lion logo with the wordmark ALERTA MUJER and the tagline SEGURIDAD PARA TODAS. Below it, a white card with `border-radius 22px`, padding 24px and a soft shadow, containing: the centered title "Bienvenida a tu espacio de seguridad" in `1.25rem 700 #1e1a3c`; a "Correo electrónico" input with an envelope prefix icon, background `#f7effb`, `border 1.5px solid #e9d5ff` and `border-radius 14px`; a "Contraseña" input with an eye-off visibility toggle on the right; a right-aligned link "¿Olvidaste tu contraseña?" in `0.8rem #7c3aed`; and a full-width pill button "Iniciar sesión" with the gradient `linear-gradient(90deg,#a855f7,#7c3aed)` and `border-radius 50px`. Under the card, a white pill button "Continuar con Google" with the Google logo, the caption "¿No tienes cuenta?", a solid purple pill button "Regístrate" in `#6d28d9`, and a centered two-line legal footnote in `0.72rem` about the Terms of Service and Privacy Notice. Complies with WCAG AA.
 
---