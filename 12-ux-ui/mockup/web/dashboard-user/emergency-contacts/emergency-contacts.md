# Emergency Contacts

**Route:** `/dashboard/emergency-contacts` (proposed — see ⚠️ below)
**Access:** USER
**Implementation Status:** ⚠️ Proposed — the `/dashboard/emergency-contacts` route does NOT exist in `navigation-map.md` for the web platform (the functionality currently exists only in the mobile app: `AgregarContacto` / `Contactos`). Pending decision before generating in Stitch.

## Base Style — Design Tokens

```css
/* Page background */
background: var(--bg-base); /* #f5f4ff */

/* Cards */
background: var(--bg-card);
border: 1.5px solid var(--border);   /* #c4b5fd */
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);

/* Informational banner */
background: var(--purple-pale);   /* #ede9fe */
border-left: 3px solid var(--purple-deep); /* #7c3aed */
color: var(--text-primary);

/* Verified badge ✅ */
background: var(--green-pale); color: var(--green); /* #dcfce7 / #16a34a */

/* Pending badge ⏳ */
background: var(--amber-pale); color: var(--amber); /* #fef3c7 / #d97706 */

/* Main icon ⭐ */
color: var(--amber); /* #d97706 */

/* Edit button in row */
color: var(--purple-deep); /* #7c3aed */

/* Delete button in row */
color: var(--red); /* #dc2626 */

/* Reorder buttons ↑↓ */
color: var(--text-muted); /* #6b7280 */

/* Danger modal */
/* red header, red confirm button */

/* Page title */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Counter "3 / max. 5" */
font-size: 0.875rem; color: var(--text-muted);

/* Name in table */
font-size: 0.9rem; font-weight: 600; color: var(--text-primary);

/* Relationship in table */
font-size: 0.8rem; color: var(--text-muted);

/* Phone */
font-family: monospace; font-size: 0.875rem; color: var(--text-primary);

/* Page padding */
padding: 24px; gap: 16px;

/* Table rows */
padding: 12px 16px; gap: 8px;
```

## Objective

Allow the user to manage up to 5 emergency contacts ordered by priority. These contacts will be automatically notified when an SOS alert is activated.

## Structure (top to bottom)

1. DashboardLayout: Sidebar + Topbar + BottomNav (mobile)
2. Title: "👥 Emergency Contacts"
3. Informational banner: "ℹ️ Contacts are automatically notified when an SOS alert is activated, in priority order"
4. Card "My Contacts (N / max. 5)" with "➕ ADD" button:

   * Table: No. | Name / Relationship | Phone | Status | Actions
   * Order 1 with ⭐ = primary contact
   * Badge ✅ Verified / ⏳ Pending in the Status column
   * Row actions: [✏️ Edit] [🗑️ Delete] [↑ Move Up] [↓ Move Down]
5. **Add/Edit Contact Modal:**

   * Full Name | Relationship (select) | Phone | Email (the notice and the tracking link arrive by email)
   * Checkbox "Mark as primary contact"
   * Buttons Cancel / 💾 Save
6. **Delete Confirmation Modal (danger):**

   * "Delete [Name] ([Relationship])?"
   * Warning: "This contact will no longer receive notifications"

## Components Used

* `app-card` — informational banner + table
* `app-button` variant `primary`, size `sm` — "➕ Add"
* `app-button` variant `primary`, size `md` — "Save contact" in modal
* `app-button` variant `secondary`, size `md` — "Cancel" in modal
* `app-button` variant `danger`, size `md` — "Delete" in danger modal
* `app-modal` size `md`, default — add/edit
* `app-modal` size `sm`, danger — delete confirmation
* `app-chip` with `dotColor` — verified/pending badge
* `app-spinner` size `sm`, inline — saving

## States

* **loading:** Row skeletons displayed in the table
* **ready:** Table with contacts ordered by priority
* **limit-reached:** "Add" button disabled with tooltip "Limit of 5 contacts"
* **no-contacts:** Empty state with 👥 icon + "Add first contact" button
* **add-modal:** Modal with empty form
* **edit-modal:** Pre-filled modal
* **saving:** Button displays spinner
* **reordering:** Rows are animated when changing position
* **error:** Descriptive red toast

## Restrictions

* **Mobile < 760px:** Contact cards instead of table; buttons inside a contextual menu; full-screen modal; sidebar → BottomNav
* **Tablet 760–1024px:** Table without email column; ↑↓ buttons visible
* **Desktop > 1024px:** Full table with all columns
* **WCAG AA:** Table with `<caption>`; reorder buttons with descriptive `aria-label`; delete button with `aria-label`; modal with `role="dialog"`, `aria-modal="true"`, focus trap; primary contact checkbox with `aria-describedby`; counter with `aria-live="polite"`
* **⚠️ Route discrepancy:** `navigation-map.md` does not define this route for the web platform — pending decision

## Prompt Ready for Google Stitch

> Design the Emergency Contacts screen for AlertaMujer Web in Angular. Web platform, DashboardLayout with sidebar and topbar. Background `#f5f4ff`. The screen has the title "👥 Emergency Contacts" in `2rem 700 #1e1a3c`, an informational banner with background `#ede9fe` and `3px solid #7c3aed` left border explaining that contacts are notified in priority order, and a white card with `border: 1.5px solid #c4b5fd` and `border-radius 12px` displaying the header "My Contacts (3 / max. 5)" with an "➕ ADD" button. The table has columns No. | Name/Relationship | Phone | Status | Actions; contact order 1 has ⭐ in `#d97706`; the Status column displays a ✅ badge (background `#dcfce7`, text `#16a34a`) or ⏳ badge (background `#fef3c7`, text `#d97706`); actions are ✏️ buttons in `#7c3aed`, 🗑️ buttons in `#dc2626`, and ↑ and ↓ buttons in `#6b7280`. Clicking ➕ or ✏️ opens a modal with a Full Name field, Relationship select (Mom/Dad/Sibling/Partner/Friend/Other), Phone with international format, Email (the notice and the tracking link arrive by email), and a checkbox to mark the contact as primary. Clicking 🗑️ opens a danger confirmation modal. On mobile, display contact cards instead of a table. The design must comply with WCAG AA accessibility guidelines.