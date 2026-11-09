1. Project Overview

NexusAsset Cloud is an open-source enterprise platform designed to unify IT asset discovery, asset lifecycle management, CMDB capabilities, IT operations, and enterprise business workflows within a centralized platform. The platform integrates with Lansweeper to collect, synchronize, and manage IT asset information while providing additional enterprise capabilities for governance, compliance, reporting, billing, auditing, and operational management.

The architecture is designed to support multi-tenant SaaS, enterprise ITAM, CMDB, Lansweeper integration, asset lifecycle management, organization and tenant management, RBAC, audit trails, REST API and event-driven integrations, web and mobile applications, automated infrastructure provisioning, centralized observability, and AI-assisted development.
1.1 Core Architectural Principles

    Domain-Driven Design (DDD): Bounded contexts aligned to business capabilities, with explicit aggregates, domain events, and ubiquitous language within each context.

    Clean Architecture: Dependency inversion; domain and application layers independent of frameworks, databases, and delivery mechanisms.

    Separation of Concerns: Each module owns its domain logic, data access, and API surface. No cross-module database access.

    SOLID Principles: Single responsibility, open/closed, Liskov substitution, interface segregation, dependency inversion.

    API-First Development: Contracts defined before implementation; OpenAPI 3.1 as the authoritative REST contract.

    Contract-First API Design: contracts/openapi/openapi.yaml is the single source of truth for REST interfaces; generated clients are derived, never hand-maintained as independent sources.

    Secure-by-Default Architecture: Tenant isolation at the database level, defense-in-depth authorization, least-privilege database roles, and secrets management.

    Database-Level Tenant Isolation: PostgreSQL Row-Level Security (RLS) enforced on every tenant-scoped table, combined with application-layer tenant filtering.

    Infrastructure as Code (IaC): Ansible playbooks, Docker Compose manifests, and version-controlled environment definitions.

    Automated Testing and Quality Gates: Unit, integration, contract, security, architecture, E2E, performance, and resilience tests enforced in CI.

    Continuous Integration and Continuous Deployment (CI/CD): Jenkins-based pipelines with automated quality gates, security scanning, and release management.

    Observable and Recoverable Systems: OpenTelemetry instrumentation, Prometheus metrics, Loki logs, Tempo traces, Grafana dashboards, and documented disaster recovery procedures.

    Documentation-Driven Engineering: Architecture Decision Records (ADRs), runbooks, API contracts, and operational documentation maintained alongside code.

