# كتالوج API والعقد المفاهيمي

NSC-API-01 | مسارات مقترحة، لا endpoints منفذة ولا OpenAPI مكتمل | 2026-11-09.

## قواعد العقد

بادئة مقترحة /api/v1. المؤسسة من سياق عضوية موثوق، لا حقل قابل لتغيير الملكية. قائمة موارد بترقيم cursor وحد 50 افتراضي و100 أقصى مقترح، وترتيب ثابت مع المعرف لفصل القيم المتساوية. مرشحات مسموحة صراحة، وأوقات UTC بتنسيق موحد.

الأخطاء: code ورسالة آمنة وfield_errors عند الحاجة وcorrelation_id. 401 لغياب الهوية، 403 لمنع فعل ظاهر، 404 للكائن المفقود أو غير المرئي دون كشف وجوده، 409 لتعارض حالة/نسخة/مفتاح عملية، 422 لمدخلات غير صالحة، 429 لتجاوز الحد. يجب توحيد هذا الاختيار في العقد الآلي لاحقًا.

الكتابة الحساسة تستخدم expected_version؛ عدم إرساله في تحديث يتطلبه العقد يرفض، واختلافه يعطي 409 مع طلب إعادة القراءة. الإنشاء والأوامر القابلة لإعادة المحاولة تستخدم Idempotency-Key مرتبطًا بالمؤسسة والفاعل والعملية وبصمة الحمولة. نفس المفتاح والحمولة يعيدان النتيجة الأصلية، ونفس المفتاح بحمولة مختلفة يرفض. مدة حفظ المفاتيح المقترحة 7 أيام، أما أحداث الاستلام والتكامل فتملك قيد منع تكرار دائمًا ضمن فترة احتفاظها.

## العمليات الأساسية

- GET /me وGET /memberships: الهوية والنطاقات المصرح بها، دون كشف أعضاء مؤسسة أخرى، FR-01/02.
- GET /catalog/items: عناصر مرئية فقط؛ POST /service-requests: item_id وform_version_id وقيم النموذج، ينتج طلبًا أو أخطاء الحقول، FR-04.
- GET /requests: ترشيح النوع والحالة والمجموعة والأولوية والمدة ضمن التفويض، FR-03/18.
- POST /incidents: عنوان ووصف وخدمة وتصنيف وأثر واستعجال ومراجع مرفقات مصرح بها؛ ينتج 201 مع رقم ونسخة وحالة وملخص SLA، FR-03/05/08.
- GET /requests/{id}: تفاصيل مرئية وتعليقات حسب الجمهور؛ لا يعيد الملاحظات الداخلية للمستفيد، FR-06.
- PATCH /requests/{id}: حقول تحرير مسموحة فقط ونسخة متوقعة؛ تغيير الحالة والتعيين عبر أوامر مستقلة، FR-07.
- POST /requests/{id}/transitions: target_status وreason وresolution عند اللزوم وexpected_version؛ يعيد الحالة الجديدة أو سبب منع الانتقال، FR-07.
- POST /requests/{id}/assignments: group_id وtechnician_id الاختياري والسبب والنسخة؛ يتحقق من أهلية المجموعة والفني، FR-05.
- POST /requests/{id}/comments: النص وvisibility؛ إذن internal للفنيين فقط، FR-06.
- POST /requests/{id}/work-logs: مدة وزمن ووصف؛ لا يقبل تغيير كاتب السجل خارج التفويض، FR-06.
- GET /requests/{id}/service-level: السياسة والنسخة والأوقات وحالة الاستجابة والحل؛ قراءة خاضعة لنطاق الطلب، FR-08.
- POST /approval-instances/{id}/decisions: approve/reject والسبب ونسخة المورد؛ قرار واحد فعال لكل موافق وخطوة، FR-09.

## بقية المجالات

- /problems: إنشاء وقراءة وتحديث، وPOST /problems/{id}/incident-links لربط الحوادث، FR-10.
- /changes: إنشاء وتقييم؛ /changes/{id}/transitions و/implementation-records لأوامر الدورة ونتيجة التنفيذ، FR-11.
- /projects و/projects/{id}/tasks و/tasks/{id}/dependencies: إدارة المشروع والتبعيات؛ /releases و/releases/{id}/deployment-attempts للتسليم، FR-12.
- /assets و/assets/{id}/assignments و/assets/{id}/reservations: الأصل والعهدة والحجز؛ /configuration-items و/configuration-relations للعلاقات، FR-13/15.
- /purchase-orders و/purchase-orders/{id}/receipts: الشراء والاستلام؛ /contracts للعقود، FR-14.
- /maintenance-plans و/maintenance-orders/{id}/results: الخطة ونتيجة المهمة، FR-15.
- /knowledge/articles و/knowledge/articles/{id}/publication-requests: مسودة ومراجعة نشر، FR-16.
- /automation/rules و/automation/rules/{id}/simulations: إدارة ومحاكاة دون تنفيذ أفعال أثناء المحاكاة، FR-17.
- /reports/service-level و/report-exports: تقرير متزامن محدود أو مهمة تصدير مؤجلة 202، FR-18.
- /integrations و/integrations/{id}/runs: إدارة الموصل وتشغيله؛ /webhooks/{connector_id}: دخول خارجي موثق لا API مفتوح، FR-19.
- /ai/suggestions: اقتراح فقط؛ /audit-events: قراءة مقيّدة؛ /service-desks: إدارة مكاتب؛ /imports: معاينة ودفعات استيراد، FR-20 إلى FR-23.

هذه مجموعات موارد وليست تعريف كل فعل أو مخطط استجابة. تفاصيل الحقول والبحث والقيود لكل مورد ومخططات OpenAPI مؤجلة لمرحلة عقد تفصيلي بعد اعتماد النطاق. لا توصف هذه القائمة بأنها تغطية API كاملة.

## الملفات والمهام الطويلة

رفع المرفق يعطي هوية وحالة quarantine؛ لا يصبح قابلًا للربط النهائي والتنزيل قبل فحصه. مهمة التقرير أو الاستيراد تعيد operation_id؛ القراءة والإلغاء إن كان آمنًا يخضعان لصلاحية صاحب العملية. لا يكفي امتلاك رابط النتيجة. فشل المهمة يوضح الصفوف المقبولة والمرفوضة ونقطة الاستئناف دون نشر بيانات نطاق آخر.
