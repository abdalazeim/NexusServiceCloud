# Enterprise Repository Structure & Architecture Blueprint

> Document ID: NSC-REF-ERS-01  
> Version: 1.1 (professionally formatted)  
> Date: 2026-11-09  
> Status: Preserved reference input for NexusServiceCloud  
> Document type: Repository structure and architecture specification reference  
> Source context: NexusAsset Cloud enterprise blueprint

## Important Notice

This document is a **formatted reference blueprint**. It preserves the structure and architectural intent of the source material while making its status explicit for NexusServiceCloud:

- It is **not** evidence that NexusServiceCloud has implemented the listed platform, infrastructure, CI/CD, or security assets.
- NexusAsset/Lansweeper domain examples in the source tree are **reference examples only** and must not be copied into NexusServiceCloud scope unless explicitly approved.
- NexusServiceCloud should adapt the organizational patterns (bounded contexts, clean architecture, contracts, governance, quality gates, observability, security) to its own `nsc_*` service-management domain.
- Technology/version baselines listed below are **target/reference baselines** from the source document; NexusServiceCloud-specific versions require separate ADRs and compatibility validation.

---

## 1. Project Overview

The reference blueprint describes an open-source enterprise platform designed to unify IT asset discovery, asset lifecycle management, CMDB capabilities, IT operations, and enterprise business workflows. In the source context, it integrates with Lansweeper for asset data collection and synchronization, with additional governance, compliance, reporting, billing, auditing, and operational capabilities.

### 1.1 Reference Capability Goals

| Capability | Reference goal |
|---|---|
| Multi-tenancy | SaaS-ready tenant isolation and tenant-aware operations. |
| ITAM / CMDB | Asset lifecycle and configuration-management foundations. |
| Integration | External discovery/data synchronization patterns. |
| Governance | RBAC, audit trails, compliance controls, and policy enforcement. |
| APIs | REST and event-driven contracts. |
| Channels | Web and mobile application surfaces. |
| Delivery | Infrastructure provisioning, CI/CD, and release management. |
| Operations | Observability, backup/recovery, and incident/runbook practices. |

### 1.2 Core Architectural Principles

| Principle | Intent |
|---|---|
| Domain-Driven Design (DDD) | Align bounded contexts to business capabilities with explicit boundaries and language. |
| Clean Architecture | Keep domain/application independent of frameworks, databases, and delivery mechanisms. |
| Separation of concerns | Each module owns its logic and exposed interfaces; avoid hidden cross-module coupling. |
| SOLID | Improve maintainability, testability, and controlled evolution. |
| API-first / contract-first | Define contracts before implementation; generated clients are derived artifacts. |
| Secure by default | Defense-in-depth authorization, least privilege, and tenant isolation. |
| Database-level tenant isolation | Apply tenant context and data controls (e.g., RLS where applicable) as part of defense in depth. |
| Infrastructure as Code | Version-controlled infrastructure, deployment, and environment definitions. |
| Automated quality gates | Linting, typing, tests, scans, and release checks in CI/CD. |
| Observable & recoverable systems | Metrics, logs, traces, SLOs, backups, and disaster-recovery procedures. |
| Documentation-driven engineering | ADRs, runbooks, contracts, and traceability maintained with change. |

---

## 2. Complete Repository Structure (Reference)