2. Complete Repository Structure
text

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
│   │   ├── project-charter.md
│   │   ├── engineering-principles.md
│   │   ├── architecture-governance.md
│   │   ├── coding-standards.md
│   │   ├── documentation-standards.md
│   │   ├── change-management.md
│   │   ├── release-governance.md
│   │   └── quality-gates.md
│   │
│   ├── policies/
│   │   ├── security-policy.md
│   │   ├── privacy-policy.md
│   │   ├── data-classification.md
│   │   ├── retention-policy.md
│   │   ├── backup-policy.md
│   │   ├── dependency-policy.md
│   │   ├── access-control-policy.md
│   │   └── tenant-isolation-policy.md
│   │
│   ├── adr/
│   │   ├── README.md
│   │   └── templates/adr-template.md
│   │
│   ├── standards/
│   │   ├── api-standards.md
│   │   ├── database-standards.md
│   │   ├── frontend-standards.md
│   │   ├── backend-standards.md
│   │   ├── devops-standards.md
│   │   └── testing-standards.md
│   │
│   ├── runtime/
│   │   ├── supported-versions.md
│   │   ├── compatibility-matrix.md
│   │   └── dependency-lifecycle.md
│   │
│   └── templates/
│       ├── adr-template.md
│       ├── feature-template.md
│       ├── incident-template.md
│       └── release-template.md
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
│   ├── 00-global-context.md
│   ├── 01-foundation.md
│   ├── 02-domain-analysis.md
│   ├── 03-backend-clean-architecture.md
│   ├── 04-database-multitenancy.md
│   ├── 05-api-openapi.md
│   ├── 06-lansweeper-integration.md
│   ├── 07-frontend-nextjs.md
│   ├── 08-design-system.md
│   ├── 09-mobile.md
│   ├── 10-event-driven-architecture.md
│   ├── 11-testing-quality.md
│   ├── 12-security.md
│   ├── 13-devops-infrastructure.md
│   ├── 14-cicd-automation.md
│   ├── 15-observability.md
│   ├── 16-backup-disaster-recovery.md
│   ├── 17-performance-optimization.md
│   ├── 18-documentation-adr.md
│   ├── 19-release-deployment.md
│   └── 20-final-audit.md
│
├── platform/
│   │
│   ├── backend/
│   │   ├── manage.py
│   │   ├── pyproject.toml
│   │   │
│   │   ├── config/
│   │   │   ├── settings/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── base.py
│   │   │   │   ├── local.py
│   │   │   │   ├── test.py
│   │   │   │   ├── staging.py
│   │   │   │   └── production.py
│   │   │   ├── urls.py
│   │   │   ├── asgi.py
│   │   │   ├── wsgi.py
│   │   │   ├── celery.py
│   │   │   └── logging.py
│   │   │
│   │   ├── contexts/
│   │   │   ├── core/
│   │   │   ├── accounts/
│   │   │   ├── identity/
│   │   │   ├── tenants/
│   │   │   ├── organizations/
│   │   │   ├── assets/
│   │   │   ├── asset_lifecycle/
│   │   │   ├── lansweeper/
│   │   │   ├── integrations/
│   │   │   ├── billing/
│   │   │   ├── subscriptions/
│   │   │   ├── notifications/
│   │   │   ├── audit/
│   │   │   ├── reporting/
│   │   │   └── compliance/
│   │   │
│   │   ├── shared/
│   │   │   ├── kernel/
│   │   │   ├── exceptions/
│   │   │   ├── events/
│   │   │   ├── permissions/
│   │   │   ├── tenant_context/
│   │   │   ├── observability/
│   │   │   └── utilities/
│   │   │
│   │   └── tests/
│   │       ├── unit/
│   │       ├── integration/
│   │       ├── security/
│   │       ├── architecture/
│   │       └── factories/
│   │
│   ├── frontend/
│   │   ├── next.config.ts
│   │   ├── tsconfig.json
│   │   ├── eslint.config.mjs
│   │   ├── components.json
│   │   ├── package.json
│   │   ├── public/
│   │   │
│   │   └── src/
│   │       ├── app/
│   │       │   ├── (public)/
│   │       │   ├── (auth)/
│   │       │   ├── (dashboard)/
│   │       │   │   ├── dashboard/
│   │       │   │   ├── assets/
│   │       │   │   ├── tenants/
│   │       │   │   ├── organizations/
│   │       │   │   ├── lansweeper/
│   │       │   │   ├── integrations/
│   │       │   │   ├── billing/
│   │       │   │   ├── subscriptions/
│   │       │   │   ├── audit/
│   │       │   │   ├── compliance/
│   │       │   │   ├── reports/
│   │       │   │   ├── notifications/
│   │       │   │   ├── settings/
│   │       │   │   └── profile/
│   │       │   ├── api/
│   │       │   │   ├── auth/
│   │       │   │   ├── proxy/
│   │       │   │   └── health/
│   │       │   ├── layout.tsx
│   │       │   ├── page.tsx
│   │       │   ├── globals.css
│   │       │   ├── error.tsx
│   │       │   ├── not-found.tsx
│   │       │   └── loading.tsx
│   │       │
│   │       ├── proxy.ts
│   │       │
│   │       ├── components/
│   │       │   ├── ui/
│   │       │   ├── layout/
│   │       │   ├── forms/
│   │       │   ├── tables/
│   │       │   ├── charts/
│   │       │   ├── feedback/
│   │       │   ├── navigation/
│   │       │   └── providers/
│   │       │
│   │       ├── features/
│   │       │   ├── auth/
│   │       │   ├── assets/
│   │       │   ├── tenants/
│   │       │   ├── organizations/
│   │       │   ├── lansweeper/
│   │       │   ├── integrations/
│   │       │   ├── billing/
│   │       │   ├── audit/
│   │       │   ├── compliance/
│   │       │   └── reporting/
│   │       │
│   │       ├── lib/
│   │       │   ├── api/
│   │       │   ├── auth/
│   │       │   ├── permissions/
│   │       │   ├── validation/
│   │       │   ├── i18n/
│   │       │   ├── telemetry/
│   │       │   └── utilities/
│   │       │
│   │       ├── hooks/
│   │       ├── stores/
│   │       ├── types/
│   │       └── styles/
│   │
│   ├── mobile/
│   │   ├── android/
│   │   ├── ios/
│   │   ├── shared-contracts/
│   │   └── docs/
│   │
│   ├── packages/
│   │   ├── ui-kit/
│   │   ├── design-tokens/
│   │   ├── api-client/
│   │   ├── validators/
│   │   ├── permissions/
│   │   ├── telemetry/
│   │   └── config/
│   │
│   └── worker/
│       ├── tasks/
│       ├── schedules/
│       ├── queues/
│       ├── monitoring/
│       └── celery.py
│
├── contracts/
│   ├── openapi/
│   │   ├── openapi.yaml
│   │   ├── schemas/
│   │   ├── paths/
│   │   └── versions/
│   │
│   ├── asyncapi/
│   │   └── asyncapi.yaml
│   │
│   ├── events/
│   │   ├── asset-events/
│   │   ├── tenant-events/
│   │   └── integration-events/
│   │
│   ├── webhooks/
│   └── generated/
│       ├── typescript/
│       ├── kotlin/
│       └── swift/
│
├── design-system/
│   ├── tokens/
│   │   ├── colors.json
│   │   ├── typography.json
│   │   ├── spacing.json
│   │   ├── sizing.json
│   │   ├── radius.json
│   │   ├── shadows.json
│   │   ├── breakpoints.json
│   │   ├── z-index.json
│   │   └── motion.json
│   │
│   ├── assets/
│   │   ├── fonts/
│   │   ├── icons/
│   │   ├── images/
│   │   └── illustrations/
│   │
│   ├── components/
│   ├── patterns/
│   │
│   ├── themes/
│   │   ├── light/
│   │   ├── dark/
│   │   └── high-contrast/
│   │
│   ├── storybook/
│   └── docs/
│       ├── tokens.md
│       ├── components.md
│       ├── patterns.md
│       ├── accessibility.md
│       ├── rtl-ltr.md
│       ├── theming.md
│       └── ai-coding-rules.md
│
├── infrastructure/
│   ├── docker/
│   │   ├── backend.Dockerfile
│   │   ├── frontend.Dockerfile
│   │   ├── worker.Dockerfile
│   │   ├── scheduler.Dockerfile
│   │   └── security-scanning.Dockerfile
│   │
│   ├── traefik/
│   │   ├── traefik.yml
│   │   ├── dynamic/
│   │   ├── tls/
│   │   └── middlewares/
│   │
│   ├── postgres/
│   │   ├── config/
│   │   ├── init/
│   │   ├── extensions/
│   │   ├── rls/
│   │   ├── roles/
│   │   ├── migrations/
│   │   └── backup/
│   │
│   ├── redis/
│   │   ├── config/
│   │   └── policies/
│   │
│   ├── rabbitmq/
│   │   └── config/
│   │
│   ├── storage/
│   │   └── object-storage/
│   │
│   └── scripts/
│       ├── health-check.sh
│       ├── wait-for-dependencies.sh
│       └── container-entrypoint.sh
│
├── deployment/
│   ├── ansible/
│   │   ├── ansible.cfg
│   │   ├── inventories/
│   │   │   ├── dev/
│   │   │   ├── staging/
│   │   │   └── production/
│   │   ├── playbooks/
│   │   │   ├── bootstrap.yml
│   │   │   ├── hardening.yml
│   │   │   ├── deploy.yml
│   │   │   ├── upgrade.yml
│   │   │   ├── rollback.yml
│   │   │   ├── backup.yml
│   │   │   └── restore.yml
│   │   └── roles/
│   │       ├── common/
│   │       ├── docker/
│   │       ├── firewall/
│   │       ├── traefik/
│   │       ├── monitoring/
│   │       ├── application/
│   │       └── backup/
│   │
│   ├── ci-cd/
│   │   ├── Jenkinsfile
│   │   ├── jenkins/
│   │   │   ├── casc/
│   │   │   ├── shared-library/
│   │   │   └── agents/
│   │   ├── github/workflows/
│   │   ├── quality-gates/
│   │   └── release/
│   │
│   ├── environments/
│   │   ├── dev/
│   │   ├── staging/
│   │   └── production/
│   │
│   └── release-management/
│       ├── versioning.md
│       ├── release-checklist.md
│       ├── rollback-policy.md
│       └── migration-policy.md
│
├── security/
│   ├── threat-model/
│   ├── policies/
│   ├── iam/
│   ├── secrets-management/
│   │   ├── sops/
│   │   └── templates/
│   ├── encryption/
│   ├── certificates/
│   ├── access-control/
│   ├── tenant-isolation/
│   ├── supply-chain/
│   │   ├── sbom/
│   │   ├── image-signing/
│   │   └── provenance/
│   ├── scans/
│   ├── compliance/
│   ├── owasp/
│   └── incident-response/
│
├── observability/
│   ├── prometheus/
│   ├── grafana/
│   │   ├── dashboards/
│   │   └── provisioning/
│   ├── loki/
│   ├── tempo/
│   ├── alertmanager/
│   ├── otel/
│   │   ├── collector/
│   │   ├── traces/
│   │   └── metrics/
│   ├── alert-rules/
│   ├── slo-sli/
│   └── synthetic-monitoring/
│
├── ops/
│   ├── runbooks/
│   ├── incident-management/
│   ├── backup-restore/
│   ├── disaster-recovery/
│   ├── capacity-planning/
│   ├── maintenance/
│   ├── database-operations/
│   ├── certificate-renewal/
│   ├── dependency-updates/
│   └── service-catalog/
│
├── tests/
│   ├── architecture/
│   ├── contract/
│   ├── api/
│   ├── integration/
│   ├── e2e/
│   ├── security/
│   ├── performance/
│   ├── load/
│   ├── resilience/
│   ├── accessibility/
│   ├── migration/
│   └── disaster-recovery/
│
├── quality/
│   ├── lint/
│   ├── type-check/
│   ├── coverage/
│   ├── security/
│   ├── performance-budgets/
│   ├── dependency-audit/
│   ├── code-quality/
│   └── quality-gate.sh
│
├── scripts/
│   ├── bootstrap.sh
│   ├── setup-dev.sh
│   ├── generate-contracts.sh
│   ├── generate-tokens.sh
│   ├── database-migrate.sh
│   ├── database-backup.sh
│   ├── database-restore.sh
│   ├── seed.py
│   ├── release.sh
│   ├── health-check.sh
│   ├── security-scan.sh
│   └── quality-gate.sh
│
└── archive/
    ├── inputs/
    ├── legacy/
    ├── snapshots/
    └── archive-index.md

