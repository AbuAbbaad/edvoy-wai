# FRONTEND_ARCHITECTURE.md

**Product:** Edvoy Workforce Intelligence
**UX Version:** 1.0 (Frozen)
**Status:** Engineering source of truth. Describes the architecture of the frozen prototype and the target structure for the production build. The prototype is a single self-contained file; production must preserve the same **component boundaries, routing keys, prop contracts, and state semantics** while splitting into a real project structure.

---

## 1. Prototype vs Production

- **Prototype (frozen):** one HTML file — React 18 + Babel-standalone via CDN, classic JSX runtime, all components/CSS/data inline, **in-memory state only** (no router library, no persistence, no build step).
- **Production target:** a standard React app (Vite or Next.js), TypeScript, the same components split into files, tokens as CSS custom properties (or Tailwind theme), a real router, a data layer, and server-enforced permissions. **The frozen UX contracts below must not change during that port.**

---

## 2. Page Hierarchy

```
App (root: auth gate, role, routing, shell)
├── Login                         (pre-auth)
├── AccessDenied                  (authed, unauthorised)
└── Authenticated shell
    ├── Rail (role-filtered nav)
    ├── Topbar (breadcrumb + active-role pill)
    └── Screen (by route key)
        ├── Dashboards:      manager, org
        ├── Insights:        late, short, absence, remote, heatmap
        ├── Detail:          profile → (DayEvidenceModal)
        ├── Workflow:        workflow, reviewlog → (ReviewTrailModal)
        ├── Import:          import → importUpload → importPreview
        │                    → importValidate → importProcess
        │                    importHistory → importDetail
        │                    (ImportedRecordsModal, CompareImportModal,
        │                     RollbackModal, QuarantineModal)
        └── Administration:  policy, holidays, users (Approve/AddUser modals),
                             health, audit
```

## 3. Component Hierarchy

**Shell layer:** `App` → `Rail` + `Topbar` + active `Screen` + `ToastHost`.

**Screen layer (22 screens):** each screen is a top-level component receiving `nav` (and `role`/`arg` where relevant). Screens compose **domain widgets** and **primitives**; screens are never nested inside one another.

**Domain-widget layer:** `KPI`, `FilterBar`+`DateRangePicker`, `InsightPage`/`InsightPreview`, `TrendChart`→`Spark`, `BarChart`, `Heatmap`/`MiniCalendar`/`MiniBar`, `ImportSteps`.

**Primitive layer:** `Button` (class), `Badge`/`ReviewBadge`/`statusPill`, `Checkbox`, `Trend`, `QuickActions`, `EmpCell`, `Banner`, `EmptyState`, `Loading`, `ErrorState`, `Modal`, `Popover`.

**Modal layer:** all built on `Modal` — `DayEvidenceModal`, `ExportModal`, `ReviewTrailModal`, `ApproveRequestModal`, `AddUserModal`, `ImportedRecordsModal`, `CompareImportModal`, `RollbackModal`, `QuarantineModal`.

**Dependency rule:** Screens → Domain widgets → Primitives → tokens. Never the reverse; a primitive never imports a screen.

## 4. Routing

- **Mechanism (frozen):** a single `screen` state key selects the active component from a `screens` map; `arg` carries the drill-down subject (an employee, an import, an import type). No URL router in the prototype.
- **Route keys (stable — production URLs should map 1:1):**
  `manager, org, late, short, absence, remote, heatmap, profile, workflow, reviewlog, import, importUpload, importPreview, importValidate, importProcess, importHistory, importDetail, policy, holidays, users, health, audit`.
- **`nav(key, arg?)`** performs navigation: pushes a breadcrumb when entering a drill-down (`DRILLDOWN` set: `profile, workflow, importDetail, importUpload, importPreview, importValidate, importProcess`), clears the trail otherwise, sets `screen`+`arg`, scrolls to top. Exposed as `window.__nav`.
- **Permission model:** `ROLE_KEYS[role]` lists the base screens a role may reach; the drill-down-aware `allowed` set adds children reachable from those. A guard redirects (with an announcement) if the active screen isn't permitted. **Production must enforce this server-side; the client model is the UX contract, not the security boundary.**
- **Production mapping:** each route key → a real path (e.g. `/team`, `/org`, `/employee/:id`, `/import/:id`); `arg` → a route param or loader. Preserve breadcrumb/back semantics.

## 5. State Management

- **Local component state** (`useState`) for view state: filters, selected employee, modal open/close, wizard step, form fields.
- **Module-level mutable stores + pub/sub** for cross-screen shared data in the prototype: `REVIEW_LOG` (+ `useReviewLog`), `USERS` (+ `useUsers`), `REQUESTS` (+ `useRequests`), `AUDIT` (+ `useAudit`). Each exposes a subscribe hook and mutator (`saveReviewRecord`, `addUser`/`setUserRole`, `addAccessRequest`/`resolveAccessRequest`, `auditLog`). These simulate a backend and **re-render subscribers on change**.
- **Globals for cross-cutting access:** `window.__nav`, `window.__toast`, `window.__role` (used by deeply-nested row actions to avoid prop-drilling).
- **Frozen honesty note:** all state is **in-memory** — it resets on refresh. **Production replaces the module stores with a real data layer** (server + cache, e.g. React Query/RTK Query). The **hook contracts** (`useReviewLog()` etc. returning current data and re-rendering on change) should be preserved so components don't change.
- **Derived state:** metrics, buckets, and filtered sets are computed in render from source arrays (`applyFilters`, `resolveRange`, `bucketTrend`, `rangeCoverage`). Keep these as pure functions in the production data/utils layer.

