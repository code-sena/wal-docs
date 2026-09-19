# Frequent Places

**Route:** `/dashboard/ubicacion/frequent-places` (proposed — see ⚠️ below)
**Access:** USER
**Implementation Status:** ⚠️ Content definition pending — this screen does NOT exist in `navigation-map.md` or in the current web code. The proposed route `/dashboard/ubicacion/frequent-places` is not registered. Pending decision before generating in Stitch.

## Base Style — Design Tokens

```css
/* Page background */
background: var(--bg-base); /* #f5f4ff */

/* Cards */
background: var(--bg-card);
border: 1.5px solid var(--border);   /* #c4b5fd */
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);

/* Frequent place badge */
background: var(--purple-pale); color: var(--purple-deep); /* #ede9fe / #7c3aed */

/* Safe place badge */
background: var(--green-pale); color: var(--green); /* #dcfce7 / #16a34a */

/* Risk place badge */
background: var(--red-pale); color: var(--red); /* #fee2e2 / #dc2626 */

/* Map markers */
color: var(--purple-deep); /* #7c3aed */

/* Dark mode — map filter */
filter: invert(1) hue-rotate(180deg) brightness(0.85) contrast(0.9) saturate(0.6);

/* Page title */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Place name */
font-size: 1rem; font-weight: 600; color: var(--text-primary);

/* Place details */
font-size: 0.875rem; color: var(--text-muted);

/* Places grid */
display: grid; grid-template-columns: repeat(2, 1fr); gap: 16px; padding: 24px;
```

## Objective

Allow the user to save, view, and manage their frequent places (home, work, etc.) on a map for quick reference and security context when alerts are activated.

## Structure (top to bottom)

1. DashboardLayout: Sidebar + Topbar + BottomNav (mobile)
2. Title: "📍 Frequent Places"
3. **"Map" Card:**

   * Leaflet map with markers for each saved place
   * Popup for each marker: name + type + address
4. **"My Places (N places)" Card:**

   * "➕ ADD PLACE" button
   * 2-column grid of place cards: icon + name + address + type (Home/Work/Other) + actions [✏️][🗑️]
5. **Add/Edit Place Modal:**

   * Name input
   * Type selector (Home / Work / Other)
   * Address input + "Use my current location" option
   * Cancel / Save buttons
6. **Delete Confirmation Modal (danger)**

## Components Used

* `app-card` — map and places grid
* `app-button` variant `primary`, size `sm` — "➕ Add Place"
* `app-modal` size `md`, default — add/edit place
* `app-modal` size `sm`, danger — delete confirmation
* `app-spinner` size `md` — loading state
* Leaflet Map — place visualization

## States

* **loading:** Spinner over map + grid skeleton
* **ready:** Map with markers + card grid
* **no-places:** "You don't have any saved frequent places" + add button
* **add-modal:** Modal with empty form
* **edit-modal:** Modal with pre-filled data
* **saving:** Button with spinner
* **error:** Red banner + retry

## Restrictions

* **Mobile < 760px:** 1-column grid; map 220px; sidebar → BottomNav
* **Tablet 760–1024px:** 2-column grid; map 280px
* **Desktop > 1024px:** Map + grid side by side or stacked
* **WCAG AA:** Map markers with `aria-label`; modal with `role="dialog"`; WCAG AA contrast
* **⚠️ Route discrepancy:** `navigation-map.md` does not define this route — pending decision

## Prompt Ready for Google Stitch

> Design the Frequent Places screen for AlertaMujer Web in Angular. Web platform, DashboardLayout with sidebar and topbar. Background `#f5f4ff`. The screen has the title "📍 Frequent Places" in `2rem 700 #1e1a3c` and two white cards with `border: 1.5px solid #c4b5fd` and `border-radius 12px`: (1) a Map Card using Leaflet and displaying `#7c3aed` markers for each saved frequent place, with a popup showing the name + type + address when clicked; (2) a "My Places" Card with an "➕ ADD PLACE" button and a 2-column card grid. Each card contains a type icon (🏠 Home / 🏢 Work / 📍 Other), place name in `1rem 600`, address in `0.875rem #6b7280`, a type badge with `#ede9fe` background and `#7c3aed` text, and edit ✏️ and delete 🗑️ buttons using `#7c3aed` and `#dc2626`. Clicking ➕ or ✏️ opens a modal with a name field, type selector, and address field with a "Use my current location" option. Clicking 🗑️ opens a danger confirmation modal. The empty state displays "You don't have any saved frequent places" with an add button. On mobile, the grid collapses to 1 column. The design must comply with WCAG AA accessibility guidelines.
