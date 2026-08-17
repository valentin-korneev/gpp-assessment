# Системный контекст GPP - первичная AS-IS модель

## Основание evidence

Контекст построен по published public baseline, повторно проверенному public-source family `gpp-public-bootstrap`, source family `gpp-functional-specification-2023` и историческому нормативно-operational source `SRC-OPS-001` от 13.08.2015.

Дополнительно учтено руководство пользователя BDA `SRC-BDA-001` от 10.03.2025.

`SRC-OPS-001` используется для ролей, access/administration rules, operational day semantics, backup/DR expectations и historical deployment/technology context. Он не считается доказательством current infrastructure или current technology stack.

Функциональная спецификация описывает состояние на 05.05.2023. Поэтому component-level technical elements, кроме отдельно подтвержденных более поздними sources, имеют currentness `current-at-source-date` или `unverified`. Официальный обзор цифровых платежей CBA за 2025 год подтверждает, что GPP как system-level payment-processing platform продолжал фактически обрабатывать платежи в 2025 году, но не подтверждает currentness конкретных modules из спецификации 2023 года.

## Назначение

GPP является единой платежной платформой для приема, обработки и расчетов по платежам за услуги бюджетных и небюджетных организаций.

## Граница системы

Внутри рабочей boundary GPP находятся именованные modules из functional specification:

- `WebPortal` - исторический/superseded собственный web-канал;
- `WebPortalVC`;
- `PacPmtProc`;
- mobile applications - исторический/superseded собственный мобильный канал;
- `MobilApi` - текущий runtime status не подтвержден;
- `PaymentService`;
- `TellerWP (BankPayment)`;
- `AdminConsole`;
- `Loader Generators`;
- `LVPCSS`;
- `IPSClient`;
- `BDA / Əməliyyat gününün idarə olunması modulu` - функциональный модуль подтвержден руководством пользователя от 10.03.2025; связь с `AdminConsole` и техническая граница развертывания остаются в `Q-BDA-001`.

Спецификация не называет отдельный central payment processing/persistence component, хотя system-level responsibility приема и обработки платежей подтверждена. Это сохраняется как `Q-ARCH-001`, а не заполняется предположительным component name.

## Основные внешние участники и системы

### Плательщики

Физические и юридические лица, оплачивающие задолженность или выполняющие advance payments через доступные payment channels.

### Сервисные организации / получатели платежей

- KXM - негосударственные service organizations, в пользу которых собираются платежи;
- бюджетные организации;
- service centers, обслуживающие бюджетные организации.

### Поставщики платежных услуг

- банки;
- почта;
- card processing centers;
- e-money / иные payment organizations.

Эти участники используют свои electronic channels или операторские workplaces для приема платежей и взаимодействуют с GPP infrastructure.

### Административные пользователи

- системный администратор Центрального банка;
- administrator participant organization;
- bank security administrator;
- bank controller;
- KXM administrator.

### Операторы

Операторы банков, почты и других финансовых организаций используют `TellerWP (BankPayment)` для приема GPP payments.

### Внешние платежные / расчетные системы

- `XÖHKS` - взаимодействие через `LVPCSS` и MT150/152 files от `Loader Generators`; вариант `XÖHK` в source нормализован как опечатка по stakeholder confirmation;
- `Instant Payment System` - взаимодействие через `IPSClient`;
- payment authorization centers - взаимодействие через `PacPmtProc`.

### Внешние системы идентификации

- `IAMAS / İAMAS` - CBA раскрывает как межведомственную автоматизированную информационно-поисковую систему Министерства внутренних дел;
- `AVIS / AVİS` - CBA раскрывает как автоматизированную налоговую информационную систему и связывает с налоговым ведомством.

Functional specification использует IAMAS/AVIS как identification `Source`, а integration contract 18.08.2025 прямо описывает real-time web-service calls HÖP -> IAMAS/AVIS для payer identification (`FACT-GPP-033`). Поэтому созданы system-boundary interfaces `IF-GPP-013` и `IF-GPP-014` с `interface_type: unknown`. Ответственный internal GPP component, точный protocol, operation/payload contract и endpoint по-прежнему не установлены. Текущая организационная принадлежность систем отдельно не подтверждена.

## Temporal reconciliation каналов

