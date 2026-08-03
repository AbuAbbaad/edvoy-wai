# Review Log — Documentation Update Set

| | |
|---|---|
| **Feature** | Review Log (US-R1 / FR20) |
| **Date** | 2026-08-03 |
| **Type** | Additive spec update — no business rule, metric, or threshold changes |
| **Prototype** | Implemented as the "Review log" screen under Workflow |

> **What this is.** The Review Log adds the missing "what's next" after a review: a filterable, exportable, auditable record of every attendance review and the action taken, with follow-up tracking and outcome reporting. This folder carries the documentation for that change — one new user story plus a precise, mergeable changelog for each affected specification.

---

## Files in this set

| File | What it does |
|---|---|
| [`05a_USER_STORY_REVIEW_LOG.md`](05a_USER_STORY_REVIEW_LOG.md) | **New.** The user story US-R1, acceptance criteria, follow-up/tone rules, and out-of-scope notes. |
| [`01_PRODUCT_REQUIREMENTS.changelog.md`](01_PRODUCT_REQUIREMENTS.changelog.md) | PRD edits: new FR20, amended FR16, acceptance criterion 25, success metrics, MVP & Phase-2 scope. |
| [`02_DATA_SPECIFICATION.changelog.md`](02_DATA_SPECIFICATION.changelog.md) | New `review` and `review_event` entities, the action vocabulary, relationships, retention. |
| [`04_PERMISSION_MATRIX.changelog.md`](04_PERMISSION_MATRIX.changelog.md) | New rows for data visibility, API, dashboard, export, and audit. |
| [`06_DASHBOARD_SPECIFICATION.changelog.md`](06_DASHBOARD_SPECIFICATION.changelog.md) | The Review Log screen, the trail modal, the actions chart, and the workflow "what's next" link. |
| [`10_MASTER_SPECIFICATION.changelog.md`](10_MASTER_SPECIFICATION.changelog.md) | Document-index registration, feature summary, and version bump to v1.1.0. |

## How to apply

Each changelog names the section, the exact insertion, and the surrounding anchor line in its target document, so edits merge unambiguously. Applying all six leaves the specification set fully describing the Review Log while preserving every existing business rule.

## Design guarantees (carried in every file)

- **Privacy.** Private manager notes never appear in the log, the trail, or any export — enforced server-side.
- **Neutral tone.** "Review", "action taken", "follow-up" — never "offender" or a score. "No action required" is a first-class outcome.
- **No colour-as-verdict.** Overdue uses a label as well as any colour; the actions chart never ranks people.
- **Hierarchy-scoped.** Access is the intersection of role and hierarchy, as everywhere else in the platform.
- **Additive.** No attendance rule, metric, threshold, or existing screen layout changes.
