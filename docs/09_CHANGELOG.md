# Changelog & Decision Log

| | |
|---|---|
| **Version** | 1.1.0 |
| **Last updated** | 2026-08-03 |
| **Status** | Living document |
| **Related** | [Master Spec](10_MASTER_SPECIFICATION.md) · [PRD](01_PRODUCT_REQUIREMENTS.md) |

> **Purpose.** Track version history, decisions taken, ideas rejected, and future improvements. This is the record of *why* the project is the way it is.

---

## Table of Contents

1. [Version History](#1-version-history)
2. [Changes](#2-changes)
3. [Decision Log](#3-decision-log)
4. [Rejected Ideas](#4-rejected-ideas)
5. [Future Improvements](#5-future-improvements)

---

## 1. Version History

| Version | Date | Summary |
|---|---|---|
| 1.1.0 | 2026-08-03 | Added Review Log feature (US-R1 / FR20): review record + audit trail, follow-up tracking, outcome analytics, export. Additive; no business-rule changes |
| 1.0.0 | 2026-08-02 | Consolidated Phase 1 (Product Discovery) + Phase 2 (Data Discovery) into the documentation repository |
| 0.2.0 | 2026-08-02 | Phase 2 Data Discovery completed (source analysis, mappings, quality findings) |
| 0.1.1 | 2026-08-02 | PRD updated: required daily hours confirmed configurable; 8.30h set as org-wide default |
| 0.1.0 | 2026-08-02 | Phase 1 PRD produced |

## 2. Changes

**1.1.0**
- Added the **Review Log** (US-R1 / FR20): a filterable, exportable record of every review with status, action taken, follow-up date and a per-review audit trail; surfaces overdue follow-ups and reports outcomes; private manager notes excluded from all listings and exports.
- Threaded additively through PRD (FR20, AC25, metrics, scope), Data Spec (§16 review + review_event entities), Permission Matrix (§5–§10 rows), Dashboard Spec (§9a screen), Master Spec (index + version).
- New user story file `05a_USER_STORY_REVIEW_LOG.md`.

**1.0.0**
- Created the 10-document repository as the single source of truth.
- Merged duplicated content; centralised the 24 acceptance criteria in the PRD and pending decisions in the Master Spec.
- Added Mermaid diagrams (architecture, roadmap, login flow, absence decision, import idempotency, data lineage, navigation).

**0.1.1**
- Required daily hours made an explicit configurable policy value (like the 20% threshold).
- **Org-wide default confirmed as 8.30 hours (8h30m = 510 min);** 9 hours is an admin override.

## 3. Decision Log

| ID | Decision | Rationale | Status |
|---|---|---|---|
| DEC-01 | Required daily hours default = **8.30 hours (510 min)**, configurable per policy, 9h as override | Matches the employee master; keeps policy out of code | ✅ Confirmed |
| DEC-02 | **Effective Hours is authoritative** for worked time | Already resolves overnight shifts to the minute in the sample | ✅ Confirmed |
| DEC-03 | **Absence never inferred from a missing row** | Prevents false accusations; highest-consequence correctness | ✅ Confirmed |
| DEC-04 | Resolve hierarchy from **master IDs only**, never the daily manager name | Daily file's manager column is display-only | ✅ Confirmed |
| DEC-05 | **Daily export is authoritative** over the monthly status report in the MVP | Avoids two classifications of one day; reconciliation deferred | ✅ Confirmed (revisit PD8) |
| DEC-06 | Flexible-shift staff **exempt from lateness** until a core start is configured | No scheduled start exists to measure against | ✅ Confirmed (revisit PD4) |
| DEC-07 | Only **Approved** leave excuses absence | Pending/Rejected/Cancelled are not commitments | ✅ Confirmed |
| DEC-08 | **Idempotent imports** via SHA-256 fingerprint + logical key `emp+date+type` | Safe daily re-uploads | ✅ Confirmed |
| DEC-09 | **Modular monolith** for the first release | Simplicity over premature microservices | ✅ Recommended (ratify in ADR) |
| DEC-10 | **Super-admin cannot be removed** if final | Prevents accidental lockout | ✅ Confirmed |
| DEC-11 | Recommended employee master **adds machine-readable fields** (real start/end times, Required Daily Minutes, numeric working days) while keeping all existing columns | Removes parsing ambiguity without breaking exports | ✅ Confirmed |
| DEC-12 | **Review Log** records reviews, actions and follow-ups with a per-review audit trail; private notes never exported; "No action required" is a first-class outcome | Closes the review loop without becoming a scoring or disciplinary surface | ✅ Confirmed |

## 4. Rejected Ideas

| Idea | Why rejected |
|---|---|
| Single hidden "attendance score" driving decisions | Punitive and opaque; replaced by explainable components shown separately |
| Inferring absence from an empty attendance row | Produces false absences; leave/holiday/weekly-off/coverage overlay required |
| Joining attendance to hierarchy by manager **name** | Names are ambiguous and display-only; IDs are the key |
| Treating remote work as negative by default | Remote is neutral; flagged only vs policy/arrangement |
| Employee-facing attendance view in the MVP | Needs controlled self-service design; deferred to Phase 2 |
| Microservices from day one | Unjustified complexity for an internal tool at this scale |

## 5. Future Improvements

Deferred to Phase 2 and beyond (see [PRD §15](01_PRODUCT_REQUIREMENTS.md#15-phase-2-scope-deferred-features)):
- Employee self-service; email alert digests with snooze/ack; manual HR exceptions with supporting files.
- Cohort/trend analysis, period comparison vs quarter & org averages, team-capacity/wellbeing signals, "what changed" summary.
- Monthly status-report import + reconciliation; early-departure & overnight metrics as first-class; PDF export styling; subject-access/data-deletion workflows; UI import roll-back.
