# Временная сверка между источниками

## 1. Назначение

Этот файл является производным AS-IS analysis view и не создает параллельный слой фактов. Все выводы опираются на canonical `FACT-*`, `INF-*`, `CMP-*`, `CAP-ASIS-*`, `IF-*` и открытые `Q-*`.

Цель - не склеивать источники разных лет в одну якобы текущую архитектуру, а явно показать, какие элементы повторяются, меняются или остаются неизвестными между 2015, 2023, 2024 и 2025 годами.

Temporal labels используются в смысле `ASSESSMENT_PROTOCOL.md`:

- `historical` - подтверждено для прошлой даты, но не переносится в current AS-IS;
- `current-at-source-date` - подтверждено на дату источника;
- `current` - более поздний evidence подтверждает текущее для соответствующего периода состояние;
- `unverified` / `UNKNOWN` - актуальность или механизм не подтверждены.

## 2. Временная матрица источников

| Область | 2015 - `SRC-OPS-001` | 2023 - `SRC-FS-001/002` | 2024 public | 2025 public | Current AS-IS handling |
|---|---|---|---|---|---|
| System-level назначение и эксплуатация | Нормативно описана эксплуатация GPP и проведение операций (`FACT-GPP-022..027`) | GPP описан как единая платформа приема, обработки и расчетов (`FACT-GPP-008`, `CAP-ASIS-004`) | Закрыты только собственные `gpp.az` и mobile app (`FACT-GPP-003/004`) | Подтверждена system-level обработка 114,4 млн платежей (`FACT-GPP-020`) | GPP как система подтверждено функционировал в 2025; currentness конкретных modules остается `Q-CUR-001` |
| Пользовательские/payment channels | Платежи регистрировались через финансовые service points участников в рамках operational day (`FACT-GPP-024`) | WebPortal, mobile apps, TellerWP, PaymentService и другие modules перечислены как элементы infrastructure (`FACT-GPP-008`) | `gpp.az` и mobile app discontinued; платежи доступны через интегрированные bank/non-bank PSP channels (`FACT-GPP-003/004`) | System-level operation подтверждена, channel decomposition не раскрыта | Current digital access через внешние интегрированные channels подтвержден; status TellerWP/PaymentService/WebPortalVC и др. после 2023 - `UNKNOWN` |
| Administrative roles и control plane | Раздельные system/security/certificate/participant administrator и operator roles; certificate + username/password access (`FACT-GPP-022/023`) | AdminConsole roles: CBA system administrator, participant administrator, bank security administrator, bank controller, KXM administrator; UI содержит permissions и dual authorization (`FACT-GPP-009/011`) | Нет релевантного evidence | Нет релевантного evidence | Administrative/security domain сохраняется как архитектурно значимый, но current role mapping и authentication scheme - `Q-SEC-001` |
| Operational day и settlement cadence | `00:00-24:00`; next-business-day reporting и settlement через XÖHKS по фиксированным окнам (`FACT-GPP-024`) | XÖHKS представлен через Loader Generators/LVPCSS, отдельно появился IPSClient (`FACT-GPP-015`) | Нет schedule evidence | Contract 1.16 подтверждает `DAY_PAYMENTS`/`DAY_PAYMENTS_FROM_BANK` в конце operational day; BDA показывает XÖHKS monitoring и settlement-state progression (`FACT-GPP-069/071`) | Exact current clock boundary/windows и execution ownership остаются `Q-OPS-001`; сама day-end settlement/reconciliation semantics подтверждена позднее 2015 года |
| Payment processing lifecycle | Регистрация платежа, participant information exchange, next-day settlement/reporting (`FACT-GPP-024`) | Единая платформа приема/обработки/расчетов; PacPmtProc authorization; Loader/LVPCSS/IPSClient downstream integration (`FACT-GPP-008/015/018`) | Own web/mobile channels прекращены, а платежи направлены в integrated PSP services (`FACT-GPP-003/004`) | System-level volume подтверждает продолжающуюся обработку | Логические стадии payment acceptance/processing и downstream reporting/settlement подтверждены cross-source, но current component orchestration и timing неизвестны (`Q-ARCH-001`, `Q-OPS-001`) |
| Persistence/data state | Operational source подтверждает Oracle persistence/replication, а `SRC-DB-001` отдельно документирует historical CF/APUS physical schema (`FACT-GPP-025..027`, `FACT-GPP-035..040`) | Есть configuration/payment-state semantics, но current physical DB/core processing component не назван (`FACT-GPP-017/018`) | Нет physical persistence evidence | Integration contract повторяет часть message/domain vocabulary, но не physical schema | Historical CF/APUS model хранится отдельно; current physical persistence/core processing topology остается `Q-ARCH-001`/`Q-DB-001` |
| Backup/archive | Формализованы Oracle/TSM backup, archive и offline media procedures (`FACT-GPP-025`) | В текущем canonical evidence 2023 отдельная backup model не описана | Нет evidence | Нет evidence | Только historical semantics; current backup/retention/restore - `Q-BCK-001` |
| DR/deployment | Three-site Main/Alternative/Backup topology, cluster failover и remote DR/failback (`FACT-GPP-026`) | Functional module list не дает physical deployment topology (`FACT-GPP-008`) | Нет evidence | Нет evidence | Current DR/deployment topology и RTO/RPO - `Q-DR-001`; 2015 topology остается отдельной historical reconstruction |
| Technology stack | UNIX/Windows, Oracle/ExaData, WebLogic, Tomcat, Active MQ, Apache, TSM (`FACT-GPP-027`) | Specification перечисляет functional/infrastructure modules, но не подтверждает continuity platform technologies 2015 | Нет evidence | Нет evidence | Ни одна technology 2015 не считается current без дополнительного evidence |
| External identification | В `SRC-OPS-001` IAMAS/AVIS не являются ключевым подтверждением текущей integration model | Централизованное управление identification types включает SC/IAMAS/AVIS и ACC/DOC/MIX; organization properties отдельно управляют post-IAMAS/AVIS debt lookup (`FACT-GPP-060`) | Нет релевантного dated evidence | Contract 2025 подтверждает IAMAS/AVIS как external identification calls, но protocol/component ownership не раскрывает | `Q-ID-001` закрыт на source-date; current contract/component ownership остается `UNKNOWN` |

