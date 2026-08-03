# User Story — Review Log

| | |
|---|---|
| **Version** | 1.0.0 |
| **Last updated** | 2026-08-03 |
| **Status** | Proposed (Phase 1 spec update) |
| **Story ID** | US-R1 |
| **Related** | [PRD](01_PRODUCT_REQUIREMENTS.md) · [Dashboard Spec](06_DASHBOARD_SPECIFICATION.md) · [Permissions](04_PERMISSION_MATRIX.md) · [Data Spec](02_DATA_SPECIFICATION.md) |

> **Purpose.** Add a durable, filterable, exportable record of every attendance review and the action taken — the defined "what's next" after a manager reviews an employee. This story is additive: it changes **no** business rule, metric, or threshold.

---

## 1. The story

**US-R1 — Review log & follow-up**

> As a **reporting manager**, I want **a log of every attendance review I have carried out, showing the action taken and any follow-up due**, so that **nothing raised for a supportive conversation is forgotten, and I — and HR — can see what was done and what is still outstanding.**

### Supporting roles

- As an **administrator**, I want to see the review log across the organisation, so I can spot teams with many overdue follow-ups.
- As a **read-only management viewer**, I want to view (not edit) review outcomes org-wide, so I can understand how attendance patterns are being handled.
- As **HR**, I want an exportable record of reviews and their HR-visible outcomes, so I can support managers — *without* ever seeing private manager notes.

## 2. Why now

The platform already lets a manager set a review status and add notes against an insight, but it stops there. There is no shared place that answers the question a manager asks next: *"what have we reviewed, what did we decide, and what still needs following up?"* The Review Log closes that loop and turns one-off review actions into an auditable record with follow-up tracking and outcome reporting.

## 3. Acceptance criteria

1. A review record is created the first time a manager sets a status or note against an employee's insight, carrying that employee, the pattern, the opening date, and the manager.
2. The log lists all review records within the viewer's hierarchy, newest first.
3. Each record shows current status, action taken, opened date, and follow-up date (or closed date).
4. The log can be filtered by free-text (employee / ID), status, action taken, and state (open / overdue / closed).
5. A follow-up date in the past on an open review is shown as **overdue**.
6. Opening a record shows its full audit trail: every status change, who made it and when, plus the HR-visible note.
7. Private manager notes never appear in the log, the audit-trail view, or any export.
8. The log is exportable; the export records who ran it, when, the filters used, and the row count; row-level access is applied.
9. Outcome analytics show the distribution of actions taken across the visible records.
10. Closing a review (Resolved / No action required) removes it from the open and overdue counts but keeps it in the log and its audit trail.

## 4. Follow-up & tone

- A review may carry a **follow-up date** — the date the manager intends to check back.
- An **open** review whose follow-up date has passed is **overdue** and is surfaced in its own KPI and filter.
- Overdue is a *prompt, not a judgement*. The tone stays supportive; it flags that a manager meant to return to something.
- "No action required" is a first-class, non-negative outcome, consistent with the product's non-disciplinary stance.

## 5. Out of scope (this iteration)

- Automated reminders / email digests for overdue follow-ups (aligns with the PRD's Phase-2 alerting; the log surfaces overdue in-app for now).
- Employee-facing visibility of their own review record (platform is not employee-facing in the first release — PRD N6).
- Cross-period outcome trend analysis (belongs with Phase-2 advanced analytics).
