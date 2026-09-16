# Design System — AlertaMujer

## 🎨 Design Tokens

### Color Palette — Mobile (React Native)

#### LIGHT Theme (Default — Purple/Lilac)
```typescript
// Primary Colors
headerBackground: rgb(202,171,222)      // #CAABDD
headerText: #1a1a1a
background: #f5f0fa
containerBackground: #ffffff
text: #1a1a1a
card: #ffffff
tabBackground: rgb(202,171,222)
tabActiveColor: #45046b
tabInactiveColor: rgb(255, 255, 255)
icon: #9b009b
headercolor1: #c4a8d6
headercolor2: #7b1db2

// Contact Colors
contactCardBg: #ffffff
contactCardBorder: #e0d0f0
contactName: #2a0045
contactSubtext: #7a6090
contactAccent: #7B1FA2
contactBadgeBg: #F3E5F5
contactBadgeText: #6A1B9A
contactBadgeBorder: #CE93D8
contactAvatarBorder: #AB47BC
contactDivider: #EDE7F6
contactIconCallBg: #F3E5F5
contactIconCallColor: #7B1FA2
contactIconWaBg: #E8F5E9
contactIconLocBg: #E8EAF6
contactIconLocColor: #3949AB
contactEditButton: #F3E5F5
contactEditButtonIcon: #7B1FA2
contactSuggestionBg: #FAF7FF
contactSuggestionBorder: #CE93D8
contactSuggestionIcon: #AB47BC
contactAddButtonBg: #7B1FA2
contactFloatingBg: #7B1FA2
contactCousserBg: #F3E5F5
contactCousserIcon: #7B1FA2
contactSearchBg: #F3EEF8
contactSearchText: #2a0045

// Assistance Gradients
assistanceEmergencyGradient: ["#c4a8d6", "#7b1db2"]
assistanceViolenceGradient: ["rgb(240, 87, 87)", "rgb(132, 0, 255)"]
assistanceMentalGradient: ["rgb(142, 189, 255)", "rgb(255, 0, 191)"]
assistanceUserIconBg: "purple"
assistanceBulbIconBg: "rgba(205, 117, 230, 0.29)"
assistanceBulbIconColor: "#1E1228"
assistanceStarIconBg: "purple"
```

#### DARK Theme
```typescript
headerBackground: #000000
headerText: #fefeff
background: #3d3d42
containerBackground: #1e1e22
text: #f0e6ff
card: #2e2e38
tabBackground: #000000
tabActiveColor: #9718ff
tabInactiveColor: rgba(230, 217, 247, 0.89)
icon: #c084fc
headercolor1: #030303
headercolor2: #200138

// Contacts Dark
contactCardBg: #28282f
contactCardBorder: #3d3d50
contactName: #e8d5ff
contactSubtext: #9e8fbb
contactAccent: #c084fc
contactBadgeBg: #3a2d55
contactBadgeText: #d8b4fe
contactBadgeBorder: #6d28d9
contactAvatarBorder: #a855f7
contactDivider: #3a3a48
contactIconCallBg: #2d2040
contactIconCallColor: #c084fc
contactIconWaBg: #1a2e22
contactIconLocBg: #1a233a
contactIconLocColor: #60a5fa
contactEditButton: #2d2040
contactEditButtonIcon: #c084fc
contactSuggestionBg: #22202e
contactSuggestionBorder: #5b21b6
contactSuggestionIcon: #a78bfa
contactAddButtonBg: #7c3aed
contactFloatingBg: #7c3aed
contactCousserBg: #2d2040
contactCousserIcon: #c084fc
contactSearchBg: #232330
contactSearchText: #e8d5ff
```

#### PINK Theme
```typescript
headerBackground: #cf3389
background: #fce4f0
containerBackground: #fffbfe
text: #3a0020
card: #fff0f7
tabBackground: #cf3389
icon: #eb0a86
headercolor1: #cf3389
headercolor2: #860e52
```

#### WINE Theme
```typescript
headerBackground: #770736
background: #fff5f5
containerBackground: #ffffff
text: #2a0000
card: #fff0f0
tabBackground: #770736
icon: #770736
headercolor1: #770736
headercolor2: #4a0020
```

