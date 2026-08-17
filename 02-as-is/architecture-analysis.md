# Архитектурный анализ - первичный каркас подсистем GPP

## Принцип декомпозиции

Functional specification перечисляет concrete modules, но не задает формальные subsystem boundaries. Ниже modules сгруппированы логически по подтвержденным responsibilities. Группировка является аналитической структурой над `CMP-*`, а не новым набором придуманных components.

## 1. Пользовательские каналы

Components:

- `CMP-GPP-001` WebPortal - historical/superseded с 10.01.2024;
- `CMP-GPP-004` mobile applications - historical/superseded с 10.01.2024;
- `CMP-GPP-005` MobilApi - RESTful backend для mobile applications на дату source; current runtime status unverified.

## 2. Каналы participant organizations и PSP integration

Components:

- `CMP-GPP-002` WebPortalVC;
- `CMP-GPP-006` PaymentService.

Responsibilities:

- предоставление integrated service organizations возможности использовать собственные internet resources;
- информационный обмен с electronic banking/payment channels PSP.

## 3. Авторизация платежей

Component:

- `CMP-GPP-003` PacPmtProc.

Responsibility:

- обмен с payment authorization centers;
- обработка authorization requests для card, bank account и electronic money instruments.

## 4. Операторский прием платежей

Component:

- `CMP-GPP-007` TellerWP (BankPayment).

Responsibility:

- automated workplace для операторов банков, почты и других финансовых организаций.

## 5. Администрирование и конфигурация

Component:

- `CMP-GPP-008` AdminConsole.

Confirmed administrative domains:

- participant organizations;
- organization hierarchy и configuration fields;
- типы и группы платежей/услуг;
- service codes;
- identification types;
- service fee tariffs;
- BIN data;
- registered payers;
- permissions;
- dual authorization;
- reports;
- payment audit;
- security;
- queries;
- monitoring.

DOCX variant дополнительно подтверждает branch/subdivision administration и detailed CRUD UI workflows.

## 6. Отчетность и settlement export

Component:

- `CMP-GPP-009` Loader Generators.

Responsibilities:

- participant reports;
- MT150/152 files для settlement-related exchange с XÖHKS.

## 7. Интеграция с внешними payment / settlement systems

Components:

- `CMP-GPP-010` LVPCSS - информационный обмен с XÖHKS;
- `CMP-GPP-011` IPSClient - информационный обмен с системой мгновенных платежей.

## 8. Core payment processing - неизвестная внутренняя область

System-level sources подтверждают прием и обработку payments, а field tables содержат semantics account/invoice/statement/payment state. Повторный public-source pass дополнительно выявил официальное описание централизованного хранения в базе данных портала банковских реквизитов integrated organizations и бюджетного классификатора (`FACT-GPP-021`). Это подтверждает наличие логического centralized persistence для reference/master data в публично описанной архитектуре.

Однако named module list по-прежнему не идентифицирует component(s), отвечающие за:

- central payment orchestration;
- account/invoice state;
- transaction persistence;
- physical core database;
- settlement state machine.

Публичное упоминание portal database не позволяет создать отдельный physical `CMP-*` и не доказывает текущую topology. Неизвестная область продолжает отслеживаться через `Q-ARCH-001`.

## 9. Внешние identification systems

`IAMAS / İAMAS` и `AVIS / AVİS` подтверждены как external systems (`FACT-GPP-019`): публичная страница CBA связывает их соответственно с системой Министерства внутренних дел и автоматизированной налоговой информационной системой. Functional specification использует их как identification sources и описывает получение данных до последующего debt lookup. `ASM-EXT-001` superseded, но конкретный GPP component, protocol и authoritative contract по-прежнему неизвестны. Поэтому system-context nodes остаются, а interface objects пока не создаются.


### FACT-GPP-020 - system-level эксплуатация GPP подтверждена за 2025 год

Официальный CBA review за 2025 год фиксирует 114,4 млн платежей через GPP на сумму 8,6 млрд манатов. Это снимает неоднозначность, могла ли остановка `gpp.az` и mobile app в 2024 году означать остановку платформы целиком: не означала. При этом данный факт не переносит currentness на отдельные modules из functional specification 2023 года, поэтому `Q-CUR-001` остается открытым.

## Ограничение семейства источников

Логическая карта не выбирает ни PDF v006, ни DOCX v005 как authoritative по filename или порядку передачи. Stakeholder сообщает, что DOCX v005 был передан позже и является более полным, однако допускается независимое происхождение документов (`ASM-SRC-001`). Shared facts строятся на overlap; source-specific delta сохраняет provenance до разрешения `Q-SRC-001`.


## 10. Первичная доменная модель конфигурации

`02-as-is/domain-entities.yaml` фиксирует conceptual/domain layer отдельно от будущей physical database model.