## 6. Reusable Components

The frozen, reusable set (do not fork or duplicate):

- **Primitives:** Button (class), Badge/ReviewBadge/statusPill, Checkbox, Trend, QuickActions, EmpCell, Banner, EmptyState, Loading, ErrorState, Modal, Popover, ToastHost.
- **Data/domain:** KPI, FilterBar, DateRangePicker, InsightPage, InsightPreview, Heatmap, MiniCalendar, MiniBar, ImportSteps.
- **Charts:** Spark, TrendChart, BarChart.
- **Modals:** DayEvidenceModal, ExportModal, ReviewTrailModal, ApproveRequestModal, AddUserModal, ImportedRecordsModal, CompareImportModal, RollbackModal, QuarantineModal.

Each is documented in COMPONENT_LIBRARY.md with props/behaviour/variants/a11y.

## 7. Folder Structure (production target)

```
src/
├── main.tsx                     # entry
├── App.tsx                      # auth gate, routing, shell
├── routes.ts                    # route-key ↔ path ↔ screen map
├── theme/
│   ├── tokens.css               # :root design tokens (from DESIGN_SYSTEM.md)
│   └── globals.css              # base, focus, resets
├── components/                  # reusable primitives + domain widgets
│   ├── primitives/              # Button, Badge, Checkbox, Trend, Modal, Popover,
│   │                            #   Banner, EmptyState, Loading, ErrorState, ...
│   ├── data/                    # KPI, FilterBar, DateRangePicker, InsightPage, ...
│   ├── charts/                  # Spark, TrendChart, BarChart, Heatmap, MiniCalendar
│   └── modals/                  # all *Modal components
├── screens/                     # one file per screen (22)
│   ├── dashboards/              # ManagerDashboard, OrgDashboard
│   ├── insights/               # FreqLate, FreqShort, AbsenceReview, RemoteAnalysis, Heatmap
│   ├── employee/                # Profile
│   ├── workflow/                # ReviewWorkflow, ReviewLog
│   ├── import/                  # ImportCentre, ImportUpload, ImportPreview,
│   │                            #   ImportValidate, ImportProcess, ImportHistory, ImportDetail
│   └── admin/                   # PolicySettings, HolidayPremiseSettings,
│                                #   UserManagement, SystemHealth, AuditLog
├── lib/
│   ├── permissions.ts           # ROLE_KEYS, ROLE_HOME, allowed-set logic
│   ├── metrics.ts               # applyFilters, attendance metric fns (pure)
│   ├── daterange.ts             # DATE_RANGES, resolveRange, bucketTrend, rangeCoverage
│   └── audit.ts                 # audit entry shape + write
├── data/                        # data layer (replaces module stores)
│   ├── reviews.ts  users.ts  requests.ts  imports.ts   # hooks: useReviewLog, useUsers, ...
└── icons/Ico.tsx                # the frozen inline-SVG icon set
```

## 8. Frontend Coding Conventions

- **Language:** TypeScript in production; type every prop contract from COMPONENT_LIBRARY.md.
- **Components:** function components + hooks; one screen per file; PascalCase component names matching the frozen names exactly (routing and docs depend on them).
- **Styling:** tokens only — never hard-code a hex/size that exists as a token. Class names follow the prototype (`.btn`, `.panel`, `.kpi`, `table.data`, `.badge`, …) or a documented equivalent; keep semantic status classes (`b-present`, etc.).
- **State:** prefer local state; use the data-layer hooks for shared entities; do not reintroduce `window.__*` globals in production — replace with context/hooks, but preserve the same behaviour (nav, toast, current role).
- **Navigation:** always go through the routing layer (`nav`/router). Never mutate `screen` state ad-hoc; never navigate without updating the breadcrumb contract.
- **Permissions:** treat client checks as UX only; the server is the boundary. Gate controls by role by **composing them out** (hide) rather than rendering-then-blocking. Never redirect silently.
- **Accessibility:** every interactive non-button element gets `role`, `tabIndex`, key handlers, and a label; keep the skip-link, focus-to-`h1`-on-route, focus-visible ring, and modal focus-trap. Preserve "never colour alone".
- **Charts:** honest axes (start at 0, no truncation); too-few-points → summary value, not a fake line; label the bucket unit.
- **Audit:** every governed mutation writes an audit entry; a toast that claims "recorded in audit log" must actually write one.
- **No dead controls:** a rendered control must resolve to a result, confirm-then-result, disabled-with-reason, or be hidden by permission/flag.
- **Change control:** these documents are the frozen source of truth. Screen behaviour, route keys, prop contracts, and tokens change only through change control — not ad-hoc edits.

---

## 9. Frozen Constraints Carried to Build (not UX, but architecture-relevant)

The following are deferred to the build phase and must be implemented **behind the existing UX contracts** without changing them: real Google-domain auth + sessions; server-side, row-level permission enforcement; a persistence layer replacing the in-memory stores; durable, tamper-evident audit; pagination/virtualisation for 5,000+ employees; real export-file generation; effective-dated assignments and indirect-report resolution; the complete attendance-day status set; the data-correction/exception screens (net-new, will get a scoped design pass); and a one-time measured accessibility audit.

---

*End of FRONTEND_ARCHITECTURE.md — Edvoy Workforce Intelligence UX v1.0 (Frozen).*