## 2.1. Уточнение модели операционного дня по руководству BDA от 10.03.2025

`SRC-BDA-001` дает более позднее функциональное подтверждение для области операционного дня, не заменяя исторические правила 2015 года и не задавая техническую семантику REST.

- подтвержден административный актор Центрального банка и отдельная функциональная область BDA (`FACT-GPP-041`);
- операционный день создается из шаблона, поддерживает закрытие и архивирование, причем архивирование требует завершения всех процессов дня (`FACT-GPP-044/045`);
- для выполнения процесса дня отображаются исполнитель, время начала и окончания, статус и доступ к XML-файлу (`FACT-GPP-046`);
- в мониторинге 2025 года видимы файлы и платежи XÖHKS, а также связанные сценарии по MT и неотправленным файлам (`FACT-GPP-048`).

Следствие: `Q-OPS-001` больше не означает полное отсутствие более поздних подтверждений по операционному дню. Функциональный жизненный цикл на дату 10.03.2025 подтвержден, но точная граница суток, окна отчетности и расчетов следующего рабочего дня, а также принадлежность оркестрации расчетов остаются `UNKNOWN`. Наличие мониторинга XÖHKS также продлевает подтверждение самой области XÖHKS до 2025 года, не доказывая неизменность транспорта, контрактов или реализации расчетов.

## 3. Reconciliation ролей и administrative model 2015 -> 2023

### 3.1. Что можно утверждать

Cross-source evidence показывает устойчивость самого административного/security perimeter, но не неизменность конкретных ролей или механизмов доступа.

