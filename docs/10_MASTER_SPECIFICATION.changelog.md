# Master Specification — Review Log update (changelog)

| | |
|---|---|
| **Applies to** | `10_MASTER_SPECIFICATION.md` v1.0.0 |
| **Change** | Register the Review Log feature across the document index (US-R1 / FR20) |
| **Type** | Additive |
| **Date** | 2026-08-03 |

---

## § Document Index

**Add** a reference to the new user story:

```
| 05a_USER_STORY_REVIEW_LOG.md | User story US-R1 — Review Log, follow-up tracking, outcome analytics, export |
```

## § What's agreed (feature summary)

**Add** a line to the feature summary:

> **Review Log.** Every attendance review is recorded with its status, the action taken, a follow-up date and a full audit trail. The log is filterable and exportable, surfaces overdue follow-ups, and reports on outcomes — while private manager notes stay private. It is the defined "what's next" after a manager reviews an employee.

## § Version

Bump the master spec to **v1.1.0** on adoption, and note in the change history:

```
| 1.1.0 | 2026-08-03 | Added Review Log feature (US-R1 / FR20): review record + audit trail, follow-up tracking, outcome analytics, export. Additive; no business-rule changes. |
```

## § Pending Decisions

No new pending decisions. The Review Log reuses **PD9** (retention) for review records and their trail; it introduces no new open questions.
