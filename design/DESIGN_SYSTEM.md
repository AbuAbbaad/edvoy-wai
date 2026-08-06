# DESIGN_SYSTEM.md

**Product:** Edvoy Workforce Intelligence
**UX Version:** 1.0 (Frozen)
**Status:** Engineering source of truth — do not redesign. Any change goes through change control.
**Source:** Extracted verbatim from the frozen prototype (`edvoy-workforce-intelligence-prototype.html`).

This document defines the visual system. Every value below is the exact token used in the frozen build. Engineering must port these as design tokens (CSS custom properties, Tailwind theme, or equivalent) without altering values.

---

## 1. Typography

**Font families**

| Role | Stack |
|---|---|
| UI / body | `'Inter', system-ui, sans-serif` |
| Monospace (numbers, IDs, codes, hashes) | `'IBM Plex Mono', ui-monospace, monospace` (token `--mono`) |

- Base body: `font-size: 14px; line-height: 1.5;` with `-webkit-font-smoothing: antialiased`.
- Numeric alignment: `.mono` and `.tnum` apply `font-variant-numeric: tabular-nums`. Use for all tabular numbers, currency, durations, IDs.

**Type scale (as used)**

| Usage | Size | Weight | Notes |
|---|---|---|---|
| Page title (`.page-title`) | ~20px | 700 | one per screen; receives focus on route change |
| Panel title (`.panel-title`) | 14.5px | 600 | card headers |
| KPI value (`.kpi-value`) | 26px | 700 | `letter-spacing:-.02em` |
| KPI label (`.kpi-label`) | 12.5px | 500 | `--ink-2` |
| Body / default | 14px | 400 | |
| Table header (`th`) | 11px | 600 | uppercase, `letter-spacing:.03em`, `--ink-3` |
| Table cell | 13px | 400 | |
| Nav item | 13.5px | 500 | 600 when active |
| Nav group label | 10.5px | 600 | uppercase, `letter-spacing:.07em`, `--ink-3` |
| Small / meta (`.small`, `.muted`) | 11–12.5px | 400–500 | secondary text |
| Badge | 11.5px | 600 | |
| Button | 13px (12px `.sm`) | 600 | |

**Rules**

- Exactly one `<h1 class="page-title">` per screen.
- Uppercase is reserved for table headers and nav group labels only.
- Never introduce a new size outside this scale; compose from existing steps.

---

## 2. Spacing

The system uses an informal 4px-based rhythm. Common values observed and to be standardised:

| Token (recommended) | Value | Used for |
|---|---|---|
| space-1 | 4px | icon gaps, tight inline |
| space-2 | 6–8px | button padding (y), badge padding, chip gaps |
| space-3 | 10–12px | control gaps, panel-head padding |
| space-4 | 14–16px | panel-body padding, card padding |
| space-5 | 18px | panel bottom margin, grid gaps |
| space-6 | 20–24px | section spacing, page gutters |

**Anchor values (do not change):**

- `.panel-body` padding: `16px`
- `.panel-head` padding: `14px 16px`
- Panel bottom margin: `18px`
- `.filterbar` padding: `12px 14px`; margin-bottom `18px`
- Rail width: `236px`; rail padding `10px 10px 20px`
- Topbar height: `56px`; padding `0 24px`
- KPI card padding: `14px 14px 13px`
- Grid gaps: KPI grid `12px`; two/three-column grids `18px`

**Radii**

| Token | Value | Applied to |
|---|---|---|
| `--radius` | 10px | panels, cards, filter bar, modals |
| `--radius-sm` | 7px | buttons, nav items, inputs (large), small containers |
| (fixed) | 20px | pills, badges, nav badges |
| (fixed) | 5–6px | bar-chart tracks, small inputs |

**Elevation**

| Token | Value | Applied to |
|---|---|---|
| `--shadow` | `0 1px 2px rgba(28,36,52,.04), 0 1px 3px rgba(28,36,52,.06)` | panels, cards, filter bar (resting) |
| `--shadow-md` | `0 4px 16px rgba(28,36,52,.10)` | popovers, elevated surfaces |
| (fixed) modal | `0 12px 34px rgba(20,30,55,.18)` region | modals, date-range menu |

---

## 3. Colours

All colours are CSS custom properties. **Never hard-code hex outside these tokens.** Each semantic colour ships with a paired `-soft` background tint.

**Neutrals**

