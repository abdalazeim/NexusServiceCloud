# DOC-05: هندسة التطبيق — NexusServiceCloud

---

| **معرّف الوثيقة** | DOC-05 |
|-------------------|--------|
| **العنوان** | هندسة التطبيق: Clean Architecture، طبقات، حالات استخدام، منافذ/محولات، أنماط |
| **الإصدار** | 1.0.0 |
| **الحالة** | ✓ معتمد |
| **المالك** | هندسة الحل / معمارية التطبيق |
| **تاريخ التحديث** | 2026-11-09 |
| **التصنيف** | داخلي — سري |
| **الوثائق ذات الصلة** | [DOC-04 نموذج المجال](../00-overview/04-domain-model.md)، [DOC-06 البنية التحتية](../00-overview/06-infrastructure-architecture.md)، [DOC-07 تصميم API](../00-overview/07-api-design.md)، [DOC-08 التكاملات](../00-overview/08-integrations-events.md)، [DOC-11 DevOps](../00-overview/11-devops-cicd.md)، [DOC-18 الملاحق](../00-overview/18-appendices.md) |
| **التتبع** | UC-APP-###، PORT-###، ADP-###، AGG-###، ENT-###، EVT-###، IEV-###، ADR-005،015،016،017،018،019،020 |

---

## 1. ملخص تنفيذي

تحدد هذه الوثيقة **هندسة التطبيق** لمنصة NexusServiceCloud بناءً على **Clean Architecture** (العمارة النظيفة) مع نمط **Modular Monolith** كقاعدة أولية قابلة للتطور نحو خدمات موزعة. تفصل العمارة **المجال** (Domain) عن **التطبيق** (Application) عن **البنية التحتية** (Infrastructure) عبر **منافذ** (Ports/Interfaces) و**محولات** (Adapters)، مما يضمن قابلية الاختبار، الاستبدال، والتطور المستقل.

---

## 2. مبادئ العمارة

| المبدأ | الوصف | التنفيذ |
|---------|--------|---------|
| **اعتمادية الداخل للخارج** | المجال لا يعرف التطبيق؛ التطبيق لا يعرف البنية التحتية | حزم Python منفصلة، استيراد باتجاه واحد |
| **المنافذ تحدد العقود** | واجهات مجردة في طبقة المجال/التطبيق | `Protocol` classes في `nxa_core/ports/` |
| **المحولات تحقق المنافذ** | تنفيذات ملموسة في طبقة البنية التحتية | Django ORM، Redis، HTTP clients، إلخ |
| **حالات الاستخدام تنسق** | منطق تنفيذي بدون منطق مجال | Classes في `nxa_core/application/use_cases/` |
| **الأحداث للتواصل غير المتزامن** | Outbox Pattern للموثوقية | `OutboxEvent` في معاملة المجال |
| **التزامن المتفائل** | `version` على كل تجميع | Django `F()` expressions أو تحقق صريح |
| **عزل المستأجر في الطبقة** | `tenant_id` يمر عبر جميع الطبقات | Middleware يضبط `tenant_context` |

---

## 3. طبقات Clean Architecture

```mermaid
graph TB
    subgraph "External Interfaces"
        UI[Next.js Web App]
        API[REST/GraphQL API]
        MOBILE[Mobile App / PWA]
        WEBHOOK[Webhooks / Integrations]
    end
    
    subgraph "Interface Adapters (Presentation)"
        CONTROLLERS[DRF ViewSets / GraphQL Resolvers]
        SERIALIZERS[DRF Serializers / Strawberry Types]
        BFF[BFF Aggregation Layer]
        MIDDLEWARE[Auth / Tenant / RateLimit Middleware]
    end
    
    subgraph "Application Layer (Use Cases)"
        UC[Use Case Classes]
        DTO[Request/Response DTOs]
        PORT_IN[Input Ports (Interfaces)]
        PORT_OUT[Output Ports (Interfaces)]
        EVENTS[Domain Event Publishers]
    end
    
    subgraph "Domain Layer (Core Business Logic)"
        AGG[Aggregates / Entities / VOs]
        DOMAIN_EVENTS[Domain Events]
        DOMAIN_SERVICES[Domain Services]
        SPECIFICATIONS[Specifications / Rules]
        REPOSITORY_IF[Repository Interfaces (Ports)]
    end
    
    subgraph "Infrastructure Layer (Adapters)"
        REPO_IMPL[Django ORM Repositories]
        CACHE_IMPL[Redis Cache Adapter]
        QUEUE_IMPL[Celery/RabbitMQ/Kafka Adapter]
        EMAIL_IMPL[SMTP/MS Graph Adapter]
        STORAGE_IMPL[MinIO/S3 Adapter]
        VAULT_IMPL[HashiCorp Vault Adapter]
        MONITORING_IMPL[Prometheus/OpenTelemetry Adapter]
        MIGRATIONS[Django Migrations]
    end
    
    UI --> CONTROLLERS
    API --> CONTROLLERS
    MOBILE --> CONTROLLERS
    WEBHOOK --> CONTROLLERS
    
    CONTROLLERS --> UC
    SERIALIZERS --> DTO
    BFF --> UC
    MIDDLEWARE --> UC
    
    UC --> PORT_IN
    UC --> PORT_OUT
    UC --> EVENTS
    UC --> REPOSITORY_IF
    
    PORT_IN -.-> UC
    PORT_OUT -.-> REPO_IMPL
    PORT_OUT -.-> CACHE_IMPL
    PORT_OUT -.-> QUEUE_IMPL
    PORT_OUT -.-> EMAIL_IMPL
    PORT_OUT -.-> STORAGE_IMPL
    PORT_OUT -.-> VAULT_IMPL
    PORT_OUT -.-> MONITORING_IMPL
    
    REPO_IMPL -.-> AGG
    DOMAIN_EVENTS -.-> OUTBOX[Outbox Table]
    OUTBOX -.-> QUEUE_IMPL
```

### 3.1 بنية المجلدات (Repository Structure)

