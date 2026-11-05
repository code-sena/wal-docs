# Wireframe: Evidence

**Route:** `/dashboard/evidence`
**Component:** `Evidence`
**Layout:** `DashboardLayout` (Sidebar + Topbar + BottomNav)
**Required Role:** USER (operator)
**Guards:** `authGuard` + `userGuard`
**Lazy loading:** Yes
**Status:** ✅ Implemented

---

## ASCII Layout

```text
┌─────────────────────────────────────────────────────────────────┐
│  ☰  AlertaMujer              [🔔]  [☀️/🌙]  [ Ana García ▼ ]   │
├──────────┬──────────────────────────────────────────────────────┤
│          │                                                        │
│  🏠 Home │   🎬 My Evidence                                      │
│  📍 Loc. │                                                        │
│  🔔 Alert│   ┌─────────────────────────────────────────────────┐ │
│  🎬 Evid.│   │  FILTERS                                        │ │
│  📱 Device│  │                                                  │ │
│          │   │  [ app-chip: All ● ] [ app-chip: Video ]        │ │
│          │   │  [ app-chip: Audio ]   [ app-chip: Photo ]      │ │
│          │   │                             [app-period-badge ▼] │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
│          │   ┌─────────────────────────────────────────────────┐ │
│          │   │  EVIDENCE (8 files)                             │ │
│          │   │                                                  │ │
│          │   │  ┌──────────┐  ┌──────────┐  ┌──────────┐      │ │
│          │   │  │ 🎬       │  │ 🎙️       │  │ 📷       │      │ │
│          │   │  │ Video    │  │ Audio    │  │ Photo    │      │ │
│          │   │  │ Today 10:32│ │ Today 09:15│ │ Yesterday 18:44 │ │
│          │   │  │ 2.4 MB   │  │ 340 KB   │  │ 1.1 MB   │      │ │
│          │   │  │ Alert #1 │  │ Alert #1 │  │ Alert #2 │      │ │
│          │   │  │[▶️][⬇️][🗑]│  │[▶️][⬇️][🗑]│  │[🔍][⬇️][🗑]│   │ │
│          │   │  └──────────┘  └──────────┘  └──────────┘      │ │
│          │   │                                                  │ │
│          │   │  ┌──────────┐  ┌──────────┐                     │ │
│          │   │  │ 🎬       │  │ 📷       │                     │ │
│          │   │  │ Video    │  │ Photo    │                     │ │
│          │   │  │ Yesterday 14:27 │ Yesterday 09:00│           │ │
│          │   │  │ 3.1 MB   │  │ 890 KB   │                     │ │
│          │   │  │ Alert #3 │  │ Alert #3 │                     │ │
│          │   │  │[▶️][⬇️][🗑]│  │[🔍][⬇️][🗑]│                  │ │
│          │   │  └──────────┘  └──────────┘                     │ │
│          │   │                                                  │ │
│          │   │  [ < Previous ]              [ Next > ]          │ │
│          │   └─────────────────────────────────────────────────┘ │
│          │                                                        │
└──────────┴──────────────────────────────────────────────────────┘
```

---

## ASCII Layout — Evidence Viewer Modal (Video/Photo)

```text
                  ┌──────────────────────────────────────┐
                  │  🎬 Evidence — Alert #1              │  [✕]
                  │                                       │
                  │  ┌─────────────────────────────────┐ │
                  │  │                                 │ │
                  │  │   [ VIDEO PLAYER /              │ │
                  │  │     IMAGE VIEWER ]              │ │
                  │  │                                 │ │
                  │  └─────────────────────────────────┘ │
                  │                                       │
                  │  Type:     Video                      │
                  │  Date:     Today, 10:32 am            │
                  │  Size:     2.4 MB                     │
                  │  Alert:    #1 — SOS                   │
                  │                                       │
                  │  [ ⬇️ DOWNLOAD ]   [ CLOSE ]          │
                  └──────────────────────────────────────┘
```

---

## Form Fields

There is no input form. This is an evidence file viewing and management screen.

### Displayed Data — Evidence Cards

| Field            | Source               | Description                                |
| ---------------- | -------------------- | ------------------------------------------ |
| Type             | `evidencia.tipo`     | `'Video'` / `'Audio'` / `'Foto'`           |
| Date             | `evidencia.tiempo`   | Formatted timestamp                        |
| Size             | `evidencia.tamaño`   | In KB or MB                                |
| Associated Alert | `evidencia.alertaId` | Alert number that generated the evidence   |
| Status           | `evidencia.estado`   | `'Activa'` / `'Archivada'` / `'Eliminada'` |

### Card Actions

| Action    | Icon    | Evidence Type        | Result                                                  |
| --------- | ------- | -------------------- | ------------------------------------------------------- |
| Play/View | ▶️ / 🔍 | Video, Audio / Photo | Opens viewing modal                                     |
| Download  | ⬇️      | All                  | Downloads the file                                      |
| Delete    | 🗑      | All                  | Confirms in `danger` modal → `EvidenceService.delete()` |

---

## Validations

Not applicable to a form. Destructive actions (delete) require confirmation in a `danger` modal.

---

## States

| State        | Description         | UI                                                     |
| ------------ | ------------------- | ------------------------------------------------------ |
| `loading`    | Retrieving evidence | Card skeletons (gray grids)                            |
| `ready`      | Evidence loaded     | Card grid with type icons                              |
| `filtered`   | Active chips        | Updated grid                                           |
| `no-data`    | No evidence         | Message: "You have no recorded evidence" + 🎬 icon     |
| `no-results` | Empty filter result | Message: "There is no evidence matching these filters" |
| `error`      | API failure         | Red banner + retry button                              |
| `viewing`    | Playback modal open | Modal with player/viewer                               |
| `deleting`   | Delete confirmation | `danger` modal with warning                            |