#### BLUE Theme
```typescript
headerBackground: #0b013b
background: #f0f0ff
containerBackground: #ffffff
text: #0b013b
card: #eef2ff
tabBackground: #0b013b
icon: #1565C0
headercolor1: #0b013b
headercolor2: #1a237e
```

#### MAGENTA Theme
```typescript
headerBackground: #490449
background: #fdf0fd
containerBackground: #ffffff
text: #2a002a
card: #fce8fc
tabBackground: #490449
icon: #6a0572
headercolor1: #490449
headercolor2: #860386
```

### Color Palette — Web (Angular)

#### Light Mode — :root
```css
/* Purples */
--color-purple-dark   : #c205b8;
--color-purple        : #c900ae;
--color-purple-mid    : #c52dca;
--color-purple-light  : #b502bb;

/* Pinks */
--color-pink          : #a70ac7;
--color-pink-hot      : #ae10df;
--color-pink-light    : #410166;

/* Text */
--color-text-dark     : #2d1457;
--color-text-mid      : #000000;
--color-text-muted    : #570ea0;

/* Surfaces */
--color-white         : #680ca5;
--color-card-bg       : rgba(142, 10, 160, 0.918);
--color-input-bg      : #5509ac;

/* Borders */
--color-border        : rgba(124, 58, 237, 0.322);
--color-border-focus  : var(--color-purple-light);

/* Gradients */
--gradient-bg  : linear-gradient(135deg, #db079c 0%, #af0763 35%, #9a07a7 70%, #430261 100%);
--gradient-btn : linear-gradient(90deg, #650dc9 0%, #54048a 50%, #5c048f 100%);

/* Admin Panel / Dashboard */
--bg-base       : #f5f4ff;
--bg-card       : #ffffff;
--bg-card-hover : #f0eeff;
--border        : #c4b5fd;
--text-primary  : #1e1a3c;
--text-muted    : #6b7280;
--text-dim      : #9ca3af;

--purple-deep       : #7c3aed;
--purple-mid        : #a78bfa;
--purple-light      : #c4b5fd;
--purple-pale       : #ede9fe;
--purple-pale-hover : #e0d8fc;
--pink              : #ec4899;

/* Semantic Colors */
--green         : #16a34a;
--green-pale    : #dcfce7;
--red           : #dc2626;
--red-light     : #f3a5a5;
--red-pale      : #fee2e2;
--red-pale-hover: #fdc9c9;
--amber         : #d97706;
--amber-pale    : #fef3c7;
```

#### Dark Mode — [data-theme="dark"]
```css
--bg-base       : #14101f;
--bg-card       : #1e1833;
--bg-card-hover : #241d3d;
--border        : #4c3a80;
--text-primary  : #ede9fe;
--text-muted    : #a8a0c4;
--text-dim      : #766e94;

--purple-deep       : #9d6ff0;
--purple-mid        : #b79bf5;
--purple-light      : #6d4fae;
--purple-pale       : #2b204a;
--purple-pale-hover : #342659;
--pink              : #ff5fa8;

--green         : #4ade80;
--green-pale    : #113322;
--red           : #f87171;
--red-light     : #c65c5c;
--red-pale      : #3a1414;
--red-pale-hover: #4a1a1a;
--amber         : #fbbf24;
--amber-pale    : #3a2a0a;
```

### Universal Semantic Colors

| Purpose | Mobile | Web (Light) | Web (Dark) |
|---------|--------|-------------|------------|
| Success | #27ae60 | #16a34a | #4ade80 |
| Error | #e74c3c | #dc2626 | #f87171 |
| Warning | rgb(255, 189, 65) | #d97706 | #fbbf24 |
| Info | #7B2CBF | #3b82f6 | #60a5fa |
| Primary | #7B1FA2 | #7c3aed | #9d6ff0 |
| Secondary | #3949AB | #a78bfa | #b79bf5 |

## 📝 Typography System

### Mobile (React Native)

**Fonts by platform:**
- Android: Roboto (system font)

