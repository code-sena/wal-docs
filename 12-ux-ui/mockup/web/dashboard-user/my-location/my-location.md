# My Location

**Route:** `/dashboard/phone-location`
**Access:** USER
**Implementation Status:** Implemented — `PhoneLocationComponent` with lazy loading; consumes `PhoneLocationService.getLocation()` and `PhoneLocationService.getHistory()`; Leaflet map with marker and accuracy circle.

## Base Style — Design Tokens

```css
/* Page background */
background: var(--bg-base); /* #f5f4ff */

/* Cards */
background: var(--bg-card);
border: 1.5px solid var(--border);   /* #c4b5fd */
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);

/* Strong GPS */
background: var(--green-pale); color: var(--green); /* #dcfce7 / #16a34a */

/* Moderate GPS */
background: var(--amber-pale); color: var(--amber); /* #fef3c7 / #d97706 */

/* Weak GPS */
background: var(--red-pale); color: var(--red); /* #fee2e2 / #dc2626 */

/* Battery > 50% */
color: var(--green);

/* Battery 20–50% */
color: var(--amber);

/* Battery < 20% */
color: var(--red);

/* Current location marker */
color: var(--purple-deep); /* #7c3aed */

/* Accuracy circle */
color: var(--purple-mid); opacity: 0.25; /* #a78bfa */

/* Selected table row */
background: var(--purple-pale); /* #ede9fe */
border-left: 3px solid var(--purple-deep);

/* Dark mode — map filter */
filter: invert(1) hue-rotate(180deg) brightness(0.85) contrast(0.9) saturate(0.6);

/* Page title */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Card headers */
font-size: 1.5rem; font-weight: 600;

/* Current location data */
font-size: 1rem; font-weight: 400; color: var(--text-primary);

/* Table header */
font-size: 0.875rem; font-weight: 600; color: var(--text-muted);

/* Table cells */
font-size: 0.875rem; color: var(--text-primary);

/* Map container */
height: 320px; border-radius: var(--dash-radius-card); overflow: hidden;

/* Page padding */
padding: 24px; gap: 24px;

/* Table rows */
padding: 12px 16px;
```

## Objective

Display the user's current device location on an interactive map with GPS, battery, and address information, along with a paginated history of previous location records.

## Structure (top to bottom)

1. DashboardLayout: Sidebar + Topbar + BottomNav (mobile)
2. Title: "📍 Phone Location"
3. **"Current Location" Card:**

   * 320px Leaflet map: purple marker at the current location + accuracy circle
   * Data below the map: 📌 Address | 🕐 Time | 📶 GPS (colored badge) | 🔋 Battery | 📐 Lat/Lng
   * "🔄 UPDATE LOCATION" button
4. **"🕐 Location History" Card:**

   * Header with `app-period-badge` dropdown
   * Table: # | Date/Time | Address | GPS | 🔋
   * Clickable row → map performs `flyTo` to the coordinates of that entry
   * Pagination: `< Previous` / `Next >`

## Components Used

* `app-card` — current location and history containers
* `app-button` variant `primary`, size `md` — "Update Location"
* `app-spinner` size `md`, inline — loading state
* `app-period-badge` with `conDropdown: true` — history period selector
* `app-chip` with `dotColor` according to GPS signal — signal chips in table
* Leaflet Map with Google Maps tiles

## States

* **loading:** `app-spinner` size `md` over the map; table skeleton
* **ready:** Map with marker; table with records
* **no-data:** "No locations recorded" + 📍 icon
* **error:** "Unable to retrieve location" + retry button
* **updating:** Spinner in button, subtle overlay over the map
* **row-selected:** Map `flyTo` + row highlighted in `#ede9fe`

## Restrictions

* **Mobile < 760px:** Map 200px; table with horizontal scrolling; sidebar → BottomNav
* **Tablet 760–1024px:** Map 240px; table without coordinates column
* **Desktop > 1024px:** Map 320px; full table with all columns
* **WCAG AA:** Map with `aria-label="Current device location map"`; table with `<caption>`; GPS cells with `aria-label="GPS Signal: Strong/Moderate/Weak"`; update button with `aria-label`; `aria-live="polite"` for loading states; WCAG AA contrast for status chips
* **Dark mode:** Inverted filter on map; dark tokens active

## Prompt Ready for Google Stitch

> Design the My Location screen for AlertaMujer Web in Angular. Web platform, DashboardLayout with sidebar and topbar. Background `#f5f4ff`. The screen has the title "📍 Phone Location" in `2rem 700 #1e1a3c` and two white cards with `border: 1.5px solid #c4b5fd` and `border-radius 12px`: (1) a "Current Location" card with a 320px-high Leaflet map, a `#7c3aed` marker at the current location, and an accuracy circle using `#a78bfa` with 0.25 opacity; below the map, a data row with 📌 Address as text, 🕐 Time timestamp, 📶 GPS badge (Strong: `#dcfce7` background / `#16a34a` text, Moderate: `#fef3c7` / `#d97706`, Weak: `#fee2e2` / `#dc2626`), 🔋 battery percentage with a small colored progress bar (green > 50%, amber 20–50%, red < 20%), and 📐 coordinates; an "🔄 UPDATE LOCATION" button with `linear-gradient(90deg,#650dc9,#54048a,#5c048f)` and `border-radius 50px`; (2) a "🕐 Location History" card with `app-period-badge` in the header, a table with columns # | Date/Time | Address | GPS | 🔋, clickable rows with hover `#f0eeff` and selection `#ede9fe` with a `3px solid #7c3aed` left border, and pagination. When a row is clicked, the map flies to those coordinates. On mobile, the map is 200px high and the table has horizontal scrolling. Dark mode applies the inverted filter to the map. The design must comply with WCAG AA accessibility guidelines.