```
backend/
├── nexusasset/                 # Django project settings
│   ├── settings/
│   │   ├── base.py
│   │   ├── development.py
│   │   ├── staging.py
│   │   └── production.py
│   ├── urls.py
│   └── wsgi.py
│
├── nxa_core/                   # Shared Kernel / Core Domain
│   ├── domain/
│   │   ├── __init__.py
│   │   ├── value_objects/      # Money, Address, Duration, Percentage, etc.
│   │   ├── events/             # DomainEvent base, EventPublisher
│   │   ├── specifications/     # Specification pattern
│   │   └── exceptions/         # Domain exceptions
│   ├── application/
│   │   ├── __init__.py
│   │   ├── ports/              # Input/Output Ports (Protocols)
│   │   │   ├── input/
│   │   │   │   ├── request_use_case.py
│   │   │   │   ├── asset_use_case.py
│   │   │   │   └── ...
│   │   │   └── output/
│   │   │       ├── request_repository.py
│   │   │       ├── asset_repository.py
│   │   │       ├── notification_service.py
│   │   │       ├── email_service.py
│   │   │       ├── storage_service.py
│   │   │       ├── cache_service.py
│   │   │       ├── vault_service.py
│   │   │       └── ai_service.py
│   │   ├── use_cases/          # Use Case implementations
│   │   │   ├── request/
│   │   │   │   ├── create_request.py
│   │   │   │   ├── assign_request.py
│   │   │   │   ├── transition_request.py
│   │   │   │   ├── resolve_request.py
│   │   │   │   └── close_request.py
│   │   │   ├── asset/
│   │   │   │   ├── create_asset.py
│   │   │   │   ├── assign_asset.py
│   │   │   │   ├── reserve_asset.py
│   │   │   │   └── retire_asset.py
│   │   │   ├── catalog/
│   │   │   │   ├── publish_catalog_item.py
│   │   │   │   └── create_form_version.py
│   │   │   ├── sla/
│   │   │   │   ├── start_sla_instance.py
│   │   │   │   ├── pause_sla_clock.py
│   │   │   │   └── execute_escalation.py
│   │   │   ├── approval/
│   │   │   │   ├── start_approval.py
│   │   │   │   ├── decide_approval.py
│   │   │   │   └── revoke_approval.py
│   │   │   ├── problem/
│   │   │   │   ├── create_problem.py
│   │   │   │   ├── link_incident.py
│   │   │   │   └── close_problem.py
│   │   │   ├── change/
│   │   │   │   ├── create_change.py
│   │   │   │   ├── schedule_change.py
│   │   │   │   ├── implement_change.py
│   │   │   │   └── rollback_change.py
│   │   │   ├── procurement/
│   │   │   │   ├── create_purchase_order.py
│   │   │   │   ├── receive_goods.py
│   │   │   │   └── create_contract.py
│   │   │   ├── integration/
│   │   │   │   ├── sync_connector.py
│   │   │   │   └── reconcile_entities.py
│   │   │   └── ai/
│   │   │       ├── generate_recommendation.py
│   │   │       └── review_recommendation.py
│   │   ├── dto/                # Data Transfer Objects
│   │   │   ├── request_dto.py
│   │   │   ├── asset_dto.py
│   │   │   └── ...
│   │   └── services/           # Application Services (Cross-cutting)
│   │       ├── transaction_manager.py
│   │       ├── event_publisher.py
│   │       └── idempotency_service.py
│   ├── infrastructure/
│   │   ├── __init__.py
│   │   ├── persistence/
│   │   │   ├── django/
│   │   │   │   ├── repositories/
│   │   │   │   │   ├── request_repository.py
│   │   │   │   │   ├── asset_repository.py
│   │   │   │   │   └── ...
│   │   │   │   ├── models/      # Django ORM Models (مخطط فيزيائي)
│   │   │   │   │   ├── request_model.py
│   │   │   │   │   ├── asset_model.py
│   │   │   │   │   └── ...
│   │   │   │   └── migrations/
│   │   │   └── redis/
│   │   │       └── cache_adapter.py
│   │   ├── messaging/
│   │   │   ├── celery/
│   │   │   │   ├── tasks/
│   │   │   │   └── app.py
│   │   │   ├── kafka/
│   │   │   │   ├── producer.py
│   │   │   │   └── consumer.py
│   │   │   └── outbox/
│   │   │       ├── publisher.py
│   │   │       └── processor.py
│   │   ├── email/
│   │   │   ├── smtp_adapter.py
│   │   │   └── msgraph_adapter.py
│   │   ├── storage/
│   │   │   └── minio_adapter.py
│   │   ├── vault/
│   │   │   └── vault_adapter.py
│   │   ├── monitoring/
│   │   │   ├── prometheus_adapter.py
│   │   │   └── otel_adapter.py
│   │   └── ai/
│   │       ├── openai_adapter.py
│   │       ├── azure_adapter.py
│   │       └── local_adapter.py
│   └── api/
│       ├── v1/
│       │   ├── routers/
│       │   │   ├── request_router.py
│       │   │   ├── asset_router.py
│       │   │   └── ...
│       │   ├── serializers/
│       │   │   ├── request_serializer.py
│       │   │   └── ...
│       │   ├── viewsets/
│       │   │   ├── request_viewset.py
│       │   │   └── ...
│       │   └── permissions/
│       │       ├── request_permission.py
│       │       └── ...
│       └── graphql/
│           ├── schema.py
│           ├── queries/
│           ├── mutations/
│           └── types/
│
├── nxa_identity/               # Bounded Context: Identity
├── nxa_tenants/                # Bounded Context: Tenants
├── nxa_catalog/                # Bounded Context: Catalog
├── nxa_requests/               # Bounded Context: Requests
├── nxa_service_levels/         # Bounded Context: SLA/OLA
├── nxa_automation/             # Bounded Context: Automation
├── nxa_approvals/              # Bounded Context: Approvals
├── nxa_problems/               # Bounded Context: Problems
├── nxa_changes/                # Bounded Context: Changes
├── nxa_knowledge/              # Bounded Context: Knowledge
├── nxa_assets/                 # Bounded Context: Assets
├── nxa_cmdb/                   # Bounded Context: CMDB
├── nxa_procurement/            # Bounded Context: Procurement
├── nxa_delivery/               # Bounded Context: Delivery
├── nxa_integrations/           # Bounded Context: Integrations
├── nxa_ai/                     # Bounded Context: AI
├── nxa_insights/               # Bounded Context: Insights
├── nxa_audit/                  # Bounded Context: Audit
└── nxa_notifications/          # Bounded Context: Notifications
```

---

