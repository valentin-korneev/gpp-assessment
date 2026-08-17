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

`02-as-is/domain-entities.yaml` фиксирует conceptual/domain layer отдельно от physical database model. Historical physical model из `SRC-DB-001` хранится в `02-as-is/normalized/data/` и не используется для автоматического создания domain entities.

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

Эта модель не отождествляет domain entities с tables. `SRC-DB-001` теперь дает historical physical mappings для части concepts, но current physical structure не подтверждена. Для `InvoiceStatement` отдельно сохранено, что 2015 `STATEMENT_EXT` и invoice/statement fields в `PAYMENT_DATA` являются лишь historical physical projections, а не определением domain entity.

## 11. Поддержанные архитектурные inference

### INF-ARCH-001 - AdminConsole как control/configuration plane

Из подтвержденного набора administrative roles и domains следует, что `AdminConsole` выполняет логическую роль централизованного control/configuration plane GPP. Это не утверждение о physical deployment layer.

### INF-ARCH-002 - поведение, управляемое конфигурацией

Organization properties и service-type parameters управляют значимой частью integration, routing, limits, payment state и fee/settlement behavior. Поэтому текущая модель поддерживает inference о configuration-driven характере существенной части поведения GPP. Это не finding и не утверждение, что вся business logic реализована конфигурацией.

## 11.1. BDA - функциональная плоскость управления операционным днем, 2025

`SRC-BDA-001` добавляет более позднее функциональное представление отдельного модуля управления операционным днем (`CMP-GPP-012`). Явно подтвержденный актор - администратор Центрального банка. Функциональная цепочка строится как «Определение процесса -> Шаблон -> Операционный день -> Выполнение процесса операционного дня» (`INF-ARCH-006/007`).

Подтверждены возможности `CAP-ASIS-014..019`: настройка процессов и шаблонов, жизненный цикл операционного дня, данные выполнения процессов, архив и мониторинг XÖHKS, платежей и файлов. Отдельная каноническая межслойная заготовка находится в `bda-cross-layer-model.yaml`, а схема рабочего процесса - в `diagrams/bda-operational-day-workflow.mmd`.

Наличие экранов мониторинга не является утверждением о техническом владении соответствующими функциями: BDA отображает сведения по XÖHKS, платежам и файлам, но функциональный источник не доказывает, что именно BDA реализует расчеты, обработку платежей или доставку файлов (`INF-ARCH-008`). Технический слой `SRC-BDA-002` разобран отдельно в разделе 16: REST API и физическое хранение BDA подтверждены, однако связь с `AdminConsole`, точные границы развертывания и механизм запуска команд остаются `Q-BDA-001`; полный каталог процессов - `Q-BDA-002`.

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

Дополнительно сформированы temporal matrix, role/capability reconciliation, currentness/confidence view и historical leakage check. В текущей canonical state leakage исторических technologies, DR topology, access mechanics или operational schedule в current AS-IS не обнаружен.

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

## 15. Historical physical database model - source 2015

`SRC-DB-001` добавляет отдельный physical-data layer и не меняет current component boundary автоматически. Source на дату version 003 описывает две database areas: `CF Transaction DB` и `APUS Reporting DB` (`FACT-GPP-035`).

### 15.1. CF Transaction DB

Нормализованный inventory содержит 27 различимых table definitions и 203 columns после сведения точных source duplicates `MESSAGE`/`AMG_MSG`. Типы сохраняются в source form (`INTEGER`, `NUMBER`, `VARCHAR2`, `NVARCHAR2`, `RAW`, `DATE`, `TIMESTAMP`, `BLOB`, `CHAR`). Ссылки из колонки `Xarici açar` классифицируются как документированные `physical_fk` на дату источника. Narrative references и аналитически восстановленные связи хранятся отдельно в `logical-foreign-keys.yaml` как `documented_logical_reference` или `inferred_logical_reference` и не объявляются Oracle constraints (`FACT-GPP-036`).

