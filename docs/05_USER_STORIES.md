# User Stories

| | |
|---|---|
| **Version** | 1.0.0 |
| **Last updated** | 2026-08-02 |
| **Status** | Approved (Phase 1) |
| **Related** | [PRD](01_PRODUCT_REQUIREMENTS.md) · [Permissions](04_PERMISSION_MATRIX.md) · [Dashboard Spec](06_DASHBOARD_SPECIFICATION.md) |

> **Purpose.** Backlog of user stories grouped by epic. Priority: **P0** = MVP-critical, **P1** = MVP if time allows, **P2** = Phase 2. Personas per [PRD §7](01_PRODUCT_REQUIREMENTS.md#7-user-personas).

---

## Table of Contents

- [Epic A — Authentication & Access](#epic-a--authentication--access)
- [Epic B — Import Centre](#epic-b--import-centre)
- [Epic C — Employee Master & Hierarchy](#epic-c--employee-master--hierarchy)
- [Epic D — Metrics & Insights](#epic-d--metrics--insights)
- [Epic E — Dashboards & Drill-down](#epic-e--dashboards--drill-down)
- [Epic F — Review Workflow](#epic-f--review-workflow)
- [Epic G — Administration & Policy](#epic-g--administration--policy)
- [Epic H — Audit, Export & Governance](#epic-h--audit-export--governance)
- [Acceptance Criteria Master List](#acceptance-criteria-master-list)

---

## Epic A — Authentication & Access

### A1
| | |
|---|---|
| **Persona** | Reporting Manager |
| **Story** | As a manager, I sign in with my Edvoy Google account so I can access the platform securely. |
| **Business value** | Secure, password-less access tied to company identity |
| **Acceptance criteria** | Sign-in restricted to edvoy.com; email validated against an active employee; secure session created; last login stored; success/failure logged |
| **Priority** | P0 |
| **Dependencies** | Employee master imported (C1) |

### A2
| | |
|---|---|
| **Persona** | Reporting Manager |
| **Story** | As a manager, I see only employees in my reporting hierarchy so privacy is preserved. |
| **Business value** | Row-level privacy by default |
| **Acceptance criteria** | Direct reports visible; indirect only if admin-enabled; no access outside line via UI, API or export |
| **Priority** | P0 |
| **Dependencies** | C2 |

### A3
| | |
|---|---|
| **Persona** | Any employee |
| **Story** | As a person without reports, I see an access-denied screen with a way to request access. |
| **Business value** | Clear path in, no dead end |
| **Acceptance criteria** | Access-denied screen; request routes to admin's pending queue; decision logged |
| **Priority** | P0 |
| **Dependencies** | A1, G3 |

## Epic B — Import Centre

### B1
| | |
|---|---|
| **Persona** | HR Data Operator |
| **Story** | As an operator, I upload the daily Keka attendance export through a guided flow. |
| **Business value** | Reliable daily operations |
| **Acceptance criteria** | Select type → upload .xlsx/.csv → header alias match → preview → validate → import valid / reject invalid / cancel |
| **Priority** | P0 |
| **Dependencies** | C1 |

### B2
| | |
|---|---|
| **Persona** | HR Data Operator |
| **Story** | As an operator, a duplicate upload of the same file does not double-count attendance. |
| **Business value** | Data integrity |
| **Acceptance criteria** | File fingerprint match ⇒ "already imported"; logical key `emp+date+type` prevents duplicate rows; updates preserve history |
| **Priority** | P0 |
| **Dependencies** | B1 |

### B3
| | |
|---|---|
| **Persona** | HR Data Operator |
| **Story** | As an operator, I download rejected rows with plain-language reasons so I can fix them. |
| **Business value** | Fast error resolution |
| **Acceptance criteria** | Validation summary (total/valid/warning/failed/new/updated/duplicate/ignored/not-found + date range); downloadable rejects with messages |
| **Priority** | P0 |
| **Dependencies** | B1 |

### B4
| | |
|---|---|
| **Persona** | HR Data Operator / Administrator |
| **Story** | As an operator, I see a full import history with uploader, timestamp, filename, status and counts. |
| **Business value** | Traceability & audit |
| **Acceptance criteria** | History lists all imports with import ID, type, uploader, timestamps, date range, counts, status, hash, mapping version |
| **Priority** | P0 |
| **Dependencies** | B1 |

### B5
| | |
|---|---|
| **Persona** | HR Data Operator |
| **Story** | As an operator, I upload holiday and leave files so absence is trustworthy. |
| **Business value** | Correct absence classification |
| **Acceptance criteria** | Holiday per-sheet parser tolerant of 6 formats, quarantining unresolved dates; only Approved leave excuses absence |
| **Priority** | P0 |
| **Dependencies** | B1 |

### B6
| | |
|---|---|
| **Persona** | Administrator |
| **Story** | As an admin, I map unmatched columns and save the mapping for future imports. |
| **Business value** | Tolerates Keka format drift |
| **Acceptance criteria** | Unmatched columns mappable; mapping saved as a reusable template; required-field gaps highlighted |
| **Priority** | P1 |
| **Dependencies** | B1 |

## Epic C — Employee Master & Hierarchy

### C1
| | |
|---|---|
| **Persona** | HR Data Operator |
| **Story** | As an operator, I upload the employee master with identity, shift, hierarchy and required hours. |
| **Business value** | The spine every calculation joins to |
| **Acceptance criteria** | All master fields imported; "8.30 hours"→510; shift window parsed; "5.5 days"→5.5 |
| **Priority** | P0 |
| **Dependencies** | — |

### C2
| | |
|---|---|
| **Persona** | Administrator |
| **Story** | As an admin, the hierarchy resolves direct and indirect reports and tolerates external managers. |
| **Business value** | Correct, complete access model |
| **Acceptance criteria** | Manager resolved by **ID** (never daily name); external IDs stubbed; self-reference recognised as root; circular chains quarantined |
| **Priority** | P0 |
| **Dependencies** | C1 |

### C3
| | |
|---|---|
| **Persona** | Administrator |
| **Story** | As an admin, I resolve unmapped attended employees (e.g. ED577). |
| **Business value** | No orphaned metrics |
| **Acceptance criteria** | Unmapped attended employees quarantined and surfaced; resolvable to a master record |
| **Priority** | P1 |
| **Dependencies** | B1, C1 |

## Epic D — Metrics & Insights

### D1
| | |
|---|---|
| **Persona** | Reporting Manager |
| **Story** | As a manager, I see who is frequently late, judged by their own shift, with evidence. |
| **Business value** | Fair, shift-aware lateness |
| **Acceptance criteria** | Late computed on fixed shifts only; flexible exempt unless core start set; ≥20% of eligible days flags; drill-through to days |
| **Priority** | P0 |
| **Dependencies** | C1, C2 |

### D2
| | |
|---|---|
| **Persona** | Reporting Manager |
| **Story** | As a manager, I see who is frequently below required hours using configurable required hours. |
| **Business value** | Support conversation on workload/hours |
| **Acceptance criteria** | Effective hours vs required (default 510); >20% of attended days flags; approved half-days excluded |
| **Priority** | P0 |
| **Dependencies** | C1 |

### D3
| | |
|---|---|
| **Persona** | Reporting Manager |
| **Story** | As a manager, absence is never inferred from a missing row alone. |
| **Business value** | No false accusations (highest-consequence correctness) |
| **Acceptance criteria** | Confirmed absence only when all §16 conditions hold; potential vs confirmed shown separately |
| **Priority** | P0 |
| **Dependencies** | B5 |

### D4
| | |
|---|---|
| **Persona** | Reporting Manager |
| **Story** | As a manager, I see remote clock-in percentage, not treated as negative by default. |
| **Business value** | Neutral, arrangement-aware remote view |
| **Acceptance criteria** | Remote % from premise map; flagged only >75% or ≠ arrangement |
| **Priority** | P0 |
| **Dependencies** | C1 |

### D5
| | |
|---|---|
| **Persona** | Reporting Manager |
| **Story** | As a manager, missing punches show as data quality, not absence or short hours. |
| **Business value** | Separates process problems from behaviour |
| **Acceptance criteria** | Missing in/out classified; 204/11 sample cases handled; feeds data-quality score |
| **Priority** | P0 |
| **Dependencies** | B1 |

## Epic E — Dashboards & Drill-down

### E1
| | |
|---|---|
| **Persona** | Reporting Manager |
| **Story** | As a manager, current-month analytics load by default with scoped filters. |
| **Business value** | Fast, relevant first view |
| **Acceptance criteria** | Default month-to-date; filters for dept, sub-dept, location, manager, date; values scoped to my line |
| **Priority** | P0 |
| **Dependencies** | D1–D5 |

### E2
| | |
|---|---|
| **Persona** | Reporting Manager |
| **Story** | As a manager, every dashboard metric drills into the days and punches behind it. |
| **Business value** | Explainability (G5) |
| **Acceptance criteria** | Every card & insight opens supporting evidence |
| **Priority** | P0 |
| **Dependencies** | E1 |

### E3
| | |
|---|---|
| **Persona** | Administrator / Read-Only |
| **Story** | As an admin, I see an organisation-wide dashboard. |
| **Business value** | Org oversight |
| **Acceptance criteria** | Org totals; filters unrestricted; read-only for Management Viewer |
| **Priority** | P0 |
| **Dependencies** | D1–D5 |

## Epic F — Review Workflow

### F1
| | |
|---|---|
| **Persona** | Reporting Manager |
| **Story** | As a manager, I record a review status and notes against an insight. |
| **Business value** | Turns insight into supportive action |
| **Acceptance criteria** | Statuses (New…Resolved); private & HR-visible notes; notes stored separately from raw data |
| **Priority** | P0 |
| **Dependencies** | E2 |

### F2
| | |
|---|---|
| **Persona** | Reporting Manager |
| **Story** | As a manager, I see coaching prompts to use insights responsibly. |
| **Business value** | Responsible, humane use |
| **Acceptance criteria** | Guidance panel with suggested questions per pattern type |
| **Priority** | P1 |
| **Dependencies** | E2 |

## Epic G — Administration & Policy

### G1
| | |
|---|---|
| **Persona** | Administrator |
| **Story** | As an admin, I configure late threshold, frequency %, required hours and remote threshold without code. |
| **Business value** | Policy stays configurable |
| **Acceptance criteria** | Policy scoping + effective dating; changes recalculate metrics; old/new logged |
| **Priority** | P0 |
| **Dependencies** | — |

### G2
| | |
|---|---|
| **Persona** | Administrator |
| **Story** | As an admin, I manage administrators but cannot remove the final super-admin. |
| **Business value** | Prevents lockout |
| **Acceptance criteria** | Add/remove admins; system blocks removal of final super-admin access |
| **Priority** | P0 |
| **Dependencies** | A1 |

### G3
| | |
|---|---|
| **Persona** | Administrator |
| **Story** | As an admin, I review and action pending access requests. |
| **Business value** | Controlled onboarding |
| **Acceptance criteria** | Pending queue; grant role / decline; decision logged |
| **Priority** | P0 |
| **Dependencies** | A3 |

### G4
| | |
|---|---|
| **Persona** | Administrator |
| **Story** | As an admin, I manage shifts, holidays and premise mappings. |
| **Business value** | Correct classification |
| **Acceptance criteria** | CRUD with effective dates; premise map covers Office/Remote/Adjustment/unclassified |
| **Priority** | P0 |
| **Dependencies** | C1 |

## Epic H — Audit, Export & Governance

### H1
| | |
|---|---|
| **Persona** | Administrator |
| **Story** | As an admin, all sensitive admin and user actions are auditable. |
| **Business value** | Accountability |
| **Acceptance criteria** | Audited: login, failed login, access/admin change, import, rollback, policy change, correction, export, note, review change; no tokens/file contents logged |
| **Priority** | P0 |
| **Dependencies** | — |

### H2
| | |
|---|---|
| **Persona** | Manager / Admin / Read-Only |
| **Story** | As a permitted user, I export core reports with row-level access applied. |
| **Business value** | Shareable, controlled reporting |
| **Acceptance criteria** | Exports record who/when/filters/count/type; scoped to hierarchy; spreadsheet-formula-injection neutralised |
| **Priority** | P1 |
| **Dependencies** | E1 |

## Acceptance Criteria Master List

The 24 MVP acceptance criteria are the single source in [`01_PRODUCT_REQUIREMENTS.md`](01_PRODUCT_REQUIREMENTS.md#12-acceptance-criteria). Story-level criteria above must all map to one or more of AC1–AC24.
