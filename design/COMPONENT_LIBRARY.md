# COMPONENT_LIBRARY.md

**Product:** Edvoy Workforce Intelligence
**UX Version:** 1.0 (Frozen)
**Status:** Engineering source of truth. Component names, props, and behaviours are as built in the frozen prototype. Do not rename or redesign; extend only through change control.

Conventions: all components are React function components using classic JSX. State is in-memory (`useState`/`useEffect`) — the frozen prototype has no persistence layer; persistence is a build-phase concern that must not change these component contracts. Navigation is done via a `nav(key, arg)` function passed as a prop or exposed globally as `window.__nav`. Toasts via `window.__toast`. Current role via `window.__role`.

Components are grouped: **Primitives**, **Data display**, **Charts**, **Overlays/Modals**, **Navigation/Shell**, **Domain widgets**.

---

## PRIMITIVES

### Button (`.btn` class — not a component)
- **Purpose:** all clickable actions.
- **Properties:** standard `<button>` props; classes `.btn` + `.primary|.ghost|.danger` + optional `.sm`; `onClick`, `disabled`.
- **Behaviour:** `.12s` transition; disabled → `opacity:.5`, not-allowed.
- **Variants:** Secondary (default), Primary, Ghost, Danger, Small.
- **Usage:** `<button className="btn primary" onClick={fn}>{Ico.check} Save</button>`
- **Accessibility:** icon-only buttons require `aria-label`; keyboard-native.

### Badge
- **Purpose:** compact status label.
- **Properties:** `kind` (`b-present|b-review|b-problem|b-remote|b-leave|b-neutral`), `children` (label), optional icon/dot.
- **Behaviour:** static; colour from token pair.
- **Variants:** by `kind`; specialised `ReviewBadge` (review status) and `statusPill()` (import status).
- **Usage:** `<Badge kind="b-present">Active</Badge>`
- **Accessibility:** always includes a text label; colour is reinforcement only.

### Checkbox
- **Purpose:** bulk-select rows, boolean toggles.
- **Properties:** `checked`, `onChange`, optional `label`.
- **Behaviour:** controlled.
- **Variants:** standalone / in table header (select-all) / in row.
- **Accessibility:** label association; keyboard toggle.

### Trend
- **Purpose:** show a metric's change vs the comparison period.
- **Properties:** `v` (signed number), `invert` (bool — for metrics where up is bad), `suffix`.
- **Behaviour:** `v===0` → flat "0 vs last month"; sign + arrow otherwise; `invert` flips the good/bad colour without changing the number.
- **Variants:** up / down / flat; inverted.
- **Accessibility:** conveys direction by arrow + text, not colour alone.

### QuickActions
- **Purpose:** per-row action menu (kebab) in tables/lists.
- **Properties:** `items:[{icon,label,onClick}]`.
- **Behaviour:** opens a small menu; items are gated by role by conditionally composing the array (e.g. "Review & notes" excluded for read-only).
- **Accessibility:** menu items are buttons with labels; keyboard reachable.

### EmpCell
- **Purpose:** consistent employee identity cell (name + id/sub, avatar seed).
- **Properties:** `e` ({name,id,title}), `seed` (avatar colour index).
- **Behaviour:** static display; colour from the `AV` avatar palette.
- **Usage:** inside `td` of people tables.
- **Accessibility:** text-based; avatar is decorative.

---

## DATA DISPLAY

### KPI
- **Purpose:** headline metric tile.
- **Properties:** `icon`, `iconStyle`, `label`, `value`, `unit`, `trend`, `invert`, `prev`, `onClick`, `info` ({title,body}), `note`.
- **Behaviour:** if `onClick` present → `.clickable`, `role="button"`, `tabIndex=0`, Enter/Space activate, hover affordance. `info` renders a `Popover` (formula/explanation) in the top-right. `note` renders a hint line under the value (e.g. filter-unavailable).
- **Variants:** static / clickable (drill-down) / with-note / with-trend.
- **Usage:** `<KPI icon={Ico.clockAlert} label="Frequently late" value={14} onClick={()=>nav("late")} info={{title,body}}/>`
- **Accessibility:** button semantics when interactive; label always present.

### Panel (`.panel` structure — composed, not a single component)
- **Purpose:** primary content container (header + body).
- **Structure:** `.panel > .panel-head (.panel-title, .panel-sub, .ph-right) + .panel-body[.flush]`.
- **Behaviour:** does not clip overflow (popovers escape); `.flush` clips its own corners for edge-to-edge tables.
- **Usage:** wrap charts, tables, forms, definition lists.

