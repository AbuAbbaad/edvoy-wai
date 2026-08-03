# Permission Matrix — Review Log update (changelog)

| | |
|---|---|
| **Applies to** | `04_PERMISSION_MATRIX.md` v1.0.0 |
| **Change** | Add Review Log visibility, dashboard, export, and audit rules (US-R1 / FR20) |
| **Type** | Additive — access is still the intersection of role and hierarchy, server-enforced |
| **Date** | 2026-08-03 |

> Legend: `✅` allowed · `❌` denied · `⬜*` only if separately authorised · **own line** = manager's own hierarchy only.

---

## §5 Data Visibility Matrix

**Add rows:**

| Data | Super Admin | Administrator | Reporting Manager | HR Operator | Read-Only |
|---|---|---|---|---|---|
| Review records (status, action, follow-up) | ✅ | ✅ | ✅ (own line) | ⬜* (HR-visible fields only) | ✅ (view) |
| Review audit trail | ✅ | ✅ | ✅ (own line) | ⬜* (HR-visible events) | ✅ (view) |
| Review note — HR-visible | ✅ | ✅ | ✅ (own line) | ⬜* | ✅ |
| Review note — private | ✅ | ✅ | ✅ (own, authoring manager) | ❌ | ❌ |

## §6 API Permission Matrix

**Add row:**

| Endpoint group | Super Admin | Administrator | Manager | HR Operator | Read-Only |
|---|---|---|---|---|---|
| `GET /reviews`, `/reviews/:id/events` (scoped) | ✅ | ✅ | ✅ (own line) | ⬜* (HR-visible) | ✅ (view) |

## §7 Dashboard Visibility Matrix

**Add row:**

| Screen | Super Admin | Administrator | Manager | HR Operator | Read-Only |
|---|---|---|---|---|---|
| Review Log | ✅ | ✅ | ✅ (own line) | ❌ | ✅ (view) |

## §9 Export Permissions

**Add row:**

| Report | Super Admin | Administrator | Manager | HR Operator | Read-Only |
|---|---|---|---|---|---|
| Review log export | ✅ | ✅ | ✅ (own line) | ⬜* (HR-visible subset) | ✅ |

> The review-log export **never** includes private manager notes. It records exported-by, timestamp, filters used, record count and report type, and applies row-level access — same as every other export.

## §10 Audit Permissions

**Confirm / amend** the audited-actions list: `review-status change` and `note creation` are already audited. **Add** `review export` to the list of audited actions.
