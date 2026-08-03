# Technical Constraints

| | |
|---|---|
| **Version** | 1.0.0 |
| **Last updated** | 2026-08-02 |
| **Status** | Recommended (to be ratified in Phase 4 ADR) |
| **Related** | [Master Spec](10_MASTER_SPECIFICATION.md) · [PRD](01_PRODUCT_REQUIREMENTS.md) · [Data Spec](02_DATA_SPECIFICATION.md) |

> **Purpose.** Summarise the agreed/recommended technical constraints. The stack is the PRD's preferred stack "unless a better justified option is proposed"; Phase 4 records the final Architecture Decision Record.

---

## Table of Contents

1. [Preferred Technology Stack](#1-preferred-technology-stack)
2. [Hosting](#2-hosting)
3. [Database](#3-database)
4. [Authentication](#4-authentication)
5. [File Storage](#5-file-storage)
6. [Background Jobs](#6-background-jobs)
7. [Testing](#7-testing)
8. [Logging](#8-logging)
9. [Monitoring](#9-monitoring)
10. [CI/CD](#10-cicd)
11. [Infrastructure](#11-infrastructure)
12. [Coding Standards](#12-coding-standards)
13. [Performance Targets](#13-performance-targets)
14. [Security Constraints](#14-security-constraints)
15. [Privacy Constraints](#15-privacy-constraints)

---

## 1. Preferred Technology Stack

| Layer | Choice |
|---|---|
| Front end | Next.js · TypeScript · React · Tailwind CSS · accessible component library · charts via a reliable React chart library |
| Back end | Modular monolith on Next.js route handlers/server components (or NestJS if separation is justified) — **modular monolith preferred for the first release** |
| Database | PostgreSQL + Prisma ORM (or equally reliable typed ORM) |
| Auth | Google OAuth via Auth.js (or equivalent maintained library) |
| Storage | AWS S3 or equivalent secure object storage |
| Jobs | BullMQ + Redis (or AWS SQS / managed job service) |
| IaC | Terraform or AWS CDK |

## 2. Hosting

- Front end & API on **AWS ECS Fargate**, App Runner, or a clearly justified alternative.
- Separate **development, staging and production** environments.
- A simpler option (Vercel + managed PostgreSQL) is acceptable **only if** it meets Edvoy's data, security and operational needs.

## 3. Database

- **PostgreSQL** with migrations, proper indexes, and effective-dated modelling for assignments, manager relationships, shifts, policies and locations.
- Row-level permission checks in the service layer; **PostgreSQL row-level security where practical**.
- Store timestamps in **UTC**, display in local timezone (Asia/Kolkata).
- Store both **original imported values and normalised values**; business key = Employee Number, primary keys = UUIDs.

## 4. Authentication

- Google OAuth / Workspace, restricted to approved domains (edvoy.com initially).
- Secure server-side sessions; role- and hierarchy-based access; server-side checks on every endpoint.

## 5. File Storage

- Private, encrypted buckets; **pre-signed access only**; retention policies (periods = [PD9](10_MASTER_SPECIFICATION.md#10-pending-decisions)).

## 6. Background Jobs

Queue-backed jobs for: file processing, metric recalculation, notifications, large exports. Import processing must be idempotent and scoped by date range.

## 7. Testing

| Level | Coverage |
|---|---|
| Unit | Date/time/duration parsing, late & short-hours calc, remote classification, eligible-day calc, absence classification, overnight handling, thresholds, policy effective dates, hierarchy, permission checks, duplicate detection |
| Integration | Employee import, attendance import, reimport, updated import, partial failure, rollback, manager login, admin access, row-level access, metric recalculation, export permissions |
| End-to-end | Google sign-in, manager dashboard journey, import workflow, filters, drill-down, review workflow, admin creation, policy update, export, access denial |
| Performance | 5,000+ employees, years of daily attendance, daily imports, large dashboard queries, concurrent managers, large exports |

## 8. Logging

- Application logs and import-processing logs.
- **Never log sensitive file contents or session tokens.**

## 9. Monitoring

- Error monitoring, performance monitoring, failed-job dashboard, health-check endpoints, database & storage monitoring, backup monitoring.
- Alerting for failed daily imports and repeated application errors. CloudWatch (or equivalent).

## 10. CI/CD

- Pipeline with automated tests, database migrations, seed data, and dependency scanning.
- Deployment and rollback guides; environment promotion dev → staging → prod.

## 11. Infrastructure

- AWS reference: ECS Fargate / RDS PostgreSQL / S3 / Secrets Manager / CloudWatch / SES; IaC via Terraform or CDK.

## 12. Coding Standards

- TypeScript throughout; typed ORM; linting & formatting enforced in CI.
- Server-side authorisation is mandatory; front-end checks are never the sole guard.
- Migrations for every schema change; no ad-hoc production DDL.

## 13. Performance Targets

| Target | Basis |
|---|---|
| Scale | ≥ 5,000 employees, several years of daily attendance |
| Dashboard load | Fast under large queries (specific SLA set in Phase 4) |
| Daily import | Complete within the daily operational window via background jobs |
| Concurrency | Many managers concurrently without degradation |
| Large exports | Handled asynchronously without blocking the UI |

> Numeric SLAs are finalised in Phase 4; the PRD requires "measurable performance targets" against the workloads above.

## 14. Security Constraints

Encryption in transit & at rest · least privilege · managed secrets · CSRF protection · input & file validation · rate limiting · **spreadsheet formula-injection neutralisation** (cells starting `= + − @`) · SQL-injection & XSS protection · secure session cookies · audit logging · dependency scanning · security headers · backup & restore · data-retention controls · soft deletion where appropriate · admin-action confirmation · export controls · sensitive-data masking where practical.

## 15. Privacy Constraints

- Attendance & leave data treated as confidential employee data.
- Row-level access by reporting hierarchy; a manager sees only their line.
- No compilation of personal data across unrelated sources.
- Subject-access and data-deletion workflows are **Phase 2**; retention periods are [PD9](10_MASTER_SPECIFICATION.md#10-pending-decisions).
