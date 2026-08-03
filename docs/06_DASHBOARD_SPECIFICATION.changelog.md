# Dashboard Specification — Review Log update (changelog)

| | |
|---|---|
| **Applies to** | `06_DASHBOARD_SPECIFICATION.md` v1.0.0 |
| **Change** | Add the Review Log screen and its components (US-R1 / FR20) |
| **Type** | Additive — no change to existing screens' layout or colour |
| **Date** | 2026-08-03 |

---

## § Screen inventory

**Add** the Review Log screen under the **Workflow** group, alongside Review Workflow.

## New screen — Review Log

**Route:** `reviewlog` · **Group:** Workflow · **Roles:** Manager (own line), Administrator, Read-Only (view).

**Purpose.** A record of every attendance review, the action taken, and its follow-up — the defined "what's next" after a review.

**Layout (top to bottom):**

1. **Header** — title "Review log", description, and actions: `Export`, `Review workflow`.
2. **Outcome KPIs** (4 cards): Total reviews · Open · Overdue follow-up · Closed. Open / Overdue / Closed cards filter the list on click.
3. **Filter bar** — free-text search (employee / ID / review number), Status, Action taken, State (open / overdue / closed), Reset.
4. **Log table** — columns: Review · Employee · Pattern · Status · Action taken · Opened · Follow-up. Rows are clickable (open the audit trail) and carry a quick-action (…) menu: view trail · open profile · continue review. Overdue follow-up dates are labelled, not colour-only.
5. **Actions-taken breakdown** (side panel) — a bar chart of reviews by action taken, within the current filter. Never ranks people.
6. **Empty state** — "No reviews match these filters" when filters exclude everything.

## New component — Review-trail modal

Read-only history for one review: current status, action taken, opened date, follow-up (or overdue / closed), the HR-visible note, and the chronological event list (timestamp · actor · action · detail). Includes a "Continue review" action that opens the Review Workflow for that employee. A privacy note states that private manager notes are never shown or exported.

## New component — Actions-taken chart

A neutral bar chart of the count of reviews by action taken. Honest axis; no colour-as-verdict.

## § Review Workflow (existing screen)

**Amend:** on **Save review**, the manager is offered **"View review log"**. The review log is the specified destination for "what's next" after a review, and the save confirms the record was added to it.

## § States

**Add** the Review Log's empty state ("no reviews match these filters") and the overdue emphasis (label + optional muted colour) to the states catalogue.