---

## Flow

```text
/dashboard/evidence
  ├── onInit → EvidenceService.getByUsuario(usuarioId)
  │             → Populates card grid
  │
  ├── Click on type chip
  │     → Locally filters grid by type (Video/Audio/Photo)
  │
  ├── Change in app-period-badge
  │     → EvidenceService.getByUsuario(usuarioId, period)
  │     → Updates grid
  │
  ├── Click [▶️] or [🔍]
  │     → Opens modal with player/viewer
  │     → Displays evidence metadata
  │
  ├── Click [⬇️]
  │     → Starts direct file download (blob download)
  │
  └── Click [🗑]
        → Opens danger modal: "Delete this evidence?"
        → [Confirm] → EvidenceService.delete(id)
        → Success toast + removes card from grid
        → [Cancel] → closes modal
```

---

## Applied Styles

### Colors

```css
/* Page background */
background: var(--bg-base); /* #f5f4ff */

/* Evidence cards */
background: var(--bg-card);
border: 1.5px solid var(--border);
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);

/* Card hover */
background: var(--bg-card-hover); /* #f0eeff */
transform: translateY(-2px);

/* Type icons */
--icon-video : background var(--purple-pale)  / color var(--purple-deep);  /* #ede9fe / #7c3aed */
--icon-audio : background var(--green-pale)   / color var(--green);         /* #dcfce7 / #16a34a */
--icon-photo : background var(--amber-pale)   / color var(--amber);         /* #fef3c7 / #d97706 */

/* Card action buttons */
--btn-play   : color var(--purple-deep);
--btn-down   : color var(--green);
--btn-delete : color var(--red);

/* Danger modal (delete) */
variant: danger → red header, red confirm button
```

### Typography

```css
/* Page title */
font-size: 2rem; font-weight: 700; color: var(--text-primary);

/* Evidence type in card */
font-size: 1rem; font-weight: 600; color: var(--text-primary);

/* Card metadata */
font-size: 0.875rem; color: var(--text-muted);

/* Type icon */
font-size: 2rem; /* large emoji/icon */
```

### Spacing

```css
/* Card grid */
display: grid;
grid-template-columns: repeat(3, 1fr); /* desktop */
gap: 16px;
padding: 24px;

/* Card */
padding: 16px;
```

---

## Components Used

| Component          | Variant/Config      | Usage                                   |
| ------------------ | ------------------- | --------------------------------------- |
| `app-card`         | —                   | Filters + grid container                |
| `app-chip`         | `active: boolean`   | Type filters (All, Video, Audio, Photo) |
| `app-period-badge` | `conDropdown: true` | Period selector                         |
| `app-modal`        | `md`, `default`     | Evidence player/viewer                  |
| `app-modal`        | `sm`, `danger`      | Delete confirmation                     |
| `app-button`       | `danger`, `sm`      | Delete button in confirmation modal     |
| `app-spinner`      | `md`, `inline`      | Loading state                           |

---

## Responsive

| Breakpoint          | Behavior                                              |
| ------------------- | ----------------------------------------------------- |
| Desktop `>1024px`   | 3-column grid                                         |
| Tablet `760–1024px` | 2-column grid                                         |
| Mobile `<760px`     | 1-column grid; sidebar → BottomNav; full-screen modal |

---

## Accessibility

* Grid with `role="list"` and cards as `role="listitem"`
* Action buttons with descriptive `aria-label`: "Play video from alert #1", "Delete evidence"
* Playback modal with `aria-label="Evidence player"`
* Delete modal with `aria-describedby` pointing to the warning text
* Chips with `role="checkbox"` and `aria-checked`
* Minimum contrast ratio of 4.5:1 for text on card backgrounds

---

## Service Integration

```typescript
// EvidenceService — methods used

// Get user's evidence
EvidenceService.getByUsuario(usuarioId: number): Observable<Evidencia[]>
// GET /evidence?usuarioId=:id

// Delete evidence
EvidenceService.delete(id: number): Observable<void>
// DELETE /evidence/:id

// Data model
interface Evidencia {
  id        : number;
  usuarioId : number;
  alertaId  : number;
  tipo      : 'Video' | 'Audio' | 'Foto';
  url       : string;         // File URL
  tamaño    : number;         // in bytes
  tiempo    : string;         // ISO timestamp
  estado    : 'Activa' | 'Archivada' | 'Eliminada';
}
```

---

## Use Cases

| # | Actor | Action                             | Result                                                                 |
| - | ----- | ---------------------------------- | ---------------------------------------------------------------------- |
| 1 | User  | Navigates to `/dashboard/evidence` | Sees grid with all their evidence                                      |
| 2 | User  | Clicks "Video" chip                | Grid filters to show only videos                                       |
| 3 | User  | Clicks [▶️] on a video             | Modal with video player                                                |
| 4 | User  | Clicks [🔍] on a photo             | Full-size image viewer modal                                           |
| 5 | User  | Clicks [⬇️]                        | Downloads the file to the device                                       |
| 6 | User  | Clicks [🗑]                        | Confirmation modal → upon confirmation, deletes and refreshes the grid |
| 7 | User  | Has no evidence                    | Empty message with instructions on how evidence is generated           |

```
```