```text
nexusasset-cloud/
│
├── README.md
├── CHANGELOG.md
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
├── SECURITY.md
├── SUPPORT.md
├── LICENSE
├── Makefile
│
├── .editorconfig
├── .gitattributes
├── .gitignore
├── .dockerignore
├── .pre-commit-config.yaml
├── .env.example
├── .npmrc
├── .python-version
├── .node-version
│
├── pyproject.toml
├── uv.lock
├── package.json
├── pnpm-lock.yaml
├── pnpm-workspace.yaml
├── turbo.json
│
├── compose.yaml
├── compose.dev.yaml
├── compose.staging.yaml
├── compose.prod.yaml
│
├── project-meta/
│   ├── governance/
│   ├── policies/
│   ├── adr/
│   ├── standards/
│   ├── runtime/
│   └── templates/
│
├── docs/
│   ├── 00-overview/
│   ├── 01-business-analysis/
│   ├── 02-requirements/
│   ├── 03-system-architecture/
│   ├── 04-domain-design/
│   ├── 05-implementation/
│   ├── 06-testing-quality/
│   ├── 07-deployment/
│   ├── 08-operations/
│   ├── 09-security/
│   ├── 10-api-contracts/
│   ├── 11-database/
│   ├── 12-design-system/
│   ├── 13-devops-cicd/
│   ├── 14-observability/
│   ├── 15-disaster-recovery/
│   ├── 16-performance-scalability/
│   ├── 17-integrations/
│   ├── 18-mobile/
│   ├── 19-user-guides/
│   ├── 20-adr-index/
│   ├── 21-diagrams/
│   └── 22-appendices/
│
├── prompts/
│   └── 00..20 phase/discipline prompts
│
├── platform/
│   ├── backend/
│   ├── frontend/
│   ├── mobile/
│   ├── packages/
│   └── worker/
│
├── contracts/
│   ├── openapi/
│   ├── asyncapi/
│   ├── events/
│   ├── webhooks/
│   └── generated/
│
├── design-system/
├── infrastructure/
├── deployment/
├── security/
├── observability/
├── ops/
├── tests/
├── quality/
├── scripts/
└── archive/
```

### 2.1 Structure Intent by Area

| Area | Intent |
|---|---|
| `project-meta/` | Governance, policies, standards, ADR support, runtime compatibility, templates. |
| `docs/` | End-to-end documentation from overview to operations, security, ADRs, and appendices. |
| `prompts/` | Controlled phased execution/review instructions. |
| `platform/` | Application implementation target (backend, frontend, mobile, packages, worker). |
| `contracts/` | Authoritative API/event contracts and derived/generated clients. |
| `design-system/` | Design tokens, themes, components, patterns, accessibility guidance. |
| `infrastructure/` & `deployment/` | Runtime topology, IaC/deployment automation, environment definitions. |
| `security/` | Threat model, IAM, secrets, tenant isolation, scans, compliance, response. |
| `observability/` & `ops/` | Telemetry, dashboards, alerts, runbooks, backup/restore, DR, maintenance. |
| `tests/` & `quality/` | Verification layers and quality gates. |
| `scripts/` | Repeatable operational/developer automation entry points. |
| `archive/` | Historical inputs and snapshots, kept out of active implementation paths. |

---

## 3. Technology Stack (Reference Baseline)

> Compatibility requirement: versions are a target baseline, not proof of compatibility. NexusServiceCloud must validate framework/runtime/plugin compatibility and record approved versions in its own ADRs/matrices before implementation.

| Layer | Reference technology | Responsibility |
|---|---|---|
| Backend language | Python 3.13 | Application development |
| Backend framework | Django 5.2 LTS | Business logic and REST services |
| Frontend framework | Next.js 16 | Web application (App Router / RSC where appropriate) |
| UI library | React 19 | Component-based UI |
| Frontend language | TypeScript | Type safety |
| Styling / UI | Tailwind CSS 4, shadcn/ui | Styling and reusable components |
| Database | PostgreSQL 18 | Primary relational data store |
| Cache / tasks | Redis 8, Celery | Cache/background jobs |
| Messaging (optional) | RabbitMQ | Advanced async messaging when justified |
| JS monorepo tooling | pnpm, Turborepo | Package/build orchestration |
| Contracts | OpenAPI 3.1, AsyncAPI | REST and event contracts |
| Mobile | Kotlin, Swift | Native mobile clients (if adopted) |
| Containers / edge | Docker, Docker Compose, Traefik v3 | Packaging, orchestration, routing/TLS |
| CI/CD / automation | Jenkins LTS, Ansible | Delivery pipelines and infrastructure automation |
| Observability | Prometheus, Grafana, Loki, OpenTelemetry, Tempo | Metrics, dashboards, logs, traces |
| Security/quality | Trivy, Gitleaks, SonarQube | Scanning and code quality |
| Testing | Pytest, Vitest, Playwright, k6 | Unit/integration/UI/E2E/performance |
| DB backup | pgBackRest / WAL-G | PostgreSQL backup and recovery |