Подтвержденный каркас включает:

- `DATA-GPP-001` ParticipantOrganization;
- `DATA-GPP-002` OrganizationUnit / филиал;
- `DATA-GPP-003` ServiceGroup;
- `DATA-GPP-004` ServiceType;
- `DATA-GPP-005` IdentificationType;
- `DATA-GPP-006` ServiceFeeTariff;
- `DATA-GPP-007` SettlementAccount;
- `DATA-GPP-008` InvoiceStatement;
- `DATA-GPP-009` Payment.

Эта модель не утверждает физическую структуру БД и не отождествляет domain entities с tables. Для `InvoiceStatement` дополнительно сохранено ограничение: точное соотношение terminology `qaimə`/invoice и `statement` требует последующих sources.

## 11. Поддержанные архитектурные inference

### INF-ARCH-001 - AdminConsole как control/configuration plane

Из подтвержденного набора administrative roles и domains следует, что `AdminConsole` выполняет логическую роль централизованного control/configuration plane GPP. Это не утверждение о physical deployment layer.

### INF-ARCH-002 - поведение, управляемое конфигурацией

Organization properties и service-type parameters управляют значимой частью integration, routing, limits, payment state и fee/settlement behavior. Поэтому текущая модель поддерживает inference о configuration-driven характере существенной части поведения GPP. Это не finding и не утверждение, что вся business logic реализована конфигурацией.

## 12. Нормативно-operational и deployment baseline 2015

`SRC-OPS-001` существенно расширяет понимание исторического operating model GPP, но все technical/deployment assertions из него маркируются `historical`.

### 12.1. Роли и access model

В 2015 году документированы отдельные роли system administrator, security administrator, certificate services administrator, participant administrator и operator. Доступ предоставлялся в пределах индивидуального набора функций по статусу; authentication сочетала электронный сертификат и последующий username/password.

Это согласуется с тем, что более поздняя specification 2023 года все еще показывает развитый administrative/security perimeter, но не доказывает сохранение конкретной authentication scheme или неизменность role model. Current confirmation требуется через `Q-SEC-001`.

### 12.2. Operational day и settlement cadence

Исторический регламент фиксирует operational day 00:00-24:00. На следующий рабочий день выполнялись сводное информирование, settlement через XÖHKS и последующее информирование participant organizations о зачислении средств в последовательно заданных временных окнах. Внутри operational day участники также обменивались payer obligation/payment data.

Это является важным evidence о day-boundary semantics и batch/settlement cadence 2015 года, но current schedule не подтвержден (`Q-OPS-001`).

### 12.3. Backup/archive expectations

Историческая policy описывает регулярный Oracle backup через TSM, weekly full + промежуточные copies, перенос данных старше 30 дней в archive layer/external storage и периодическое двухкопийное offline хранение. Это подтверждает formal backup/retention process в 2015 году, но не current media/technology/retention strategy (`Q-BCK-001`).

### 12.4. Целевой визуальный просмотр DR/topology diagram

На странице 13 Appendix 3 визуально показаны:

- Main site и Alternative site внутри одного Main Center;
- общий disk array для Main/Alternative;
- отдельный Backup site в Backup Center;
- отдельный disk array в backup contour;
- Oracle replication между main и backup contours.

Сопровождающий recovery plan добавляет operational semantics: локальный отказ Main site приводит к автоматическому cluster failover на Alternative site; если Main+Alternative не восстанавливаются в течение одного часа, GPP переводится на Backup site в one-site mode с rerouting participant traffic. Failback выполняется после database synchronization с возвратом applications/traffic и восстановлением replication.

Для canonical model создана реконструкция `diagrams/historical-dr-topology-2015.mmd`. Exact hostnames, paths и operational commands из source намеренно не перенесены: они не нужны для доказательства topology/procedure semantics и могут быть чувствительными. Current DR topology/RTO/RPO остается открытым через `Q-DR-001`.

### 12.5. Исторический technology stack - граница актуальности

Source упоминает UNIX/Windows servers, Oracle/Oracle ExaData, WebLogic, Tomcat, Active MQ, Apache и TSM. Эти technologies считаются подтвержденными только как historical 2015 operational context (`FACT-GPP-027`). Ни одна из них не добавляется как current component/runtime dependency без более нового evidence.

## 13. Временная сверка между источниками

После обработки `SRC-OPS-001` выполнена отдельная reconciliation исторического operational baseline 2015 года с functional model 2023 года и более поздними public signals 2024-2025. Подробная derived view сохранена в `cross-source-reconciliation.md`.

Reconciliation не создает искусственную "current architecture" путем объединения разных дат. Основные выводы:

- administrative/security perimeter как function domain виден и в 2015, и в 2023 (`INF-ARCH-003`), но current role mapping, authentication и segregation of duties не подтверждены;
- payment lifecycle на логическом уровне разделяет payment acceptance/processing и downstream reporting/settlement concerns (`INF-ARCH-004`), но exact current orchestration и core processing component неизвестны;
- XÖHKS повторяется как settlement integration concern в 2015 и 2023 (`INF-ARCH-005`), тогда как IPS появляется отдельным integration concern только в 2023 evidence и не трактуется как replacement XÖHKS;
- technology/deployment mechanics 2015 года остаются historical и не повышаются по currentness из-за функциональной преемственности отдельных domains.

Дополнительно сформированы temporal matrix, role/capability reconciliation, currentness/confidence view и historical leakage check. На текущем WIP leakage исторических technologies, DR topology, access mechanics или operational schedule в current AS-IS не обнаружен.

## 14. Integration contract baseline 18.08.2025

`SRC-INT-001` добавляет более поздний и существенно более детальный contract-level view интеграции service organizations с HÖP. Он не заменяет автоматически всю component map 2023 года, но уточняет границу `WebPortalVC` и вводит отдельные contract surfaces, которые ранее были скрыты за `interface_type: unknown`.

### 14.1. Раздельная interface classification

До normalization каждый mechanism классифицирован отдельно:

- `IF-GPP-009` `ServiceCompanyWS` - SOAP, HÖP -> XT в реальном времени;
- `IF-GPP-008` `GPPPaymentWS` - SOAP, XT -> HÖP в реальном времени;
- `IF-GPP-010` `SCVirtualCabinetWS` - SOAP callback HÖP -> XT для завершения платежа;
- `IF-GPP-011` reconciliation - HTTP/XML через POST, не SOAP и не file transport;
- `IF-GPP-012` Bank -> XT credit notification - documented recommendation с unknown transport;
- `IF-GPP-013`/`IF-GPP-014` HÖP -> IAMAS/AVIS - real-time web-service calls с unknown protocol/contract.

Это подтверждает, что один source содержит несколько integration styles; его нельзя целиком считать SOAP.

### 14.2. Request/response и asynchronous semantics

`ServiceCompanyWS` содержит synchronous request/response operations для identification/debt lookup и control operations, а `NotifyAboutPayment`, `NotifyAboutPaymentCancel` и `NotifyAboutError` являются semantic notifications, которые при этом технически имеют SOAP request/ack semantics.

Для XT-initiated payment flow `InitiatePayment` вызывается через `GPPPaymentWS`, но завершение business process является asynchronous: HÖP позднее вызывает XT-hosted `SCVirtualCabinetWS` через `CompletePayment` либо `CompletePaymentWithError`. Сам callback снова является request/response SOAP exchange. SOAP retry/backoff semantics source не определяет.

`messageHeader.transactionID` является cross-message correlation identifier одного payment process и генерируется стороной, инициировавшей процесс (`FACT-GPP-031`).

### 14.3. Reconciliation как отдельный HTTP/XML contract

`INVOICE_PAY_APUS`, `DAY_PAYMENTS` и `DAY_PAYMENTS_FROM_BANK` передаются HTTP POST с XML body. Source задает no-body acknowledgement, HTTP 200 как acceptance и resend на следующей generation при любом non-200. Payment uniqueness определяется `receiptNumber`; message-level reference - сочетанием `UserID + SerialNumber`.

Inline XSD source реконструирован как `NORM-XML-001`. Это не превращает reconciliation в file/XML: transport в source явно HTTP POST.

### 14.4. Contract provenance boundary

WSDL links в source сохраняются как `referenced-unavailable`. Для `GPPPaymentWS` документирован test endpoint reference, но он хранится как `referenced-unverified`; production endpoint отсутствует. Поэтому normalized SOAP artifacts содержат abstract WSDL `message`/`portType` + XSD и **не** содержат выдуманные binding/service/endpoint/SOAPAction.

Реконструкции:

- `NORM-SOAP-001` - ServiceCompanyWS;
- `NORM-SOAP-002` - GPPPaymentWS;
- `NORM-SOAP-003` - SCVirtualCabinetWS;
- `NORM-XML-001` - сверка HTTP/XML.

### 14.5. Неразрешенные contract fragments

Модели `regUnRegDebtNotificationRequest`, `debtNotificationRequest`, `notifServiceAccountInvoices`, `notifServiceAccount`, `notifInvoice` документированы как SOAP type fragments, но operation/service/direction отсутствуют. Они не привязаны к выдуманному portType; gap вынесен в `Q-INT-001`.

В source сохранены четыре contract inconsistencies (`CONTR-INT-001` - `CONTR-INT-004`), поэтому normalization не исправляет их молча.

Canonical integration view сохранен в `diagrams/integration-view.mmd`.