| Token | Hex | Use |
|---|---|---|
| `--bg` | `#F7F8FA` | app background |
| `--surface` | `#FFFFFF` | cards, panels, rail, topbar |
| `--surface-2` | `#FBFCFD` | zebra rows, hovers, inset areas |
| `--ink` | `#1C2434` | primary text |
| `--ink-2` | `#54606E` | secondary text |
| `--ink-3` | `#6B7480` | tertiary / meta text |
| `--border` | `#E3E7ED` | default borders |
| `--border-2` | `#EDF0F4` | subtle inner dividers |

**Brand**

| Token | Hex | Use |
|---|---|---|
| `--brand` | `#2F5FE0` | primary actions, active nav, focus ring |
| `--brand-ink` | `#1E3F9E` | brand text, primary-button hover |
| `--brand-soft` | `#EAF0FE` | active nav bg, selection, brand tints |

**Semantic (status) — each with `-soft` pair**

| Meaning | Token | Hex | Soft |
|---|---|---|---|
| Positive / present / on-time | `--present` | `#2E7D5B` | `--present-soft` `#E6F2EC` |
| Attention / review / warning | `--review` | `#B7791F` | `--review-soft` `#FBF1DE` |
| Problem / error / rejected | `--problem` | `#C0453B` | `--problem-soft` `#FBEAE8` |
| Remote (neutral) | `--remote` | `#6D5AE0` | `--remote-soft` `#EEEBFB` |
| Leave | `--leave` | `#2F80B8` | `--leave-soft` `#E6F1F8` |
| Slate / neutral data | `--slate` | `#5A6474` | `--slate-soft` `#EEF1F5` |

**Semantic rules (business-meaning bindings — frozen):**

- **Remote is neutral** — always `--remote`, never a warning colour, unless it breaches an approved arrangement.
- **Confirmed vs Potential absence** must remain visually distinct (Confirmed → `--review`/`--problem` weight; Potential → `--slate`).
- **Never use colour alone** to convey status — always pair with a label, icon, or letter (see Accessibility).
- Positive trend where "up is good" uses `--present`; where "up is bad" (lateness, absence) the trend is inverted via the `invert` flag, not by recolouring.

---

## 4. Buttons

Base class `.btn` + modifier. Height and weight are fixed; only variant colours change.

| Variant | Class | Appearance |
|---|---|---|
| Secondary (default) | `.btn` | white bg, `--border`, `--ink` text, 600 weight |
| Primary | `.btn.primary` | `--brand` bg, white text; hover `--brand-ink` |
| Ghost | `.btn.ghost` | transparent, `--ink-2`; hover `--surface-2` |
| Danger | `.btn.danger` | `--problem` text, `#EAC7C4` border; hover `--problem-soft` bg |
| Small (size modifier) | `.btn.sm` | padding `5px 10px`, 12px — combine with any variant |

**Specs (frozen):**

- Default padding `8px 13px`; font `13px/600`; radius `--radius-sm`; transition `.12s`.
- Icon inside button: `15×15px`, `gap:7px`.
- Disabled: `opacity:.5; cursor:not-allowed`.
- Hover (secondary): bg `--surface-2`, border `#CBD2DD`.

**Usage rules:**

- One primary button per action group (the safe/affirmative path). Destructive actions use `.danger`, never `.primary`.
- Icon + label is preferred for primary actions; icon-only buttons must carry an `aria-label`.

---

## 5. Cards (Panels & KPIs)

**Panel** (`.panel`) — the primary content container.

- `--surface` bg, `1px` `--border`, `--radius`, `--shadow`, `margin-bottom:18px`.
- `.panel-head`: `14px 16px`, bottom `1px --border-2`, holds `.panel-title` (14.5/600), optional `.panel-sub`, and `.ph-right` (right-aligned actions/popover).
- `.panel-body`: `16px`. Modifier `.flush` removes padding and clips its own bottom corners (for edge-to-edge tables).
- **Overflow rule (frozen fix):** `.panel` does **not** clip overflow (so header popovers/tooltips escape); corner-rounding for edge-to-edge tables lives on `.panel-body.flush`.

**KPI card** (`.kpi`) — metric tile.

- `--surface`, `1px --border`, `--radius`, `--shadow`, padding `14px 14px 13px`, `position:relative`, flex column.
- Structure: `.kpi-top` (icon chip + `.kpi-label`) → `.kpi-value` (26/700) → optional `.kpi-note` (11px `--ink-3`) → optional `.kpi-foot` (trend).
- Clickable KPIs get `.clickable`, `role="button"`, `tabIndex=0`, Enter/Space activation, and a hover affordance.
- `.kpi-info` slot (top-right) hosts a `Popover` explaining the metric's formula.
- `.kpi-note` renders a hint such as "Not available for this filter — shown org-wide only" when an aggregate can't be derived under the active filter.