## 4. كتالوج حالات الاستخدام (Use Case Catalog) — 120+ حالة

### 4.1 نمط تطبيق حالة الاستخدام

```python
# nxa_core/application/use_cases/request/create_request.py
from dataclasses import dataclass
from typing import Protocol
from nxa_core.domain.value_objects import CorrelationId
from nxa_core.application.ports.output import RequestRepository, NotificationService
from nxa_core.application.dto import CreateRequestDTO, RequestDTO
from nxa_core.domain.events import RequestCreated


class CreateRequestUseCase:
    """
    UC-APP-REQS-001: إنشاء طلب خدمة أو حادث
    """
    
    def __init__(
        self,
        request_repo: RequestRepository,
        notification_service: NotificationService,
        event_publisher: EventPublisher,
    ):
        self._request_repo = request_repo
        self._notification_service = notification_service
        self._event_publisher = event_publisher
    
    def execute(self, dto: CreateRequestDTO, correlation_id: CorrelationId) -> RequestDTO:
        # 1. التحقق من الصلاحيات (Input Port responsibility)
        # 2. إنشاء تجميع Request
        request = Request.create(
            tenant_id=dto.tenant_id,
            type=dto.type,
            source=dto.source,
            title=dto.title,
            description=dto.description,
            requester_id=dto.requester_id,
            service_desk_id=dto.service_desk_id,
            service_id=dto.service_id,
            catalog_item_id=dto.catalog_item_id,
            form_version_id=dto.form_version_id,
            classification=dto.classification,
            impact=dto.impact,
            urgency=dto.urgency,
        )
        
        # 3. حفظ في المستودع (Output Port)
        self._request_repo.save(request)
        
        # 4. نشر حدث المجال (Outbox Pattern)
        self._event_publisher.publish(RequestCreated(
            request_id=request.request_id,
            tenant_id=request.tenant_id,
            type=request.type,
            ticket_number=request.ticket_number,
            priority=request.priority,
            correlation_id=correlation_id,
        ))
        
        # 5. إشعار غير متزامن (Outbox)
        self._notification_service.queue_notification(
            template="request_created",
            recipient=request.requester_id,
            context={"ticket_number": request.ticket_number},
        )
        
        return RequestDTO.from_domain(request)
```

### 4.2 قائمة حالات الاستخدام الرئيسية (مختصر)

