# Register
 
**Route:** `/auth/register`
**Access:** PUBLIC
**Implementation status:** Mocked with the full structure; form not connected to the real AuthService — frontend validations implemented, POST /auth/register pending integration.
 
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
 
/* Title typography */
font-size: 2rem; font-weight: 700; color: var(--color-text-dark); /* #2d1457 */
 
/* Subtitle typography */
font-size: 0.875rem; font-weight: 400; color: var(--text-muted); /* #6b7280 */
 
/* Input labels */
font-size: 0.9rem; font-weight: 400;
 
/* "Already have an account?" link */
font-size: 0.875rem; color: var(--color-purple); /* #c900ae */
 
/* Button */
font-size: 0.9rem; font-weight: 600; letter-spacing: 0.5px; color: #ffffff;
 
/* Card padding */
padding: 40px 36px; max-width: 480px; width: 100%;
 
/* Gap between fields */
gap: 14px; /* gap-3.5 */
 
/* Decorative orbs */
background: var(--gradient-bg); filter: blur(60px); opacity: 0.5;
border-radius: 50%;
```
 
## Objective
Allow a new user to create her account on AlertaMujer Web by entering her personal data and accepting the terms, in order to access the safety dashboard.
 
## Structure (top to bottom)
1. Decorative purple orb (top-left corner)
2. Decorative pink orb (bottom-right corner)
3. Centered card (max-width 480px) with:
   - Brand badge: 🔔 icon + "ALERTA MUJER" + tagline
   - Title: "Create account"
   - Subtitle: "Fill in your details to get started"
   - 2-column grid: Name input + Surname input
   - 2-column grid: Age input + Phone input
   - Email address input (full width)
   - Password input + 👁 visibility toggle
   - Confirm password input + 👁 visibility toggle
   - Password strength bar (4 levels: Weak / Fair / Good / Strong)
   - Checkbox: "I accept the terms and conditions"
   - Primary button: "🛡️ CREATE ACCOUNT"
   - Link: "Already have an account? Sign in" → `/auth/login`
## Components used
- `app-button` variant `primary`, size `lg` — "Create account" CTA
- `app-spinner` size `sm`, inline — loading state inside the button
- Brand badge (logo + name) — same as Login
- Native `<input>` fields with prefix icons
- Strength bar `<div>` progressbar (4 segments)
- Native `<input type="checkbox">` with a `<label>`
## States
- **default:** Empty form, button disabled (opacity 0.6)
- **in progress:** Partially filled fields, validations active on blur
- **valid and complete:** All fields valid + checkbox checked → button enabled
- **submitting:** Button with `app-spinner`, disabled
- **inline error:** Red border on the invalid field + message below
- **server error:** Red banner "That email is already registered" above the button
- **success:** Redirects to `/dashboard/home` with the token saved in localStorage
## Constraints
- **Mobile < 760px:** Card at 90% width; the 2-column grid collapses to 1 column; padding reduced to 24px; orbs hidden
- **Tablet 760–1024px:** Centered card; 2-column grid kept; reduced orbs
- **Desktop > 1024px:** Centered card, max 480px; orbs visible on the sides
- **WCAG AA:** Minimum contrast 4.5:1; `<label>` elements associated with each input; errors with `role="alert"`; autocomplete: `given-name`, `family-name`, `email`, `new-password`
- **Dark mode:** Card with `rgba(30,24,51,0.95)`; dark tokens active
## Prompt ready for Google Stitch
> Design the registration screen of AlertaMujer Web in Angular. Web platform, centered page layout with no navbar (Auth layout), background with the linear gradient `linear-gradient(135deg, #db079c 0%, #af0763 35%, #9a07a7 70%, #430261 100%)` and two blurred decorative orbs on the sides. In the center, a white card `rgba(255,255,255,0.95)` with `border-radius 28px` and `box-shadow 0 24px 60px rgba(78,16,177,0.18)`, padding of 40px 36px, max width 480px. The card contains: a brand badge with the 🔔 icon and the text ALERTA MUJER, the title "Create account" in `2rem 700 #2d1457`, the subtitle "Fill in your details to get started" in `0.875rem #6b7280`; a form with a 2-column grid for Name and Surname (inputs with background `rgba(245,233,233,0.9)` and `border-radius 14px`), a 2-column grid for Age and Phone, a full-width Email address field, Password and Confirm password fields each with a visibility toggle (👁 icon), a 4-level strength bar (Weak red `#dc2626` → Fair amber `#d97706` → Good light green → Strong green `#16a34a`); a checkbox "I accept the terms and conditions"; a primary button "🛡️ CREATE ACCOUNT" with the gradient `linear-gradient(90deg, #650dc9, #54048a, #5c048f)`, `border-radius 50px` and shadow `0 8px 24px rgba(233,30,140,0.38)`; a link "Already have an account? Sign in" in `#c900ae`. On mobile the 2-column grid collapses to 1. Complies with WCAG AA.