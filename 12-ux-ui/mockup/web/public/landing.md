# Landing Page

```text
┌─────────────────────────────────────────────────────────────────┐
│  [🔔 AlertaMujer]           [Features] [How it works]           │
│                              [Download]  [Log in]                │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  [ PURPLE ORB ]                          [ PINK ORB ]            │
│                                                                   │
│   ┌──────────────────────────────────────────────────────────┐   │
│   │                   HERO SECTION                           │   │
│   │                                                          │   │
│   │   Your safety,                                           │   │
│   │   our priority                                           │   │
│   │                                                          │   │
│   │   Immediate alerts · Emergency contacts                  │   │
│   │   Risk zones · Secure evidence                            │   │
│   │                                                          │   │
│   │   [ 🛡️ GET STARTED ]       [ See how it works ]          │   │
│   │                                                          │   │
│   │                  [ APP IMAGE / MOCKUP ]                  │   │
│   └──────────────────────────────────────────────────────────┘   │
│                                                                   │
├─────────────────────────────────────────────────────────────────┤
│                   MAIN FEATURES                                  │
│                                                                   │
│   ┌───────────────┐  ┌───────────────┐  ┌───────────────┐       │
│   │  📍           │  │  👥           │  │  🎬           │       │
│   │  Real-time    │  │  Emergency    │  │  Automatic    │       │
│   │  location     │  │  contacts     │  │  evidence     │       │
│   └───────────────┘  └───────────────┘  └───────────────┘       │
│                                                                   │
│   ┌───────────────┐  ┌───────────────┐  ┌───────────────┐       │
│   │  🗺️           │  │  📊           │  │  🔔           │       │
│   │  Risk         │  │  Admin        │  │  Discreet     │       │
│   │  zones        │  │  dashboard    │  │  alerts       │       │
│   └───────────────┘  └───────────────┘  └───────────────┘       │
│                                                                   │
├─────────────────────────────────────────────────────────────────┤
│                   HOW IT WORKS                                   │
│                                                                   │
│   1 ─── Download the app ─── 2 ─── Set up contacts               │
│                                                                   │
│   3 ─── Activate alert ───── 4 ─── Receive help                 │
│                                                                   │
├─────────────────────────────────────────────────────────────────┤
│                   FINAL CALL TO ACTION                            │
│                                                                   │
│         Ready to protect yourself?                               │
│         [ 🛡️ CREATE FREE ACCOUNT ]   [ Log in ]                 │
│                                                                   │
├─────────────────────────────────────────────────────────────────┤
│  © AlertaMujer 2026   [Privacy] [Terms] [Contact]               │
└─────────────────────────────────────────────────────────────────┘
```

---

## Component Sections

### Navbar (LandingNavbar)

* Logo + brand name on the left
* Navigation links: Features, How it works, Download
* Secondary button: `Log in` → `/auth/login`
* Primary button: `Create account` → `/auth/register`
* Light/dark theme switch
* On mobile: collapsible hamburger menu

### Hero Section

| Element       | Description                                                         |
| ------------- | ------------------------------------------------------------------- |
| Tagline       | Small label (0.62rem, 700) above the title                          |
| H1            | Main title (2.5rem, 800, --color-text-dark)                         |
| Subtitle      | Brief description (1rem, 400, --text-muted)                         |
| Primary CTA   | `app-button` variant `primary` size `lg` → `/auth/register`         |
| Secondary CTA | `app-button` variant `secondary` size `lg` → "How it works" section |
| Hero image    | App illustration/mockup, visible on desktop, reduced on mobile      |
| Orbs          | Two decorative circular elements with `--gradient-bg` and blur      |

### Features Section

* 6 cards (`app-card`) in a 3×2 grid (desktop) → 2×3 (tablet) → 1×6 (mobile)
* Each card: emoji icon, H3 title (1.5rem, 600), description (0.875rem)
* Background: `var(--bg-base)`

### How It Works Section

* 4 numbered steps in a horizontal layout (desktop) → vertical (mobile)
* Connected with a decorative line or arrow
* Alternate background (`--bg-card`)

### Final CTA

* Background: `var(--gradient-bg)`
* White text
* Two buttons (create account, log in)

### Footer

* Three columns: brand, legal links, social media
* Dark background (`#14101f` in dark mode, `--bg-base` in light mode)

---

## Validations

Not applicable. No form fields on the landing page.

---

## States

