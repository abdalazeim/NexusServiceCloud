<div align="center">

<pre>
╔══════════════════════════════════════════════════════════╗
║                                                          ║
║                 NEXUS SERVICE CLOUD                      ║
║        "Where Service Operations Meet Enterprise"        ║
║                                                          ║
║      ITSM · ESM · SLA/OLA · Knowledge · Governance       ║
║   Multi-Tenancy · Audit · BFF · API · Design Package     ║
║                                                          ║
╚══════════════════════════════════════════════════════════╝
</pre>

| Type | Status | Version | License |
|:---:|:---:|:---:|:---:|
| Enterprise analysis & design package | Proposed design for review | 1.0.0 | See `project-meta/` |

[README (Arabic main)](README.md) · [العربية](README_AR.md) · [Enterprise Architecture](EnterpriseArchitecture.md)

</div>

---

## ◈ Project Snapshot

**NexusServiceCloud** is an analysis and design package for a unified service management and technical support platform. The target vision brings incidents, service requests, service levels, problems, changes, releases, knowledge, assets/CMDB, procurement, and contracts into one governed operating model.

> **Repository state:** target specifications only. This repository does not contain a runnable production application, runtime configuration, database migrations, or executable tests. Do not treat documentation, blueprints, or diagrams as proof of implementation or production readiness.

| Capability area | Target value |
|---|---|
| Service desk | Incidents, service requests, catalog, classification, assignment, comments, attachments |
| Service levels | SLA/OLA policies, calendars, measurement segments, escalation, and service-time indicators |
| Operational control | Problems, known errors, changes, approvals, releases, and recorded delivery activities |
| Knowledge & delivery | Knowledge base, projects, tasks, dependencies, and release/deployment records |
| Resources | Links to assets, CMDB/configuration items, procurement, contracts, maintenance, and reservations |
| Multi-tenancy | Independent tenant isolation; departments/service desks inside a tenant are not separate tenants |
| Governance & audit | Permissions, audit evidence, architecture decisions, and requirement traceability |
| Access channels | Web UI, BFF, API contracts, and a proposed mobile strategy |

---

## ◈ Repository Layout

| Path | Purpose |
|---|---|
| `README.md` | Main project entry (Arabic) |
| `README_EN.md` | English project overview (this file) |
| `README_AR.md` | Arabic project overview |
| `EnterpriseArchitecture.md` | Enterprise architecture blueprint/reference |
| `docs/` | Enterprise documentation (`00-overview` .. `14-appendices`, plus audits) |
| `prompts/` | Review/execution instructions for this documentation effort |
| `project-meta/` | Documentation policy, governance references, and changelog |
| `Architecture.md` / `Architecture.txt` | Preserved reference inputs (not modified) |
| `Enterprise Repository Structure.md/.txt` | Preserved reference inputs (not modified) |

```text
.
├── README.md
├── README_EN.md
├── README_AR.md
├── EnterpriseArchitecture.md
├── docs/
│   ├── 00-overview/
│   ├── 01-analysis/
│   ├── 02-design/
│   ├── 03-implementation/
│   ├── 04-testing/
│   ├── 05-deployment/
│   ├── 06-operations/
│   ├── 07-security/
│   ├── 08-mobile/
│   ├── 09-api/
│   ├── 10-design-system/
│   ├── 11-governance/
│   ├── 12-adr/
│   ├── 13-diagrams/
│   ├── 14-appendices/
│   └── documentation-audit/
├── prompts/
└── project-meta/
```

---

## ◈ Target System Architecture

| Channel / layer | Target |
|---|---|
| Users | Beneficiaries, support technicians, team leads, service owners, problem/change/asset managers, auditors |
| Edge | Secure entry gateway, TLS, routing, and edge controls |
| Web | Administrative/operational web experience with a BFF for screen-oriented reads and session protection |
| Application | Use cases, workflow orchestration, authorization checks, and domain events |
| Domain | Business modules with clear boundaries, initially as a modular monolith |
| Data | Relational operational source of truth, tenant isolation, and separate file/object storage for attachments |
| Background work | Notifications, SLA timers, reliable events, and asynchronous processing |
| Integrations | Email, monitoring, discovery/assets, identity, and external providers through explicit contracts |