| Function domain | 2015 | 2023 | Reconciliation |
|---|---|---|---|
| System/platform administration | System administrator имеет широкий operational perimeter, включая platform service, rights, operational day и participant activity (`FACT-GPP-022`) | CBA system administrator является одной из ролей AdminConsole (`FACT-GPP-009`) | Есть continuity function domain; identity role и точный набор полномочий между датами не доказаны |
| Participant/user administration | Participant administrator контролирует reporting/user activity; system administrator управляет правами и participant status (`FACT-GPP-022`) | Participant administrator и KXM administrator присутствуют в AdminConsole; participant management и permissions видны в UI (`FACT-GPP-009/011/012`) | Participant administration остается видимым domain; exact delegation и boundaries требуют `Q-SEC-001` |
| Security administration | Отдельный security administrator отвечает за information security (`FACT-GPP-022`) | Bank security administrator присутствует в AdminConsole; security domain и permissions видны в UI (`FACT-GPP-009/011`) | Security administration присутствует в обоих source dates, но organizational scope изменен/неполон и current state неизвестен |
| PKI/certificate administration | Отдельный certificate services administrator и certificate-based access (`FACT-GPP-022/023`) | Отдельная certificate-services role в canonical 2023 evidence не подтверждена | Нельзя считать role сохранившейся или исчезнувшей; mechanism/ownership - `Q-SEC-001` |
| Four-eyes/control | Отдельный bank controller в правилах 2015 не подтвержден | Bank controller и dual authorization видны в 2023 AdminConsole (`FACT-GPP-009/011`) | 2023 evidence делает control/dual-authorization domain более явным; origin/evolution не устанавливается |
| Payment operator | Operator определен как лицо в bank/financial organization, ответственное за payment operations (`FACT-GPP-022`) | TellerWP предназначен для operators банков, почты и других financial organizations (`CAP-ASIS-011`) | Сильная функциональная преемственность operator-facing payment acceptance на уровне role purpose, но current TellerWP runtime остается `Q-CUR-001` |

### 3.2. Role x capability matrix

Обозначения: `C` - direct source mapping подтверждает роль и функцию; `S` - role/surface/domain видимы, но точное назначение capability этой роли не доказано; `-` - соответствующий mapping не подтвержден source evidence.

| Роль / дата источника | Администрирование platform/service | Администрирование participant/user | Security/PKI | Операционный день | Прием платежей | Reports/audit/monitoring |
|---|---:|---:|---:|---:|---:|---:|
| System administrator - 2015 | C | C | - | C | - | C |
| Security administrator - 2015 | - | - | C | - | - | - |
| Certificate services administrator - 2015 | - | - | C | - | - | - |
| Participant administrator - 2015 | - | S | - | - | - | C |
| Operator - 2015 | - | - | - | - | C | - |
| CBA system administrator - 2023 | S | S | S | - | - | S |
| Participant administrator - 2023 | - | S | - | - | - | S |
| Bank security administrator - 2023 | - | - | S | - | - | S |
| Bank controller - 2023 | - | - | S | - | - | S |
| KXM administrator - 2023 | - | S | - | - | - | S |
| TellerWP operator - 2023 | - | - | - | - | C | - |

Матрица не переносится в current authorization design. Особенно для 2023 `S` означает только, что role и соответствующий AdminConsole domain видимы в одном source, но source не дает полного RBAC matrix.

## 4. Устойчивая operational semantics против implementation mechanics

Это разделение предназначено для предотвращения historical leakage. Даже "устойчивая semantics" не считается current автоматически: она лишь лучше переносится между источниками как архитектурное понятие, если получает позднее подтверждение.

| Область | Operational / domain semantics | Historical implementation mechanics 2015 | Cross-source handling |
|---|---|---|---|
| Access control | Ограничение доступных функций по роли/status, разделение administrative responsibilities | Электронный сертификат + username/password, sealed password cards, конкретные role names | Ролевое/permission-based administration подтверждается также 2023; конкретные authentication mechanics остаются historical |
| Participant administration | Onboarding/status/rights/control participant organizations и users | Конкретное распределение действий между system и participant administrators | Participant management/permissions также видны в AdminConsole 2023; exact responsibility matrix не переносится |
| Operational day | Наличие business/operational day boundary, reporting и settlement activities | `00:00-24:00`, fixed next-business-day windows | Day semantics подтверждена как historical и частично public context, но exact current schedule - `UNKNOWN` |
| Payment lifecycle | Отдельные стадии приема/обработки, participant information/reporting и settlement | Конкретная next-day sequence 2015 | 2023 module responsibilities поддерживают логическое разделение стадий; current orchestration - `UNKNOWN` |
| Backup/recovery | Формализованные backup, restore, archive и retention activities | Oracle/TSM, weekly/full-level strategy, 30-day archive movement, DVD/tape copies | Только historical; нет более позднего source для current implementation |
| Disaster recovery | Локальный failover, remote DR transition, failback после data synchronization | Main/Alternative shared-storage cluster, Backup site, Oracle replication, one-hour trigger и concrete scripts | Только historical deployment evidence; current topology/RTO/RPO - `UNKNOWN` |
| Settlement integration | Отдельный downstream clearing/settlement integration concern | XÖHKS по конкретному 2015 schedule | XÖHKS снова присутствует в 2023 module model; continuity concern поддержана, но current flow/timing не доказаны |

