# Data Specification — Review Log update (changelog)

| | |
|---|---|
| **Applies to** | `02_DATA_SPECIFICATION.md` v1.0.0 |
| **Change** | Add the `review` and `review_event` entities (US-R1 / FR20) |
| **Type** | Additive — no change to attendance, leave, holiday, or master structures |
| **Date** | 2026-08-03 |

> The Review Log needs two new derived entities. They sit alongside the existing data model; they are populated by manager action, not by import.

---

## New entity — `review`

One row per attendance review a manager conducts.

| Field | Type | Notes |
|---|---|---|
| `review_id` | string (PK) | Human-readable, e.g. `RV-1042` |
| `employee_number` | string (FK → employee master) | Who the review concerns; hierarchy-scoped |
| `pattern` | enum | Origin insight: `late` · `short_hours` · `absence` · `remote` |
| `opened_at` | datetime | Set on first status/note |
| `status` | enum | One of the nine existing review statuses (unchanged) |
| `action` | enum | Outcome — see the action vocabulary below |
| `follow_up_date` | date · nullable | Drives the overdue flag |
| `closed_at` | datetime · nullable | Set when status becomes `resolved` or `no_action` |
| `note_hr` | text · nullable | HR-visible note; **included** in exports |
| `note_private` | text · nullable | Private manager note; **never** listed, shown in trail, or exported |
| `manager_number` | string (FK → employee master) | The reviewing manager |

## New entity — `review_event` (audit trail)

Append-only history for a review. One row per change.

| Field | Type | Notes |
|---|---|---|
| `event_id` | string (PK) | |
| `review_id` | string (FK → review) | |
| `at` | datetime | When the change happened |
| `actor` | string | Who made the change |
| `action` | string | e.g. `Review opened`, `Status → Action agreed` |
| `detail` | text · nullable | Optional context (never the private note) |

## Action vocabulary (new controlled list)

Distinct from status. Status = *where is this review?*; action = *what did we do?*

| `action` value | Label |
|---|---|
| `none` | No action required |
| `monitor` | Monitoring for a period |
| `shift` | Shift / schedule review |
| `support` | Support offered |
| `clarify` | Clarification requested |
| `datafix` | Data correction raised |
| `arrangement` | Flexible arrangement agreed |
| `escalate` | Escalated to HR |

## Relationships

- A `review` belongs to exactly one employee and one manager.
- A `review` has many `review_event` rows (its trail).
- Notes are split into `note_private` (manager-only) and `note_hr` (shareable). This split is enforced server-side, not merely hidden in the UI.

## §Retention (§ Data Lineage / retention)

Review records and their events follow the same retention policy as audit data (**PD9**). They are never derived from, and never alter, imported attendance rows.
