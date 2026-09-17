# User Home

**Route:** `/dashboard/home`
**Access:** USER
**Implementation Status:** Implemented — `Home` component with lazy loading; consumes `AlertsService.getByUsuario()` and `RiskZonesService.getZonas()`; Leaflet map integrated.

## Base Style — Design Tokens

```css
/* Page background */
background: var(--bg-base); /* #f5f4ff */

/* Cards */
background: var(--bg-card);          /* #ffffff */
border: 1.5px solid var(--border);   /* #c4b5fd */
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);
/* 0 0 0 1.5px var(--border), 0 4px 16px rgba(124,58,237,0.10) */

/* Stat cards — large number */
font-size: 2rem; font-weight: 700; color: var(--text-primary); /* #1e1a3c */

/* Stat cards — label */
font-size: 0.875rem; color: var(--text-muted); /* #6b7280 */

/* SOS alert badge */
background: var(--red-pale); color: var(--red); /* #fee2e2 / #dc2626 */

/* Pending alert badge */
background: var(--amber-pale); color: var(--amber); /* #fef3c7 / #d97706 */

/* Handled alert badge */
background: var(--green-pale); color: var(--green); /* #dcfce7 / #16a34a */

/* High-risk zone */
fill: #ef4444; stroke: #dc2626; opacity: 0.25;

/* Medium-risk zone */
fill: #f59e0b; stroke: #d97706; opacity: 0.25;

/* Low-risk zone */
fill: #22c55e; stroke: #16a34a; opacity: 0.25;

/* Dark mode — map filter */
filter: invert(1) hue-rotate(180deg) brightness(0.85) contrast(0.9) saturate(0.6);

/* Page title */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Sidebar / topbar — background */
background: var(--bg-card); border-right: 1.5px solid var(--border);
```

## Objective

Provide the authenticated user with a security overview: device status, recent alerts, a map showing nearby risk zones, and quick access to the main features.

## Structure (top to bottom)

1. **DashboardLayout:** Left sidebar (links: Home, Location, Alerts, Evidence, Device) + Topbar (logo, notification bell 🔔, theme toggle, user avatar)
2. **Greeting:** "Hello, [Name] 👋" + current date
3. **Stat cards grid (4 cards):**

   * 🔔 Active alerts (number + red badge if > 0)
   * ✅ Alerts handled this month
   * 📍 Last known location (relative timestamp)
   * 📱 Device status (Connected/Disconnected badge)
4. **Map Card — Nearby Zones:**

   * Leaflet map with circles colored according to risk level (High/Medium/Low)
   * Color legend
   * Marker showing the user's current position
5. **Recent Alerts Card:**

   * List of the 5 latest alerts with type, status, and timestamp
   * "View all →" link → `/dashboard/alert-history`
6. **Quick Access Grid (4 buttons):**

   * 📍 My Location → `/dashboard/phone-location`
   * 🎬 Evidence → `/dashboard/evidence`
   * 👥 Contacts → `/dashboard/emergency-contacts`
   * 📱 Device → `/dashboard/device-status`

## Components Used

* `app-card` — stat cards, map card, recent alerts card
* `app-button` variant `secondary`, size `sm` — quick access
* `app-chip` with `dotColor` — alert status badges
* `app-spinner` size `md` — loading state
* Leaflet Map with Google Maps tiles — risk zones

## States

* **loading:** Skeleton in stat cards + spinner on map
* **ready:** Data loaded, map with risk circles, alerts visible
* **no-alerts:** Alerts card displays "You have no recent alerts" + 🔔 icon
* **device-disconnected:** Device stat card displays red badge + warning
* **error:** Red banner + retry button in affected cards

## Restrictions

* **Mobile < 760px:** Sidebar collapses to BottomNav; stat cards grid 2×2; map 200px high; quick access in 2×2
* **Tablet 760–1024px:** Sidebar visible in collapsed state (icons only); stat cards grid 2×2; map 280px
* **Desktop > 1024px:** Expanded sidebar; stat cards grid 4×1; map 320px; alerts and map displayed in a 2-column grid
* **WCAG AA:** Stat cards with `role="status"`; map with `aria-label`; `aria-live="polite"` on counters; badges with visible text
* **Dark mode:** Dark tokens active; map with inverted filter

## Prompt Ready for Google Stitch

> Design the Home screen for AlertaMujer Web in Angular for an authenticated user (operator role). Web platform, DashboardLayout with a left sidebar (links: Home, Location, Alerts, Evidence, Device) on a white background with `border-right: 1.5px solid #c4b5fd`, and a topbar with logo, notification bell 🔔, theme toggle, and avatar. Page background `#f5f4ff`. The screen contains: a greeting "Hello, [Name] 👋" with the date in `2rem 700 #1e1a3c`; a grid of 4 white stat cards with `border: 1.5px solid #c4b5fd` and `border-radius 12px` showing Active Alerts, Handled Alerts, Last Known Location, and Device Status with a large `2rem 700` number and semantic color badges; a Leaflet map card with risk-zone circles (High red `#ef4444 0.25`, Medium amber `#f59e0b 0.25`, Low green `#22c55e 0.25`) and a marker showing the user's current position; a recent alerts card with a list of 5 items (type + status badge + timestamp) and a "View all →" link; and a 2×2 quick-access grid for Location, Evidence, Contacts, and Device. On mobile, the sidebar collapses to BottomNav and the stat card grid becomes 2×2. The design must comply with WCAG AA accessibility guidelines.
