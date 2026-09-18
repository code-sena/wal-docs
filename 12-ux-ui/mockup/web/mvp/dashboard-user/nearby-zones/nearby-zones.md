# Nearby Zones

**Route:** `/dashboard/ubicacion/nearby-zones` (proposed — see ⚠️ below)
**Access:** USER
**Implementation Status:** ⚠️ Content definition pending — this screen does NOT exist in `navigation-map.md` or in the current web code. The functionality to view zones exists in Home (`/dashboard/home`) through the map using `RiskZonesService.getZonas()`, but not as a dedicated screen. Decision pending before generating it in Stitch.

## Base Style — Design Tokens

```css
/* Page background */
background: var(--bg-base); /* #f5f4ff */

/* Cards */
background: var(--bg-card);
border: 1.5px solid var(--border);   /* #c4b5fd */
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);

/* High-risk zone */
fill: #ef4444; stroke: #dc2626; opacity: 0.25;
background: var(--red-pale); color: var(--red);

/* Medium-risk zone */
fill: #f59e0b; stroke: var(--amber); opacity: 0.25;
background: var(--amber-pale); color: var(--amber);

/* Low-risk zone */
fill: #22c55e; stroke: var(--green); opacity: 0.25;
background: var(--green-pale); color: var(--green);

/* User's current position marker */
color: var(--purple-deep); /* #7c3aed */

/* Dark mode — map filter */
filter: invert(1) hue-rotate(180deg) brightness(0.85) contrast(0.9) saturate(0.6);

/* Page title */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Zone name */
font-size: 1rem; font-weight: 700; color: var(--text-primary);

/* Alert count */
font-size: 0.875rem; color: var(--text-muted);

/* Active level filter chips */
background: var(--purple-pale); border: 1.5px solid var(--purple-deep);

/* Zone list grid */
display: flex; flex-direction: column; gap: 8px; overflow-y: auto;
```

## Objective

Show the user the risk zones near her current location on an interactive map, with information about danger level and alert count, so she can make informed safety decisions.

## Structure (top to bottom)

1. DashboardLayout: Sidebar + Topbar + BottomNav (mobile)
2. Title: "🗺️ Nearby Zones"
3. **Split layout (desktop):** Map on the left | Right panel (360px)
4. **Leaflet Map Card:**

   * Color-coded circles by risk level (High red, Medium amber, Low green) with 0.25 opacity
   * User's current position marker in purple
   * Legend: ● High ● Medium ● Low
   * Popup when clicking a zone: name + level + N alerts
5. **Right Panel — Zones:**

   * Input 🔍 "Search zone..."
   * Level filter chips: All ● / High / Medium / Low
   * Zone card list: level badge + name + "N alerts" + distance
   * Click on a card → map `flyTo` to that zone

## Components Used

* `app-card` — map and zones panel
* `app-chip` with `active: boolean` — level filters
* `app-spinner` size `lg` — initial zone loading
* Leaflet Map with Google Maps tiles — risk circles

## States

* **loading:** Spinner over map + skeleton in list
* **ready:** Map with circles + zone list
* **searching:** List filters by name with debounce
* **level-filtered:** List filters by risk level
* **no-nearby-zones:** "There are no registered risk zones near your location"
* **error:** Red banner + retry

## Restrictions

* **Mobile < 760px:** Stacked layout (map on top, list below); map 240px; sidebar → BottomNav
* **Tablet 760–1024px:** Stacked layout; panel with scrolling; map 320px
* **Desktop > 1024px:** Split layout `grid-template-columns: 1fr 360px`; full-height map
* **WCAG AA:** Map with `aria-label`; circles with `role="button"` and `aria-label`; zone cards with the risk level written as text (not color alone); WCAG AA contrast on badges
* **⚠️ Route discrepancy:** `navigation-map.md` does not define this route — decision pending

## Prompt Ready for Google Stitch

> Design the Nearby Zones screen for AlertaMujer Web in Angular for an authenticated user. Web platform, DashboardLayout with sidebar and topbar. Background `#f5f4ff`. The screen has the title "🗺️ Nearby Zones" in `2rem 700 #1e1a3c` and a split layout on desktop: map on the left (flexible width) and a zones panel on the right (fixed 360px), both using white cards with `border: 1.5px solid #c4b5fd` and `border-radius 12px`. The Leaflet map displays risk zone circles with colors by level (High: `fill #ef4444 stroke #dc2626 opacity 0.25`, Medium: `fill #f59e0b stroke #d97706 opacity 0.25`, Low: `fill #22c55e stroke #16a34a opacity 0.25`), the user's current position marker in `#7c3aed`, a color legend, and a popup for each zone showing its name + level + alert count. The right panel has a 🔍 search input, filter chips (All, High, Medium, Low) with the active state using `#ede9fe` / `#7c3aed`, and a list of zone cards, each with a colored level badge, name in `1rem 700`, alert count, and distance. When a card is clicked, the map flies to that zone. On mobile and tablet, the layout stacks vertically. Dark mode applies the inverted filter to the map. The design must comply with WCAG AA accessibility guidelines.
