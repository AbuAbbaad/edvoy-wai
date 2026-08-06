# SCREEN_SPECIFICATION.md

**Product:** Edvoy Workforce Intelligence
**UX Version:** 1.0 (Frozen)
**Status:** Engineering source of truth. Screens, permissions, and rules are as built in the frozen prototype.

**Roles:** `manager` (Reporting Manager), `admin` (Administrator / Super Admin), `hr` (HR Data Operator), `readonly` (Read-Only Viewer). Access = intersection of role permission **and** hierarchy scope; in production this is server-enforced (build-phase). Home screens: manager → My team; admin → Organisation; hr → Import Centre; readonly → Organisation.

**Global business rules (apply to every analytics screen):**
- "No row = no data, **never** absent." Missing data shows as a coverage gap, not absence.
- Confirmed vs Potential absence are always separated.
- Effective Hours is the authoritative worked-time measure.
- Frequency thresholds use eligible days as the denominator, with small-denominator suppression.
- Remote is neutral unless it breaches an approved arrangement.
- Default reporting period is This month (month-to-date); adjustable via the date-range control; trends re-bucket to the selected range.
- Business rules/thresholds are configurable and effective-dated; changing them never changes their meaning.

Each screen below documents: **Purpose · Layout · Widgets · Navigation · Actions · Permissions · Validations · Error States · Loading States · Business Rules.**

---

## 1. Login
- **Purpose:** authenticate the user (Google domain sign-in — simulated in v1.0).
- **Layout:** two-column; brand aside + sign-in panel (aside hidden ≤900px).
- **Widgets:** sign-in button; product framing.
- **Navigation:** on success → role home; unauthorised → Access Denied.
- **Actions:** Sign in.
- **Permissions:** public (pre-auth).
- **Validations:** (build-phase) domain restriction to `@edvoy.com`; email must match an active employee.
- **Error States:** sign-in failure message.
- **Loading States:** sign-in progress animation.
- **Business Rules:** only active employees with a valid domain account may enter; identity binding is server-side at build.

## 2. Access Denied
- **Purpose:** path for authenticated-but-unauthorised users to request access.
- **Layout:** centered message + request form.
- **Widgets:** message; **optional justification field**; "Request access" button; prior-request status. **No role selector.**
- **Navigation:** submits into the admin pending-request queue.
- **Actions:** Submit access request (identity + optional justification only).
- **Permissions:** authenticated, unauthorised.
- **Validations:** justification optional; no role is captured from the applicant.
- **Error States:** submission failure toast.
- **Loading States:** submit feedback.
- **Business Rules (frozen §3a):** the applicant never chooses a role; an administrator assigns it on approval (default Read-Only).

## 3. Manager Dashboard ("My team")
- **Purpose:** a reporting manager's home — who on their line needs attention.
- **Layout:** page head → FilterBar (with date-range) → coverage banner(s) → KPI grid → charts (on-time by dept trend, etc.) → insight previews.
- **Widgets:** KPI cards (needs review, late, short hours, absence, remote); `TrendChart`; `InsightPreview` lists; coverage banner.
- **Navigation:** KPI cards and previews drill to insight lists / review workflow / review log; employees → Profile.
- **Actions:** filter, change date range, open reviews, drill down.
- **Permissions:** `manager`, `admin`. Scope = own reporting line.
- **Validations:** filters scoped to access.
- **Error States:** `ErrorState` (metric engine) with retry; failed regions isolated.
- **Loading States:** `Loading` in panels; `empty`/`incomplete` states for no/partial data.
- **Business Rules:** all global rules; scope limited to direct (and, at build, indirect) reports.