Payment persistence в 2015 модели денормализована: `PAYMENT_DATA` содержит payment state/amount/date/method и одновременно statement/invoice identifiers/debt/current-debt context; `PAYMENT_EXT` и `STATEMENT_EXT` хранят дополнительные payment/statement attributes (`FACT-GPP-038`). Это поддерживает mapping к `DATA-GPP-009`/`DATA-GPP-008`, но не означает, что соответствующая domain entity равна одной таблице.

Также виден отдельный technical persistence contour для `MESSAGE`, `MESSAGE_STORAGE`, `OPERATION`, `OPERATION_SEARCH`, `AMG_MSG`, `PAYMENT_NOTIFICATION` и `PAYMENT_NOTIFICATION_TYPES` (`FACT-GPP-039`). Он моделируется как infrastructure concern, а не новый business domain entity.

### 15.2. APUS Reporting DB

Section 4 определяет `PAYMENT_DATA_AGGR_DAY` и `PAYMENT_DATA_AGGR_LAST_DATE` (`FACT-GPP-037`). Одновременно section 3.4 говорит о синхронизации `CF.PAYMENT_DATA` с одноименной table reporting area, но APUS section ее definition не содержит (`CONTR-DB-002`). Поэтому `APUS.PAYMENT_DATA` остается referenced-but-undefined до `Q-DB-003`.

### 15.3. Source anomalies и currentness boundary

- два несовместимых column sets опубликованы как `SC_IDEN_MTD_PREFIX_MAP` (`CONTR-DB-001`);
- `MESSAGE.OUTBOUND` имеет внутренне противоречивое описание enum (`CONTR-DB-003`);
- `PAYMENT_DATA.EPMT_STATE_CODE` использует value `2` для двух status meanings (`CONTR-DB-004`);
- документированные key/foreign-key data неполны, поэтому отсутствие constraint в source не трактуется как отсутствие constraint в реальной DB.

Совпадение части concepts с specification 2023 и message names с contract 2025 может говорить о semantic continuity, но не подтверждает continuity physical schema. Реальные Oracle constraints сверх явно документированных остаются `unverified`; логические связи восстанавливаются отдельным inference-слоем. Current CF/APUS topology и соответствие physical objects 2015 года production-состоянию остаются `Q-DB-001`.



## 16. BDA: техническая сквозная модель на 02.04.2025

`SRC-BDA-002` переводит BDA из чисто функционального представления `SRC-BDA-001` в частично установленную сквозную архитектурную модель. Источник прямо описывает REST API и структуры БД BDA: 39 операций, 15 именованных API-моделей и 11 физических таблиц в двух схемах.

### 16.1. REST и граница реконструкции

Контракт нормализован как `NORM-REST-001` (`normalized/rest/bda-api/openapi.yaml`). Реконструкция не означает, что исходная система публикует именно такой Swagger/OpenAPI: исходным материалом является человекочитаемая техническая спецификация с внутренними несогласованностями.

Неописанные тела запросов на изменение, тела ошибок и форматы ответов операций экспорта оставлены как пробелы. Два конфликтующих REST-пути сохранены в `CONTR-BDA-001/002`; несогласованности моделей - в `CONTR-BDA-003..005`.

Источник содержит примеры, похожие на реальные JWT, учетные данные в команде и внутренние сетевые адреса. В нормализованных артефактах они заменены безопасными заполнителями; исходные значения не сохраняются.

### 16.2. API-модель -> физическое хранение

Для конфигурационных и управляющих объектов установлены следующие соответствия:

- `Period` -> `BDA.PROCESS`; параметры -> `BDA.PARAMETERS` с `TYPE=PERIOD`;
- `Template` -> `BDA.TEMPLATE` + `BDA.TEMPLATE_PROCESS` + `BDA.PROCESS`; параметры -> `BDA.PARAMETERS` с `TYPE=TEMPLATE`;
- `OperationDay` -> `BDA.OPERATION_DAY`;
- `OperationPeriod` -> `BDA.OPERATION_PROCESS`; параметры дня -> `BDA.PARAMETERS` с `TYPE=OPERATION_DAY`;
- `Archive` -> `BDA_ARCHIVE.OPERATION_DAY`;
- `ArchiveOperationPeriod` -> `BDA_ARCHIVE.OPERATION_PROCESS`;
- `Login`/`Authority` -> `BDA.USERS`, `BDA.AUTHORITY`, `BDA.USER_AUTHORITIES`.

