# User Management
 
**Route:** `/admin/usuarios`
**Access:** ADMIN
**Implementation status:** Implemented — `UserComponent` with lazy loading; consumes `UsersService` (getAll, getById, create, update, updateEstado, delete); paginated table with search and filters.
 
## Base style — design tokens
```css
/* Page background */
background: var(--bg-base); /* #f5f4ff */
 
/* Cards */
background: var(--bg-card);
border: 1.5px solid var(--border);   /* #c4b5fd */
border-radius: var(--dash-radius-card); /* 12px */
box-shadow: var(--dash-shadow-card);
 
/* Active status badge */
background: var(--green-pale); color: var(--green); /* #dcfce7 / #16a34a */
 
/* Inactive status badge */
background: #f3f4f6; color: var(--text-dim); /* #9ca3af */
 
/* Blocked status badge */
background: var(--red-pale); color: var(--red); /* #fee2e2 / #dc2626 */
 
/* Warning status badge */
background: var(--amber-pale); color: var(--amber);
 
/* Active filter chip */
background: var(--purple-pale); border: 1.5px solid var(--purple-deep);
color: var(--purple-deep);
 
/* Row hover */
background: var(--bg-card-hover); /* #f0eeff */ cursor: pointer;
 
/* Name in table */
font-size: 0.9rem; font-weight: 600; color: var(--text-primary);
 
/* Email / phone */
font-size: 0.875rem; color: var(--text-muted);
 
/* Page title */
font-size: 2rem; font-weight: 700; color: var(--text-primary);
 
/* Record counter */
font-size: 0.875rem; color: var(--text-muted);
 
/* Page padding */
padding: 24px; gap: 16px;
 
/* Table rows */
padding: 12px 16px;
```
 
## Objective
Allow the administrator to view, create, edit, activate/deactivate and delete user accounts (operator role) with real-time search and filtering by status.
 
## Structure (top to bottom)
1. AdminLayout: Sidebar + Topbar
2. Title: "👥 User Management"
3. "Filters and search" card:
   - Input 🔍 "Search by name, email, phone..." (300ms debounce)
   - Chips: All● / Active / Inactive / Blocked
   - `app-period-badge` — registration period
4. "Users (N records)" card with a "➕ NEW USER" button:
   - Paginated table: # | Name | Email | Phone | Status | Actions
   - Per-row actions: [✏️ Edit] [🗑️ Delete]
   - Click on name → detail modal with metrics
   - Pagination with "Page X / Y"
5. **Create/Edit User Modal:**
   - 2-col grid: Name + Surname | Age + Phone
   - Email | Password (on create only) | Status (radio: Active/Inactive)
6. **View Detail Modal:**
   - Personal data + Role + Registration date + Total/pending alerts + Devices
   - Buttons: ✏️ Edit / 🚫 Deactivate / 🔴 Block
7. **Confirm Delete Modal (danger)**
## Components used
- `app-card` — filters and table
- `app-chip` with `active: boolean` — status filters
- `app-period-badge` with `conDropdown: true` — registration period
- `app-button` variant `primary`, size `sm` — "➕ New user"
- `app-modal` size `md`, default — create/edit/detail
- `app-modal` size `sm`, danger — confirm deletion
- `app-spinner` size `sm`, inline — saving
## States
- **loading:** Table skeleton (5 rows)
- **ready:** Table with pagination
- **searching:** Table updates with 300ms debounce
- **filtered:** Active chips + updated counter
- **create-modal:** Empty form
- **edit-modal:** Pre-filled form
- **detail-modal:** Read-only view with metrics
- **saving:** Button with spinner
- **deleting:** Danger modal active
- **error:** Red toast
## Constraints
- **Mobile < 760px:** Per-user cards instead of a table; collapsed sidebar
- **Tablet 760–1024px:** Table without the phone column; compact search box
- **Desktop > 1024px:** Full table with all columns
- **WCAG AA:** Table with `<caption>`; search box with `aria-label` and `role="searchbox"`; buttons with a descriptive `aria-label` per user; modals with `role="dialog"`, `aria-modal="true"`, trapped focus; badges with visible text; pagination with `aria-label="Page X of Y"`
- **Dark mode:** Dark tokens active
## Prompt ready for Google Stitch
> Design the User Management screen of AlertaMujer Web in Angular for the Admin role. Web platform, AdminLayout with sidebar and topbar. Background `#f5f4ff`. The screen has the title "👥 User Management" in `2rem 700 #1e1a3c` and two white cards with `border: 1.5px solid #c4b5fd` and `border-radius 12px`: (1) a Filters card with a 🔍 input "Search by name, email, phone..." with debounce, multi-select chips (All, Active, Inactive, Blocked) with the active state in `#ede9fe`/`#7c3aed`, and an `app-period-badge`; (2) a paginated table card with the header "Users (1,234 records)" and a "➕ NEW USER" button, a table with the columns # | Name (0.9rem 600) | Email (0.875rem #6b7280) | Phone | Status | Actions, status badges (Active background `#dcfce7` text `#16a34a`, Inactive background `#f3f4f6` text `#9ca3af`, Blocked background `#fee2e2` text `#dc2626`), ✏️ and 🗑️ buttons per row, rows with hover `#f0eeff` and cursor pointer, pagination "Page X / Y". Create/edit modal with a 2-column grid for Name+Surname and Age+Phone, email, password (on create only) and status radio. Detail modal with data + metrics (alerts, devices) + Edit/Deactivate/Block actions. Danger modal for deletion. Complies with WCAG AA.
 