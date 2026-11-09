# تتبع المصدر والمتطلب والتصميم والقبول

NSC-TRACE-01 | تتبع وثائقي، لا تغطية تنفيذ | 2026-11-09.

S1 الوثيقة العربية، S2 الإنجليزية، S3 قالب المنصة، S4 بنية NexusAsset، S5 مدخلا TXT. تعريفها في [المصادر](../14-appendices/01-sources-glossary.md). أرقام الأقسام تخص الوثائق الأصلية. D يدل على قرار تصميم أضيف هنا لتفصيل المصدر، ولا ينسب إليه كمتطلب حرفي.

كل FR موصوف في [المتطلبات](../01-analysis/02-functional-requirements.md)، وكل AC في [خطة القبول](../04-testing/01-acceptance-plan.md).

- FR-01 ← S3 §4 وD للعزل ← [الأمن والتنظيم](../07-security/01-security-tenancy.md) ← AC-01 ← P1.
- FR-02 ← S3 §4.2 ← [الأمن](../07-security/01-security-tenancy.md) ← AC-02 ← P1.
- FR-03 ← S1 §4 وS3 §3/5 ← [الدورات](../02-design/03-workflows.md) ← AC-03 ← P1.
- FR-04 ← S3 §7.2 و§4.5 ← [البيانات](../02-design/02-data-model.md) ← AC-04 ← P1.
- FR-05 ← S3 §5.1 ← [البيانات](../02-design/02-data-model.md) ← AC-05 ← P1.
- FR-06 ← S3 §10.1 وD لخصوصية التعليقات ← [الأمن](../07-security/01-security-tenancy.md) ← AC-06 ← P1.
- FR-07 ← S3 §5.4/6 ← [الدورات](../02-design/03-workflows.md) ← AC-07 ← P1.
- FR-08 ← S3 §5.2/5.3 ← [SLA/OLA](../02-design/04-service-levels.md) ← AC-08 ← P1/P2.
- FR-09 ← S3 §6.3 ← [الموافقات](../02-design/03-workflows.md) ← AC-09 ← P1/P2.
- FR-10 ← S3 §6.1/6.2 ← [المشكلات](../02-design/03-workflows.md) ← AC-10 ← P2.
- FR-11 ← S1 §3.3 وS3 §6.3 ← [التغييرات](../02-design/03-workflows.md) ← AC-11 ← P2.
- FR-12 ← S3 §6.4/8/9/10 ← [التسليم](../02-design/02-data-model.md) و[تجربة الاستخدام](../10-design-system/01-ux-navigation.md) ← AC-12 ← P3/P4.
- FR-13 ← S1 §3.4/3.5 وS3 §6.5/6.6 ← [الأصول وCMDB](../02-design/02-data-model.md) ← AC-13 ← P3.
- FR-14 ← S3 §3/6.5 ← [الشراء](../02-design/03-workflows.md) ← AC-14 ← P3.
- FR-15 ← S1 §3.9 وS3 §6.6 ← [بيانات الصيانة](../02-design/02-data-model.md) ← AC-15 ← P3.
- FR-16 ← S3 §6.7 ← [المعرفة](../02-design/02-data-model.md) ← AC-16 ← P1.
- FR-17 ← S3 §5.5/6.8 ← [الأتمتة](../02-design/03-workflows.md) و[الأحداث](../02-design/05-integrations-events.md) ← AC-17 ← P1/P2.
- FR-18 ← S1 §2.5 وS3 §4.8/10 ← [المؤشرات](../01-analysis/03-quality-requirements.md) ← AC-18 ← P1/P3.
- FR-19 ← S1 §5.7 وS3 §4.7/4.10 ← [التكاملات](../02-design/05-integrations-events.md) ← AC-19 ← P4.
- FR-20 ← S1 §5 وS3 §2.5 ← [ضوابط AI](../02-design/05-integrations-events.md) ← AC-20 ← P4.
- FR-21 ← S3 §10.1 وD لسجل الأمان ← [الأمن](../07-security/01-security-tenancy.md) ← AC-21 ← P1.
- FR-22 ← S1 §2.7 وS3 §2.6/4.4 ← [العزل](../07-security/01-security-tenancy.md) ← AC-22 ← P4.
- FR-23 ← S3 §5.1/4.2 وD للترحيل ← [النشر والترحيل](../05-deployment/01-deployment-recovery.md) ← AC-23 ← P1/P3.
- FR-24 ← D مستلهم من قنوات S4/S5، إضافة مقترحة لا وعد في S1 ← [الهاتف](../08-mobile/01-mobile-scope.md) ← AC-24 ← P4.

## تتبع المتطلبات غير الوظيفية

NFR-01 إلى [الأمن](../07-security/01-security-tenancy.md) والتحقق السلبي؛ NFR-02 إلى ملف الحمل وخطة قياس؛ NFR-03 إلى [الرصد](../06-operations/01-operations.md) ومراقبة المعاملات؛ NFR-04 إلى [تمرين الاستعادة](../05-deployment/01-deployment-recovery.md)؛ NFR-05 إلى Outbox وAC-03/17/19؛ NFR-06 إلى خريطة الشاشات وجولة إتاحة؛ NFR-07 إلى RUN-01/04؛ NFR-08 إلى Q-05؛ NFR-09 إلى ضوابط حدود الوحدات؛ NFR-10 إلى Q-07 ومصفوفة توافق مستقبلية.

## حدود التغطية

تم تحويل الوحدات التشغيلية إلى خط أساس مفاهيمي ومعايير قبول. لا توجد مواصفة آلية كاملة للواجهات أو تصميم فيزيائي نهائي لكل جدول أو محرك Workflow عام. ممارسات ITIL وAgile مراجع نطاق وتطوير، وليست التزامًا بتنفيذ كل مفهوم كميزة. تفاصيل Sprint والتسعير ومورد AI والهواتف قرارات مؤجلة معلنة.