## 5. Payment lifecycle reconciliation 2015 <-> 2023/2024/2025

### 5.1. Логическая последовательность

| Этап | Evidence 2015 | Evidence 2023 | Более позднее evidence / текущая трактовка |
|---|---|---|---|
| 1. Payer/obligation context | Participants передают payer-base changes и obligations в течение operational day (`FACT-GPP-024`) | Identification/service/payment configuration и invoice/payment-state rules существуют (`FACT-GPP-013/017/018`) | Exact current inbound obligation flow не подтвержден |
| 2. Payment initiation/acceptance | Payments принимаются через financial service points и регистрируются в GPP (`FACT-GPP-024`) | WebPortal/mobile/TellerWP/PSP integration modules описывают несколько entry channels (`FACT-GPP-008/015`) | 2024 own web/mobile discontinued, integrated external bank/non-bank channels подтверждены (`FACT-GPP-003/004`) |
| 3. Instrument authorization | В operational rules 2015 отдельный authorization component не является частью extracted model | PacPmtProc выполняет authorization requests (`FACT-GPP-015`, `CAP-ASIS-010`) | Current runtime status PacPmtProc - `Q-CUR-001` |
| 4. Core processing / payment state | GPP регистрирует операции; physical core не выделен в rules | GPP принимает/обрабатывает платежи; service config управляет partial/full/advance payment state (`CAP-ASIS-004`, `FACT-GPP-018`) | System-level processing подтверждено в 2025 (`FACT-GPP-020`), physical core/persistence - `Q-ARCH-001` |
| 5. Participant information/reporting | Payment information передается участникам; next-day summaries формализованы (`FACT-GPP-024`) | Loader Generators формирует participant reports (`FACT-GPP-015`) | Public page также говорит о real-time participant information и end-of-day reconciliation, но currentness `unverified` (`FACT-GPP-006`) |
| 6. Clearing/settlement | Next-business-day settlement через XÖHKS (`FACT-GPP-024`) | Loader Generators формирует MT150/152 для XÖHKS; LVPCSS взаимодействует с XÖHKS (`FACT-GPP-015`, `IF-GPP-005/006`) | Current schedule и exact orchestration - `Q-OPS-001`/`Q-SETTLE-001` |
| 7. Instant-payment integration | Не подтверждена extracted historical model | IPSClient взаимодействует с Instant Payment System; `SignedPain001`/`IpsTransitAccountConsent` различают AÖS/XÖHKS и consent/signature scenarios (`FACT-GPP-015/064`) | Current CBA site подтверждает system-level GPP↔IPS integration (`FACT-GPP-072`); IPS не моделируется как replacement XÖHKS, а exact internal component/routing остается `Q-CUR-001`/`Q-ARCH-001` |
| 8. Reconciliation / account-credit information | После settlement участникам передается информация о credited funds (`FACT-GPP-024`) | Reporting/export capability присутствует (`CAP-ASIS-012`) | Contract 1.16 подтверждает `INVOICE_PAY_APUS` по параметризованному schedule и `DAY_PAYMENTS`/`DAY_PAYMENTS_FROM_BANK` в конце operational day, включая reconciliation bank-credit data (`FACT-GPP-069`) |

### 5.2. Архитектурный вывод

Cross-source evidence поддерживает логическое разделение GPP как минимум на payment acceptance/processing и downstream participant reporting/settlement concerns. Это не доказывает неизменность component topology. В 2015 source lifecycle описан как operational procedure, в 2023 source - как набор platform/module responsibilities.

Эта reconciliation закреплена как `INF-ARCH-004`; exact current orchestration остается ограничена `Q-ARCH-001`, `Q-OPS-001`, `Q-SETTLE-001` и `Q-CUR-001`.

## 6. Проверка преемственности XÖHKS / IPS

### XÖHKS

- 2015: XÖHKS является явно названным settlement mechanism следующего рабочего дня (`FACT-GPP-024`).
- 2023: XÖHKS сохраняется в architecture vocabulary через Loader Generators MT150/152 и LVPCSS (`FACT-GPP-015`, `IF-GPP-005/006`).
- Следствие: XÖHKS является устойчивым integration concern как минимум между source dates 2015 и 2023 (`INF-ARCH-005`).
- Ограничение: это не подтверждает current 2026 integration, не доказывает неизменность contract, settlement windows или orchestration.