3. Technology Stack
Layer	Technology	Responsibility
Backend Language	Python 3.13	Application development
Backend Framework	Django 5.2 LTS	Business logic and REST services; LTS support with CompositePrimaryKey for multi-field primary keys
Frontend Framework	Next.js 16	Web application with App Router and React Server Components
UI Library	React 19	Component-based UI
Language	TypeScript	Frontend type safety
Styling	Tailwind CSS 4	Utility-first styling
UI Components	shadcn/ui	Reusable interface components
Database	PostgreSQL 18	Primary relational database with RLS support
Cache	Redis 8	Caching and background task support
Task Processing	Celery	Asynchronous jobs
Message Broker	RabbitMQ	Optional advanced messaging
Monorepo	pnpm	JavaScript package management
Build Orchestration	Turborepo	Monorepo task execution
API Specification	OpenAPI 3.1	REST API contracts
Event Specification	AsyncAPI	Event-driven contracts
Android	Kotlin	Android application
iOS	Swift	iOS application
Containers	Docker	Application packaging
Container Orchestration	Docker Compose	Deployment and service orchestration
Reverse Proxy	Traefik v3	Routing and TLS
CI/CD	Jenkins LTS	Build and delivery automation
Infrastructure Automation	Ansible	Server provisioning and configuration
Metrics	Prometheus	Metrics collection
Dashboards	Grafana	Monitoring dashboards
Logs	Loki	Centralized log aggregation
Tracing	OpenTelemetry	Distributed observability
Trace Storage	Tempo	Distributed trace storage
Security Scanning	Trivy	Vulnerability scanning
Secret Detection	Gitleaks	Secret detection
Code Quality	SonarQube	Static analysis and code quality
Backend Testing	Pytest	Python tests
Frontend Testing	Vitest	JavaScript/TypeScript tests
E2E Testing	Playwright	Browser automation
Load Testing	k6	Performance and load tests
Database Backup	pgBackRest / WAL-G	PostgreSQL backup and recovery

