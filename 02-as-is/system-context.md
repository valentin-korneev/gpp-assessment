# System context GPP - первичная AS-IS модель

## Evidence basis

Контекст построен по published public baseline, повторно проверенному public-source family `gpp-public-bootstrap` и source family `gpp-functional-specification-2023`.

Функциональная спецификация описывает состояние на 05.05.2023. Поэтому component-level technical elements, кроме отдельно подтвержденных более поздними sources, имеют currentness `current-at-source-date` или `unverified`. Официальный обзор цифровых платежей CBA за 2025 год подтверждает, что GPP как system-level payment-processing platform продолжал фактически обрабатывать платежи в 2025 году, но не подтверждает currentness конкретных modules из спецификации 2023 года.

## Назначение

GPP является единой платежной платформой для приема, обработки и расчетов по платежам за услуги бюджетных и небюджетных организаций.

## Boundary

Внутри рабочей boundary GPP находятся именованные modules из functional specification:

- `WebPortal` - historical/superseded direct web channel;
- `WebPortalVC`;
- `PacPmtProc`;
- mobile applications - historical/superseded direct mobile channel;
- `MobilApi` - current runtime status unverified;
- `PaymentService`;
- `TellerWP (BankPayment)`;
- `AdminConsole`;
- `Loader Generators`;
- `LVPCSS`;
- `IPSClient`.

Спецификация не называет отдельный central payment processing/persistence component, хотя system-level responsibility приема и обработки платежей подтверждена. Это сохраняется как `Q-ARCH-001`, а не заполняется предположительным component name.

## Основные external actors и systems

### Плательщики

Физические и юридические лица, оплачивающие задолженность или выполняющие advance payments через доступные payment channels.

### Service / beneficiary organizations

- KXM - негосударственные service organizations, в пользу которых собираются платежи;
- бюджетные организации;
- service centers, обслуживающие бюджетные организации.

### Payment service providers

- банки;
- почта;
- card processing centers;
- e-money / иные payment organizations.

Эти участники используют свои electronic channels или операторские workplaces для приема платежей и взаимодействуют с GPP infrastructure.

### Administrative users

- Central Bank system administrator;
- administrator participant organization;
- bank security administrator;
- bank controller;
- KXM administrator.

### Operators

Операторы банков, почты и других финансовых организаций используют `TellerWP (BankPayment)` для приема GPP payments.

### External payment / settlement systems

- `XÖHKS` - взаимодействие через `LVPCSS` и MT150/152 files от `Loader Generators`; вариант `XÖHK` в source нормализован как опечатка по stakeholder confirmation;
- `Instant Payment System` - взаимодействие через `IPSClient`;
- payment authorization centers - взаимодействие через `PacPmtProc`.

### External identification systems

- `IAMAS / İAMAS` - CBA раскрывает как межведомственную автоматизированную информационно-поисковую систему Министерства внутренних дел;
- `AVIS / AVİS` - CBA раскрывает как автоматизированную налоговую информационную систему и связывает с налоговым ведомством.

Functional specification использует IAMAS/AVIS как identification `Source` и описывает получение от них данных. Повторный public-source pass подтвердил, что это внешние по отношению к GPP государственные информационные системы (`FACT-GPP-019`), поэтому `ASM-EXT-001` superseded. При этом ответственный internal GPP component, protocol и interface contract по-прежнему не установлены, поэтому отдельные `IF-*` для них преждевременно не создаются. Текущая организационная принадлежность систем отдельно не подтверждена.

## Temporal reconciliation каналов

Functional specification 2023 описывает `WebPortal` и mobile applications как infrastructure elements. Более позднее official CBA notice подтверждает прекращение `gpp.az` и mobile application с 10.01.2024. Официальный CBA review за 2025 год при этом фиксирует 114,4 млн платежей через GPP на сумму 8,6 млрд манатов.

Поэтому:

- `WebPortal` и mobile applications сохраняются в historical architecture;
- они не отображаются как действующие current AS-IS channels;
- прекращение first-party web/mobile channels не означает прекращение GPP как payment-processing platform: system-level эксплуатация подтверждена за 2025 год;
- текущий status `MobilApi` не выводится автоматически ни из закрытия client application, ни из system-level activity GPP;
- действующий user access после закрытия first-party channels подтвержден через integrated bank/non-bank PSP internet/mobile channels.

## Administrative context

Targeted visual review AdminConsole показывает не только organization setup, но и более широкий administrative perimeter:

- reports;
- payment audit;
- management tools;
- security;
- queries;
- monitoring.

Management tools дополнительно показывают participant organization management, BIN data, service fee tariff management, payment/service types and groups, registered payers, permissions, dual authorization и identification type management.

Эти функции рассматриваются как current-at-source-date 05.05.2023, пока последующие sources не подтвердят current production state.


## Temporal notation диаграммы

`diagrams/system-context.mmd` теперь явно различает:

- elements, подтвержденные только состоянием `05.05.2023` (`current-at-source-date`);
- superseded direct channels по более позднему evidence;
- элементы с отдельно неизвестным current status, например `MobilApi`;
- assumptions/unknown boundaries;
- актуально подтвержденный внешний digital access через integrated PSP channels.

Такое отображение предотвращает чтение функциональной спецификации 2023 года как автоматически текущей production architecture.

## Architecture interpretation

`INF-ARCH-001` рассматривает AdminConsole как логический centralized control/configuration plane. `INF-ARCH-002` фиксирует поддержанный configuration-driven характер значимой части system behavior. Оба объекта являются inference, а не FACT и не finding.
