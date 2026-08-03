# PRD — Review Log update (changelog)

| | |
|---|---|
| **Applies to** | `01_PRODUCT_REQUIREMENTS.md` v1.0.0 |
| **Change** | Add the Review Log feature (US-R1) |
| **Type** | Additive — no business rule, metric, or threshold changes |
| **Date** | 2026-08-03 |

> Apply the edits below to the PRD. Each states the section, the exact insertion, and the surrounding anchor line so the merge is unambiguous.

---

## §9 Functional Requirements

**Add a new row** immediately after `FR19`:

```
| FR20 | Review | Review log: a filterable, exportable record of reviews with status, action taken, follow-up date and a per-review audit trail; private manager notes are excluded from all listings and exports |
```

**Amend `FR16`** to note the record is created:

- **Before:** `FR16 | Review | Review status + private/HR notes, kept separate from raw data`
- **After:** `FR16 | Review | Review status + private/HR notes, kept separate from raw data; a status or note creates or updates a review record that appears in the review log (FR20)`

## §12 Acceptance Criteria

Change the intro line from "all 24 criteria hold" to **"all 25 criteria hold"**, and **add**:

```
25. Reviews are recorded in a filterable, exportable log with follow-up tracking and a per-review audit trail; private notes are never exposed.
```

## §13 Success Metrics

**Add two rows** to the metrics table:

```
| Review follow-up completion rate | Reviews are followed through, not dropped |
| Overdue reviews trending down | Follow-up discipline is improving |
```

## §14 MVP Scope

**Amend** the review-workflow bullet:

- **Before:** `Manager review workflow: status and notes.`
- **After:** `Manager review workflow: status and notes, plus a review log with follow-up tracking and outcome reporting.`

## §15 Phase 2 Scope

**Add** (to make the deferral explicit):

```
- Automated in-app / email reminders for overdue review follow-ups (the MVP surfaces overdue reviews in-app only).
```

## §11 User Stories

**Add a row** to the epics table:

```
| Review Workflow | As a manager, I see a log of every review and the action taken, with follow-ups that are due (US-R1) |
```
