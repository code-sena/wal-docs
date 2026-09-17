# Layout — User Dashboard

**Applies to:** All screens under `/dashboard/*`
**Access:** USER (authGuard + userGuard)
**Angular Component:** `DashboardLayout`

## Prompt used (Google Stitch)

Design the base layout of the AlertaMujer Web Dashboard in Angular for an authenticated user (operator role). Web platform, 1440px desktop width. The layout has three fixed zones: left sidebar, top bar, and main content area.

**Left sidebar (~240px desktop, collapsible on tablet/mobile):**
Background --bg-card (#ffffff light / #1e1833 dark), border-right 1.5px --border (#c4b5fd / #4c3a80).
Sidebar header: logo (logo.png 40×40px) + "ALERTA MUJER" text in 0.62rem/700/0.1em letter-spacing.
List of navigation items with Tabler Icons (ti-*), each item: icon + label, padding 12px 16px, border-radius --dash-radius-sm 6px, active state with --purple-pale background and --purple-deep text color, hover with --bg-card-hover background.
Items: Dashboard (ti-home), Alerts (ti-bell), Contacts (ti-users), Zones (ti-map), Resources / Assistance (ti-lifebuoy), Device (ti-device-mobile), Settings (ti-settings).
On mobile (<760px) the sidebar collapses into a BottomNav with 5 main items in a fixed bottom bar (64px height, --bg-card background, --border border-top).

**Top bar (64px height):**
--bg-card background, 1.5px --border border-bottom, sticky top-0 position, elevated z-index.
Left side: hamburger button (ti-menu-2) to collapse/expand the sidebar on tablet.
Center/right: greeting "Hello, [Name]", 36px circular avatar with initials (--purple-pale background, --purple-deep text), notifications icon (ti-bell) with counter badge in --red, light/dark theme switch (ti-sun / ti-moon).

**Main content area:**
Occupies the remaining space (width: calc(100% - 240px) on desktop).
Inner padding: 24px (p-6).
Background: --bg-base (#f5f4ff light / #14101f dark).
Vertical scroll independent from the sidebar.

Base typography: Nunito, sans-serif. Dark mode color tokens [data-theme="dark"]: --bg-base #14101f, --bg-card #1e1833, --border #4c3a80, --text-primary #ede9fe, --text-muted #a8a0c4.

## Structure

1. **Left sidebar** (fixed or sticky position, 100vh height)
   - Header: logo + brand name
   - Navigation list: 7 items with Tabler icon + label
   - Active state: `--purple-pale` background, `--purple-deep` text
   - Hover state: `--bg-card-hover` background
2. **Top bar** (sticky, 64px tall)
   - Hamburger button (tablet/mobile)
   - Greeting + user avatar
   - Notification bell with badge
   - Light/dark theme switch
3. **Content area** (`main`)
   - 24px padding
   - `--bg-base` background
   - Independent scroll
4. **BottomNav** (mobile only, < 760px)
   - 5 items: Dashboard, Alerts, Contacts, Zones, More

## Components used

- Tabler Icons (`ti-*`)
- Circular avatar with initials
- Notification counter badge (`--red`)
- Theme toggle switch (`ThemeService`)
- `app-chip` or active-state styling on nav items

## States

- **Expanded sidebar** (desktop): 240px visible with labels
- **Collapsed sidebar** (tablet): 64px, icons only, no labels
- **BottomNav** (mobile < 760px): fixed bottom bar, 5 items
- **Active item**: `--purple-pale` background, `--purple-deep` text
- **Dark theme**: `[data-theme="dark"]` tokens across all zones

## Constraints

- **Desktop > 1024px:** Expanded 240px sidebar + top bar + content
- **Tablet 760–1024px:** Sidebar collapsed to 64px (icons only) + top bar
- **Mobile < 760px:** Sidebar hidden, fixed BottomNav at the bottom
- **WCAG AA:** `<nav aria-label="Main navigation">` on the sidebar; items with `aria-current="page"` when active; bell with `aria-label="X unread notifications"`; skip link to content `#main-content`
- **Dark mode:** `[data-theme="dark"]` with `--bg-base #14101f`, `--bg-card #1e1833`, `--border #4c3a80`

## Related files

| File | Status | Description |
|---|---|---|
| layout.png | ⬜ Pending | Image exported from Google Stitch |
| layout.html | ⬜ Pending | Reference HTML implementation |