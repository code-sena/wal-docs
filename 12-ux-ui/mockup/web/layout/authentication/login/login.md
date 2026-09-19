# Login
 
**Route:** `/auth/login`
**Access:** PUBLIC
**Implementation status:** Implemented — `LoginComponent` with full frontend validations; consumes `AuthService.login()`; role-based redirect to `/dashboard/home` (operator) or `/admin/dashboard` (admin).
 
## Base style — design tokens
```css
/* Page background */
background: var(--gradient-bg);
/* linear-gradient(135deg, #db079c 0%, #af0763 35%, #9a07a7 70%, #430261 100%) */
 
/* Auth card */
background: rgba(255, 255, 255, 0.95);
border-radius: var(--radius-card); /* 28px */
box-shadow: var(--shadow-card);
/* 0 24px 60px rgba(78,16,177,0.18), 0 4px 16px rgba(91,33,182,0.10) */
 
/* Inputs */
background: rgba(245, 233, 233, 0.9);
border: 2px solid rgba(236, 72, 153, 0.15);
border-radius: var(--radius-input); /* 14px */
 
/* Input focus */
background: var(--bg-card);
border: 2px solid #db1879;
box-shadow: 0 0 0 4px rgba(236, 72, 153, 0.12);
 
/* Input error */
border-color: var(--red); /* #dc2626 */
 
/* Primary button */
background: var(--gradient-btn);
/* linear-gradient(90deg, #650dc9 0%, #54048a 50%, #5c048f 100%) */
border-radius: var(--radius-btn); /* 50px */
box-shadow: var(--shadow-btn);
/* 0 8px 24px rgba(233,30,140,0.38) */
 
/* Title */
font-size: 2rem; font-weight: 700; color: var(--color-text-dark); /* #2d1457 */
 
/* Subtitle */
font-size: 0.875rem; font-weight: 400; color: var(--text-muted); /* #6b7280 */
 
/* Input labels */
font-size: 0.9rem; font-weight: 400;
 
/* "Forgot?" link */
font-size: 0.875rem; color: var(--color-purple); /* #c900ae */
 
/* Button */
font-size: 0.9rem; font-weight: 600; letter-spacing: 0.5px; color: #ffffff;
 
/* Card */
padding: 40px 36px; max-width: 440px; width: 100%;
 
/* Field gap */
gap: 14px; /* gap-3.5 */
 
/* Form margin top */
margin-top: 28px; /* mt-7 */
 
/* Decorative orbs */
background: var(--gradient-bg); filter: blur(60px); opacity: 0.5; border-radius: 50%;
```
 
## Objective
Authenticate users (operator) and administrators, saving token + role in localStorage and redirecting to the corresponding dashboard based on role.
 
## Structure (top to bottom)
1. Decorative purple orb (top-left corner)
2. Decorative pink orb (bottom-right corner)
3. Centered card (max-width 440px) with:
   - Brand badge: 🔔 icon + "ALERTA MUJER" + "Safety for all women"
   - Title: "Sign in"
   - Subtitle: "Welcome back."
   - Email address input (with ✉️ icon)
   - Password input (with 🔒 icon) + 👁 visibility toggle
   - "Forgot your password?" link → `/auth/forgot-password`
   - Primary button: "🛡️ SIGN IN"
   - Link: "Don't have an account? Sign up" → `/auth/register`
## Components used
- `app-button` variant `primary`, size `lg` — "Sign in"
- `app-spinner` size `sm`, inline — loading state inside the button
- Brand badge — AlertaMujer logo + name
- Native `<input>` fields with prefix icons
## States
- **initial:** Empty form, button disabled (opacity 0.6)
- **complete:** Fields with valid format, button enabled
- **submitting:** Button with `app-spinner`, disabled
- **credential error:** Red banner "Incorrect email or password" above the button with `role="alert"`
- **server error:** "Error signing in. Please try again."
- **success:** Saves `token`, `role`, `userId` in localStorage; redirects by role
## Constraints
- **Mobile < 760px:** Card at 90% width; padding reduced to 24px; orbs hidden
- **Tablet 760–1024px:** Centered card; reduced orbs
- **Desktop > 1024px:** Centered card, max 440px; orbs visible on the sides
- **WCAG AA:** `<form aria-label="Sign-in form">`; associated `<label>` elements; `autocomplete="email"` and `autocomplete="current-password"`; visibility toggle with `aria-label="Show/Hide password"`; errors with `role="alert"`; visible focus `box-shadow: 0 0 0 3px rgba(157,111,240,0.2)`
- **Dark mode:** Card `rgba(30,24,51,0.95)`; dark tokens active
## Prompt ready for Google Stitch
> Design the Login screen of AlertaMujer Web in Angular. Web platform, centered page layout with no navbar (Auth layout), background with the gradient `linear-gradient(135deg,#db079c,#af0763,#9a07a7,#430261)` and two blurred decorative orbs on the sides. In the center, a white card `rgba(255,255,255,0.95)` with `border-radius 28px`, `box-shadow 0 24px 60px rgba(78,16,177,0.18)` and padding `40px 36px`, max width 440px. The card contains: a brand badge with the 🔔 icon and the text ALERTA MUJER with a tagline; the title "Sign in" in `2rem 700 #2d1457`; the subtitle "Welcome back." in `0.875rem #6b7280`; an Email input with a prefix ✉️ icon (background `rgba(245,233,233,0.9)`, border `2px solid rgba(236,72,153,0.15)`, `border-radius 14px`, on focus border `2px solid #db1879` and `box-shadow 0 0 0 4px rgba(236,72,153,0.12)`); a Password input with a prefix 🔒 icon and a 👁 visibility toggle; a "Forgot your password?" link in `#c900ae`; a primary button "🛡️ SIGN IN" with the gradient `linear-gradient(90deg,#650dc9,#54048a,#5c048f)`, `border-radius 50px` and shadow `0 8px 24px rgba(233,30,140,0.38)` (disabled if the form is invalid; with a spinner in the loading state); a "Don't have an account? Sign up" link. On mobile the card takes up 90% of the width and the orbs are hidden. Complies with WCAG AA.
 