| السياق | UC-APP | العنوان | المدخلات (DTO) | المخرجات (DTO) | المنافذ المستخدمة |
|--------|--------|--------|----------------|----------------|------------------|
| **REQS** | UC-APP-REQS-001 | إنشاء طلب/حادث | CreateRequestDTO | RequestDTO | RequestRepo، NotificationService، EventPublisher |
| **REQS** | UC-APP-REQS-002 | تعيين طلب | AssignRequestDTO | RequestDTO | RequestRepo، NotificationService، EventPublisher |
| **REQS** | UC-APP-REQS-003 | انتقال حالة طلب | TransitionRequestDTO | RequestDTO | RequestRepo، EventPublisher، SLAService |
| **REQS** | UC-APP-REQS-004 | حل طلب | ResolveRequestDTO | RequestDTO | RequestRepo، KnowledgeService، NotificationService، EventPublisher |
| **REQS** | UC-APP-REQS-005 | إغلاق طلب | CloseRequestDTO | RequestDTO | RequestRepo، SurveyService، EventPublisher |
| **REQS** | UC-APP-REQS-006 | إعادة فتح طلب | ReopenRequestDTO | RequestDTO | RequestRepo، EventPublisher |
| **REQS** | UC-APP-REQS-007 | إضافة تعليق | AddCommentDTO | CommentDTO | RequestRepo، NotificationService، EventPublisher |
| **REQS** | UC-APP-REQS-008 | رفع مرفق | UploadAttachmentDTO | AttachmentDTO | RequestRepo، StorageService، ScanService، EventPublisher |
| **REQS** | UC-APP-REQS-009 | تسجيل وقت عمل | RecordWorkLogDTO | WorkLogDTO | RequestRepo، EventPublisher |
| **CATL** | UC-APP-CATL-001 | نشر عنصر كتالوج | PublishCatalogItemDTO | CatalogItemDTO | CatalogRepo، FormVersionRepo، ApprovalPolicyRepo، EventPublisher |
| **CATL** | UC-APP-CATL-002 | إنشاء نسخة نموذج | CreateFormVersionDTO | FormVersionDTO | FormVersionRepo، EventPublisher |
| **SVCL** | UC-APP-SVCL-001 | بدء نسخة SLA | StartSLAInstanceDTO | SLAInstanceDTO | SLAInstanceRepo، CalendarService، EventPublisher |
| **SVCL** | UC-APP-SVCL-002 | إيقاف ساعة SLA | PauseSLAClockDTO | SLAInstanceDTO | SLAInstanceRepo، ClockSegmentRepo، EventPublisher |
| **SVCL** | UC-APP-SVCL-003 | تنفيذ تصعيد | ExecuteEscalationDTO | EscalationExecutionDTO | SLAInstanceRepo، EscalationExecutionRepo، NotificationService، EventPublisher |
| **AUTO** | UC-APP-AUTO-001 | تنفيذ قاعدة أتمتة | ExecuteAutomationRuleDTO | AutomationExecutionDTO | AutomationRuleRepo، AutomationExecutionRepo، ActionExecutors[]، OutboxPublisher، EventPublisher |
| **APPR** | UC-APP-APPR-001 | بدء موافقة | StartApprovalDTO | ApprovalInstanceDTO | ApprovalInstanceRepo، ApprovalPolicyRepo، NotificationService، EventPublisher |
| **APPR** | UC-APP-APPR-002 | قرار موافقة | DecideApprovalDTO | ApprovalInstanceDTO | ApprovalInstanceRepo، ApprovalDecisionRepo، EventPublisher |
| **APPR** | UC-APP-APPR-003 | إلغاء موافقة | RevokeApprovalDTO | ApprovalInstanceDTO | ApprovalInstanceRepo، EventPublisher |
| **PROB** | UC-APP-PROB-001 | إنشاء مشكلة | CreateProblemDTO | ProblemDTO | ProblemRepo، EventPublisher |
| **PROB** | UC-APP-PROB-002 | ربط حادث بمشكلة | LinkIncidentToProblemDTO | ProblemIncidentDTO | ProblemRepo، RequestRepo، EventPublisher |
| **PROB** | UC-APP-PROB-003 | إغلاق مشكلة | CloseProblemDTO | ProblemDTO | ProblemRepo، EventPublisher |
| **CHNG** | UC-APP-CHNG-001 | إنشاء تغيير | CreateChangeDTO | ChangeDTO | ChangeRepo، ApprovalService، EventPublisher |
| **CHNG** | UC-APP-CHNG-002 | جدولة تغيير | ScheduleChangeDTO | ChangeDTO | ChangeRepo، CalendarService، EventPublisher |
| **CHNG** | UC-APP-CHNG-003 | تنفيذ تغيير | ImplementChangeDTO | ChangeDTO | ChangeRepo، ChangeTaskRepo، DeploymentService، EventPublisher |
| **CHNG** | UC-APP-CHNG-004 | تراجع تغيير | RollbackChangeDTO | ChangeDTO | ChangeRepo، DeploymentService، EventPublisher |
| **KNOW** | UC-APP-KNOW-001 | إنشاء مقال من حل | CreateArticleFromResolutionDTO | KnowledgeArticleDTO | KnowledgeArticleRepo، KnowledgeRevisionRepo، EventPublisher |
| **KNOW** | UC-APP-KNOW-002 | نشر مراجعة مقال | PublishKnowledgeRevisionDTO | KnowledgeRevisionDTO | KnowledgeRevisionRepo، EventPublisher |
| **ASST** | UC-APP-ASST-001 | إنشاء أصل | CreateAssetDTO | AssetDTO | AssetRepo، EventPublisher |
| **ASST** | UC-APP-ASST-002 | تسليم أصل (عهدة) | AssignAssetDTO | AssetAssignmentDTO | AssetRepo، AssetAssignmentRepo، EventPublisher |
| **ASST** | UC-APP-ASST-003 | حجز أصل | ReserveAssetDTO | ReservationDTO | AssetRepo، ReservationRepo، EventPublisher |
| **ASST** | UC-APP-ASST-004 | صيانة أصل | PerformMaintenanceDTO | MaintenanceOccurrenceDTO | AssetRepo، MaintenanceOccurrenceRepo، EventPublisher |
| **ASST** | UC-APP-ASST-005 | تقاعد أصل | RetireAssetDTO | AssetDTO | AssetRepo، EventPublisher |
| **CMDB** | UC-APP-CMDB-001 | إنشاء CI | CreateCIDTO | CIDTO | CIRepo، EventPublisher |
| **CMDB** | UC-APP-CMDB-002 | ربط أصل بـ CI | LinkAssetToCIDTO | AssetCILinkDTO | AssetRepo، CIRepo، AssetCILinkRepo، EventPublisher |
| **CMDB** | UC-APP-CMDB-003 | إنشاء علاقة CI | CreateCIRelationDTO | CIRelationDTO | CIRepo، CIRelationRepo، EventPublisher |
| **PROC** | UC-APP-PROC-001 | إنشاء أمر شراء | CreatePurchaseOrderDTO | PurchaseOrderDTO | PORepo، PurchaseLineRepo، ApprovalService، EventPublisher |
| **PROC** | UC-APP-PROC-002 | استلام بضاعة | ReceiveGoodsDTO | ReceiptDTO | PORepo، ReceiptRepo، AssetRepo، EventPublisher |
| **PROC** | UC-APP-PROC-003 | إنشاء عقد | CreateContractDTO | ContractDTO | ContractRepo، EventPublisher |
| **DLVR** | UC-APP-DLVR-001 | إنشاء مشروع | CreateProjectDTO | ProjectDTO | ProjectRepo، EventPublisher |
| **DLVR** | UC-APP-DLVR-002 | إضافة مهمة | CreateTaskDTO | TaskDTO | TaskRepo، EventPublisher |
| **DLVR** | UC-APP-DLVR-003 | إضافة تبعية مهمة | AddTaskDependencyDTO | TaskDependencyDTO | TaskRepo، TaskDependencyRepo، CycleDetector، EventPublisher |
| **DLVR** | UC-APP-DLVR-004 | إنشاء إصدار | CreateReleaseDTO | ReleaseDTO | ReleaseRepo، EventPublisher |
| **DLVR** | UC-APP-DLVR-005 | نشر إصدار | DeployReleaseDTO | DeploymentAttemptDTO | ReleaseRepo، DeploymentService، ChangeRepo، EventPublisher |
| **INTG** | UC-APP-INTG-001 | مزامنة موصل | SyncConnectorDTO | SyncJobDTO | ConnectorRepo، SyncJobRepo، FieldMappingRepo، ReconciliationService، EventPublisher |
| **INTG** | UC-APP-INTG-002 | تسوية كيانات | ReconcileEntitiesDTO | ReconciliationResultDTO | SyncJobRepo، ReconciliationResultRepo، AssetRepo/CIRepo، EventPublisher |
| **AIML** | UC-APP-AIML-001 | توليد اقتراح AI | GenerateAIRecommendationDTO | AIRecommendationDTO | AIModelVersionRepo، AIRecommendationRepo، AIInferenceService، EventPublisher |
| **AIML** | UC-APP-AIML-002 | مراجعة اقتراح AI | ReviewAIRecommendationDTO | AIRecommendationDTO | AIRecommendationRepo، EventPublisher |
| **INSG** | UC-APP-INSG-001 | توليد تقرير | GenerateReportDTO | ReportResultDTO | ReportRepo، QueryExecutor، ExportService، EventPublisher |
| **INSG** | UC-APP-INSG-002 | تقديم استبيان | SubmitSurveyResponseDTO | SurveyResponseDTO | SurveyRepo، SurveyResponseRepo، EventPublisher |
| **AUDT** | UC-APP-AUDT-001 | تصدير تدقيق | ExportAuditEventsDTO | AuditExportDTO | AuditEventRepo، ExportService، EventPublisher |
| **NOTF** | UC-APP-NOTF-001 | إرسال إشعار | SendNotificationDTO | NotificationDeliveryDTO | NotificationTemplateRepo، NotificationDeliveryRepo، ChannelAdapters[]، EventPublisher |
| **IDNT** | UC-APP-IDNT-001 | إلغاء جلسة مستخدم | RevokeUserSessionDTO | UserSessionDTO | UserSessionRepo، EventPublisher |
| **TENT** | UC-APP-TENT-001 | توفير مستأجر | ProvisionTenantDTO | TenantDTO | TenantRepo، SchemaManager، SeedService، EventPublisher |