Compatibility requirement: The stack is a target baseline, not an assumption that every listed major version is automatically compatible. Verify framework support, security status, runtime requirements, and plugin compatibility before pinning versions.
4. Backend Architecture
4.1 Architectural Pattern

The backend follows a Modular Monolith architecture using Domain-Driven Design (DDD), Clean Architecture, and Bounded Contexts. Each module is a DDD bounded context with clear boundaries: domain entities, value objects, application services, domain services, infrastructure adapters, API interfaces, and tests.

The backend avoids unnecessary microservice complexity during initial development. Modules communicate through explicit interfaces and domain events, enabling future extraction to microservices via the Strangler Fig pattern if scalability demands it.
4.2 Bounded Contexts
Context	Primary Responsibility
Core	Shared application foundations, base classes, common utilities
Accounts	User account management
Identity	Authentication and identity
Tenants	Multi-tenant management, tenant context
Organizations	Organizational hierarchy
Assets	IT asset management
Asset Lifecycle	Asset lifecycle and ownership
Lansweeper	Discovery integration and synchronization
Integrations	External system connectivity
Billing	Financial transactions and billing
Subscriptions	Plans and subscription management
Notifications	Email and system notifications
Audit	Audit trails and activity tracking
Reporting	Reports and analytical queries
Compliance	Governance and compliance management

