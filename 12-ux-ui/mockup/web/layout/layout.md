# Layout — Shared Skeleton (Sidebar + Header)

> Shared visual skeleton used by all authenticated screens of the web platform.
> Defines the base structure: navigation sidebar, top header, and main content area.

---

## Prompt Used

> *(Paste the prompt sent to Google Stitch or another design tool here)*

---

## Design Notes

### Sidebar

- Width: ~240 px (collapsible to 64 px in compact mode)
- Main navigation items: Dashboard, Alerts, Contacts, Zones, Resources, Devices, Settings
- Bottom section: user profile + logout button
- Background color: according to `design-system.md` (main palette)

### Header

- Height: ~64 px
- Content: active section title, notifications (icon + badge), user avatar
- Position: fixed at the top of the content area

### Content Area

- Occupies the remaining space after the sidebar and header
- Internal padding: 24 px
- Independent vertical scrolling from the sidebar

### Responsive

- At resolutions below 1024 px, the sidebar collapses automatically
- The header displays a hamburger button to expand the sidebar

---

## Related Files

| File | Status | Description |
|------|--------|-------------|
| `layout.png` | ⬜ Pending | Exported design image |
| `layout.html` | ⬜ Pending | Reference HTML implementation |

---

## Correlations

- Design system (colors, typography, spacing) → `12-ux-ui/design-system.md`
- Navigation map → `12-ux-ui/navigation-map.md`