### Architecture Rules

| Rule | Meaning |
|---|---|
| Dependency direction | `Interfaces -> Application -> Domain` (inward only) |
| Infrastructure | Implements ports/adapters; does not own business rules |
| Domain purity | Domain has no HTTP, ORM, or external-provider dependencies |
| Authorization | Final authorization decision is made server-side for every operation and object |
| Consistency | State, history, audit, and reliable events use clear transaction boundaries |
| Isolation | Tenants are isolated; a department inside a tenant is not automatically a tenant |

---

## ◈ Proposed Bounded Contexts

| Context | Initial responsibility |
|---|---|
| `nsc_identity` | Identity, sessions, memberships, and authorization |
| `nsc_tenants` | Organizations, sites, departments, and service desks |
| `nsc_catalog` | Services, catalog items, forms, and audience |
| `nsc_requests` | Requests/incidents, comments, assignment, and state history |
| `nsc_service_levels` | SLA/OLA policies, calendars, measurement segments, escalation |
| `nsc_approvals` | Approval policies, decisions, and delegation |
| `nsc_problems` | Root cause, workarounds, and known errors |
| `nsc_changes` | RFCs, risk, execution, rollback, and change scheduling |
| `nsc_delivery` | Projects, tasks, dependencies, releases, deployment records |
| `nsc_assets` / `nsc_cmdb` | Assets, custody, configuration items, and relationships |
| `nsc_procurement` | Purchasing, receiving, vendors, and contracts |
| `nsc_knowledge` | Articles, review, publishing, and audience |
| `nsc_integrations` | Email, monitoring, connectors, and field mappings |
| `nsc_insights` | Derived reports/surveys with declared freshness |
| `nsc_audit` / `nsc_notifications` | Audit and delivery without changing the original business decision |
| `nsc_ai` | Permission-aware suggestions, fully disableable |

---

## ◈ Reading Path

1. [Vision, scope, and phases](docs/00-overview/01-vision-scope.md)
2. [Business analysis and use cases](docs/01-analysis/01-business-analysis.md)
3. [Functional requirements and acceptance criteria](docs/01-analysis/02-functional-requirements.md)
4. [Quality requirements and success indicators](docs/01-analysis/03-quality-requirements.md)
5. [Architecture and bounded contexts](docs/02-design/01-architecture.md)
6. [Data model and consistency rules](docs/02-design/02-data-model.md)
7. [Workflows and approvals](docs/02-design/03-workflows.md)
8. [SLA/OLA accounting](docs/02-design/04-service-levels.md)
9. [Complete documentation map](docs/README.md)
10. [Traceability, decisions, and risks](docs/11-governance/02-traceability.md)

---

## ◈ Relationship to References

Original NexusServiceCloud documents are the source of business scope. NexusAsset CloudMonorepo was used as an organizational reference for separating domain, application, infrastructure, and governance concerns. That does **not** imply copying asset-discovery scope, adopting Lansweeper, or carrying package versions and pricing plans into this project.

The root `Architecture.txt` and `Enterprise Repository Structure.txt` files describe a different preserved reference and remain unchanged. In case of conflict, use the current NexusServiceCloud package and the [source log](docs/14-appendices/01-sources-glossary.md).

---

## ◈ Delivery Boundaries

- Markdown documentation only; Mermaid diagrams are explanatory.
- Proposals are not commercially, securely, or operationally approved until recorded by decision.
- No claim of complete APIs, ITIL implementation/certification, production readiness, or passing operational tests.
- A future platform structure mentioned in design is not a generated application repository.
- Versions, pricing, AI cost, and trial terms require product-owner decisions.
- Technology families may be discussed as candidates; specific version/compatibility decisions belong in later ADRs and matrices.
