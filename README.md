# Edvoy Workforce Attendance Intelligence Platform

| | |
|---|---|
| **Version** | 1.0.0 |
| **Last updated** | 2026-08-02 |
| **Status** | Phases 1–2 complete · Phase 3 (UX) next |

Management-facing attendance intelligence over Edvoy's existing Keka exports. It turns daily attendance, the employee master, leave and holidays into policy-aware, explainable insights that help managers support their teams — **not** a surveillance or scoring tool.

## Start here

👉 **[docs/10_MASTER_SPECIFICATION.md](docs/10_MASTER_SPECIFICATION.md)** — the project homepage and single source of truth.

## Document index

| Doc | Purpose |
|---|---|
| [01 Product Requirements](docs/01_PRODUCT_REQUIREMENTS.md) | What, for whom, why; MVP vs later scope |
| [02 Data Specification](docs/02_DATA_SPECIFICATION.md) | Sources, dictionary, mappings, validation, import rules |
| [03 Attendance Rules](docs/03_ATTENDANCE_RULES.md) | Every formula, threshold and edge case |
| [04 Permission Matrix](docs/04_PERMISSION_MATRIX.md) | Roles, hierarchy, access matrices |
| [05 User Stories](docs/05_USER_STORIES.md) | Epics & stories with acceptance criteria |
| [06 Dashboard Specification](docs/06_DASHBOARD_SPECIFICATION.md) | Every screen and its states |
| [07 UX Decisions](docs/07_UX_DECISIONS.md) | Navigation, accessibility, terminology |
| [08 Technical Constraints](docs/08_TECHNICAL_CONSTRAINTS.md) | Stack, security, privacy, performance |
| [09 Changelog](docs/09_CHANGELOG.md) | Version history & decision log |
| [10 Master Specification](docs/10_MASTER_SPECIFICATION.md) | Project homepage |

## Repository structure

```
edvoy-wai/
├── README.md                          # This file — entry point
├── docs/                              # Single source of truth (Phases 1–2)
│   ├── 01_PRODUCT_REQUIREMENTS.md
│   ├── 02_DATA_SPECIFICATION.md
│   ├── 03_ATTENDANCE_RULES.md
│   ├── 04_PERMISSION_MATRIX.md
│   ├── 05_USER_STORIES.md
│   ├── 06_DASHBOARD_SPECIFICATION.md
│   ├── 07_UX_DECISIONS.md
│   ├── 08_TECHNICAL_CONSTRAINTS.md
│   ├── 09_CHANGELOG.md
│   └── 10_MASTER_SPECIFICATION.md
│
├── prototype/                         # Phase 3 — clickable prototype (not yet created)
│   ├── wireframes/
│   └── design-system/
│
├── apps/                              # Phase 5 — application code (not yet created)
│   └── web/                           # Next.js modular monolith
│       ├── app/                       # Routes & server handlers
│       ├── components/                # UI components
│       ├── lib/                       # Metric engine, import pipeline, auth
│       └── tests/                     # Unit · integration · e2e
│
├── packages/                          # Shared TS packages (types, config)
│
├── prisma/                            # Phase 5 — schema & migrations
│   ├── schema.prisma
│   ├── migrations/
│   └── seed.ts                        # Demonstration seed data
│
├── infra/                             # Phase 7 — Terraform / CDK
│   ├── dev/
│   ├── staging/
│   └── prod/
│
├── .github/
│   └── workflows/                     # CI/CD (tests, migrations, scanning)
│
└── ops/                               # Phase 8 — runbook & operational docs
    └── runbook.md
```

> Only `README.md` and `docs/` exist today. The remaining folders are the agreed target layout for later phases and are shown so the whole team shares one map.

## Phase status

| Phase | Status |
|---|---|
| 1 Product Discovery | ✅ |
| 2 Data Discovery | ✅ |
| 3 UX & Prototype | ⏭ Next |
| 4 Technical Design | Planned |
| 5 MVP Development | Planned |
| 6 Hardening | Planned |
| 7 Deployment | Planned |
| 8 Handover | Planned |

## Pending decisions

Ten decisions remain open (PD1–PD10) and must not be assumed — see [Master Spec §10](docs/10_MASTER_SPECIFICATION.md#10-pending-decisions).
