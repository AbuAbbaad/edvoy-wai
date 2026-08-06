# INTERACTION_GUIDELINES.md

**Product:** Edvoy Workforce Intelligence
**UX Version:** 1.0 (Frozen)
**Status:** Engineering source of truth for behaviour. Patterns below are as built; do not vary them per-screen.

---

## 1. Navigation

- **Structure:** a persistent left **rail** grouped into sections (Dashboards, Attendance insights, Workflow, Import Centre, Administration). Items are filtered by role — a user only sees what their role permits.
- **Home per role:** manager → My team; admin → Organisation; hr → Import Centre; readonly → Organisation.
- **Active state:** the current rail item is highlighted (`--brand-soft` bg, `--brand-ink` text, 600 weight).
- **Breadcrumbs:** a trail builds as the user drills into sub-screens (profile, import detail, review workflow, etc.). Back-navigation returns to the exact prior screen and subject.
- **Focus on navigate:** on every route change, focus moves to the screen's `<h1>` so keyboard/screen-reader users land in the right place.
- **Guarded navigation (frozen rule):** if a user reaches a screen their role can't access, the app **announces** the redirect ("That screen isn't available for your role — returned to your home view") and returns them home. **Never redirect silently.**
- **Live badges:** the rail shows counts where action is waiting (pending access requests on Users & admins; open reviews on Review workflow).

## 2. Click Behaviour

- **Primary vs secondary:** one primary (`.btn.primary`) affirmative action per group; destructive actions use `.btn.danger`, never primary.
- **Drill-through:** clicking a KPI card, chart bar, or table row that represents a population navigates to that population (or its detail). These carry `role="button"`/clickable-row semantics and a hover affordance.
- **Row actions:** per-row `QuickActions` menu (Open profile, Review & notes, View heatmap). Actions the role can't perform are **omitted from the menu**, not shown-then-blocked.
- **No dead controls (frozen principle):** every clickable control resolves to a result, a confirmation, a disabled state with a reason, or is hidden when permission/flag forbids it. A visible-but-inert control is a defect.
- **Idempotent re-click:** clicking the current screen/subject is a no-op that scrolls to top, not a re-navigation.

## 3. Hover Behaviour

- **Buttons/nav/rows:** subtle background shift on hover (`--surface-2`, or `--brand-soft` for clickable rows), `.12s` transition.
- **Charts:** hovering a `Spark`/`TrendChart` reveals a tooltip (bucket label + value), a crosshair line, and an emphasised data point. The tooltip layers above the chart and is never clipped by its panel.
- **Bars:** drillable bars show a pointer and a "View employees behind this bar" affordance.
- **Popovers are click, not hover:** metric explanations (formulas) open on click of an info button, so they're reachable by keyboard and touch — hover is not required to obtain information.

## 4. Keyboard Navigation

- **Focus ring:** always visible — `2px solid --brand`, `2px` offset (`:focus-visible`). Never suppressed.
- **Skip link:** jumps to main content.
- **Operable controls:** clickable KPIs, bars, and rows expose `tabIndex=0`, `role="button"`, and activate on **Enter/Space**.
- **Selects & inputs:** native, fully keyboard-operable; sortable table headers are focusable and toggle on activation.
- **Modals:** trap focus while open; **Escape** closes; focus returns to a sensible point on close.
- **Menus/popovers:** reachable and dismissible by keyboard.
- *(Global shortcuts / command palette are a build-phase enhancement, not part of the frozen contract.)*

## 5. Confirmation Dialogues

Required before any irreversible or side-effectful action. Each confirmation **names the impact**:

- **Roll back an import:** "Roll back N rows" with the counts and effect stated; admin-only.
- **Reject all & cancel (import):** "Discard this import? Nothing has been saved yet." — framed as a safe abort.
- **Approve access request:** an explicit role must be chosen (default Read-Only) before granting.
- **Policy save:** effective date is explicit; the change records old → new.

Rules: the affirmative/destructive button carries the correct variant (`.primary` for safe, `.danger` for destructive); Cancel is always available; confirming writes an audit entry where the action is governed.

## 6. Notifications

- **Toasts** (`ToastHost`, via `window.__toast`) confirm completed actions and explain outcomes ("Access approved for … as Read-Only · user created, recorded in audit log"). They are transient and non-blocking.
- **Banners** (`Banner`) are persistent, in-context messages: `info` (coverage notes, requests-waiting), `warn` (safeguards), `err` (failed import with reason). Banners may carry a single action.
- **Rule:** a notification states *what happened* and, for errors, *what is safe*. Never a bare code. Governed actions that toast "recorded in audit log" must actually write the audit entry.

## 7. Import Workflow

A guided four-step wizard with a visible step indicator (`ImportSteps`):

1. **Upload** — pick type, download template (real CSV: headers + one example, no employee data), select `.xlsx/.csv` file.
2. **Preview & map** — header detection + aliases; map source columns to fields; preview rows.
3. **Validate** — summary of valid / updated / warnings / rejected; resolve quarantine; download rejected rows (with a reason per row). Commit **Import valid rows**, or **Reject all & cancel** (safe abort, confirmed, nothing written).
4. **Process** — phase states: Queued → Validating → Importing → Calculating metrics → Completed / Completed with warnings / Failed.

**Post-import (Import Detail):** View imported records (read-only, scoped, rows drill to day evidence); Compare with previous (diff vs the previous successful import of the same type; "no differences" when identical); Reprocess (re-run without re-upload, audited); Roll back (reverse a committed import, named impact, admin-only, audited).

**Rules:** nothing is written before commit; imports are idempotent; a failed import quarantines the whole bad sheet rather than guessing (e.g. impossible dates), with an explaining banner.

## 8. Drill-down Behaviour

- **Explainability principle:** every aggregate is traceable to the records behind it. Dashboards → insight lists → employee profile → the specific day's punch evidence.
- **Entry points:** KPI cards, chart bars, and table rows drill to their population or detail. Charts that represent a population are drillable where feasible (e.g. remote-by-location bars → remote list; review-outcome bars → filter the log by that action).
- **Breadcrumb + back:** drill-downs push a breadcrumb; back returns to the exact origin (screen + subject). Drill-down screens are part of the "allowed" set for the originating role, so a permitted drill never bounces.
- **Day evidence:** the terminal drill-down is `DayEvidenceModal` — the punches/status for one employee-day.

## 9. Modal Behaviour

- **Base:** all dialogs use `Modal` — overlay, focus-in on open, **Escape** and click-outside to close, a titled header, and a footer action region.
- **When to use a modal:** a focused sub-task or confirmation that shouldn't lose the underlying context — approvals, add/edit user, roll-back/compare/imported-records, quarantine resolution, review trail, export, day evidence.
- **Content rules:** read-only comparison/record modals never offer edit affordances; permissioned actions (roll back, continue review) are hidden inside the modal for roles that lack them; private notes are shown only to the authoring manager.
- **Stacking:** one modal at a time; a confirmation replaces rather than stacks where possible.

---

*End of INTERACTION_GUIDELINES.md — Edvoy Workforce Intelligence UX v1.0 (Frozen).*