### InsightPage
- **Purpose:** the shared template for all four attendance insight lists (late, short hours, absence, remote).
- **Properties:** `title, desc, scope, threshold, columns, rows, nav, defaultSort, formula, empty, coach, reportType, toast`.
- **Behaviour:** renders header + methodology/threshold explainer + sortable table + coach note + export; row actions include Open profile, Review & notes (role-gated), View heatmap; supports bulk select + export.
- **Variants:** configured per insight type via props.
- **Usage:** `FreqLate`, `FreqShort`, `AbsenceReview`, `RemoteAnalysis` are thin wrappers supplying props.
- **Accessibility:** sortable `th` are keyboard operable; row drill-downs reachable.

### InsightPreview
- **Purpose:** condensed insight list embedded on a dashboard (top N).
- **Properties:** rows, nav, type.
- **Behaviour:** links through to the full `InsightPage`.

### FilterBar
- **Purpose:** shared filter + scope + date-range control across analytics screens.
- **Properties:** `scope`, `value` (filter object), `onChange`, `savedViews`.
- **Behaviour:** hosts the `DateRangePicker`, dropdown filters (dept, sub, location, manager, shift, employee), saved-view chips, active-filter chips (excludes range fields), and reset (reset preserves the default range). Filters are access-scoped.
- **Accessibility:** labelled selects; chips removable by keyboard.

### DateRangePicker
- **Purpose:** the customisable reporting-period control (frozen §17a).
- **Properties:** `value` ({range,cStart,cEnd}), `onChange`.
- **Behaviour:** button shows current range label + sub-range; menu lists presets **Today, Yesterday, This week, This month (default), Last 3 months**, plus **Custom range** (From/To date inputs + Apply). Closes on outside click. Calendar-aligned windows.
- **Variants:** preset selected / custom active.
- **Accessibility:** `aria-haspopup`, `aria-expanded`, `role="listbox"`/`option`, `aria-selected`.

### Heatmap / MiniCalendar / MiniBar
- **Purpose:** compact attendance visualisations (team heatmap; per-employee month calendar; inline bars).
- **Behaviour:** cells pair colour with a letter/status code; cells drill to `DayEvidenceModal`.
- **Accessibility:** never colour alone; cells keyboard-activatable where interactive.

---

## CHARTS

### Spark
- **Purpose:** line/area trend with hover detail.
- **Properties:** `data[]`, `color`, `height`, `label`, `fmt`, `labels[]`, `summaryOnly`, `unit`.
- **Behaviour:** SVG line+gradient; HTML hover hit-areas show a tooltip (label+value), crosshair, emphasised point; `summaryOnly` (or `<2` points) renders a single value + "too short a range for a trend" instead of a fake line.
- **Variants:** trend line / summary value.
- **Accessibility:** `role="img"` + descriptive `label`; focusable per-point segments with `aria-label`.

### TrendChart
- **Purpose:** range-aware wrapper over `Spark` (frozen §17b bucketing).
- **Properties:** `base[]` (weekly base series), `range` (resolved range), `color`, `unitFmt`, `metricLabel`, `height`.
- **Behaviour:** calls `bucketTrend(base,range)` to re-bucket to hourly/daily/weekly/monthly; labels the bucket unit + range; delegates rendering to `Spark`.
- **Usage:** every dashboard trend (attendance, absence, missing-punch, late, effective hours).
- **Accessibility:** inherits `Spark`; aria label describes unit + series.

### BarChart
- **Purpose:** horizontal comparison bars (departments, locations, review outcomes).
- **Properties:** `rows:[{label,v,color}]`, `unit`, `onBar`.
- **Behaviour:** proportional fills; `onBar(row)` makes bars drillable (pointer, button role, Enter/Space).
- **Variants:** static / drillable.
- **Accessibility:** drillable bars carry `aria-label` "<label>: <v>. View employees".

---

## OVERLAYS / MODALS

### Modal
- **Purpose:** base modal shell for all dialogs.
- **Properties:** `title`, `sub`, `children`, `onClose`, `footer`, `wide`.
- **Behaviour:** overlay; focus moves in on open; Escape closes; click-out closes; `footer` region for actions.
- **Variants:** default / `wide`.
- **Accessibility:** focus trap, Escape, labelled by title.

