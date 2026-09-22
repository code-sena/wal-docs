# Layout — Admin Dashboard

**Applies to:** All screens under `/admin/*`
**Access:** ADMIN (authGuard + adminGuard)
**Angular Component:** `AdminLayout`

## Prompt used (Google Stitch)

Design the base layout of the AlertaMujer Web Admin Panel in Angular for administrators. Web platform, 1440px desktop width. The layout has three fixed zones: left admin sidebar, top bar, and main content area.

**Left sidebar (~240px desktop, collapsible on tablet):**
Background --bg-card (#ffffff light / #1e1833 dark), border-right 1.5px --border (#c4b5fd / #4c3a80).
Header: logo (logo.png 40×40px) + "ALERTA MUJER" text in 0.62rem/700 + "Admin" badge in --purple-pale/--purple-deep, font-size 0.7rem.
List of navigation items with Tabler Icons (ti-*), each item: icon + label, padding 12px 16px, border-radius --dash-radius-sm 6px, active state with --purple-pale background and --purple-deep text color, hover with --bg-card-hover background.
Admin items: Dashboard (ti-layout-dashboard), Users (ti-users), Alerts (ti-bell-ringing), Zones (ti-map-pin), Reports (ti-file-analytics), Emergencies (ti-ambulance), Evidence (ti-video), Moderators (ti-shield-check), Devices (ti-device-mobile).
On tablet (760–1024px) the sidebar collapses to 64px, icons only. On mobile (<760px) the sidebar hides behind an overlay and is accessed via the hamburger button.

**Top bar (64px height):**
--bg-card background, 1.5px --border border-bottom, sticky top-0 position.
Left side: hamburger button for the sidebar.
Center: breadcrumb of the current section (e.g. "Alerts > History").
Right side: "ADMIN" badge (--purple-pale/--purple-deep), 36px circular avatar with initials, theme switch, notifications icon with badge.

**Main content area:**
Occupies the remaining space.
Inner padding: 24px (p-6).
Background: --bg-base (#f5f4ff light / #14101f dark).
Independent vertical scroll.

Typography: Nunito. Dark mode tokens [data-theme="dark"]: --bg-base #14101f, --bg-card #1e1833, --border #4c3a80, --text-primary #ede9fe.

## Structure

1. **Left sidebar** (fixed, 100vh)
   - Header: logo + "ALERTA MUJER" + "Admin" badge
   - Navigation list: 9 items with Tabler icon + label
   - Active state: `--purple-pale` background, `--purple-deep` text
2. **Top bar** (sticky, 64px)
   - Hamburger button
   - Breadcrumb of current section
   - "ADMIN" badge + avatar + theme switch + bell
3. **Content area** (`main`)
   - 24px padding, `--bg-base` background, own scroll
4. **Overlay** (mobile): `rgba(0,0,0,0.5)` background when the sidebar opens

## Components used

- Tabler Icons (`ti-*`)
- "ADMIN" badge (`--purple-pale` / `--purple-deep`)
- Circular avatar with initials
- Navigation breadcrumb
- Notification counter badge
- Theme toggle (`ThemeService`)

## States

- **Expanded sidebar** (desktop): 240px with labels and Admin badge
- **Collapsed sidebar** (tablet): 64px, icons only
- **Sidebar as drawer** (mobile): slides in from the left with overlay
- **Active item**: `--purple-pale` background, `--purple-deep` text
- **Dark theme**: `[data-theme="dark"]` tokens across all zones

## Constraints

- **Desktop > 1024px:** Fixed 240px sidebar + top bar + content
- **Tablet 760–1024px:** Sidebar collapsed to 64px (icons only)
- **Mobile < 760px:** Sidebar as a drawer with overlay, hamburger button in the top bar
- **WCAG AA:** `<nav aria-label="Admin navigation">` on the sidebar; `role="banner"` on the top bar; `aria-current="page"` on the active item; drawer with `role="dialog"` and focus trap on mobile
- **Dark mode:** `[data-theme="dark"]` with full dark tokens

## Related files

| File | Status | Description |
|---|---|---|
| layout.png | ⬜ Pending | Image exported from Google Stitch |
| layout.html | ⬜ Pending | Reference HTML implementation |