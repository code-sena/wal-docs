# Mockups — Mobile (React Native / Expo)

> Screens designed for the Android mobile app.
> Each screen lives in its own subfolder with the prompt used and the reference HTML. The texts shown in the screens are the Spanish texts of the interface.

---

## Structure

```
mobile/
├── authentication/
│   ├── login/                 ← sign in
│   └── register/              ← account registration
├── onboarding/                ← first-use guide
├── home/
│   ├── sos-button/            ← home with the SOS button
│   ├── sos-activation/        ← 5-second cancellable countdown
│   └── active-alert/          ← alert in progress
├── contacts/                  ← trusted contacts
├── location/                  ← map with zones
├── assistance/                ← help lines and resources
├── alert-history/             ← alert history
├── settings/                  ← settings
└── navigation/
    └── side-drawer/           ← side menu
```

## Convention per screen

```
[screen-name]/
├── [screen-name].md    ← prompt used + design notes
└── [screen-name].html  ← reference HTML implementation
```

## Related documents

- Design system → `12-ux-ui/design-system.md`
- Navigation map → `12-ux-ui/navigation-map.md`
- Web mockups → `12-ux-ui/mockup/web/README.md`