---

## 6. Forms

**Text/number input** (`.inp`)

- `1px --border`, radius `6px`, padding `7px 10px`, mono font, `13px`. Default width `88px`, right-aligned (tuned for numeric policy fields). Width/alignment are overridden inline for text fields (`width:100%; text-align:left`).

**Select** — native `<select>` with `.inp` or contextual styling (e.g. role picker, filter dropdowns, date-range menu). Keep `font-family:inherit`.

**Checkbox** (`Checkbox` component) — custom control for bulk-select rows and toggles.

**Date input** — native `<input type="date">` styled via `.inp` (used in custom date-range, follow-up date, effective date, add-holiday).

**Rules:**

- Every field has a visible label (`.pm-l` or an explicit `<label>`); placeholder is never the only label.
- Validation is inline and adjacent to the field (see Error States); invalid submit is blocked, not silently ignored.
- Email fields validate the `@edvoy.com` domain inline before enabling submit (Add user / access request).

---

## 7. Tables

`table.data` — the single table pattern across all lists.

- Full width, `border-collapse`, `13px`.
- **Header** (`th`): uppercase 11/600, `--ink-3`, `--surface-2` bg, `1px --border` bottom, `white-space:nowrap`, `position:sticky; top:0`. Sortable headers add `.sortable` (pointer, hover `--ink`) with a `.sort-ar` indicator.
- **Cell** (`td`): `11px 14px`, `1px --border-2` bottom, `vertical-align:middle`, `nowrap`. Last row omits bottom border.
- **Zebra:** even rows `--surface-2`.
- **Clickable rows:** `tr.clickable` → pointer; hover `--brand-soft` (overrides zebra).
- **Scroll container:** wrap wide tables in `.tbl-wrap` (`overflow-x:auto; max-width:100%`). Never let a table force page-level horizontal scroll (frozen fix — e.g. Review Log uses `minmax(0,1fr)` grid track + `.tbl-wrap`).

---

## 8. Charts

Three chart primitives — no external chart library; all hand-built SVG/HTML.

**`Spark` / `TrendChart`** — line/area trend.

- SVG line (`stroke-width:2.2`, round joins) over a soft gradient fill; `viewBox 0 0 560 <h>`, default height `120px`.
- Interactive hover: HTML hit-areas (`.spark-hit` / `.spark-seg`) sit above the SVG; hovering shows `.spark-tip` (a tooltip with label + value, `--ink` bg, `z-index:40`) and a crosshair line + emphasised point. Keyboard-focusable segments carry `aria-label` per point.
- **Honest-axis rules (frozen):** baseline starts at 0; the bucket unit is labelled (Hourly/Daily/Weekly/Monthly). A range too short for a trend (e.g. Today) renders `.spark-summary` — a single value + "too short a range for a trend" — never a one-point line. `TrendChart` re-buckets the base series to the active date range (see date-range control).

**`BarChart`** — horizontal comparison bars.

- `.bc-row` grid `150px 1fr 52px` (label / track / value). Track `.bc-track` height `20px`, radius `5px`; fill `.bc-fill` coloured per row; value `.bc-val` mono, right-aligned.
- Drillable bars get `onBar`: pointer, `role="button"`, `tabIndex=0`, Enter/Space, `aria-label` "<label>: <v>. View employees".

**`MiniBar` / `MiniCalendar` / `Heatmap`** — compact in-context visualisations; heatmap pairs colour with a letter code (never colour alone).

**Rules:** honest axes (start at 0, no truncation); colour per semantic tokens; every chart that represents a population is drillable to that population where feasible.

---

## 9. Icons

- Single inline-SVG icon set, referenced as `Ico.<name>` (e.g. `Ico.grid`, `Ico.calendar`, `Ico.shield`, `Ico.check`, `Ico.download`, `Ico.refresh`, `Ico.note`, `Ico.upload`, `Ico.users`, `Ico.clockAlert`, `Ico.hourglass`, `Ico.userx`, `Ico.home`, `Ico.history`, `Ico.sliders`, `Ico.map`, `Ico.pulse`, `Ico.building`, `Ico.checklist`, `Ico.info`, `Ico.alert`, `Ico.user`, `Ico.x`, `Ico.chevR`, `Ico.chevD`, `Ico.up`, `Ico.down`, `Ico.file`, `Ico.db`, `Ico.clock`).
- Default render ~15–17px; nav icons `17×17` at `opacity:.9`; button icons `15×15`; badge icons `12×12`.
- Sizeable via `React.cloneElement(Ico.x,{s:<px>})`.
- Icons are decorative unless they are the only content of a control — then the control needs an `aria-label`.
- Do not add new icons outside the set without design sign-off (frozen set).

