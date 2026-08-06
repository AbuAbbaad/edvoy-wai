# Edvoy Workforce Intelligence — UX v1.0 (Frozen)

**Status:** FROZEN — this documentation set is the engineering source of truth. Do not redesign. Any change goes through change control against these documents.

**Frozen from:** `edvoy-workforce-intelligence-prototype.html` (the reviewed, test-passing prototype).

## Documents

| # | Document | Covers |
|---|---|---|
| 1 | [DESIGN_SYSTEM.md](DESIGN_SYSTEM.md) | Typography, spacing, colours, buttons, cards, forms, tables, charts, icons, badges, empty/loading/error states, responsive behaviour, accessibility |
| 2 | [COMPONENT_LIBRARY.md](COMPONENT_LIBRARY.md) | Every reusable component — purpose, properties, behaviour, variants, usage, accessibility notes |
| 3 | [SCREEN_SPECIFICATION.md](SCREEN_SPECIFICATION.md) | Every screen — purpose, layout, widgets, navigation, actions, permissions, validations, error/loading states, business rules |
| 4 | [INTERACTION_GUIDELINES.md](INTERACTION_GUIDELINES.md) | Navigation, click, hover, keyboard, confirmations, notifications, import workflow, drill-down, modals |
| 5 | [FRONTEND_ARCHITECTURE.md](FRONTEND_ARCHITECTURE.md) | Page & component hierarchy, routing, state management, reusable components, folder structure, coding conventions |

## Scope of the freeze

**Locked:** navigation & IA, dashboards & insight lists, review workflow & Review Log, the import wizard & action-flows, access-request governance, the date-range control & trend bucketing, the employee detail page, and the design system.

**Explicitly outside the freeze (net-new, will get a scoped design pass when built):** the data-correction / exception screens and the effective-dated assignment-history view.

## Conditions attached to the freeze

1. A one-time **measured accessibility audit** during build (tokens were reasoned toward WCAG AA, not instrument-verified).
2. The deferred **backend items** (auth, server-side permissions, persistence, durable audit, scale, full attendance-day status set, real exports) tracked as the pilot's critical path — implemented **behind** the frozen UX contracts without changing them.
3. Any change to a frozen screen, route key, prop contract, or token goes through **change control**, not ad-hoc edits.

## Honesty note

The prototype holds all state **in memory** — governance actions, reviews, filters, and audit entries reset on refresh. This is expected at prototype stage and is the reason the backend items above are on the critical path, not optional. These documents describe the frozen **UX**; they do not assert that backend behaviour is implemented.