---

## 5. عقود المنافذ (Port Contracts)

### 5.1 منافذ الإخراج (Output Ports / Driven Ports)

```python
# nxa_core/application/ports/output/request_repository.py
from typing import Protocol, Optional, List
from nxa_core.domain.aggregates import Request
from nxa_core.domain.value_objects import CorrelationId
from nxa_core.application.dto import RequestListFilters, PaginatedResult


class RequestRepository(Protocol):
    """منفذ مستودع الطلبات - عقد تخزين/استرجاع"""
    
    def save(self, request: Request) -> None:
        """حفظ تجميع طلب (جديد أو محدث)"""
        ...
    
    def get_by_id(self, request_id: str, tenant_id: str) -> Optional[Request]:
        """استرجاع طلب بمعرفه داخل مؤسسة"""
        ...
    
    def get_by_ticket_number(self, ticket_number: str, tenant_id: str) -> Optional[Request]:
        """استرجاع طلب برقم التذكرة داخل مؤسسة"""
        ...
    
    def list(self, filters: RequestListFilters, tenant_id: str) -> PaginatedResult[Request]:
        """قائمة طلبات مع مرشحات وترقيم صفحات"""
        ...
    
    def exists_by_message_id(self, message_id: str, tenant_id: str) -> bool:
        """فحص وجود طلب بـ Message-ID (لإزالة تكرار البريد)"""
        ...
    
    def get_active_by_group(self, group_id: str, tenant_id: str) -> List[Request]:
        """طلبات نشطة لمجموعة (للتصعيد/التوزيع)"""
        ...


# nxa_core/application/ports/output/notification_service.py
from typing import Protocol, Optional
from nxa_core.domain.value_objects import CorrelationId


class NotificationService(Protocol):
    """منفذ خدمة الإشعارات - عقد وضع في طابور"""
    
    def queue_notification(
        self,
        template: str,
        recipient: str,  # user_id أو email
        context: dict,
        correlation_id: Optional[CorrelationId] = None,
        priority: str = "normal",  # high/normal/low
        channels: Optional[List[str]] = None,  # override default channels
    ) -> None:
        """وضع إشعار في طابور الإرسال (Outbox Pattern)"""
        ...
    
    def send_immediate(
        self,
        template: str,
        recipient: str,
        context: dict,
        channels: List[str],
    ) -> bool:
        """إرسال فوري متزامن (للحالات الحرجة فقط)"""
        ...


# nxa_core/application/ports/output/storage_service.py
from typing import Protocol, BinaryIO
from nxa_core.domain.value_objects import StorageKey


class StorageService(Protocol):
    """منفذ تخزين الملفات - عقد رفع/تنزيل/فحص"""
    
    def upload(
        self,
        file_obj: BinaryIO,
        filename: str,
        content_type: str,
        tenant_id: str,
        parent_type: str,  # request/comment/worklog/knowledge
        parent_id: str,
    ) -> StorageKey:
        """رفع ملف، إرجاع مفتاح التخزين"""
        ...
    
    def download(self, storage_key: StorageKey) -> BinaryIO:
        """تنزيل ملف بمفتاح التخزين"""
        ...
    
    def delete(self, storage_key: StorageKey) -> bool:
        """حذف ملف"""
        ...
    
    def get_presigned_url(self, storage_key: StorageKey, expires_in: int = 3600) -> str:
        """رابط موقع مسبق للتنزيل المباشر"""
        ...
    
    def scan_status(self, storage_key: StorageKey) -> str:
        """حالة الفحص: pending/clean/infected/quarantined"""
        ...


# nxa_core/application/ports/output/cache_service.py
from typing import Protocol, Optional, Any, List
from nxa_core.domain.value_objects import CacheKey


class CacheService(Protocol):
    """منفذ ذاكرة مؤقتة - عقد GET/SET/DELETE/INVALIDATE"""
    
    def get(self, key: CacheKey) -> Optional[Any]:
        ...
    
    def set(self, key: CacheKey, value: Any, ttl_seconds: int = 3600) -> None:
        ...
    
    def delete(self, key: CacheKey) -> None:
        ...
    
    def delete_pattern(self, pattern: str) -> int:
        """حذف بمطابقة نمط، إرجاع عدد المحذوفات"""
        ...
    
    def get_many(self, keys: List[CacheKey]) -> dict:
        ...
    
    def set_many(self, mapping: dict, ttl_seconds: int = 3600) -> None:
        ...
```

### 5.2 منافذ الإدخال (Input Ports / Driving Ports)

```python
# nxa_core/application/ports/input/request_use_case.py
from typing import Protocol
from nxa_core.application.dto import (
    CreateRequestDTO, RequestDTO,
    AssignRequestDTO, TransitionRequestDTO,
    ResolveRequestDTO, CloseRequestDTO,
    ReopenRequestDTO, AddCommentDTO,
    UploadAttachmentDTO, RecordWorkLogDTO,
)
from nxa_core.domain.value_objects import CorrelationId


class RequestUseCase(Protocol):
    """منفذ حالات استخدام الطلبات - عقد تنفيذي"""
    
    def create_request(self, dto: CreateRequestDTO, correlation_id: CorrelationId) -> RequestDTO:
        ...
    
    def assign_request(self, dto: AssignRequestDTO, correlation_id: CorrelationId) -> RequestDTO:
        ...
    
    def transition_request(self, dto: TransitionRequestDTO, correlation_id: CorrelationId) -> RequestDTO:
        ...
    
    def resolve_request(self, dto: ResolveRequestDTO, correlation_id: CorrelationId) -> RequestDTO:
        ...
    
    def close_request(self, dto: CloseRequestDTO, correlation_id: CorrelationId) -> RequestDTO:
        ...
    
    def reopen_request(self, dto: ReopenRequestDTO, correlation_id: CorrelationId) -> RequestDTO:
        ...
    
    def add_comment(self, dto: AddCommentDTO, correlation_id: CorrelationId) -> CommentDTO:
        ...
    
    def upload_attachment(self, dto: UploadAttachmentDTO, correlation_id: CorrelationId) -> AttachmentDTO:
        ...
    
    def record_worklog(self, dto: RecordWorkLogDTO, correlation_id: CorrelationId) -> WorkLogDTO:
        ...
```