Functional specification 2023 описывает `WebPortal` и mobile applications как infrastructure elements. Более позднее official CBA notice подтверждает прекращение `gpp.az` и mobile application с 10.01.2024. Официальный CBA review за 2025 год при этом фиксирует 114,4 млн платежей через GPP на сумму 8,6 млрд манатов.

Поэтому:

- `WebPortal` и mobile applications сохраняются в historical architecture;
- они не отображаются как действующие current AS-IS channels;
- прекращение first-party web/mobile channels не означает прекращение GPP как payment-processing platform: system-level эксплуатация подтверждена за 2025 год;
- текущий status `MobilApi` не выводится автоматически ни из закрытия client application, ни из system-level activity GPP;
- действующий user access после закрытия first-party channels подтвержден через integrated bank/non-bank PSP internet/mobile channels.

## Административный контекст

Targeted visual review AdminConsole показывает не только organization setup, но и более широкий administrative perimeter:

- reports;
- payment audit;
- management tools;
- security;
- queries;
- monitoring.

Management tools дополнительно показывают participant organization management, BIN data, service fee tariff management, payment/service types and groups, registered payers, permissions, dual authorization и identification type management.

Эти функции рассматриваются как current-at-source-date 05.05.2023, пока последующие sources не подтвердят current production state.

### BDA - администрирование операционного дня, 2025

`SRC-BDA-001` подтверждает отдельную административную функциональную область управления операционным днем на 10.03.2025. Она охватывает определения процессов, шаблоны, создание, закрытие и архивирование операционного дня, запуск процессов дня с отображением исполнителя, времени начала и окончания и статуса, архив, а также сценарии мониторинга XÖHKS, платежей и файлов MT/неотправленных файлов.

Это более позднее подтверждение области управления операционным днем, но не доказательство того, что BDA является отдельным компонентом развертывания или частью `AdminConsole`. Техническая граница, серверные сервисы и REST-контракт пока не моделируются (`Q-BDA-001`). Точная граница суток и расчетные окна остаются в `Q-OPS-001`.


## Исторический нормативно-операционный контекст 2015

Правила, утвержденные Правлением CBA 13.08.2015, добавляют более ранний operational baseline, который не следует смешивать с current AS-IS:

- роли включали system administrator, security administrator, certificate services administrator, participant administrator и operator;
- доступ к индивидуально ограниченному набору функций строился на электронном сертификате с последующим username/password;
- system administrator отвечал в том числе за software/platform service, Oracle backup/replication, system component management, operational day и participant activity;
- operational day был определен как 00:00-24:00, а reporting/settlement действия продолжались на следующий рабочий день по фиксированным временным окнам;
- backup/archive и disaster recovery были формализованы отдельными приложениями к Правилам.

Эти сведения имеют `currentness: historical`. В частности, certificate/password mechanism, конкретные роли, расписание, backup technology и deployment topology 2015 года не переносятся в current state без нового evidence. Current gaps вынесены в `Q-OPS-001`, `Q-SEC-001`, `Q-BCK-001` и `Q-DR-001`.

## Историческая DR topology 2015

Targeted visual review диаграммы на странице 13 `SRC-OPS-001` подтверждает three-site model: Main и Alternative sites находятся в основном центре и используют общий disk array; Backup site находится в отдельном резервном центре и имеет собственный disk array; на diagram показана Oracle replication между основным и резервным контурами. Текст recovery plan дополняет diagram cluster failover Main -> Alternative и переходом на Backup в one-site mode, если отказ Main+Alternative не устраняется в течение одного часа.

Canonical reconstruction сохранена в `diagrams/historical-dr-topology-2015.mmd`. Она представляет только historical 2015 deployment evidence и не расширяет current system-context diagram новыми current components.

## Временные обозначения диаграммы

`diagrams/system-context.mmd` теперь явно различает:

- elements, подтвержденные только состоянием `05.05.2023` (`current-at-source-date`);
- superseded direct channels по более позднему evidence;
- элементы с отдельно неизвестным current status, например `MobilApi`;
- assumptions/unknown boundaries;
- актуально подтвержденный внешний digital access через integrated PSP channels.

Такое отображение предотвращает чтение функциональной спецификации 2023 года как автоматически текущей production architecture.

## Архитектурная интерпретация

`INF-ARCH-001` рассматривает AdminConsole как логический centralized control/configuration plane. `INF-ARCH-002` фиксирует поддержанный configuration-driven характер значимой части system behavior. Оба объекта являются inference, а не FACT и не finding.