---

## 4. Backend Architecture Reference

### 4.1 Architectural Pattern

The reference backend follows a **modular monolith** with DDD and Clean Architecture:

- Bounded contexts with explicit module boundaries.
- Domain entities/value objects and application/domain services.
- Infrastructure adapters behind ports/interfaces.
- Domain events and explicit integration contracts.
- Avoid premature microservice decomposition; extract only with evidence.

### 4.2 Reference Bounded Contexts (Source Example)

The source NexusAsset example includes contexts such as core, accounts, identity, tenants, organizations, assets, asset lifecycle, Lansweeper, integrations, billing, subscriptions, notifications, audit, reporting, and compliance.

### 4.3 NexusServiceCloud Context Mapping

NexusServiceCloud should use its own service-management contexts (`nsc_*`) instead of NexusAsset/Lansweeper contexts. Indicative mapping:

| NexusServiceCloud context | Primary responsibility |
|---|---|
| `nsc_identity` | Identity, sessions, memberships, authorization inputs |
| `nsc_tenants` | Organizations, sites, departments, service desks |
| `nsc_catalog` | Services, catalog items, forms, audience |
| `nsc_requests` | Incidents/service requests, assignment, comments, state history |
| `nsc_service_levels` | SLA/OLA policies, calendars, escalations, measurement |
| `nsc_approvals` | Approval policies, decisions, delegation |
| `nsc_problems` / `nsc_changes` / `nsc_delivery` | Problem, change, release/delivery control records |
| `nsc_assets` / `nsc_cmdb` | Asset and configuration relationships (only if in approved scope) |
| `nsc_procurement` | Purchase/receiving/vendor/contract records related to service |
| `nsc_knowledge` | Knowledge lifecycle and audience |
| `nsc_integrations` | Email, monitoring, discovery connectors, field mappings |
| `nsc_insights` | Reports/surveys/derived read models |
| `nsc_audit` / `nsc_notifications` / `nsc_ai` | Audit, delivery, and assistive AI controls |

---

## 5. Frontend Architecture Reference

The reference frontend pattern uses a modern React application with App Router conventions, feature-oriented organization, strict typing, accessible UI, centralized validation, and generated API clients.

### 5.1 Frontend Standards

| Standard | Expected practice |
|---|---|
| Feature orientation | Co-locate feature UI, hooks, types, and integration boundaries. |
| Typed contracts | Use generated or centrally maintained typed clients from authoritative contracts. |
| Validation | Centralize request/form validation; never trust client-only checks. |
| Accessibility | Target WCAG-oriented semantics, keyboard support, and contrast rules. |
| Responsive/i18n | Support responsive layouts and RTL/LTR where required. |
| Design tokens | Use a single token source; avoid hardcoded visual primitives. |
| Security boundary | Frontend is never the final authorization boundary. |

---

## 6. Multi-Tenancy and Database Architecture Reference

The reference design treats tenant isolation as a defense-in-depth requirement.

| Control | Target behavior |
|---|---|
| Tenant-aware model | Tenant ownership is explicit on tenant-scoped records. |
| Tenant context | Tenant context is set and validated per request/transaction. |
| RLS / data controls | Data-level controls restrict cross-tenant reads/writes where applicable. |
| Least privilege | Runtime database roles avoid unnecessary bypass privileges. |
| Background work | Jobs/tasks propagate and validate tenant context safely. |
| Verification | Automated tests/checklists verify cross-tenant isolation and admin exceptions. |

NexusServiceCloud mapping: organizations are isolated tenants; departments and service desks inside an organization are operational scopes unless a later approved decision states otherwise.

---

## 7. API and Integration Architecture Reference

### 7.1 API Standards

- Contract-first REST (OpenAPI 3.1 as reference).
- Versioning, consistent errors, pagination, filtering, sorting, validation.
- Authentication/authorization and rate limits.
- Idempotency for applicable operations.
- Structured documentation and traceable changes.

### 7.2 Contract Source of Truth

Authoritative contracts should live in dedicated contract locations. Generated clients/types are derived artifacts and must not become independent hand-maintained sources.

