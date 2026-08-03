# Permission Matrix

| | |
|---|---|
| **Version** | 1.0.0 |
| **Last updated** | 2026-08-02 |
| **Status** | Approved (Phase 1) |
| **Related** | [Master Spec](10_MASTER_SPECIFICATION.md) · [PRD](01_PRODUCT_REQUIREMENTS.md) · [Dashboard Spec](06_DASHBOARD_SPECIFICATION.md) |

> **Purpose.** Define authentication, roles, reporting-hierarchy rules, and every access matrix. **Access is always the intersection of role and hierarchy** — the smaller of what the role allows and what the person's reporting line permits. Front-end checks are never the only guard; every endpoint is enforced server-side.

---

## Table of Contents

1. [Authentication](#1-authentication)
2. [Google Login](#2-google-login)
3. [User Roles](#3-user-roles)
4. [Reporting Hierarchy Rules](#4-reporting-hierarchy-rules)
5. [Data Visibility Matrix](#5-data-visibility-matrix)
6. [API Permission Matrix](#6-api-permission-matrix)
7. [Dashboard Visibility Matrix](#7-dashboard-visibility-matrix)
8. [Import Permissions](#8-import-permissions)
9. [Export Permissions](#9-export-permissions)
10. [Audit Permissions](#10-audit-permissions)

---

## 1. Authentication

| Requirement | Rule |
|---|---|
| Provider | Google Workspace / Google OAuth (Auth.js or equivalent) |
| Domain restriction | Approved company domains only, initially `edvoy.com` (domain list = [PD10](10_MASTER_SPECIFICATION.md#10-pending-decisions)) |
| Identity binding | Authenticated email validated against an **active employee** (join key = master Work Email) |
| Sessions | Secure server-side sessions; automatic expiry & logout; last-login stored |
| Logging | Successful **and** failed sign-ins logged |
| Authorisation | Role-based + row-level hierarchy on every endpoint |
| Access denied | Screen with a "request access" route; requests appear in the admin's pending queue |

## 2. Google Login

```mermaid
flowchart TD
    A[User clicks Sign in with Google] --> B{Domain in allow-list?}
    B -- no --> D[Access denied]
    B -- yes --> C{Email maps to active employee?}
    C -- no --> R[Access-denied + Request access]
    C -- yes --> E{Has a role and/or reports?}
    E -- no --> R
    E -- yes --> F[Create secure session · store last login · log success]
    F --> G[Land on role's default screen]
    R --> Q[(Pending access request → Admin)]
```

## 3. User Roles

| Role | Who | Scope |
|---|---|---|
| **Super Administrator** | `syed@edvoy.com` (initial) | Everything an Administrator has, plus is the protected final admin |
| **Administrator** | People Ops / IT | Organisation-wide, full configuration & management |
| **Reporting Manager** | Employee with ≥1 active direct report | Own hierarchy only |
| **HR Data Operator** | HR executive / importer | Imports & import history; **no** analytics unless separately granted |
| **Read-Only User (Management Viewer)** | HR / senior leadership | Org-wide dashboards & reports, view-only |

**Super-admin safeguard:** an administrator can add/remove other administrators, but the system **must prevent removal of the final super-admin access** (the last remaining super admin cannot be removed accidentally).

## 4. Reporting Hierarchy Rules

| Rule | Detail |
|---|---|
| Direct reports | A manager sees every active employee whose reporting manager is that manager |
| Indirect reports | Visible **only if an administrator enables it**; otherwise view stops at direct reports ([PD5](10_MASTER_SPECIFICATION.md#10-pending-decisions)) |
| Strict boundary | A manager can never see, filter to, or export anyone outside their own line |
| Admin & Viewer | See the whole organisation (edit vs read-only by role) |
| Effective-dated | Access follows the reporting relationship **as it stood during the viewed period** |
| External managers | Managers referenced only by external IDs (1, 172, 177, 193, 200, 2070, BD005) must still have reachable reports via an admin/designate ([PD1](10_MASTER_SPECIFICATION.md#10-pending-decisions)) |

## 5. Data Visibility Matrix

| Data | Super Admin | Administrator | Reporting Manager | HR Operator | Read-Only |
|---|---|---|---|---|---|
| Own hierarchy employees | ✅ (all) | ✅ (all) | ✅ (own line) | ⬜* | ✅ (all) |
| Employees outside line | ✅ | ✅ | ❌ | ⬜* | ✅ |
| Attendance records | ✅ | ✅ | ✅ (own line) | ⬜* | ✅ |
| Employee-level insights | ✅ | ✅ | ✅ (own line) | ⬜* | ✅ |
| Manager notes (private) | ✅ | ✅ | ✅ (own) | ❌ | ❌ |
| Manager notes (HR-visible) | ✅ | ✅ | ✅ (own line) | ⬜* | ✅ |
| Import files & errors | ✅ | ✅ | ❌ | ✅ | ❌ |
| Policy configuration | ✅ | ✅ | ❌ | ❌ | ❌ |
| Audit log | ✅ | ✅ | ❌ | ❌ | ❌ |

`✅` allowed · `❌` denied · `⬜*` only if separately authorised (HR Operator does not receive analytics by default).

## 6. API Permission Matrix

| Endpoint group | Super Admin | Administrator | Manager | HR Operator | Read-Only |
|---|---|---|---|---|---|
| `GET /me`, `POST /auth/*` | ✅ | ✅ | ✅ | ✅ | ✅ |
| `GET /employees` (scoped) | ✅ | ✅ | ✅ (own line) | ⬜* | ✅ |
| `GET /attendance`, `/metrics` (scoped) | ✅ | ✅ | ✅ (own line) | ⬜* | ✅ |
| `POST /imports/*` | ✅ | ✅ | ❌ | ✅ | ❌ |
| `GET /imports/history` | ✅ | ✅ | ❌ | ✅ | ❌ |
| `POST /reviews`, `/notes` | ✅ | ✅ | ✅ (own line) | ❌ | ❌ |
| `PUT /policies/*`, `/shifts/*`, `/holidays/*`, `/premises/*` | ✅ | ✅ | ❌ | ❌ | ❌ |
| `POST /users`, `/roles`, `/admins` | ✅ | ✅ (not final super-admin) | ❌ | ❌ | ❌ |
| `POST /corrections` | ✅ | ✅ | ❌ | ⬜* | ❌ |
| `POST /exports/*` (scoped) | ✅ | ✅ | ✅ (own line) | ⬜* | ✅ |
| `GET /audit` | ✅ | ✅ | ❌ | ❌ | ❌ |

Every scoped endpoint enforces row-level hierarchy server-side, independent of any front-end state.

## 7. Dashboard Visibility Matrix

| Screen | Super Admin | Administrator | Manager | HR Operator | Read-Only |
|---|---|---|---|---|---|
| Manager Dashboard | ✅ | ✅ | ✅ (own line) | ❌ | ✅ (all) |
| Organisation Dashboard | ✅ | ✅ | ❌ | ❌ | ✅ |
| Employee Detail | ✅ | ✅ | ✅ (own line) | ⬜* | ✅ |
| Attendance Heatmap | ✅ | ✅ | ✅ (own line) | ⬜* | ✅ |
| Insight Lists / Review | ✅ | ✅ | ✅ (own line) | ❌ | ✅ (view) |
| Import Centre & History | ✅ | ✅ | ❌ | ✅ | ❌ |
| Policy / Shift / Holiday / Premise settings | ✅ | ✅ | ❌ | ❌ | ❌ |
| User & Role Management | ✅ | ✅ | ❌ | ❌ | ❌ |
| Audit Log | ✅ | ✅ | ❌ | ❌ | ❌ |
| System Health & Data Coverage | ✅ | ✅ | ⬜ (coverage only) | ✅ (import health) | ⬜ |

## 8. Import Permissions

| Action | Super Admin | Administrator | HR Operator | Others |
|---|---|---|---|---|
| Upload employee master | ✅ | ✅ | ✅ | ❌ |
| Upload attendance | ✅ | ✅ | ✅ | ❌ |
| Upload holiday / leave | ✅ | ✅ | ✅ | ❌ |
| Review validation results | ✅ | ✅ | ✅ | ❌ |
| Download error files | ✅ | ✅ | ✅ | ❌ |
| Retry / reprocess import | ✅ | ✅ | ✅ | ❌ |
| Roll back import | ✅ | ✅ | ❌ | ❌ |

## 9. Export Permissions

| Report | Super Admin | Administrator | Manager | HR Operator | Read-Only |
|---|---|---|---|---|---|
| Team attendance summary | ✅ | ✅ | ✅ (own line) | ⬜* | ✅ |
| Employee attendance detail | ✅ | ✅ | ✅ (own line) | ⬜* | ✅ |
| Late / short-hours / absence / remote / missing-punch | ✅ | ✅ | ✅ (own line) | ⬜* | ✅ |
| Import audit report | ✅ | ✅ | ❌ | ✅ | ❌ |
| Manager review status | ✅ | ✅ | ✅ (own line) | ❌ | ✅ |
| Data-quality report | ✅ | ✅ | ⬜ | ✅ | ⬜ |

Every export records exported-by, timestamp, filters used, record count and report type; **row-level access is applied to exports**.

## 10. Audit Permissions

| Capability | Super Admin | Administrator | Others |
|---|---|---|---|
| View audit log | ✅ | ✅ | ❌ |
| Export audit log | ✅ | ✅ | ❌ |
| Audited actions (all roles) | login, failed login, access change, admin create/remove, upload, import, rollback, policy change, manual correction, export, note creation, review-status change | | |

> Sensitive file contents and session tokens are **never** logged. See privacy constraints in [`08_TECHNICAL_CONSTRAINTS.md`](08_TECHNICAL_CONSTRAINTS.md).