| Use | Size | Weight | Line Height | Letter Spacing |
|-----|------|--------|-------------|----------------|
| Card titles | 24px | 700 | 32px | -0.5px |
| Subtitles | 14–16px | 400–600 | 18–22px | 0px |
| Body text | 13–14px | 400–500 | 20–22px | 0px |
| Small text | 10–12px | 500–700 | 14–16px | 0.2px |
| Primary buttons | 16px | 700 | 20px | 0.5px |
| Secondary buttons | 16px | 600 | 20px | 0.5px |
| Input text | 16px | 400 | 20px | 0px |
| Header titles | 22–24px | bold | 28px | -0.5px |
| Tab labels | 11px | 700 | 14px | 0.5px |
| Large numbers | 32–42px | bold | 48px | -1px |

### Web (Angular)

**Base font:** Nunito, sans-serif

| Use | Size | Weight | Line Height | Letter Spacing |
|-----|------|--------|-------------|----------------|
| H1 Landing | 2.5rem | 800 | 3rem | -0.02em |
| H2 Sections | 2rem | 700 | 2.5rem | -0.01em |
| H3 Cards | 1.5rem | 600 | 2rem | 0px |
| Body text | 1rem | 400 | 1.6rem | 0px |
| Small text | 0.875rem | 400 | 1.4rem | 0px |
| Buttons | 0.9rem | 600 | 1.2rem | 0.5px |
| Inputs | 0.9rem | 400 | 1.4rem | 0px |
| Nav links | 0.85rem | 500 | 1.2rem | 0.5px |
| Tagline | 0.62rem | 700 | 1rem | 0.1em |

## 📐 Spacing and Grid System

### Mobile (React Native)

**Base:** 4px grid system

| Purpose | Value |
|---------|-------|
| Minimum spacing | 4px |
| Small spacing | 8px |
| Medium spacing | 12–16px |
| Large spacing | 20–24px |
| Extra large spacing | 30–40px |
| Card padding | 20–24px |
| Horizontal margin | 16–20px |
| Element gap | 8–12px |

### Web (Angular)

**Base:** 4px grid system (Tailwind)

| Purpose | Value | Tailwind |
|---------|-------|---------|
| Minimum spacing | 4px | space-1 |
| Small spacing | 8px | space-2 |
| Medium spacing | 16px | space-4 |
| Large spacing | 24px | space-6 |
| Extra large spacing | 32px | space-8 |
| Card padding | 24px | p-6 |
| Card margin | 16px | m-4 |
| Form row gap | 14px | gap-3.5 |
| Form top margin | 28px | mt-7 |

**Breakpoints:**
- Mobile: < 760px
- Tablet: 760px – 1024px
- Desktop: > 1024px

## 🔲 Border Radius

### Mobile
| Element | Radius |
|---------|--------|
| Primary buttons | 12–16px |
| Large buttons | 25–30px |
| Standard cards | 20–24px |
| Large cards | 28px |
| Inputs | 20px |
| Modals | 30px |
| Avatars | 35–40px |
| Circular icons | 10–15px |
| Badges | 12–20px |
| SOS button | 50px (circular) |

### Web
| Element | CSS Variable | Value |
|---------|--------------|-------|
| Auth cards | --radius-card | 28px |
| Inputs | --radius-input | 14px |
| Buttons | --radius-btn | 50px (pill) |
| Checkboxes | --radius-checkbox | 5px |
| Dashboard cards | --dash-radius-card | 12px |
| Small elements | --dash-radius-sm | 6px |

## 🌑 Shadows

### Mobile
```typescript
// Cards
shadowColor: COLORS.purpleDark or "#000"
shadowOffset: { width: 0, height: 8 }
shadowOpacity: 0.15
shadowRadius: 24
elevation: 10

// Buttons
shadowColor: COLORS.purpleDark
shadowOffset: { width: 0, height: 4 }
shadowOpacity: 0.3
shadowRadius: 8
elevation: 6

// Tabs
shadowColor: "#7B2CBF"
shadowOffset: { width: 0, height: -3 }
shadowOpacity: 0.15
shadowRadius: 8
elevation: 10

// Floating button
shadowColor: "#000"
shadowOffset: { width: 0, height: 4 }
shadowOpacity: 0.4
shadowRadius: 8
elevation: 8
```