---

## 10. Badges

`.badge` — status pill. `11.5/600`, padding `3px 9px 3px 7px`, radius `20px`, `nowrap`.

- Optional leading `.b-dot` (`6×6` round) or `12×12` icon.
- Semantic kinds map to token pairs, e.g. `b-present` (`--present` on `--present-soft`), `b-review`, `b-problem`, `b-remote`, `b-leave`, `b-neutral` (slate). Text colour is the strong token; background is the `-soft` pair.
- `ReviewBadge` and `statusPill()` are specialised badge renderers for review status and import status respectively.
- **Rule:** a badge always carries a text label — the dot/colour is reinforcement, never the sole signal.

---

## 11. Empty States

`EmptyState` component — shown when a list/table/region has no data.

- Centered icon + title + description; optional single action button.
- Copy is factual and non-alarming, and distinguishes *no data* from *zero/absent* (core business principle: "no row = no data, never absent").
- Example: "No team data for this period" → description explains imports, with a "Go to Import Centre" action.

---

## 12. Loading States

`Loading` component — placeholder during async/simulated processing.

- Used inside a panel (`<div className="panel"><Loading/></div>`), and as row skeletons in modals (e.g. imported-records).
- Multi-step processes (import, save-&-recalculate) surface **named phase states** rather than a bare spinner: e.g. Saving → Recalculating → Saved; Queued → Validating → Importing → Calculating metrics → Completed.

---

## 13. Error States

Two levels:

**System error** (`ErrorState`) — a region failed to load.
- Icon + title + reassuring description that separates system failure from data loss (e.g. "The metric engine didn't respond. This is a system error, not a data problem — your imports are safe.") + a Retry action.

**Inline / banner error** (`Banner kind="err"`) — a specific, explained problem (e.g. failed import with the reason). Uses `--problem` palette.

**Field validation** — inline message adjacent to the field (e.g. "Must be an @edvoy.com address."), and the affirmative action stays disabled until valid.

**Principle:** errors always explain *what* and *what's safe*, never a bare code.

---

## 14. Responsive Behaviour

Desktop-first; the frozen breakpoints are:

| Breakpoint | Effect |
|---|---|
| `max-width: 1080px` | `.grid-2` / `.grid-3` collapse to a single column; `.rl-grid` (Review Log) forces one column |
| `max-width: 920px` | rail / dense layout adjustments (compact shell) |
| `max-width: 900px` | Login two-column layout collapses; `.login-aside` hidden |

**Rules:**

- Wide tables scroll inside `.tbl-wrap`; they never force page-level horizontal scroll.
- Multi-column dashboards reflow to single column at ≤1080px.
- The prototype is optimised for desktop management use; mobile is functional-but-secondary. (Enhanced small-screen table treatment — sticky first column, defined stacking — is a tracked build-phase item, not part of the frozen visual spec.)

---

## 15. Accessibility

Frozen accessibility contract (implemented in v1.0):

- **Focus:** global `:focus-visible` → `2px solid --brand`, `2px` offset. Never remove focus outlines.
- **Skip link** to main content; **focus moves to the page `<h1>`** on every route change.
- **Keyboard operability:** clickable KPIs, bars, and rows expose `role="button"`, `tabIndex=0`, and Enter/Space handlers. Modals trap focus and close on Escape.
- **Labels:** icon-only controls and chart points carry `aria-label`; tables use `<th scope="col">`.
- **Never colour alone:** status is always paired with a label, icon, or letter code (heatmap included).
- **No silent redirects:** if a role can't reach a screen, the app announces it ("That screen isn't available for your role — returned to your home view") rather than bouncing silently.
- **Contrast:** the palette was designed toward WCAG AA and `--ink-3` darkened accordingly. **Open condition of the freeze:** a one-time *measured* contrast audit during build (tokens were reasoned, not instrument-verified). This does not imply visual redesign.

---

*End of DESIGN_SYSTEM.md — Edvoy Workforce Intelligence UX v1.0 (Frozen).*