---

## 6. أنماط التنفيذ (Implementation Patterns)

### 6.1 نمط Outbox للأحداث الموثوقة

```python
# nxa_core/infrastructure/messaging/outbox/publisher.py
from dataclasses import dataclass
from typing import List
from nxa_core.domain.events import DomainEvent
from nxa_core.infrastructure.persistence.django.models import OutboxEventModel
from django.db import transaction
import json


@dataclass
class OutboxEvent:
    event_type: str
    payload: dict
    correlation_id: str
    tenant_id: str
    aggregate_id: str
    aggregate_type: str


class DjangoOutboxPublisher:
    """ناشر Outbox باستخدام Django ORM داخل المعاملة"""
    
    def __init__(self):
        pass
    
    def publish(self, events: List[OutboxEvent]) -> None:
        """حفظ الأحداث في جدول outbox_events داخل المعاملة الحالية"""
        for event in events:
            OutboxEventModel.objects.create(
                event_type=event.event_type,
                payload=event.payload,
                correlation_id=event.correlation_id,
                tenant_id=event.tenant_id,
                aggregate_id=event.aggregate_id,
                aggregate_type=event.aggregate_type,
            )
    
    @transaction.atomic
    def publish_in_transaction(self, events: List[OutboxEvent]) -> None:
        """نشر ضمن معاملة صريحة (للاستخدام المستقل)"""
        self.publish(events)


# nxa_core/infrastructure/messaging/outbox/processor.py
class OutboxProcessor:
    """معالج Outbox: يقرأ الأحداث وينشرها لـ Message Broker"""
    
    def __init__(self, kafka_producer, batch_size: int = 100):
        self._producer = kafka_producer
        self._batch_size = batch_size
    
    def process_batch(self) -> int:
        """معالجة دفعة من الأحداث غير المنشورة"""
        events = OutboxEventModel.objects.filter(
            published_at__isnull=True
        ).order_by('created_at')[:self._batch_size]
        
        processed = 0
        for event in events:
            try:
                self._producer.send(
                    topic=f"nxa.{event.aggregate_type.lower()}.{event.event_type.lower()}",
                    key=event.aggregate_id.encode(),
                    value={
                        "event_type": event.event_type,
                        "payload": event.payload,
                        "correlation_id": event.correlation_id,
                        "tenant_id": event.tenant_id,
                        "aggregate_id": event.aggregate_id,
                        "aggregate_type": event.aggregate_type,
                        "event_id": str(event.id),
                        "created_at": event.created_at.isoformat(),
                    },
                )
                event.published_at = timezone.now()
                event.save(update_fields=['published_at'])
                processed += 1
            except Exception as e:
                event.retry_count += 1
                event.last_error = str(e)
                event.save(update_fields=['retry_count', 'last_error'])
        
        return processed
```

### 6.2 نمط InboxReceipt لعدم تكرار الأحداث

```python
# nxa_core/infrastructure/messaging/inbox/processor.py
class InboxProcessor:
    """يمنع إعادة معالجة نفس الحدث لنفس المستهلك"""
    
    def __init__(self, consumer_id: str):
        self._consumer_id = consumer_id
    
    def try_claim(self, event_id: str, event_type: str) -> bool:
        """
        محاولة المطالبة بالحدث.
        يرجع True إذا كان الحدث جديدًا لهذا المستهلك، False إذا معالج مسبقًا.
        """
        created, _ = InboxReceiptModel.objects.get_or_create(
            event_id=event_id,
            consumer_id=self._consumer_id,
            defaults={'event_type': event_type}
        )
        return created
    
    def process_if_new(self, event_id: str, event_type: str, handler: Callable) -> bool:
        """معالجة الحدث فقط إذا كان جديدًا"""
        if self.try_claim(event_id, event_type):
            handler()
            return True
        return False
```

### 6.3 إدارة المعاملات (Transaction Management)

```python
# nxa_core/application/services/transaction_manager.py
from contextlib import contextmanager
from typing import Callable, TypeVar, Generator
from django.db import transaction, connections
import logging

T = TypeVar('T')

logger = logging.getLogger(__name__)


class TransactionManager:
    """مدير معاملات مع دعم Outbox و Idempotency"""
    
    @contextmanager
    def atomic(self, use_outbox: bool = True) -> Generator[None, None, None]:
        """معاملة ذرية مع خيار Outbox"""
        with transaction.atomic():
            # تهيئة Outbox collector إذا مطلوب
            if use_outbox:
                from nxa_core.infrastructure.messaging.outbox.collector import OutboxCollector
                OutboxCollector.start_collection()
            
            try:
                yield
                
                if use_outbox:
                    OutboxCollector.flush()  # حفظ الأحداث المجمعة
            except Exception:
                if use_outbox:
                    OutboxCollector.clear()  # تنظيف الأحداث عند الفشل
                raise
    
    def execute_with_retry(
        self,
        operation: Callable[[], T],
        max_retries: int = 3,
        retry_on: tuple = (Exception,),
    ) -> T:
        """تنفيذ عملية مع إعادة محاولة عند تعارض تزامن"""
        last_exception = None
        
        for attempt in range(max_retries):
            try:
                with self.atomic():
                    return operation()
            except retry_on as e:
                last_exception = e
                if "version" in str(e).lower() or "concurrent" in str(e).lower():
                    logger.warning(f"Concurrency conflict, retry {attempt + 1}/{max_retries}")
                    continue
                raise
        
        raise last_exception
```

### 6.4 حقن التبعيات (Dependency Injection)

