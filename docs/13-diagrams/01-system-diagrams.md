# مخططات النظام

NSC-DIAG-01 | مخططات تصميم مفاهيمي | 2026-11-09. المخططات تفسر الوثائق ولا تصف بنية منشورة. الأسماء قصيرة لتسهيل العرض في قارئ Markdown يدعم Mermaid.

## السياق وحدود الثقة

```mermaid
flowchart LR
  U[Requester] --> W[Web Portal]
  T[Technician] --> W
  A[Tenant Admin] --> W
  W --> API[Authorized API]
  M[Future Mobile] --> API
  API --> D[Domain Modules]
  D --> DB[(Tenant Scoped Data)]
  D --> O[(Transactional Outbox)]
  O --> WK[Background Worker]
  WK --> E[Email Provider]
  EXT[Monitoring or Discovery] --> I[Authenticated Connector]
  I --> API
  API --> F[Private Attachment Store]
```

كل سهم بين قناة خارجية وAPI أو موصل نقطة تحقق هوية وصلاحية ومدخلات. ظهور قاعدة واحدة لا يعني أن المؤسسات تشترك في صلاحية البيانات.

## تسلسل إنشاء حادث وإشعاره

```mermaid
sequenceDiagram
  participant User
  participant API
  participant Requests
  participant DB
  participant Worker
  participant Mail
  User->>API: Submit incident with operation key
  API->>API: Verify membership and permission
  API->>Requests: Create incident
  Requests->>DB: Save request, SLA snapshot, audit and outbox atomically
  DB-->>Requests: Commit
  Requests-->>API: Reference and version
  API-->>User: Created
  Worker->>DB: Claim pending event
  Worker->>Mail: Deliver notification
  alt Delivery acknowledged
    Worker->>DB: Record delivered
  else Temporary failure
    Worker->>DB: Record retry and next attempt
  end
```

## دورة طلب مختصرة

```mermaid
stateDiagram-v2
  [*] --> Open
  Open --> WaitingApproval: Service request policy
  WaitingApproval --> Open: Approved
  WaitingApproval --> Rejected: Rejected
  Open --> InProgress
  InProgress --> PendingRequester
  PendingRequester --> InProgress
  InProgress --> PendingExternal
  PendingExternal --> InProgress
  InProgress --> Resolved
  Resolved --> Closed
  Resolved --> InProgress: Reopened
  Closed --> InProgress: Authorized reopen
  Open --> Cancelled
  InProgress --> Cancelled
```

المخطط مختصر؛ شروط الإلغاء من بقية الحالات وقواعد الوقت والتفويض مرجعها [دورات العمل](../02-design/03-workflows.md)، وليس كل سهم متاحًا لكل دور.

## العلاقات الرئيسية

```mermaid
erDiagram
  TENANT ||--o{ REQUEST : owns
  TENANT ||--o{ ASSET : owns
  SERVICE ||--o{ CATALOG_ITEM : offers
  CATALOG_ITEM o|--o{ REQUEST : defines
  REQUEST ||--o{ COMMENT : contains
  REQUEST ||--o{ SLA_INSTANCE : measured_by
  REQUEST ||--o{ APPROVAL_INSTANCE : reviewed_by
  REQUEST ||--o{ PROBLEM_INCIDENT : linked_by
  PROBLEM ||--o{ PROBLEM_INCIDENT : groups
  PROBLEM ||--o{ CHANGE_LINK : motivates
  CHANGE ||--o{ CHANGE_LINK : addresses
  ASSET ||--o{ ASSET_ASSIGNMENT : tracks
  ASSET ||--o{ ASSET_CI_LINK : represented_by
  CONFIGURATION_ITEM ||--o{ ASSET_CI_LINK : represents
  PURCHASE_ORDER ||--o{ RECEIPT : receives
  KNOWLEDGE_ARTICLE ||--o{ KNOWLEDGE_REVISION : versions
```

هذه عينة علاقات وليست ERD فيزيائيًا كاملًا. توجد قيود إضافية مثل عهدة فعالة واحدة وتطابق المؤسسة في [نموذج البيانات](../02-design/02-data-model.md).