Each context maintains strict internal structure: domain entities, value objects, application services, domain services, infrastructure adapters, API interfaces, and tests. Cross-module database access is prohibited; integration occurs via interfaces or events.
5. Frontend Architecture

The frontend uses Next.js 16 App Router with React Server Components and a Turbo monorepo of @app/* packages confining backend access behind a single data-access package.
5.1 Main Responsibilities

Public website, authentication and account interfaces, enterprise dashboard, asset management, tenant administration, organization management, Lansweeper integration, billing and subscriptions, audit and compliance, reporting and analytics, notifications, and user settings.
5.2 Frontend Engineering Standards

    Feature-oriented organization: Features live in src/features/* with co-located components, hooks, and types.

    Reusable components: UI primitives in src/components/ui/, shared patterns in packages/ui-kit/.

    Strict TypeScript configuration: Strict mode, no implicit any, exhaustive type checking.

    Consistent API client implementation: Generated from OpenAPI contracts; no hand-written fetch calls in components.

    Centralized validation: Zod schemas shared between forms and API boundaries.

    Accessible UI components: WCAG 2.1 AA compliance; keyboard navigation and screen reader support.

    Responsive layouts: Mobile-first breakpoints from design tokens.

    RTL and LTR support: Full internationalization with next-intl; RTL layouts for Arabic and Hebrew.

    Centralized design tokens: Generated from design-system/tokens/*.json; no hardcoded colors, spacing, or typography.

    Server-side authorization enforcement: The frontend is never the final security boundary; all sensitive authorization decisions are validated by the backend.

6. Multi-Tenancy and Database Architecture

PostgreSQL is the primary data storage engine. The platform implements defense-in-depth tenant isolation.
6.1 Tenant Isolation Controls

    Tenant-aware data models: Every tenant-scoped table includes a tenant_id column with a foreign key to the tenants table.

    Explicit tenant context: Set per transaction via SET LOCAL or set_config('app.current_tenant_id', ...).

    PostgreSQL Row-Level Security (RLS): Enabled and FORCED on every tenant-scoped table; policies filter by current_setting('app.current_tenant_id').

    Two database roles: An owner role for migrations and an application role with NOBYPASSRLS for runtime queries.

    Tenant-aware query filtering: Application-layer filters as the first line of defense; RLS as the second.

    Tenant isolation integration tests: Automated tests verify cross-tenant read and write attempts fail.

    Auditable tenant access: All tenant-scoped operations logged in audit trails.

6.2 RLS Security Requirements

    Define explicit tenant policies on every applicable table.

    Apply ENABLE ROW LEVEL SECURITY and FORCE ROW LEVEL SECURITY.

    Avoid application database roles with BYPASSRLS; use a non-owner app role.

    Set tenant context safely for each transaction; prevent leakage across pooled connections.

    Test cross-tenant reads, writes, aggregations, subqueries, and background jobs.

    Verify administrative access paths and document intentional exceptions.

    RLS supplements application authorization; it does not replace it.

6.3 Database Engineering

Version-controlled migrations, explicit indexes, foreign-key integrity, appropriate unique constraints, transaction management, query optimization, database performance monitoring, backup and restore automation, and migration rollback procedures where feasible.
7. API and Integration Architecture
7.1 API Standards

The REST API follows OpenAPI 3.1, versioned endpoints, consistent error responses, pagination, filtering and sorting, request validation, authentication and authorization, rate limiting, idempotency for applicable operations, and structured API documentation.
7.2 API Source of Truth

contracts/openapi/openapi.yaml is the authoritative REST API contract. Generated clients and types are derived artifacts; they must not become independent manually maintained contract sources.
7.3 Lansweeper Integration

The integration layer supports Lansweeper API connectivity, GraphQL data queries, webhook processing, asset discovery synchronization, delta and bulk synchronization, data normalization, duplicate detection, retry policies, rate-limit handling, integration health monitoring, synchronization history, and audit logging.

Key capabilities:

    GraphQL Data API: Query detailed asset data from Lansweeper.

    Webhooks: Receive notifications for NEW_ASSET, UPDATED_ASSET, DELETED_ASSET, data source changes, and export completions.

    Delta synchronization: Only fetch changed assets since the last sync.

    Failure isolation: External integration failures must not corrupt internal asset records.

8. Event-Driven Architecture

The platform uses asynchronous processing where it improves reliability, scalability, and responsiveness.

Recommended capabilities:

    Domain events and integration events.

    Transactional Outbox pattern: Publish events reliably after database commits.

    Idempotent event handlers.

    Retry policies with exponential backoff.

    Dead Letter Queues (DLQ) for failed events.

    Event versioning and schema evolution.

    Message monitoring and alerting.

    Event processing audit trails.

RabbitMQ is optional; introduce it when messaging requirements justify it. Redis may be sufficient for simpler Celery workloads.
9. Design System

The design system provides a consistent visual foundation across web and mobile applications.
9.1 Components

Color palette, typography, spacing, sizing, border radius, shadows, breakpoints, z-index, motion, themes (light, dark, high-contrast), icons, reusable components, UI patterns, and accessibility standards.
9.2 Single Source of Truth

design-system/tokens/*.json is the authoritative source for design tokens. CSS variables, TypeScript token definitions, and other supported outputs are generated from these files. Duplicate independent token definitions are prohibited.
10. DevOps and Infrastructure Architecture

The DevOps architecture is based on containerization, automation, repeatable deployments, and centralized infrastructure management.
10.1 Docker

Docker packages application components into reproducible container images.

Expected services:

    Backend API (Django + Gunicorn/Uvicorn)

    Frontend application (Next.js)

    Celery worker

    Celery scheduler (beat)

    PostgreSQL

    Redis

    RabbitMQ (when required)

    Traefik

    Observability stack (Prometheus, Loki, Tempo, Grafana, OpenTelemetry Collector)

Production container standards:

    Non-root execution where possible.

    Health checks for every service.

    Resource limits (CPU, memory).

    Persistent storage for stateful services.

    Explicit image versions (no latest tags).

    Secure environment configuration via secrets.

    Structured logging to stdout/stderr.

    Minimal production images (multi-stage builds, distroless where feasible).

10.2 Docker Compose

Docker Compose is the primary service orchestration mechanism for development, staging, and initial production deployments.

Compose file structure:

    compose.yaml: Base service definitions.

    compose.dev.yaml: Development overrides (hot reload, debug ports, seed data).

    compose.staging.yaml: Staging overrides (production-like settings, staging secrets).

    compose.prod.yaml: Production overrides (resource limits, restart policies, production secrets).

Service dependency management: Use depends_on with health checks; wait-for-dependencies.sh handles startup ordering.
10.3 Observability Stack

A Docker Compose-based observability stack provides production-grade metrics, logs, and traces without a SaaS bill:
Component	Role
OpenTelemetry Collector	Receives OTLP signals; routes traces to Tempo and logs to Loki
Prometheus	Metrics storage and querying
Grafana	Dashboards and visualization
Loki	Log aggregation and querying
Tempo	Distributed trace storage
Alertmanager	Alert routing and deduplication

The stack is deployable with a single make up command.
10.4 Ansible

Ansible handles server provisioning, configuration management, and application deployment.

Playbooks:

    bootstrap.yml: Initial server setup (users, SSH, packages).

    hardening.yml: Security hardening (firewall, fail2ban, kernel parameters).

    deploy.yml: Application deployment.

    upgrade.yml: Rolling upgrades.

    rollback.yml: Rollback to previous version.

    backup.yml: Database and configuration backup.

    restore.yml: Restore from backup.

10.5 CI/CD Pipeline

Jenkins LTS is the primary CI/CD engine, with GitHub Actions workflows as secondary automation.

Pipeline stages:

    Checkout and dependency installation.

    Lint (Ruff for Python, ESLint for TypeScript).

    Type check (mypy for Python, tsc --noEmit for TypeScript).

    Unit tests (Pytest, Vitest).

    Integration tests (Pytest with database).

    Contract tests (OpenAPI and AsyncAPI validation).

    Architecture tests (import-linter, dependency rules).

    Security scans (Trivy, Gitleaks, OWASP dependency check).

    Build container images.

    Push to registry.

    Deploy to staging/production via Ansible.

    Smoke tests and health checks.

Quality gates: All stages must pass before merge or deployment.
11. Security Architecture
11.1 Defense-in-Depth

    Network: Traefik TLS termination, firewall rules, network segmentation.

    Application: RBAC, tenant-aware authorization, input validation, output encoding.

    Database: RLS, least-privilege roles, encryption at rest, audit logging.

    Secrets: SOPS-encrypted secrets, no hardcoded credentials, rotation policies.

    Supply chain: SBOM generation, image signing, provenance attestation.

    Monitoring: Security event logging, anomaly detection, incident response runbooks.

11.2 Tenant Isolation Verification

Automated tests verify:

    Cross-tenant reads return empty results.

    Cross-tenant writes fail.

    Aggregations and subqueries do not leak data.

    Background jobs respect tenant context.

    Administrative paths are audited.

12. Testing Strategy
Test Type	Tool	Scope
Unit	Pytest, Vitest	Individual functions, classes, components
Integration	Pytest	Database, external services, API
Contract	Schemathesis, Dredd	OpenAPI/AsyncAPI compliance
Architecture	import-linter	Module boundary violations
Security	Bandit, Trivy, OWASP ZAP	Vulnerabilities, misconfigurations
E2E	Playwright	User workflows
Performance	k6	Load, stress, spike tests
Resilience	Chaos Toolkit	Failure injection
Accessibility	axe-core	WCAG compliance
Migration	Pytest	Schema migrations up/down

Coverage targets: Backend ≥ 85%, frontend ≥ 80%, critical paths ≥ 95%.
13. Operations and Runbooks
13.1 Runbooks

    Incident management: Severity levels, escalation paths, communication templates.

    Backup and restore: Database backup schedule, restore procedures, verification.

    Disaster recovery: RTO/RPO targets, failover procedures, post-recovery validation.

    Capacity planning: Metrics to monitor, scaling triggers, resource forecasting.

    Database operations: Migration procedures, index maintenance, vacuum/analyze schedules.

    Certificate renewal: Automated renewal via Traefik/Let's Encrypt; manual fallback.

    Dependency updates: Dependabot/Renovate policies, security patch SLAs.

13.2 Service Catalog

A service catalog documents all platform services, owners, SLAs, dependencies, and runbook links.
14. Release Management

    Versioning: Semantic Versioning (SemVer).

    Release checklist: All tests pass, security scans clean, documentation updated, migration tested, rollback plan documented.

    Rollback policy: Database migrations must be reversible; application rollback via previous container image.

    Migration policy: Forward-only migrations with tested rollback scripts; no destructive migrations without backup verification.

15. Recommendations for Implementation

    Start with modular monolith: Avoid microservice complexity until scalability demands extraction.

    Implement RLS from day one: Retrofit is significantly harder than initial implementation.

    Establish CI/CD early: Quality gates prevent architectural drift.

    Instrument before optimizing: Deploy observability stack with the first production release.

    Document decisions via ADRs: Every architectural choice recorded with context and consequences.

    Automate tenant isolation testing: Cross-tenant leakage is a critical security incident.

    Use contract-first API design: Generated clients eliminate drift between frontend and backend.

    Centralize design tokens: Visual consistency across web and mobile from a single source.