```python
# nxa_core/di/container.py
from typing import Dict, Type, Any
from nxa_core.application.ports.output import (
    RequestRepository, NotificationService, StorageService,
    CacheService, EmailService, VaultService, AIService
)
from nxa_core.infrastructure.persistence.django.repositories import (
    DjangoRequestRepository, DjangoAssetRepository, ...
)
from nxa_core.infrastructure.cache import RedisCacheAdapter
from nxa_core.infrastructure.storage import MinIOStorageAdapter
from nxa_core.infrastructure.email import SMTPAdapter, MSGraphAdapter
from nxa_core.infrastructure.vault import VaultAdapter
from nxa_core.infrastructure.ai import OpenAIAdapter, AzureAIAdapter


class Container:
    """حاوية حقن تبعيات بسيطة"""
    
    _instance = None
    _services: Dict[Type, Any] = {}
    _factories: Dict[Type, Callable] = {}
    
    @classmethod
    def configure(cls, settings) -> None:
        """تكوين الحاوية بالإعدادات"""
        cls._services = {}
        cls._factories = {}
        
        # مستودعات
        cls.register_factory(RequestRepository, lambda: DjangoRequestRepository())
        cls.register_factory(AssetRepository, lambda: DjangoAssetRepository())
        # ... باقي المستودعات
        
        # خدمات بنية تحتية
        cls.register_factory(CacheService, lambda: RedisCacheAdapter(settings.REDIS_URL))
        cls.register_factory(StorageService, lambda: MinIOStorageAdapter(
            endpoint=settings.MINIO_ENDPOINT,
            access_key=settings.MINIO_ACCESS_KEY,
            secret_key=settings.MINIO_SECRET_KEY,
            bucket=settings.MINIO_BUCKET,
        ))
        cls.register_factory(EmailService, lambda: SMTPAdapter(
            host=settings.SMTP_HOST,
            port=settings.SMTP_PORT,
            username=settings.SMTP_USER,
            password=settings.SMTP_PASSWORD,
        ))
        cls.register_factory(VaultService, lambda: VaultAdapter(
            url=settings.VAULT_URL,
            token=settings.VAULT_TOKEN,
        ))
        
        # AI Service (قابل للتعطيل)
        if settings.AI_ENABLED:
            if settings.AI_PROVIDER == "openai":
                cls.register_factory(AIService, lambda: OpenAIAdapter(settings.OPENAI_API_KEY))
            elif settings.AI_PROVIDER == "azure":
                cls.register_factory(AIService, lambda: AzureAIAdapter(
                    endpoint=settings.AZURE_OPENAI_ENDPOINT,
                    key=settings.AZURE_OPENAI_KEY,
                ))
    
    @classmethod
    def register_factory(cls, interface: Type, factory: Callable) -> None:
        cls._factories[interface] = factory
    
    @classmethod
    def get(cls, interface: Type) -> Any:
        if interface not in cls._services:
            if interface not in cls._factories:
                raise ValueError(f"No factory registered for {interface}")
            cls._services[interface] = cls._factories[interface]()
        return cls._services[interface]
    
    @classmethod
    def reset(cls) -> None:
        """للاختبارات فقط"""
        cls._services.clear()
```

---

## 7. المعاملات عبر السياقات (Cross-Context Transactions)

### 7.1 نمط Saga للمعاملات الموزعة

```python
# nxa_core/application/sagas/asset_assignment_saga.py
from dataclasses import dataclass
from typing import List, Callable
from nxa_core.application.services.transaction_manager import TransactionManager


@dataclass
class SagaStep:
    name: str
    execute: Callable
    compensate: Callable


class AssetAssignmentSaga:
    """
    Saga لتسليم أصل: حجز → تحقق مخزون → إنشاء عهدة → تحديث أصل → إشعار
    أي فشل يعوض الخطوات السابقة بترتيب عكسي
    """
    
    def __init__(self, tx_manager: TransactionManager):
        self._tx = tx_manager
        self._steps: List[SagaStep] = []
        self._completed: List[SagaStep] = []
    
    def add_step(self, name: str, execute: Callable, compensate: Callable) -> None:
        self._steps.append(SagaStep(name, execute, compensate))
    
    def execute(self) -> bool:
        try:
            for step in self._steps:
                logger.info(f"Executing saga step: {step.name}")
                step.execute()
                self._completed.append(step)
            return True
        except Exception as e:
            logger.error(f"Saga failed at step, compensating: {e}")
            self._compensate()
            return False
    
    def _compensate(self) -> None:
        for step in reversed(self._completed):
            try:
                logger.info(f"Compensating step: {step.name}")
                step.compensate()
            except Exception as ce:
                logger.critical(f"Compensation failed for {step.name}: {ce}")
                # تنبيه يدوي مطلوب
```

### 7.2 مثال: Saga إنشاء أصل من طلب شراء

```python
def create_asset_from_receipt_saga(receipt_id: str, line_id: str) -> bool:
    """Saga: استلام → إنشاء أصل → ربط عقد/ضمان → إشعار"""
    
    saga = AssetAssignmentSaga(tx_manager)
    
    # 1. تحقق من استلام الخط
    def verify_receipt():
        receipt_line = receipt_line_repo.get(line_id)
        if receipt_line.quantity_received <= 0:
            raise ValueError("No quantity to create asset")
        if receipt_line.asset_created:
            raise ValueError("Asset already created for this line")
    
    def compensate_verify():
        pass  # لا إجراء مطلوب
    
    saga.add_step("verify_receipt", verify_receipt, compensate_verify)
    
    # 2. إنشاء أصل
    created_asset = {}
    
    def create_asset():
        asset = Asset.create(
            tenant_id=receipt_line.po.tenant_id,
            asset_tag=generate_asset_tag(),
            serial_number=receipt_line.serial_number,
            asset_type_id=receipt_line.asset_type_id,
            supplier_id=receipt_line.po.supplier_id,
            purchase_date=receipt_line.receipt.received_at.date(),
            warranty_expires_at=calculate_warranty(receipt_line.warranty_months),
        )
        asset_repo.save(asset)
        created_asset['asset'] = asset
    
    def compensate_create_asset():
        if 'asset' in created_asset:
            asset_repo.delete(created_asset['asset'].asset_id)
    
    saga.add_step("create_asset", create_asset, compensate_create_asset)
    
    # 3. ربط عقد/ضمان
    def link_contract():
        if receipt_line.po.contract:
            ContractAsset.objects.create(
                contract=receipt_line.po.contract,
                asset=created_asset['asset'],
                coverage_start=receipt_line.receipt.received_at,
                coverage_end=created_asset['asset'].warranty_expires_at,
            )
    
    def compensate_link_contract():
        ContractAsset.objects.filter(asset=created_asset.get('asset')).delete()
    
    saga.add_step("link_contract", link_contract, compensate_link_contract)
    
    # 4. تحديث خط الطلب
    def mark_line_asset_created():
        receipt_line.asset_created = True
        receipt_line.asset = created_asset['asset']
        receipt_line.save(update_fields=['asset_created', 'asset'])
    
    def compensate_mark_line():
        receipt_line.asset_created = False
        receipt_line.asset = None
        receipt_line.save(update_fields=['asset_created', 'asset'])
    
    saga.add_step("mark_line_asset_created", mark_line_asset_created, compensate_mark_line)
    
    # 5. إشعار
    def notify():
        notification_service.queue_notification(
            template="asset_created_from_receipt",
            recipient=receipt_line.po.requested_by_user_id,
            context={"asset_tag": created_asset['asset'].asset_tag}
        )
    
    def compensate_notify():
        pass  # الإشعار غير حرج
    
    saga.add_step("notify", notify, compensate_notify)
    
    return saga.execute()
```