`BDA.PARAMETERS.REFERENCE_ID` - документированная условная логическая ссылка, а не один физический FK. Полные ограничения PK/FK источник не публикует, поэтому наличие физических ограничений остается `unverified`.

### 16.3. Семантика выполнения процесса

`POST .../operation-period/start/{operationPeriodId}` возвращает сообщение об асинхронном запуске. `BDA.OPERATION_PROCESS` хранит поля начала/окончания, исполнителя и технические статусы `IN_PROGRESS`, `DONE`, `HAS_ERROR`; `BDA.PROCESS` хранит команду запуска. Совокупность подтверждений связывает пользовательский запуск с REST-операцией и хранением состояния, но не идентифицирует компонент, который исполняет команду. Поэтому владение развертыванием и исполнением не достраивается.

### 16.4. Аутентификация и ошибки

Подтверждены вход по `username/password`, выдача JWT + authorities и хранение паролей в виде bcrypt-хэшей. `401` систематически указан как ошибка аутентификации, `400` - как ошибочный ответ; `404` явно появляется только в одном детальном разделе.

Источник не определяет `Authorization`/`Bearer`, обновление/отзыв токена/выход, управление ключами подписи или единый формат тела ошибки. HTTP Bearer в реконструированном OpenAPI помечен только как `INFERENCE`; авторитетная семантика остается в `Q-BDA-003/004`.

### 16.5. Мониторинг как намеренно неполная цепочка

Для сценариев мониторинга установлен путь `workflow -> capability -> REST -> API model`, но источник не показывает их физические таблицы или представления. Это зафиксировано как пробел документации (`FACT-GPP-059`, `INF-ARCH-012`), а не как вывод об отсутствии постоянного хранения.

То же относится к XML-артефакту процесса: операции экспорта и просмотра подробностей подтверждены, но физическое хранение не документировано.

Полная трассировка сохранена в `bda-cross-layer-model.yaml`.


## 17. Повторный cross-source pass: уточненная целостная AS-IS картина

Повторная сверка всех предоставленных source families и актуальных публичных материалов не меняет system boundary, но заметно повышает точность нескольких внутренних областей. Главное изменение состоит не в появлении нового названного «core» component, а в лучшем разделении **каналов, integration contracts, configuration/control, operational-day orchestration, settlement/reconciliation и instant-payment path**.

### 17.1. Логические плоскости системы

На текущем evidence level GPP рационально моделировать следующими логическими плоскостями, не превращая их автоматически в deployable components:

1. **Access/payment channels.** Собственные `gpp.az` и mobile app superseded с 10.01.2024; дальнейший доступ подтвержден через интегрированные банковские и небанковские PSP channels (`FACT-GPP-003/004`).
2. **Integration/service layer.** `PaymentService`/`WebPortalVC` и contract family 2025 описывают двунаправленный SOAP exchange с service organizations и отдельный HTTP/XML reconciliation path (`IF-GPP-008..011`).
3. **Core online payment/state area.** Существование централизованной обработки payment/invoice/state подтверждено на уровне системы, но named component, deployment topology и current physical persistence по-прежнему не определены (`Q-ARCH-001`, `Q-DB-001`).
4. **Configuration/control plane.** `AdminConsole` и organization/service configuration управляют identification, limits, PSP delegation, fee/reporting, payment-state и routing semantics (`INF-ARCH-001/002`, `FACT-GPP-060..067`).
5. **Operational-day job/orchestration plane.** BDA хранит process definitions, process commands, parameters, templates, operational-day state, execution state и monitoring (`FACT-GPP-049..059`, `FACT-GPP-070/071`).
6. **Settlement/reconciliation plane.** Источники 2015, 2023 и 2025 последовательно показывают day-end/next-business-day reporting and settlement concerns, включая XÖHKS, MT flows, DAY_PAYMENTS и bank-account-credit reconciliation (`FACT-GPP-024`, `FACT-GPP-069/071`).
7. **Instant-payment path.** AÖS/IPS является отдельным real-time payment path; functional configuration 2023 различает XÖHKS и AÖS scenarios, а current CBA site подтверждает system-level GPP integration with IPS (`FACT-GPP-064/072`). Это не позволяет отождествить current internal implementation с named `IPSClient` 2023 года.
8. **Historical persistence plane.** `CF Transaction DB` и `APUS Reporting DB` являются документированным physical baseline 2015, но не current production schema (`Q-DB-001`).

