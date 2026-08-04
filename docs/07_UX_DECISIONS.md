# UX Decisions

| | |
|---|---|
| **Version** | 1.1.0 |
| **Last updated** | 2026-08-05 |
| **Status** | Draft for Phase 3 (from approved Phase 1 principles) |
| **Related** | [PRD](01_PRODUCT_REQUIREMENTS.md) · [Dashboard Spec](06_DASHBOARD_SPECIFICATION.md) · [Technical Constraints](08_TECHNICAL_CONSTRAINTS.md) |

> **Purpose.** Capture the UX decisions and principles agreed during discovery so Phase 3 design work stays consistent. These translate the product principles (never accusatory, always explainable, privacy by default) into interface rules.

---

## Table of Contents

1. [Design Principles](#1-design-principles)
2. [Navigation](#2-navigation)
3. [Layout](#3-layout)
4. [Colour Philosophy](#4-colour-philosophy)
5. [Accessibility](#5-accessibility)
6. [Responsive Behaviour](#6-responsive-behaviour)
7. [Dashboard Philosophy](#7-dashboard-philosophy)
8. [Terminology](#8-terminology)
9. [Icons](#9-icons)
10. [Tables](#10-tables)
11. [Charts](#11-charts)
12. [Forms](#12-forms)
13. [Import Wizard](#13-import-wizard)
14. [Notification Design](#14-notification-design)
15. [Error Handling](#15-error-handling)
16. [Manager Experience](#16-manager-experience)
17. [HR Experience](#17-hr-experience)
18. [Admin Experience](#18-admin-experience)

---

## 1. Design Principles

| # | Principle |
|---|---|
| 1 | **Never accusatory** — neutral language ("Needs review", not "offender") |
| 2 | **Always explainable** — every number drills to its evidence |
| 3 | **Patterns over incidents** — surface trends, not single days |
| 4 | **Privacy by default** — a manager sees only their line |
| 5 | **Legible to non-technical managers** — plain words, minimal jargon |
| 6 | **Honest visuals** — no misleading axes or cluttered charts |
| 7 | **Support, not surveillance** — coaching prompts accompany insights |

## 2. Navigation

- Role-filtered left navigation; items hidden if the role lacks access.
- Manager default landing = Manager Dashboard (month-to-date); Admin default = Organisation Dashboard.
- Breadcrumbs into Employee Detail and Import Detail so drill-downs are reversible.

## 3. Layout

- Desktop-first, responsive down to tablet.
- Consistent page frame: filter bar on top, content below, coverage warnings pinned above content when present.
- Cards in a responsive grid; insight lists full-width beneath.

## 4. Colour Philosophy

- Calm, professional palette; status colours are **muted, not alarmist** (no aggressive reds for normal variance).
- Colour reinforces meaning but **never carries it alone** (see Accessibility).
- Reserve strong emphasis for genuine data problems (e.g. failed import), not for employee behaviour.

## 5. Accessibility

| Decision | Detail |
|---|---|
| WCAG-friendly components | Accessible component library; sufficient contrast |
| Never colour-only | Heatmap and statuses also convey meaning via icon/label/text |
| Keyboard & screen-reader | Full keyboard navigation; semantic markup; ARIA where needed |
| Tooltips have text equivalents | Definitions reachable without hover-only interaction |

## 6. Responsive Behaviour

- Desktop-first (primary users are managers/HR on laptops).
- Tables scroll horizontally with sticky first column on narrow viewports.
- Charts reflow to single-column stacks on small screens.

## 7. Dashboard Philosophy

- Default to **current calendar month to date**.
- A **date-range control** lets the user widen or narrow that window — Today, Yesterday, This week, This month (default), Last 3 months, or a custom range — so the dashboard answers both daily operational questions and quarter-length pattern questions. Windows are calendar-aligned by default; see [Dashboard Spec §17a](06_DASHBOARD_SPECIFICATION.md#17a-date-range-control).
- **Trends follow the range.** Trend charts re-bucket to the selected window (hourly / daily / weekly / monthly) rather than a fixed four-week axis; a range too short for a trend shows a single summary value, never a one-point line. This keeps "honest visuals" true across ranges; see [Dashboard Spec §17b](06_DASHBOARD_SPECIFICATION.md#17b-trend-bucketing-by-range).
- A very short range legitimately suppresses frequency flags (a day is not a pattern) and shows a coverage note instead of an unreliable percentage.
- Lead with a small number of meaningful cards; avoid overwhelming density.
- Every card carries current value, previous-period comparison (of equal length to the selected range), trend and a definition tooltip.
- Coverage warnings are prominent when data is missing/stale, so no one misreads gaps as facts.

## 8. Terminology

Consistent, neutral vocabulary across all screens and documents:

| Use | Not |
|---|---|
| Needs review | Offender / problem employee |
| Potential absence | Absent (when leave data is incomplete) |
| Missing clock-out | Short day / left early |
| No data | Absent (when no row exists) |
| Frequently late | Habitually late |
| Remote attendance | WFH abuse |

## 9. Icons

- Icons pair with text labels, never replace them for status.
- One consistent icon set (lucide-react per the tech stack) for a coherent look.

## 10. Tables

- Prefer tables over long prose for structured data (per the requirements' own style).
- Evidence columns always visible in insight lists; a review-status column on every insight table.
- Sortable headers; sticky header row; zebra striping for scanability.

## 11. Charts

- Simple and honest: weekly attendance trend, effective hours by sub-department, on-time %, late/short-hours/absence/missing-punch trends, office vs remote, department & location comparisons, weekday pattern.
- No dual hidden axes, truncated baselines, or 3D effects.

## 12. Forms

- Inline validation with plain-language messages.
- Effective-dated settings clearly show "applies from" dates.
- Destructive/admin actions require explicit confirmation.

## 13. Import Wizard

- Seven guided steps (Select → Upload → Preview & map → Validate → Summary → Process → Result).
- Show expected columns, last successful import, a downloadable template and guidance up front.
- Validation summary distinguishes valid / warning / failed / new / updated / duplicate / ignored / not-found and the file's date range.
- Named processing stages (Queued … Completed with warnings) so long imports feel transparent.

## 14. Notification Design

- MVP: in-app coverage/import warnings only. Email digests, snooze and acknowledgement are **Phase 2**.
- Anti-fatigue by design: group into digests, never repeat the same unresolved pattern, allow snooze/ack — applied when notifications ship in Phase 2.

## 15. Error Handling

- Distinguish **user-fixable** (bad file, unmapped column) from **system** errors.
- Rejected rows are downloadable with a reason per row.
- Errors never expose sensitive data or stack traces to end users.

## 16. Manager Experience

- Opens on their team, month-to-date, with the fewest clicks to "who needs a conversation and why".
- Coaching-guidance panel offers supportive questions (approved arrangement? transport/health/workload? incomplete record? shift still suitable? working beyond hours? more support needed?).
- Attendance framed alongside outcomes, approved flexibility, wellbeing and workload — never as a standalone verdict.

## 17. HR Experience

- Import-first: the Import Centre and history are the HR Operator's home.
- Clear validation and error-download flows; no analytics clutter unless separately granted.

## 18. Admin Experience

- Organisation-wide oversight plus configuration (policies, shifts, holidays, premises, users).
- Safeguards visible: the final super-admin cannot be removed; sensitive actions confirm and audit.