---

## 8. إدارة التكوين والإعدادات (Configuration Management)

```python
# nxa_core/config/settings.py
from pydantic_settings import BaseSettings
from typing import Optional, List
import os


class Settings(BaseSettings):
    """إعدادات التطبيق من متغيرات البيئة"""
    
    # التطبيق
    APP_NAME: str = "NexusServiceCloud"
    ENVIRONMENT: str = "development"  # development/staging/production
    DEBUG: bool = False
    SECRET_KEY: str
    ALLOWED_HOSTS: List[str] = ["*"]
    
    # قاعدة البيانات
    DATABASE_URL: str = "postgresql://user:pass@localhost:5432/nexus"
    DATABASE_POOL_SIZE: int = 20
    DATABASE_MAX_OVERFLOW: int = 10
    
    # Redis
    REDIS_URL: str = "redis://localhost:6379/0"
    REDIS_CACHE_TTL: int = 3600
    
    # رسالة/طابور
    CELERY_BROKER_URL: str = "redis://localhost:6379/1"
    CELERY_RESULT_BACKEND: str = "redis://localhost:6379/2"
    KAFKA_BOOTSTRAP_SERVERS: str = "localhost:9092"
    
    # تخزين
    MINIO_ENDPOINT: str = "localhost:9000"
    MINIO_ACCESS_KEY: str
    MINIO_SECRET_KEY: str
    MINIO_BUCKET: str = "nexus-attachments"
    MINIO_SECURE: bool = False
    
    # Vault
    VAULT_URL: str = "http://localhost:8200"
    VAULT_TOKEN: str
    VAULT_MOUNT_POINT: str = "secret"
    
    # بريد
    SMTP_HOST: str = "localhost"
    SMTP_PORT: int = 587
    SMTP_USER: str
    SMTP_PASSWORD: str
    SMTP_USE_TLS: bool = True
    MS_GRAPH_CLIENT_ID: Optional[str] = None
    MS_GRAPH_CLIENT_SECRET: Optional[str] = None
    MS_GRAPH_TENANT_ID: Optional[str] = None
    
    # ذكاء اصطناعي
    AI_ENABLED: bool = False
    AI_PROVIDER: str = "openai"  # openai/azure/local
    OPENAI_API_KEY: Optional[str] = None
    AZURE_OPENAI_ENDPOINT: Optional[str] = None
    AZURE_OPENAI_KEY: Optional[str] = None
    
    # مراقبة
    OTEL_EXPORTER_OTLP_ENDPOINT: str = "http://localhost:4317"
    PROMETHEUS_METRICS_PORT: int = 9090
    
    # أمان
    JWT_ALGORITHM: str = "RS256"
    JWT_PRIVATE_KEY_PATH: str
    JWT_PUBLIC_KEY_PATH: str
    JWT_ACCESS_TOKEN_EXPIRE_MINUTES: int = 15
    JWT_REFRESH_TOKEN_EXPIRE_DAYS: int = 7
    
    # معدل التحديد
    RATE_LIMIT_REQUESTS: int = 1000
    RATE_LIMIT_WINDOW_SECONDS: int = 60
    
    class Config:
        env_file = ".env"
        case_sensitive = True


settings = Settings()
```

---

## 9. قرارات معمارية مرتبطة (Related ADRs)

| ADR | العنوان | القرار الرئيسي |
|-----|--------|----------------|
| **ADR-005** | Django/DRF + PostgreSQL + RLS | المكدس التقني الأساسي للعزل متعدد المستأجرين |
| **ADR-015** | Clean Architecture Layers | فصل المجال/التطبيق/البنية التحتية |
| **ADR-016** | Modular Monolith First | البدء بوحدة نمطية موحدة، التطور لخدمات موزعة لاحقاً |
| **ADR-017** | Outbox Pattern for Events | أحداث موثوقة داخل معاملة المجال |
| **ADR-018** | Repository Pattern + Ports/Adapters | فصل منطق المجال عن تفاصيل التخزين |
| **ADR-019** | Optimistic Concurrency with Version | تحكم تزامن غير تشاؤمي |
| **ADR-020** | Saga Pattern for Cross-Context | معاملات موزعة مع تعويض |
| **ADR-021** | CQRS for Read Models | فصل نماذج القراءة للكتابات الثقيلة |
| **ADR-022** | Feature Flags for AI/Advanced | تشغيل/إيقاف قدرات بدون نشر |

---

## 10. المراجع

| معرّف | العنوان | الرابط |
|-------|--------|-------|
| DOC-00 | الفهرس الرئيسي | [00-master-index.md](../00-overview/00-master-index.md) |
| DOC-04 | نموذج المجال | [04-domain-model.md](../00-overview/04-domain-model.md) |
| DOC-06 | هندسة البنية التحتية | [06-infrastructure-architecture.md](../00-overview/06-infrastructure-architecture.md) |
| DOC-07 | تصميم API | [07-api-design.md](../00-overview/07-api-design.md) |
| DOC-08 | التكاملات والأحداث | [08-integrations-events.md](../00-overview/08-integrations-events.md) |
| DOC-11 | DevOps و CI/CD | [11-devops-cicd.md](../00-overview/11-devops-cicd.md) |
| DOC-18 | الملاحق (ADRs كاملة) | [18-appendices.md](../00-overview/18-appendices.md) |

---

> **التحكم في الوثيقة:** يتم صيانة هذه الوثيقة بواسطة هندسة الحل ومعمارية التطبيق. تتطلب التغييرات مراجعة من لجنة مراجعة المعمارية. المراجعة القادمة: 2027-02-09.