Эта структура является **архитектурной декомпозицией**, а не finding/recommendation и не утверждением о количестве runtime services.

### 17.2. BDA как administrative/job-orchestration control plane

Повторная сверка усиливает `INF-ARCH-013`: BDA значительно лучше объясняется как административная плоскость управления operational-day jobs, чем как core online payment processor. Основание: BDA хранит `PROCESS_RUN_COMMAND`, имеет process types для day-payment, MT, service-fee, beneficiary-summary и resend workloads, поддерживает template/day sequencing, asynchronous process start и отдельный monitoring lifecycle. При этом source не показывает, что BDA владеет online payment state machine или settlement engine.

Точный command runner/deployment boundary остается неизвестным. Дополнительно выявлена source inconsistency `CONTR-BDA-006`: version history 0.0.2 говорит о добавлении MX processes, но опубликованный TYPE/PROCESS_TYPE list их не перечисляет, хотя `APPLICATION_TYPE` отдельно ссылается на process type `mx`. Поэтому полный каталог и execution semantics остаются `Q-BDA-002`.

### 17.3. XÖHKS и IPS/AÖS: coexistence вместо replacement

`INF-ARCH-014` теперь поддерживается несколькими независимыми слоями evidence. Functional source 2023 прямо различает XÖHKS и AÖS payment scenarios в `SignedPain001`, а `IpsTransitAccountConsent` описывает consent-authentication для TopUp по AÖS-related flow. BDA 2025 продолжает мониторить XÖHKS и day-boundary settlement states. Integration contract 2025 фиксирует `DAY_PAYMENTS` и `DAY_PAYMENTS_FROM_BANK` в конце operational day. Публичная страница CBA одновременно описывает GPP как интегрированный с IPS.

Следовательно, текущая canonical interpretation не трактует IPS как простую замену XÖHKS: это разные payment/settlement concerns с возможным пересечением в общей GPP state/configuration area. Точный routing и shared-core ownership остаются `UNKNOWN`.

### 17.4. Что повторный pass закрыл на уровне source-date

Четыре ранее открытых вопроса теперь имеют достаточный direct evidence для resolution на дату соответствующего source: `Q-ID-001`, `Q-PAY-001`, `Q-IPS-001`, `Q-PSP-001`. Это не повышает их semantics автоматически до current 2026; component/runtime currentness остается в `Q-CUR-001` и связанных current questions.

Другие вопросы существенно сужены: `Q-CFG-001`, `Q-SETTLE-001`, `Q-FEE-001`, `Q-PAY-002`, `Q-PAY-003`, `Q-BIN-001`, `Q-OPS-001`, `Q-BDA-002`. Для channel-specific payment limits добавлен отдельный `Q-PAY-004`, поскольку contract 1.13+ расширяет invoice maximums, но не задает их полный precedence относительно organization-level `MaxPaymentAmount`.

### 17.5. Остаточный AS-IS uncertainty perimeter

После повторной проверки наиболее существенные пробелы находятся уже не в общей business semantics, а в **current physical/operational implementation**: named core processing/persistence components; production status modules 2023; current CF/APUS or successor database model; current auth/PKI/SoD; current backup/DR; exact operational-day clock windows; authoritative WSDL/runtime endpoints; BDA deployment/runner and full MX command catalog; точная deployed JWT transport/lifecycle.

Эти пробелы не заполняются историческими данными 2015 года, reviewer comments или реконструированными contracts. Они должны оставаться explicit `UNKNOWN` либо быть закрыты новыми operational/deployment artifacts.