### IPS / AÖS

- В extracted 2015 model Instant Payment System не зафиксирован.
- В 2023 появляется отдельный IPSClient (`FACT-GPP-015`, `IF-GPP-007`).
- Тот же functional source различает XÖHKS и AÖS scenarios в `SignedPain001`, а `IpsTransitAccountConsent` задает consent-authentication для TopUp flows (`FACT-GPP-064`). Поэтому исходный semantic вопрос `Q-IPS-001` закрыт на дату source.
- Текущий сайт CBA прямо описывает GPP как интегрированный с IPS и доступность через IPS более 1000 GPP-connected services (`FACT-GPP-072`).
- При этом evidence не доказывает, что внутренний current component все еще называется `IPSClient`, и не дает полной routing/orchestration map. Поэтому component-level currentness остается `Q-CUR-001`, а core/routing ownership - `Q-ARCH-001`.
- Cross-source interpretation закреплена как `INF-ARCH-014`: XÖHKS-oriented day-end settlement concern и real-time IPS/AÖS path сосуществуют; replacement relationship не предполагается.

## 7. Currentness / confidence view

### 7.1. Более сильный current evidence

- `FACT-GPP-003/004` - с 10.01.2024 собственные `gpp.az` и mobile app прекращены; integrated bank/non-bank PSP channels остаются путем доступа.
- `FACT-GPP-020` / `CAP-ASIS-001` - system-level payment processing через GPP подтверждено за 2025 год.
- `FACT-GPP-069/071` - integration contract и BDA 2025 подтверждают day-end reconciliation semantics, XÖHKS monitoring и settlement-state progression.
- `FACT-GPP-072` - текущая CBA IPS page дает сильный current-site signal GPP↔IPS integration, без переноса currentness на внутреннее имя `IPSClient`.

### 7.2. `current-at-source-date` 05.05.2023

- Named modules `CMP-GPP-002/003/005..011`, кроме уже superseded собственных web/mobile channel elements;
- AdminConsole roles/domains и configuration capabilities;
- XÖHKS и IPS integration responsibilities;
- семантика состояния платежа и конфигурации.

Эти элементы не повышаются до current 2026 только из-за того, что система продолжала работать в 2025 году.

### 7.3. `unverified` public context

- real-time participant information и end-of-day reconciliation (`FACT-GPP-006`);
- Closed Telecommunication Network + обмен сертификатами CBA (`FACT-GPP-007`);
- внешние источники идентификационных данных IAMAS/AVIS (`FACT-GPP-019`);
- централизованное хранение справочных/master data (`FACT-GPP-021`).

### 7.4. `historical` 2015

- механика ролей/доступа (`FACT-GPP-022/023`);
- точные временные окна операционного дня (`FACT-GPP-024`);
- реализация backup/archive (`FACT-GPP-025`);
- трехсайтовая DR/deployment topology (`FACT-GPP-026`);
- технологический стек (`FACT-GPP-027`);
- physical database model CF/APUS, tables/columns и документированные связи на дату source (`FACT-GPP-035..040`).

### 7.5. Ключевые current UNKNOWN

- физический component/topology основной обработки платежей и persistence - `Q-ARCH-001`;
- current status большинства modules 2023 - `Q-CUR-001`;
- exact clock boundaries/settlement execution ownership - `Q-OPS-001`;
- аутентификация/авторизация/SoD - `Q-SEC-001`;
- резервное копирование/retention/RPO - `Q-BCK-001`;
- топология DR, RTO/RPO и failover - `Q-DR-001`;
- соответствие historical CF/APUS physical model фактической production database state - `Q-DB-001`.

## 8. Historical leakage check

Проверка выполнена после cross-source reconciliation.

Scope:

- `02-as-is/components.yaml`;
- `02-as-is/capabilities.yaml`;
- `02-as-is/interfaces.yaml`;
- `02-as-is/domain-entities.yaml`;
- `02-as-is/diagrams/system-context.mmd` и `subsystems.mmd`;
- narrative current-state sections `system-context.md` и `architecture-analysis.md`.

Результат:

- Oracle/ExaData/WebLogic/Tomcat/Active MQ/TSM, DVD/tape mechanics и 2015 three-site DR topology не добавлены в current components/capabilities/interfaces;
- historical deployment topology находится только в отдельной `diagrams/historical-dr-topology-2015.mmd` и явно маркированных historical narrative/evidence sections;
- certificate + username/password mechanism не представлен как current authentication scheme;
- exact `00:00-24:00` и next-day windows не представлены как current operational schedule;
- current system-context diagram не содержит 2015 physical deployment nodes или technologies.

В текущей canonical state historical leakage не обнаружен.

## Интеграционный контракт 2025 и функциональная спецификация 2023

`SRC-INT-001` уточняет ряд interfaces, которые в functional specification 2023 были видимы только на уровне module relationship.

- `IF-GPP-008` больше не остается `unknown`: для service organization internet/mobile resources -> `WebPortalVC` подтвержден SOAP `GPPPaymentWS` и real-time operation set.
- HÖP -> XT interaction раскрывается как отдельный SOAP `ServiceCompanyWS` (`IF-GPP-009`), а завершение XT-initiated payment - как отдельный callback SOAP `SCVirtualCabinetWS` (`IF-GPP-010`). Это важное разделение направлений и responsibilities, которого component list 2023 не давал.
- Reconciliation payment information (`IF-GPP-011`) подтвержден как HTTP POST/XML с explicit retry/ack semantics. Его нельзя смешивать с `Loader Generators` file interfaces: новый source описывает другой transport contract и не связывает его с `CMP-GPP-009`.
- IAMAS/AVIS boundary теперь имеет достаточное evidence для system-level interface objects `IF-GPP-013`/`IF-GPP-014`, но protocol и responsible internal component остаются unknown.

Документ 2025 имеет более высокую temporal relevance именно для integration contract semantics, но не используется для автоматического объявления всех named modules 2023 текущими production components. WSDL/endpoint references также не считаются доказательством runtime availability.

## 9. Physical data model 2015 -> functional/integration semantics 2023/2025

`SRC-DB-001` добавляет detailed physical model, но cross-source reconciliation выполняется только на conceptual level. Ни одно совпадение имен или fields не повышает physical table currentness.

| Historical physical object | Более поздний concept/evidence | Reconciliation |
|---|---|---|
| `SERVICE_TYPES` | `DATA-GPP-004` / service-type configuration 2023 | Сильное conceptual соответствие service code/name, partial/pre-payment, fee/cancellation configuration; current table name/schema не подтверждены |
| `IDENTIFICATION_METHOD`, `SC_SUPPORTED_IDEN_MTD`, `IDEN_MTD_PREFIX` | `DATA-GPP-005` и IAMAS/AVIS/SC identification configuration 2023/2025 | Поддерживается continuity identification concept; physical tables 2015 не считаются current |
| `PAYMENT_DATA`, `PAYMENT_EXT` | `DATA-GPP-009` Payment и payment contract semantics 2023/2025 | Historical physical storage хорошо согласуется с domain fields, но current persistence layout неизвестен |
| `STATEMENT_EXT` + statement fields в `PAYMENT_DATA` | `DATA-GPP-008` InvoiceStatement и invoice/debt SOAP types 2023/2025 | Поддерживается mapping physical projection -> domain concept без отождествления entity/table |
| `AMG_MSG.MSG_TYPE` examples `INVOICE_PAY_APUS`, `DAY_PAYMENTS` | HTTP/XML reconciliation contract 2025 (`IF-GPP-011`) | Имена сообщений показывают semantic continuity между 2015 и 2025, но не доказывают, что `AMG_MSG` или его schema продолжают использоваться |
| `PAYMENT_DATA_AGGR_DAY` | reporting/payment aggregation concern | Historical reporting projection подтверждена; current reporting storage/ETL не описаны более поздними sources |

Отдельный mapping сохранен в `normalized/data/physical-domain-mapping.yaml`. Logical relationships сохранены в `normalized/data/logical-foreign-keys.yaml` и отделены от physical constraints. Current database status остается `Q-DB-001`; inconsistent table/enum definitions остаются `CONTR-DB-001..004`.



## 10. BDA: руководство пользователя 2025 -> техническая REST/API/DB-спецификация 2025