## 4. Organisation Dashboard
- **Purpose:** whole-organisation attendance overview (admin/read-only home).
- **Layout:** as Manager Dashboard, org scope; adds an **"access requests waiting"** banner (admin only) and an unmapped-employee banner.
- **Widgets:** KPI grid (recomputes under active filter; aggregate rates show "—" with a hint when a filter is active); comparison `BarChart`s (drillable); `TrendChart`s; coverage warnings.
- **Navigation:** cards/bars drill to insight lists; requests banner → Users & admins.
- **Actions:** filter, change range, drill down, review requests (admin).
- **Permissions:** `admin`, `readonly`. Read-only is view-only.
- **Validations:** filters scoped; range ≠ data filter (doesn't dim aggregate KPIs incorrectly).
- **Error States:** as Manager Dashboard.
- **Loading States:** as Manager Dashboard.
- **Business Rules:** all global rules; unmapped attendance rows are surfaced, never silently attributed.

## 5–8. Insight Lists — Frequently Late / Below Required Hours / Absence Review / Remote Attendance
*(One shared template, `InsightPage`; four configured instances.)*
- **Purpose:** the population flagged by one metric, with the methodology shown.
- **Layout:** page head → threshold/methodology explainer → sortable table → coach note → export.
- **Widgets:** sortable `table.data`; per-row `QuickActions` (Open profile, Review & notes [role-gated], View heatmap); bulk select; `ExportModal`.
- **Navigation:** row → Profile; Review & notes → Review Workflow; heatmap link.
- **Actions:** sort, filter (via dashboard scope), bulk-select, export, drill to person.
- **Permissions:** `manager`, `admin`, `readonly` (all four lists). Read-only sees no "Review & notes".
- **Validations:** threshold denominators use eligible days; suppressed where too small.
- **Error States:** empty state ("no one meets this threshold") distinct from no-data.
- **Loading States:** table skeleton / panel loading.
- **Business Rules:** metric-specific — Late (>30min after start+grace, fixed shifts, ≥20% eligible); Short hours (>20% of attended below required); Absence (Confirmed vs Potential separated; >3 confirmed unexplained); Remote (>75% or off approved arrangement, neutral).

## 9. Team Heatmap
- **Purpose:** calendar grid of attendance status across the team.
- **Layout:** legend + scrollable heatmap grid.
- **Widgets:** `Heatmap` cells (colour + letter code); legend.
- **Navigation:** a cell → `DayEvidenceModal` (punch evidence).
- **Actions:** hover/focus a cell; open day evidence.
- **Permissions:** `manager`, `admin`, `readonly`.
- **Validations:** blank = no data (not absent).
- **Error States:** empty/coverage state.
- **Loading States:** grid skeleton.
- **Business Rules:** status codes follow the attendance-day classification; colour never sole signal.

## 10. Employee Profile (Detail)
- **Purpose:** one employee's attendance detail and evidence.
- **Layout:** header (identity, review badge, Review & notes) → tabs (daily / trends) → mini-calendar + per-week `TrendChart`s + day list.
- **Widgets:** `EmpCell`, `MiniCalendar`, `TrendChart` (late minutes, effective hours), day rows, `DayEvidenceModal`.
- **Navigation:** "Review & notes" → Review Workflow (with this employee); days → evidence.
- **Actions:** switch tab, open day evidence, start a review (non-readonly).
- **Permissions:** `manager`, `admin`, `readonly`. **"Review & notes" hidden for `readonly`** (frozen fix — no dead control, no silent redirect).
- **Validations:** none (read surface) beyond scope.
- **Error States:** missing-data days rendered as gaps.
- **Loading States:** panel loading.
- **Business Rules:** Effective Hours authoritative; no-data ≠ absent; drill-down explainability required.

## 11. Review Workflow
- **Purpose:** conduct a review for a flagged employee and record it.
- **Layout:** queue/selected employee → status control + agreed action + follow-up date + notes → save.
- **Widgets:** status select, action dropdown, follow-up date, HR note, private note, Save.
- **Navigation:** Save → creates/updates a Review Log record; "View review log".
- **Actions:** select employee, set status/action/follow-up, write notes, Save review.
- **Permissions:** `manager`, `admin`. (Read-only cannot reach this screen; its entry points are hidden.)
- **Validations:** status required; follow-up date valid.
- **Error States:** save failure toast.
- **Loading States:** save feedback.
- **Business Rules:** "No action required" is a valid, non-negative outcome; private notes are never exported; each save writes an audit-trail entry.

## 12. Review Log
- **Purpose:** the filterable, exportable record of all reviews (hierarchy-scoped).
- **Layout:** KPI mini-dashboard (Total / Open / Overdue / Closed) → filters + scope label → table → outcomes `BarChart` → coach note.
- **Widgets:** clickable KPIs (Total clears filters); filters; `table.data`; drillable outcomes chart; `ReviewTrailModal`; `ExportModal`.
- **Navigation:** row → audit trail; outcome bar → filter by action; profile/continue-review links (non-readonly).
- **Actions:** filter, click KPI to filter/reset, open trail, export, continue review (non-readonly).
- **Permissions:** `manager`, `admin`, `readonly` (view-only; edit actions hidden for readonly). Manager scope = own line; admin = all.
- **Validations:** export excludes private notes.
- **Error States:** empty ("no reviews match these filters") distinct from no reviews.
- **Loading States:** table render.
- **Business Rules:** private notes visible only to the authoring manager; hierarchy scoping applies; audit trail per review.

## 13. Import Centre
- **Purpose:** entry to the guided import wizard; choose an import type.
- **Layout:** import-type cards + recent activity.
- **Widgets:** type cards (master, attendance, holiday, leave); `ImportSteps` indicator downstream.
- **Navigation:** a type → Upload step.
- **Actions:** select type; **Download template** (real CSV, headers + example).
- **Permissions:** `admin`, `hr`.
- **Validations:** template disabled if a type has no schema.
- **Error States:** —
- **Loading States:** template generation feedback.
- **Business Rules:** template carries expected headers only, no employee data.

## 14. Import — Upload
- **Purpose:** upload a source file for the chosen type.
- **Layout:** step indicator → type header + expected columns → file drop/select → Validate.
- **Widgets:** file input; expected-columns list; Download template; Validate / Back.
- **Navigation:** Validate → Preview & map; Back → Import Centre.
- **Actions:** upload, download template, proceed.
- **Permissions:** `admin`, `hr`.
- **Validations:** file type `.xlsx/.csv`.
- **Error States:** invalid file feedback.
- **Loading States:** upload/parse feedback.
- **Business Rules:** nothing is written at upload; commit happens later.

## 15. Import — Preview & Map
- **Purpose:** map source columns to fields; preview parsed rows.
- **Layout:** mapping grid + preview table.
- **Widgets:** column-mapping controls (header detection + aliases); preview.
- **Navigation:** → Validate.
- **Actions:** adjust mapping, proceed.
- **Permissions:** `admin`, `hr`.
- **Validations:** required fields mapped.
- **Error States:** unmapped-required warnings.
- **Loading States:** parse preview.
- **Business Rules:** saved mapping templates are a build-phase enhancement; the mapping step and contract are frozen.

## 16. Import — Validate
- **Purpose:** validate the batch before commit; resolve quarantine.
- **Layout:** validation summary (valid/updated/warnings/rejected) → quarantine list → commit / cancel.
- **Widgets:** summary counts; `QuarantineModal`; download rejected rows; **Import valid rows** / **Reject all & cancel**.
- **Navigation:** Import → Processing; Reject all & cancel → confirm → Import Centre.
- **Actions:** resolve quarantine, download rejects, import valid, cancel.
- **Permissions:** `admin`, `hr`.
- **Validations:** reject/warn/quarantine rules; commit only valid rows.
- **Error States:** per-row rejection reasons; failed-parse banner.
- **Loading States:** validation progress.
- **Business Rules:** "Reject all & cancel" is a **safe abort** (nothing written), confirmed, and audited — distinct from roll back.

## 17. Import — Processing
- **Purpose:** show commit + metric recalculation progress and outcome.
- **Layout:** phase progress → result summary.
- **Widgets:** phase states; result counts.
- **Navigation:** → Import Detail / Import History.
- **Actions:** view detail.
- **Permissions:** `admin`, `hr`.
- **Validations:** idempotency prevents double counting (mechanism is build-phase; contract frozen).
- **Error States:** failed import → explained banner (e.g. impossible dates quarantined).
- **Loading States:** Queued → Validating → Importing → Calculating metrics → Completed / Completed with warnings / Failed.
- **Business Rules:** updates preserve history; imports are idempotent.

## 18. Import History
- **Purpose:** list of all past imports.
- **Layout:** table of imports (type, file, counts, status, when).
- **Widgets:** `table.data`; new-import action.
- **Navigation:** a row → Import Detail.
- **Actions:** open detail, start new import.
- **Permissions:** `admin`, `hr`.
- **Validations:** —
- **Error States:** empty state.
- **Loading States:** table render.
- **Business Rules:** full history retained with hash, mapping version, counts.

## 19. Import Detail
- **Purpose:** one import's processing facts and row outcomes, with actions.
- **Layout:** header (id + status + actions) → Processing panel (def list) → Row outcomes `BarChart` → failure banner if failed.
- **Widgets:** definition rows (uploaded by/at, range, timings, mapping version, file hash); `BarChart`; **View imported records**, **Compare with previous**, **Reprocess**, **Roll back**.
- **Navigation:** records → `ImportedRecordsModal` (rows drill to day evidence); compare → `CompareImportModal`; roll back → `RollbackModal`.
- **Actions:** view records, compare, reprocess, roll back, download reports.
- **Permissions:** `manager` (view via drill-down), `admin`, `hr`. **Roll back is admin-only** (HR Operator cannot); hidden, not disabled, when forbidden.
- **Validations:** Compare disabled if no prior import of the type ("no differences" when identical).
- **Error States:** failed-import banner with reason.
- **Loading States:** record/compare modal skeletons.
- **Business Rules (frozen §12a):** every named control resolves to a result, confirm-then-result, disabled-with-reason, or permission-hidden state. Reprocess and roll back are audited; roll back reverses a committed import with named impact.

## 20. Policy & Shift Settings
- **Purpose:** configure attendance policy thresholds and shifts.
- **Layout:** editors for grace, late threshold, frequency %, required hours, short-hours measure, remote threshold, min-records; shift definitions; effective date + save.
- **Widgets:** numeric inputs, toggles, effective-date input, **Save & recalculate** (Saving → Recalculating → Saved).
- **Navigation:** stays on screen; writes audit.
- **Actions:** edit a policy, set effective date, save & recalculate.
- **Permissions:** `admin` only.
- **Validations:** numeric ranges + effective-date ordering checked before save; invalid input blocks save inline.
- **Error States:** recalculation failure → saved policy stands, retry offered.
- **Loading States:** Saving → Recalculating → Saved.
- **Business Rules:** thresholds configurable + effective-dated; **meaning never changes**, only the value; each change audited old → new.

## 21. Holiday & Premise Settings
- **Purpose:** manage location holidays and premises.
- **Layout:** location selector → holiday table → premise classification list.
- **Widgets:** location select; holiday table (date + name, remove); **Add holiday** (effective date + name); premise class controls.
- **Navigation:** stays on screen; writes audit.
- **Actions:** select location, add/remove holiday, set premise class.
- **Permissions:** `admin` only.
- **Validations:** date + name required to add; sorted insert.
- **Error States:** empty-state per location.
- **Loading States:** —
- **Business Rules:** holidays are location-scoped and affect eligible-day calculations; changes audited.

## 22. Users & Admin Management
- **Purpose:** manage users, roles, and pending access requests.
- **Layout:** super-admin safeguard banner → pending-requests queue → users table.
- **Widgets:** requests queue (identity + justification, no applicant role); **Approve…** (`ApproveRequestModal`, role picker default Read-Only) / **Decline**; **Add user** (`AddUserModal`); per-row **Edit role**.
- **Navigation:** approve/add/edit act in place; write audit.
- **Actions:** approve (with role), decline, add user, change role.
- **Permissions:** `admin` only.
- **Validations:** approval requires an explicit role choice; `@edvoy.com` email; **final super-admin cannot be removed**.
- **Error States:** empty queue state; validation messages.
- **Loading States:** action feedback toasts.
- **Business Rules (frozen §3a):** applicant supplies no role; admin assigns on approval; every add/role-change/approve/decline audited old → new.

## 23. System Health & Data Coverage
- **Purpose:** show data freshness, coverage gaps, and system status.
- **Layout:** coverage/health panels; gaps list.
- **Widgets:** coverage metrics; drill to gaps; Import Centre link.
- **Navigation:** gaps → Import Centre.
- **Actions:** drill into gaps.
- **Permissions:** `admin`, `hr`.
- **Validations:** —
- **Error States:** stale/missing-data warnings.
- **Loading States:** panel loading.
- **Business Rules:** low coverage is a data gap, never absence; monitoring/alerting is build-phase.

## 24. Audit Log
- **Purpose:** the record of governed actions.
- **Layout:** searchable table of audit entries (actor, action, entity, when).
- **Widgets:** search; `table.data`.
- **Navigation:** —
- **Actions:** search/filter.
- **Permissions:** `admin` only.
- **Validations:** —
- **Error States:** empty state.
- **Loading States:** table render.
- **Business Rules:** entries are written for approve/decline, add-user, role-change, import reprocess/rollback/cancel, policy change, holiday change. **Persistence + tamper-evidence are build-phase** — in v1.0 entries are live but in-memory.

---

*Screen count: 22 distinct screens (insight lists 5–8 share one template). Route keys: `manager, org, late, short, absence, remote, heatmap, profile, workflow, reviewlog, import, importUpload, importPreview, importValidate, importProcess, importHistory, importDetail, policy, holidays, users, health, audit`.*

*End of SCREEN_SPECIFICATION.md — Edvoy Workforce Intelligence UX v1.0 (Frozen).*
