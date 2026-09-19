# Admin Dashboard

**Route:** `/admin/dashboard`  
**Access:** ADMIN  
**Implementation Status:** Implemented — `AdminDashboardComponent` with lazy loading; consumes `AlertsService.getAll()`, `UsersService.getAll()`, and `RiskZonesService.getZonas()`; 30s polling for active alerts.

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

/* Stat card — large number */
font-size: 2rem; font-weight: 700; color: var(--text-primary); /* #1e1a3c */

/* Stat card — label */
font-size: 0.875rem; color: var(--text-muted); /* #6b7280 */

/* "Active Alerts" stat card — urgent accent */
color: var(--red); /* #dc2626 */

/* SOS alert badge — urgent */
background: var(--red-pale); color: var(--red);

/* "Pending" alert badge */
background: var(--amber-pale); color: var(--amber); /* #d97706 */

/* "Attended" alert badge */
background: var(--green-pale); color: var(--green); /* #16a34a */

/* Urgent row (SOS > 5 min unattended) */
background: var(--red-pale);
animation: pulse 2s infinite; /* opacity 1 ↔ 0.7 */

/* Row hover */
background: var(--bg-card-hover); /* #f0eeff */ cursor: pointer;

/* High Risk Zone */
fill: #ef4444; stroke: #dc2626; opacity: 0.25;

/* Medium Risk Zone */
fill: #f59e0b; stroke: #d97706; opacity: 0.25;

/* Low Risk Zone */
fill: #22c55e; stroke: #16a34a; opacity: 0.25;

/* Dark mode — map filter */
filter: invert(1) hue-rotate(180deg) brightness(0.85) contrast(0.9) saturate(0.6);

/* Stat cards grid */
display: grid; grid-template-columns: repeat(4, 1fr); gap: 16px;

/* Page title */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Page padding */
padding: 24px; gap: 24px;
```

## Objective

Provide the administrator with a centralized view of the system's real-time status: active alerts, user metrics, risk zones on the map, and quick access to all management sections.

## Structure (Top to Bottom)

1. **AdminLayout:** Left sidebar (Dashboard, Users, Alerts, Zones, Emergencies, Moderators, Devices) + Topbar (logo, notification bell 🔔, theme toggle, admin avatar)

2. **Title:** "📊 Administration Dashboard" + greeting + date

3. **4 Stat Cards Grid:**
   - 🔔 Active Alerts (number in red if > 0)
   - 👥 Total Users
   - 🗺️ Risk Zones
   - 📱 Connected Devices

4. **Recent Alerts Card (5-row paginated table):**
   - Columns: # | Type | User | Location | Time | Status
   - Unattended SOS rows older than 5 minutes use the `pulse` animation with `var(--red-pale)` background
   - "View all →" link → `/admin/alertas`

5. **Risk Zones Map Card:**
   - Leaflet map with colored circles (High/Medium/Low)
   - Real-time markers for active alerts
   - Color legend

6. **2×3 Quick Access Grid:**
   - 👥 Users · 🔔 Alerts · 🗺️ Zones · 🚨 Emergencies · 📋 Moderators · 📱 Devices

## Components Used

- `app-card` — stat cards, alerts table, map, quick access
- `app-chip` with `dotColor` — status badges in the table
- `app-button` variant `secondary`, size `sm` — quick access
- `app-spinner` size `md` — loading state
- Leaflet map with Google Maps tiles — real-time zones and alerts

## States

- **loading:** Skeletons in stat cards + spinner in map + table skeleton
- **ready:** All data loaded and displayed
- **urgent-alert:** Pulsing red row for SOS alerts older than 5 minutes that remain unattended
- **active-polling:** Alerts table refreshes every 30 seconds without reloading the page
- **no-active-alerts:** Stat card displays 0 in green + message "No pending alerts"
- **error:** Red banner per section + individual retry button

## Restrictions

- **Mobile < 760px:** Stat cards grid 2×2; simplified table; 240px map; quick access 2×3; sidebar → BottomNav
- **Tablet 760–1024px:** Stat cards grid 2×2; 280px map; collapsed sidebar (icons only)
- **Desktop > 1024px:** 4-column stat cards grid; expanded sidebar; map + table in a 2-column grid
- **WCAG AA:** Stat cards with `role="status"` and `aria-live="polite"`; table with `<caption>`; urgent rows with `aria-label="Urgent unattended alert"`; map with `aria-label`; text contrast over pale red background ≥ 4.5:1
- **Dark mode:** Dark tokens enabled; inverted map filter

## Prompt Ready for Google Stitch

> Design the AlertaMujer Web Administration Dashboard in Angular. Web platform, `AdminLayout` with a left sidebar (Dashboard, Users, Alerts, Zones, Emergencies, Moderators, Devices) with a white background and `border-right: 1.5px solid #c4b5fd` and a topbar with logo, notification bell, theme toggle, and avatar. Page background `#f5f4ff`. The screen contains: title "📊 Administration Dashboard" using `2rem 700 #1e1a3c`; a grid of 4 white stat cards with `border: 1.5px solid #c4b5fd` and `border-radius: 12px`, displaying Active Alerts (number in `#dc2626` if > 0), Total Users, Risk Zones, and Connected Devices, with a large number using `2rem 700` and a label using `0.875rem #6b7280`; a card with a table of 5 recent alerts with columns # | Type | User | Location | Time | Status, status badges (Pending amber `#d97706`, Attended green `#16a34a`), urgent SOS rows with `#fee2e2` background and `pulse 2s` animation, and a "View all →" link; a card with a Leaflet map displaying risk zone circles (High `#ef4444 0.25`, Medium `#f59e0b 0.25`, Low `#22c55e 0.25`) and markers for active alerts; a 2×3 grid of quick-access buttons for the 6 admin sections. On mobile, the stat card grid is 2×2 and the sidebar collapses. Comply with WCAG AA.