### Web
```css
/* Auth cards */
--shadow-card       : 0 24px 60px rgba(78, 16, 177, 0.18), 0 4px 16px rgba(91, 33, 182, 0.10);
--shadow-btn        : 0 8px 24px rgba(233, 30, 140, 0.38);
--shadow-btn-hover  : 0 12px 32px rgba(233, 30, 140, 0.48);
--shadow-card-badge : 0 12px 36px rgba(80, 20, 180, 0.22);

/* Dashboard cards */
--dash-shadow-card : 0 0 0 1.5px var(--border), 0 4px 16px rgba(124, 58, 237, 0.10);
```

## 🧩 Base Components

### Buttons

#### Mobile — Primary Button (Confirm)
**File:** `src/components/ui/button/aceptar.tsx`

**Props:**
- `title: string` — Button text
- `onPress: () => void` — Handler function
- `color?: string` — Custom color (default: "#331753")

**States:**
- Normal: opacity 1
- Pressed: opacity 0.85, scale 0.92
- Disabled: opacity 0.5

**Styles:**
```typescript
width: "90%"
padding: 15
borderRadius: 12
backgroundColor: customizable
textColor: "#fff"
fontWeight: "bold"
fontSize: 16
```

#### Mobile — Secondary Button (Cancel)
**File:** `src/components/ui/button/cancelar.tsx`

**Props:**
- `title: string` — Button text
- `onPress: () => void` — Handler function
- `bgColor?: string` — Background color (default: "#ffffff")
- `textColor?: string` — Text color (default: "#000000")

**Styles:**
```typescript
width: "90%"
padding: 16
borderRadius: 12
borderWidth: 1
borderColor: "#ffffff"
```

#### Web — Button Component
**File:** `app/shared/components/button/button.scss`

**Variants:**
- `primary`: Purple/pink gradient with shadow
- `secondary`: Transparent with purple border
- `danger`: Transparent with red border

**Sizes:**
- `sm`: 8px 18px padding, 13px font
- `md`: 12px 28px padding, 15px font
- `lg`: 15px 36px padding, 17px font

**States:**
- `disabled`: opacity 0.6, cursor not-allowed
- `loading`: shows spinner component
- `hover`: transform translateY(-1px), increased shadow
- `active`: transform translateY(0)

### Inputs

#### Mobile — TextInput

**States:**
```typescript
// Normal
backgroundColor: theme.contactSearchBg
borderColor: theme.contactCardBorder

// Focus
borderColor: theme.contactAccent
shadowColor: theme.contactAccent

// Error
borderColor: #e74c3c
backgroundColor: "#FFF5F5"
```

**Validations:**
- Inline error below the input
- Visible error icon
- Descriptive error message

#### Web — Input Fields

**States:**
```css
/* Normal */
background: rgba(245, 233, 233, 0.9)
border: 2px solid rgba(236, 72, 153, 0.15)

/* Focus */
background: var(--bg-card)
border: 2px solid #db1879
box-shadow: 0 0 0 4px rgba(236, 72, 153, 0.12)

/* Error */
border-color: var(--red)
error-message visible
```

### Cards

#### Mobile — Base Card
**File:** `src/components/ui/card/card.tsx`

**Props:**
- `title?: string` — Card title
- `titleHighlight?: string` — Highlighted text
- `children?: React.ReactNode` — Content
- `style?: any` — Additional styles
- `gradientColors?: readonly [string, string, ...string[]]` — Gradient colors

**Styles:**
```typescript
marginHorizontal: 14
marginTop: 24
marginBottom: 20
borderRadius: 28
shadowColor: COLORS.purpleDark
shadowOffset: { width: 0, height: 8 }
shadowOpacity: 0.15
shadowRadius: 24
elevation: 10
borderWidth: 1
borderColor: COLORS.purpleLight
padding: 24
```

#### Web — Card Component
**File:** `app/shared/components/card/card.scss`

**Styles:**
```css
background: var(--bg-card)
border: 1.5px solid var(--border)
radius: var(--dash-radius-card)
shadow: var(--dash-shadow-card)
hover: background var(--bg-card-hover)
```

### Modals

#### Mobile — Confirmation Modal
**File:** `src/components/ui/modalConfirmacion/confirmacion.tsx`