### 7.3 Integration Pattern

| Concern | Practice |
|---|---|
| Connectivity | Explicit adapters/ports per external system. |
| Reliability | Retries, backoff, idempotency, and reconciliation rules. |
| Data quality | Normalization, duplicate handling, and field ownership/mapping. |
| Failure isolation | External failures must not corrupt internal operational records. |
| Auditability | Synchronization history and relevant events are recorded. |

NexusServiceCloud integrations are expected to focus on identity, email, monitoring, service-related discovery/connectors, and approved external systems—not Lansweeper by default.

---

## 8. Event-Driven Architecture Reference

Asynchronous processing is used where it improves reliability, scalability, and responsiveness.

| Capability | Purpose |
|---|---|
| Domain/integration events | Communicate significant state changes across boundaries. |
| Transactional outbox | Publish events reliably relative to committed state changes. |
| Idempotent handlers | Make retries safe. |
| Retry/DLQ policies | Handle transient and permanent processing failures. |
| Event versioning | Support schema evolution. |
| Monitoring/audit | Observe event flow and preserve operational evidence. |

---

## 9. Design System Reference

| Component | Purpose |
|---|---|
| Tokens | Single source for color, typography, spacing, sizing, radius, shadows, motion. |
| Themes | Light/dark/high-contrast and brand-consistent variants. |
| Components/patterns | Reusable accessible UI building blocks and interaction patterns. |
| Documentation | Usage rules, accessibility, RTL/LTR, and implementation guidance. |

---

## 10. DevOps and Infrastructure Reference

### 10.1 Containerization and Runtime

Reference services may include API, frontend, workers/schedulers, data stores, cache/messaging, edge routing, and observability components. Production-oriented standards include health checks, explicit image versions, least privilege, resource limits, secure configuration, persistent state handling, and structured logging.

### 10.2 Deployment and Automation

| Area | Reference practice |
|---|---|
| Docker Compose | Environment-specific orchestration overlays for dev/staging/prod. |
| IaC / config management | Ansible or equivalent automation for provisioning, deployment, rollback, backup/restore. |
| CI/CD | Automated lint, type checks, tests, contracts, security scans, build, release gates. |
| Release management | Versioning, checklists, rollback plans, migration policies. |

---

## 11. Security Architecture Reference

| Layer | Control examples |
|---|---|
| Network/edge | TLS termination, routing controls, firewall/network segmentation. |
| Application | RBAC/ABAC, tenant-aware authorization, validation, secure output handling. |
| Data | RLS/tenant controls, least-privilege roles, encryption, audit logging. |
| Secrets/supply chain | Managed secrets, rotation, SBOM/signing/provenance where adopted. |
| Detection/response | Security logging, anomaly detection, incident response runbooks. |

Security controls are design targets until implemented, verified, and approved.

---

## 12. Testing Strategy Reference

| Test type | Focus |
|---|---|
| Unit | Isolated domain/application/component behavior. |
| Integration | Persistence, adapters, APIs, and service collaboration. |
| Contract | OpenAPI/AsyncAPI and consumer/provider compatibility. |
| Architecture | Dependency direction and module boundary rules. |
| Security | Vulnerability scanning, configuration checks, and abuse cases. |
| E2E | Critical user workflows across channels. |
| Performance/resilience | Load, stress, failover, and recovery behavior. |
| Accessibility/migration | WCAG-oriented checks and schema/data migration safety. |

Coverage targets in the source are examples, not NexusServiceCloud commitments.

---

## 13. NexusServiceCloud Adoption Guidance

1. **Preserve meaning, adapt scope:** reuse structural patterns, not NexusAsset business scope.
2. **Rename contexts deliberately:** use `nsc_*` bounded contexts aligned to service management.
3. **Keep contracts authoritative:** do not let generated artifacts replace source contracts.
4. **Record decisions:** create NexusServiceCloud ADRs for stack, tenancy, integration, and operational choices.
5. **Prove before claiming:** tests, scans, runbooks, and production controls must be evidenced before readiness claims.
6. **Use this file as reference:** treat it as an input to design governance, not an implementation checklist already completed.