`SRC-BDA-002` обработан после `SRC-BDA-001`, поэтому технические сведения не использовались для ретроспективного расширения фактов руководства пользователя. Они наложены отдельным слоем на уже установленную функциональную модель.

| Пользовательский процесс | Возможность | REST/API | Физическая привязка | Результат |
|---|---|---|---|---|
| Определения процессов | `CAP-ASIS-014` | `periods`, `Period`, `ParameterSet` | `BDA.PROCESS`, `BDA.PARAMETERS` | Трассируется; `days` не имеет документированной колонки БД |
| Шаблоны | `CAP-ASIS-015` | `templates`, `Template`, `ParameterSet` | `BDA.TEMPLATE`, `TEMPLATE_PROCESS`, `PROCESS`, `PARAMETERS` | Трассируется; путь из раздела 2.1 конфликтует с обзором |
| Операционный день | `CAP-ASIS-016` | `operation-days`, `OperationDay` | `BDA.OPERATION_DAY` | Трассируется |
| Процесс дня | `CAP-ASIS-017` | `operation-period`, `OperationPeriod`, `ParameterSet` | `BDA.OPERATION_PROCESS`, `PARAMETERS` | Трассируется; механизм запуска команд остается `UNKNOWN` |
| Архив | `CAP-ASIS-018` | `archives`, `Archive`, `ArchiveOperationPeriod` | `BDA_ARCHIVE.OPERATION_DAY`, `OPERATION_PROCESS` | Трассируется частично; хранение XML остается `UNKNOWN` |
| Мониторинг | `CAP-ASIS-019` | `monitoring-menus` + 6 моделей | Не документирован | Пробел документации без искусственной привязки к таблице |

Сквозная сверка также поддерживает семантические соответствия состояний `Cari -> CURRENT`, `Bağlanmış -> CLOSED`, `İcradadır -> IN_PROGRESS`, `Tamamlanıb -> DONE` как `INFERENCE`, а не как буквальную таблицу соответствий из источника.

Физическая модель BDA 2025 является отдельным более поздним техническим подтверждением и **не** заменяет автоматически историческую модель `CF/APUS` 2015 года: это разные области. Нельзя делать вывод, что таблицы BDA представляют всю текущую модель данных GPP.

Аутентификация BDA подтверждает локальный контур JWT/authority и хранение паролей в виде bcrypt-хэшей, но не закрывает общий `Q-SEC-001` и не доказывает общесистемную модель идентификации.


## 11. Повторный question-driven pass по всем источникам

Повторная проверка была выполнена не только по новым keywords, а по каждому существенному открытому question cluster. Результат показывает, что часть прежних UNKNOWN была вызвана не отсутствием данных вообще, а тем, что semantics находилась в reviewer-enriched configuration tables и не была связана с другими source layers.

Прямым source evidence закрыты на дату specification: `Q-ID-001` (централизованные identification types сосуществуют с organization-level post-identification properties), `Q-PAY-001` (берется меньшее из invoice maximum и organization `MaxPaymentAmount`), `Q-IPS-001` (consent/TopUp и signed pain.001 scenarios), `Q-PSP-001` (`supported_psp_code_list` как delegation allow-list, не acquiring relationship).

Существенно сужены, но не закрыты current questions по `VirtualBranchID`, `IncludeServiceCode`, fee/VAT reporting, `Whole_payment`, discount/penalty, BIN administration, operational-day timing и BDA process catalog. Для новых channel-specific maxima из integration contract 1.13+ введен `Q-PAY-004`.

Отдельно повторный pass **не нашел** достаточного evidence для current core deployment/persistence (`Q-ARCH-001`, `Q-DB-001`), current auth/SoD (`Q-SEC-001`), backup/DR (`Q-BCK-001`, `Q-DR-001`), debt-notification operation binding (`Q-INT-001`), authoritative WSDL/runtime contract (`Q-INT-002`), BDA-to-AdminConsole deployment mapping (`Q-BDA-001`) и complete MX/process-command catalog (`Q-BDA-002`). Эти gaps сохраняются как explicit UNKNOWN, а не заполняются extrapolation из 2015/2023 sources.

В BDA дополнительно зафиксирована `CONTR-BDA-006`: change history заявляет MX processes, но enumerated process list их не содержит, при том что `APPLICATION_TYPE` отдельно ссылается на `mx`. Это является source inconsistency, а не основанием реконструировать отсутствующие process names.
