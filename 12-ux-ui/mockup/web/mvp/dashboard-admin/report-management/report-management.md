# Report Management
 
**Route:** `/admin/report-management`
**Access:** ADMIN
**Implementation status:** Implemented with lazy loading — `ReportManagementComponent` registered; consumes `ReportService` (generate, download); interface for generating and downloading reports in different formats.
 
## Base style — design tokens
```css
/* Page background */
background: var(--bg-base); /* #f5f4ff */
 
/* Cards */
background: var(--bg-card);
border: 1.5px solid var(--border);   /* #c4b5fd */
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);
 
/* Report type card */
background: var(--bg-card);
border: 1.5px solid var(--border);
border-radius: var(--dash-radius-card);
 
/* Report card hover */
background: var(--bg-card-hover); /* #f0eeff */
border-color: var(--purple-deep); /* #7c3aed */
transform: translateY(-2px);
 
/* Report type icon — Alerts */
background: var(--red-pale); color: var(--red);
 
/* Report type icon — Users */
background: var(--purple-pale); color: var(--purple-deep);
 
/* Report type icon — Zones */
background: var(--green-pale); color: var(--green);
 
/* Report type icon — Devices */
background: var(--amber-pale); color: var(--amber);
 
/* PDF format badge */
background: #fee2e2; color: #dc2626;
 
/* Excel format badge */
background: #dcfce7; color: #16a34a;
 
/* CSV format badge */
background: #fef3c7; color: #d97706;
 
/* Download button */
color: var(--green); /* #16a34a */
 
/* Active period filter chip */
background: var(--purple-pale); border: 1.5px solid var(--purple-deep);
 
/* Page title */
font-size: 2rem; font-weight: 700; color: var(--text-primary);
 
/* Report type name */
font-size: 1rem; font-weight: 700; color: var(--text-primary);
 
/* Description */
font-size: 0.875rem; color: var(--text-muted);
 
/* Report types grid */
display: grid; grid-template-columns: repeat(2, 1fr); gap: 16px; padding: 24px;
```
 
## Objective
Allow the administrator to generate and download system reports (alerts, users, zones, devices) in different formats (PDF, Excel, CSV) with period and date-range filters.
 
## Structure (top to bottom)
1. AdminLayout: Sidebar + Topbar
2. Title: "📋 Report Management"
3. "Generate Report" card:
   - Period selector: `app-period-badge` + custom date inputs
   - 2×2 grid of report type cards:
     - 🔔 Alerts Report (filters: status, type, period)
     - 👥 Users Report (filters: status, role, registration period)
     - 🗺️ Zones Report (filters: risk level)
     - 📱 Devices Report (filters: connection status)
   - Each card: colored icon + name + description + available format badges (PDF/Excel/CSV) + "⬇️ GENERATE AND DOWNLOAD" button
4. "Generated reports history" card:
   - Table: Type | Period | Format | Generation date | Actions [⬇️]
   - Pagination
## Components used
- `app-card` — generation and history cards
- `app-period-badge` with `conDropdown: true` — global period selector
- `app-button` variant `primary`, size `sm` — "Generate and download" on each card
- `app-spinner` size `sm`, inline — generating report (may take a while)
- `app-chip` — format badges (PDF/Excel/CSV)
## States
- **ready:** Grid of report cards + empty history or with records
- **generating:** Button with spinner + "Generating..." text + indeterminate progress bar
- **downloading:** Green "Download started" toast
- **no-history:** "No reports generated yet" + 📋 icon
- **generation-error:** Red toast "Error generating the report. Please try again."
- **download-error:** Red toast "The file could not be downloaded."
## Constraints
- **Mobile < 760px:** 1-column grid; collapsed sidebar
- **Tablet 760–1024px:** 2-column grid; compact history table
- **Desktop > 1024px:** 2×2 grid; full history table
- **WCAG AA:** Buttons with descriptive `aria-label` ("Generate alerts report in PDF"); spinner with `aria-label="Generating report"`; history table with `<caption>`; format badges with visible text; toasts with `role="status"` / `role="alert"`
- **Dark mode:** Dark tokens active
## Prompt ready for Google Stitch
> Design the Report Management screen of AlertaMujer Web in Angular for the Admin role. Web platform, AdminLayout with sidebar and topbar. Background `#f5f4ff`. The screen has the title "📋 Report Management" in `2rem 700 #1e1a3c` and two white cards with `border: 1.5px solid #c4b5fd` and `border-radius 12px`: (1) a "Generate Report" card with an `app-period-badge` in the header and a 2×2 grid of report type cards, each with `border-radius 12px`, hover `#f0eeff` and border `#7c3aed`, an icon on a colored background (🔔 Alerts in `#fee2e2`/`#dc2626`, 👥 Users in `#ede9fe`/`#7c3aed`, 🗺️ Zones in `#dcfce7`/`#16a34a`, 📱 Devices in `#fef3c7`/`#d97706`), name in `1rem 700`, description in `0.875rem #6b7280`, available format badges (PDF in red, Excel in green, CSV in amber) and a "⬇️ GENERATE AND DOWNLOAD" button with a loading state with a spinner; (2) a "Report history" card with a table with the columns Type | Period | Format | Date | Download, with a ⬇️ button in `#16a34a` per row and pagination. The empty state shows "No reports generated yet". On mobile the grid collapses to 1 column. Complies with WCAG AA.
 