| State      | Description            | UI                                    |
| ---------- | ---------------------- | ------------------------------------- |
| `loading`  | Initial render         | Skeletons in feature cards            |
| `ready`    | Content visible        | Entry animations (fade-in)            |
| `scrolled` | User has scrolled 80px | Navbar with solid background + shadow |

---

## Flow

```text
/ (landing)
  ├── [Create account]       → /auth/register
  ├── [Log in]               → /auth/login
  ├── [Get started]          → /auth/register
  └── [See how it works]     → scroll to #how-it-works section
```

---

## Applied Styles

### Colors

```css
/* General background */
background: var(--gradient-bg);   /* Hero: linear-gradient(135deg, #db079c 0%, #af0763 35%, #9a07a7 70%, #430261 100%) */

/* Navbar on scroll */
background: rgba(255,255,255,0.95);
backdrop-filter: blur(12px);

/* Feature cards */
background: var(--bg-card);       /* #ffffff */
border: 1.5px solid var(--border); /* #c4b5fd */

/* Final CTA */
background: var(--gradient-bg);
```

### Typography

```css
/* Hero H1 */
font-size: 2.5rem;
font-weight: 800;
line-height: 3rem;
letter-spacing: -0.02em;
color: #ffffff;   /* over gradient background */

/* Tagline */
font-size: 0.62rem;
font-weight: 700;
letter-spacing: 0.1em;
text-transform: uppercase;

/* Section H2 */
font-size: 2rem;
font-weight: 700;
color: var(--text-primary); /* #1e1a3c */

/* Card H3 */
font-size: 1.5rem;
font-weight: 600;
```

### Spacing

```css
/* Section padding */
padding: 80px 24px;

/* Cards */
padding: 24px;      /* p-6 */
gap: 16px;          /* gap-4 */
border-radius: var(--dash-radius-card); /* 12px */

/* Hero buttons */
gap: 16px;
```

### Shadows

```css
/* Primary button */
box-shadow: var(--shadow-btn); /* 0 8px 24px rgba(233,30,140,0.38) */

/* Cards */
box-shadow: var(--dash-shadow-card);
```

### Radius

```css
--radius-btn  : 50px;   /* Pill buttons */
--dash-radius-card: 12px; /* Feature cards */
```

---

## Components Used

| Component                        | Usage                                   |
| -------------------------------- | --------------------------------------- |
| `app-button` variant `primary`   | "Create account" and "Get started" CTAs |
| `app-button` variant `secondary` | "See how it works", "Log in"            |
| `app-card`                       | Feature cards                           |
| `LandingNavbar`                  | Public navigation bar                   |

---

## Responsive

| Breakpoint          | Behavior                                                          |
| ------------------- | ----------------------------------------------------------------- |
| Desktop `>1024px`   | 3-column feature grid; horizontal hero with side image            |
| Tablet `760–1024px` | 2-column grid; reduced hero image                                 |
| Mobile `<760px`     | 1-column grid; hero image hidden; hamburger menu; stacked buttons |

---

## Accessibility

* `role="banner"` on navbar; `role="main"` on main content; `role="contentinfo"` on footer
* CTA buttons with descriptive `aria-label`
* Decorative images with `alt=""`
* Minimum 4.5:1 contrast for text over gradient (white text)
* Skip link to main content (`#main-content`)
* Sequential tab order: navbar → hero → features → how it works → CTA → footer

---

## Service Integration

Does not consume API services. Fully static page.

```typescript
// Programmatic navigation when clicking CTAs
router.navigate(['/auth/register']);
router.navigate(['/auth/login']);
```

---

## Use Cases

| # | Actor                       | Action                        | Result                                                            |
| - | --------------------------- | ----------------------------- | ----------------------------------------------------------------- |
| 1 | Anonymous visitor           | Visits `/` for the first time | Sees the landing page with the value proposition                  |
| 2 | Anonymous visitor           | Clicks "Get started"          | Redirected to `/auth/register`                                    |
| 3 | Registered user             | Clicks "Log in"               | Redirected to `/auth/login`                                       |
| 4 | Visitor                     | Scrolls down                  | Navbar becomes fixed with a solid background                      |
| 5 | Authenticated user (USER)   | Accesses `/`                  | Automatically redirected to `/dashboard/home` (via `userGuard`)   |
| 6 | Authenticated administrator | Accesses `/`                  | Automatically redirected to `/admin/dashboard` (via `adminGuard`) |

```
```
