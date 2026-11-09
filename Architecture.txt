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