**Props:**
- `visible: boolean` — Visibility
- `department: string` — Department to confirm
- `municipality: string` — Municipality to confirm
- `onConfirm: () => void` — Confirm callback
- `onGoBack: () => void` — Go back callback
- `confirmOnly: boolean` — Show only confirm button

**Styles:**
```typescript
overlay: backgroundColor 'rgba(0,0,0,0.6)'
modalCard:
  width: '85%'
  backgroundColor: '#FFF'
  borderRadius: 30
  padding: 25
  elevation: 10
iconCircle: 70x70px, borderRadius 35, backgroundColor '#F3E5F5'
btnConfirm: height 55, backgroundColor '#471e63', borderRadius 15
btnGoBack: height 55, backgroundColor transparent, borderWidth 2
```

#### Web — Modal Component
**File:** `app/shared/components/modal/modal.scss`

**Sizes:**
- `sm`: Small modal
- `md`: Medium modal (default)
- `lg`: Large modal
- `zone`: Special modal for zones

**Variants:**
- `default`: Standard style
- `danger`: Style for destructive actions

**Behavior:**
- `closeOnOutsideClick`: Closes when clicking outside
- `close` event: Emitted when closed

### Badges and Chips

#### Mobile — Suggestion Chips
**Use:** Location name suggestions

**Styles:**
```typescript
backgroundColor: COLORS.white
borderWidth: 1.5
borderColor: COLORS.accent
borderRadius: 20
paddingVertical: 8
paddingHorizontal: 14
```

#### Web — Chip Component
**File:** `app/shared/components/chip/chip.scss`

**Props:**
```typescript
label: string
active: boolean
dotColor?: string   // Optional dot color
count?: number      // Optional counter
```

**Use:** Interactive filters and tags with active/inactive state

### Navigation Bars

#### Mobile — Navigation Tabs
**File:** `src/components/ui/Tabs/TabsNavegationStyle.ts`

**Styles:**
```typescript
tabBarLabelStyle: {
  fontSize: 11,
  fontWeight: "700",
  letterSpacing: 0.5,
  marginBottom: 6,
  marginTop: 2,
}
tabBarStyle: {
  height: 70,
  paddingBottom: 12,
  paddingTop: 4,
  elevation: 10,
  shadowColor: "#7B2CBF",
  shadowOffset: { width: 0, height: -3 },
  shadowOpacity: 0.15,
  shadowRadius: 8,
}
```

#### Web — Dashboard Navbar
**File:** `app/shared/components/dashboard-navbar/dashboard-navbar.scss`

**Components:**
- Logo and branding
- User profile
- Theme switch
- Notifications
- Logout menu

## ♿ Accessibility and UX Rules

> These accessibility rules directly implement **RNF11** (accessible interface, large elements,
> contrasting colors for use under stress) and **RNF12** (rapid activation methods outside the
> main interface). See `04-requirements/non-functional.md` for the full definitions.

### Color Contrast (WCAG AA)

**Implemented standards:**
- Automatic text contrast (getContrastText on mobile)
- Normal text: minimum 4.5:1
- Large text: minimum 3:1
- UI components: minimum 3:1

**Verification:**
- All primary texts meet WCAG AA
- Semantic colors with sufficient contrast
- Dark mode with adjusted contrast

### Minimum Touch Targets

**Mobile:**
- Buttons: minimum 44×44dp
- SOS button: 68% of screen width
- Tabs: 70px height
- Circular icons: minimum 48×48dp

**Web:**
- Buttons: minimum 44px height
- Navigation links: 12px padding
- Inputs: minimum 48px height
- Touch targets on mobile: 48×48px

### Haptic Feedback

**Mobile:**
- Long press on SOS button: intense vibration
- Alert activation: vibration pattern
- Action confirmation: short vibration
- Error: long vibration

**Implementation:**
```typescript
import { Vibration } from 'expo-haptics';

// Alert activated
Vibration.vibrate([100, 50, 100]);

// Error
Vibration.vibrate(500);

// Confirmation
Vibration.vibrate(50);
```

### Discreet Alert Mechanisms

**Mobile:**
1. **Triple tap**: Tap the screen 3 times quickly
2. **Physical button**: Hold volume button for 3 seconds
3. **Shake activation**: Shake the device vigorously

