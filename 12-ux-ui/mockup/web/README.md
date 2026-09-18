# Mockups — Web (Angular)

> Screens designed for the Woman Alert web platform.
> Includes the shared layout skeleton and MVP screens organized by dashboard type and module.

---

## Structure

```text
web/
├── layout/                           ← Shared platform layout
│   ├── layout.md                     ← General web layout
│   ├── dashboard-user/
│   │   └── layout.md                 ← User dashboard layout
│   └── dashboard-admin/
│       └── layout.md                 ← Admin dashboard layout
│
└── mvp/                              ← MVP screens
    ├── public/                       ← Public screens
    │   └── landing.md
    │
    ├── dashboard-user/               ← User screens
    │   ├── alert-history.md
    │   ├── assistence.md
    │   ├── device-status.md
    │   ├── emergency-contacts.md
    │   ├── evidence.md
    │   ├── frequent-places.md
    │   ├── home.md
    │   ├── location-history.md
    │   ├── my-location.md
    │   └── nearby-zones.md
    │
    └── dashboard-admin/              ← Administrative screens
        ├── admin-dashboard.md
        ├── alert-management.md
        ├── devices-management.md
        ├── emergency-management.md
        ├── evidence.management.md
        ├── moderator-management.md
        ├── report-management.md
        ├── user-management.md
        └── zone-management.md