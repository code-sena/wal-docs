# Assistance

**Route:** `/dashboard/assistance` (proposed — see ⚠️ below)
**Access:** USER
**Implementation status:** ⚠️ Content needs to be defined — this screen does NOT exist in `navigation-map.md` for the web platform. In the mobile app, the `Assistance` screen exists as a bottom navigation tab, but its web equivalent is not registered. Pending decision before generating it in Stitch.

## Base Style — Design Tokens

```css
/* Page background */
background: var(--bg-base); /* #f5f4ff */

/* Cards */
background: var(--bg-card);
border: 1.5px solid var(--border);   /* #c4b5fd */
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);

/* Emergency Card — gradient */
background: linear-gradient(135deg, #c4a8d6, #7b1db2); /* asistenciaEmergenciaGradiente */
color: #ffffff;

/* Violence Card — gradient */
background: linear-gradient(135deg, rgb(240,87,87), rgb(132,0,255)); /* asistenciaViolenciaGradiente */
color: #ffffff;

/* Mental Health Card — gradient */
background: linear-gradient(135deg, rgb(142,189,255), rgb(255,0,191)); /* asistenciaMentalGradiente */
color: #ffffff;

/* Category icon */
/* Emergency */ background: purple;
/* Light bulb */ background: rgba(205,117,230,0.29); color: #1E1228;
/* Star */ background: purple;

/* Category card title */
font-size: 1.5rem; font-weight: 700; color: #ffffff;

/* Card description */
font-size: 0.875rem; color: rgba(255,255,255,0.9);

/* Button inside card */
background: rgba(255,255,255,0.2); border: 1px solid rgba(255,255,255,0.4);
color: #ffffff; border-radius: var(--radius-btn); /* 50px */

/* Page title */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Category grid */
display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; padding: 24px;
```

## Objective

Provide the user with quick access to assistance resources and support lines by category (Emergencies, Gender-Based Violence, Mental Health), including contact information and immediate communication options.

## Structure (Top to Bottom)

1. DashboardLayout: Sidebar + Topbar + BottomNav (mobile)
2. Title: "🆘 Assistance"
3. 3-column grid of category cards with design-system gradients:

   * **Emergencies 🚨** (lilac gradient `#c4a8d6 → #7b1db2`): Line 123, Police 112
   * **Gender-Based Violence 💜** (red→purple gradient `rgb(240,87,87) → rgb(132,0,255)`): Line 155, Prosecutor's Office
   * **Mental Health 💙** (light blue→magenta gradient `rgb(142,189,255) → rgb(255,0,191)`): Line 106, Well-being
4. Each card includes: icon + title + description + hotline number + "📞 CALL" / "💬 CHAT" button
5. "Additional Resources" card: list of links to institutions (ICBF, Family Police Stations, etc.)
6. "Frequently Asked Questions" card: accordion with personal safety FAQs

## Components Used

* `app-card` — container for additional resources and FAQ
* Gradient cards (native, not `app-card`) — assistance categories
* `app-button` variant `primary`, size `md` — call/chat buttons inside cards
* `app-spinner` size `md` — loading state (if resources are fetched)

## States

* **loading:** Category card skeleton
* **ready:** Category grid with visible information
* **error:** Red banner + retry button (if resources are loaded from API)

## Restrictions

* **Mobile < 760px:** 1-column grid; category cards full width; sidebar → BottomNav
* **Tablet 760–1024px:** 2-column grid + 1 card (or 3 compact columns)
* **Desktop > 1024px:** 3-column grid
* **WCAG AA:** Cards with white text contrast over gradients ≥ 4.5:1; call buttons with `aria-label`; FAQ accordion with `aria-expanded`
* **⚠️ Route discrepancy:** `navigation-map.md` does not define this route for the web platform — pending decision

## Prompt Ready for Google Stitch

> Design the AlertaMujer Web Assistance screen in Angular. Web platform, DashboardLayout with sidebar and topbar. Background `#f5f4ff`. The screen has the title "🆘 Assistance" in `2rem 700 #1e1a3c` and a 3-column grid of cards using the design-system gradients: (1) Emergency Card with `linear-gradient(135deg, #c4a8d6, #7b1db2)`, 🚨 icon inside a purple circle, title "Emergencies" in `1.5rem 700 #ffffff`, description in `0.875rem rgba(255,255,255,0.9)`, hotline number displayed prominently, and a "📞 CALL" button with `rgba(255,255,255,0.2)` background and `border-radius 50px`; (2) Gender-Based Violence Card with `linear-gradient(135deg, rgb(240,87,87), rgb(132,0,255))`, same structure; (3) Mental Health Card with `linear-gradient(135deg, rgb(142,189,255), rgb(255,0,191))`, same structure. Below, there is a white card with `border: 1.5px solid #c4b5fd` containing a list of additional resources (ICBF, Family Police Stations), and another card with an accordion-style FAQ. On mobile, the grid collapses to 1 column. Comply with WCAG AA with white text contrast over gradients.