**Confirmation:**
- 3–5 second countdown
- Intense vibration
- Alarm sound
- Option to cancel

### Error Recovery Patterns

**Inputs:**
- Real-time validation (onChange)
- Inline error below the field
- Visible error icon
- Descriptive error message
- Correction suggestion

**Destructive actions:**
- Confirmation modal before deleting
- Cancel button with red icon (#E53935)
- Clear warning text
- Reversible action when possible
- Action history

**Network connectivity:**
- Connection status indicator
- Automatic retry (exponential backoff)
- Specific error message
- Manual retry action
- Offline data cache

## ⏱️ Feedback Timings

### Mobile
| Action | Duration | Easing |
|--------|----------|--------|
| Button press | 100ms | ease-out |
| Glow animation | 1600–2500ms | quad out |
| Tab button | 100ms each direction | ease-in-out |
| Slide navigation | 300ms | ease-in-out |
| Modal fade | 250ms | ease-out |
| Loading | Indefinite (ActivityIndicator) | — |

### Web
| Action | Duration | Easing |
|--------|----------|--------|
| Hover states | 0.2s | ease |
| General transitions | 0.3s | ease |
| Modals | 0.5s | ease |
| GPU transforms | 0.2s | ease |
| Smooth scroll | 0.5s | ease-in-out |

## 🌓 Dark Mode

### Mobile
**Implementation:**
- 6 theme modes: light, dark, pink, wine, blue, magenta
- Smooth transition between modes
- All components adapted
- Automatic text contrast

**Main changes:**
- Darker backgrounds (#3d3d42, #1e1e22)
- Lighter text (#f0e6ff, #e8d5ff)
- More visible borders (#3d3d50)
- Increased shadows for contrast

### Web
**Implementation:**
- Activated via `data-theme="dark"` attribute on html element
- Smooth transition between modes
- All components adapted

**Main changes:**
- Darker backgrounds (#14101f, #1e1833)
- Lighter text (#ede9fe, #a8a0c4)
- More visible borders (#4c3a80)
- Increased shadows for contrast
- Maps with inverted filter for dark mode

## 📱 Responsive Design

### Mobile
**Relative values:**
- SOS button: 68% of screen width
- Titles: width × 0.038
- Padding: based on safe area insets

**Orientation:**
- Portrait: Main layout
- Landscape: Grid adaptation
- Safe area: Dynamic insets

### Web
**Breakpoints:**
- Mobile: < 760px
- Tablet: 760px – 1024px
- Desktop: > 1024px

**Adaptations:**
- Desktop: 3-column layout
- Tablet: 2-column layout
- Mobile: 1-column layout

## 🔐 Security and Privacy

### Sensitive Data Handling
- Passwords: visibility toggle
- Personal data: encryption in transit
- Location: explicit permissions
- Contacts: read permissions
- Camera/Microphone: per-use permissions

### Runtime Permissions
**Mobile:**
- GPS: Required for core functionality
- SMS: Required for alerts
- Camera: Optional for evidence
- Microphone: Optional for evidence
- Contacts: Required for notifications
- Notifications: Required for alerts

**Web:**
- Geolocation API: For location
- Notifications API: For alerts
- MediaDevices API: For camera/microphone

## 🎯 Loading States

### Mobile
```typescript
// ActivityIndicator
<ActivityIndicator
  size="large"
  color={COLORS.accent}
/>

// Skeleton loading
<SkeletonPlaceholder>
  <SkeletonPlaceholder.Item width={200} height={20} />
  <SkeletonPlaceholder.Item width={150} height={15} />
</SkeletonPlaceholder>
```

### Web
```typescript
// Spinner component
<app-spinner size="md" mode="inline" />

// Skeleton cards
<div class="skeleton-card"></div>
<div class="skeleton-text"></div>
```

## 📊 Performance Metrics

### Mobile
- Frame rate: 60fps target
- Time to interactive: < 3s
- Bundle size: Optimized with code splitting
- Images: Lazy loading and compression

### Web
- First Contentful Paint: < 1.5s
- Time to Interactive: < 3s
- Bundle size: Code splitting by route
- Images: WebP and lazy loading

---

This design system combines React Native and Angular best practices to maintain visual and functional consistency between the mobile application and the web administrative panel.