### Popover
- **Purpose:** inline explanation (metric formulas, methodology).
- **Properties:** `title`, `children`, `left` (align), `icon`.
- **Behaviour:** toggled by an info button; stops propagation; positioned (escapes panel because panels don't clip).
- **Accessibility:** trigger has `aria-expanded` and a descriptive `aria-label` ("How this is calculated: <title>").

### DayEvidenceModal
- **Purpose:** show the punch/day evidence behind a heatmap cell or profile day (explainability).
- **Properties:** the day/employee context, `onClose`.
- **Behaviour:** read-only detail; opened from heatmap, profile, mini-calendar.

### ExportModal
- **Purpose:** controlled export dialog.
- **Properties:** `reportType`, `count`, `filters`, `scope`, `onClose`, `toast`.
- **Behaviour:** records scope/filters/count; respects private-note exclusion (Review Log export labelled accordingly). In the frozen prototype it emits a toast; real file generation is a build-phase wiring point that must keep this dialog contract.
- **Accessibility:** modal semantics.

### Import & governance modals
- **ApproveRequestModal** — admin picks the role to grant (default Read-Only) on approving an access request; writes audit; creates the user.
- **AddUserModal** — admin creates a user with identity + selected role; `@edvoy.com` validation.
- **ImportedRecordsModal** — read-only, access-scoped table of rows an import inserted/updated; rows drill to day evidence.
- **CompareImportModal** — diff vs the previous successful import of the same type (added/updated/unchanged/removed); disabled when no prior import; "no differences" when identical.
- **RollbackModal** — named-impact confirmation to reverse a committed import; permission-gated (HR Operator cannot).
- **QuarantineModal** — resolve a quarantined validation issue during import.
- **ReviewTrailModal** — full audit trail of a review; shows private note only to the authoring manager, withheld otherwise; "Continue review" hidden for read-only.
- **Common contract:** built on `Modal`; confirmation before irreversible/side-effect actions; write an audit entry via `auditLog()` on commit; role-gated where the spec requires.

---

## NAVIGATION / SHELL

### App
- **Purpose:** root — holds routing, role, sign-in, breadcrumb trail, and renders shell + active screen.
- **State:** `signedIn, denied, role (manager|admin|hr|readonly), screen, arg, trail`.
- **Behaviour:** builds the role-filtered rail from `NAV_GROUPS`; computes the drill-down-aware `allowed` set; guards unreachable screens with an **announced** redirect (no silent bounce); exposes `window.__nav/__toast/__role`; moves focus to `<h1>` on route change.

### Login / AccessDenied
- **Login** — Google-styled sign-in (simulated); routes to role home. **AccessDenied** — for authenticated-but-unauthorised users; a "Request access" form capturing **identity + optional justification only (no role)**; routes the request to the admin queue.

### Rail / Topbar (within App)
- Role-filtered navigation groups; live badges (pending requests on Users; review count on Workflow). Topbar carries the breadcrumb and the **Active-role** pill (`switchRole`).

### ToastHost / Loading / EmptyState / ErrorState / Banner
- Cross-cutting feedback primitives (see DESIGN_SYSTEM §11–13). `Banner` props: `kind` (`warn|info|err`), `icon`, `children`, `action`.

### ImportSteps
- **Purpose:** the import wizard step indicator (Upload → Preview & map → Validate → Process).
- **Properties:** `active` (step index).

---

## DOMAIN WIDGETS

These compose the primitives into screen-level regions and are documented per-screen in SCREEN_SPECIFICATION.md: `ManagerDashboard`, `OrgDashboard`, `FreqLate`, `FreqShort`, `AbsenceReview`, `RemoteAnalysis`, `Heatmap`, `Profile`, `ReviewWorkflow`, `ReviewLog`, `ImportCentre`, `ImportUpload`, `ImportPreview`, `ImportValidate`, `ImportProcess`, `ImportHistory`, `ImportDetail`, `PolicySettings`, `HolidayPremiseSettings`, `UserManagement`, `SystemHealth`, `AuditLog`.

Each is a screen-level component receiving `nav` (and `role`/`arg` where relevant). They must not be reused as sub-widgets — they are screens.

---

*End of COMPONENT_LIBRARY.md — Edvoy Workforce Intelligence UX v1.0 (Frozen).*
