# System context GPP - первичная AS-IS модель

## Evidence basis

Контекст построен по published public baseline и source family `gpp-functional-specification-2023`.

Функциональная спецификация описывает состояние на 05.05.2023. Поэтому все technical elements, кроме отдельно подтвержденных более поздними sources, имеют currentness `current-at-source-date` или `unverified`.

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

### External identification systems - working boundary

- `IAMAS`;
- `AVIS`.

Спецификация использует IAMAS/AVIS как identification `Source` и описывает получение от них данных. По согласованной рабочей классификации они считаются внешними системами до получения обратного evidence (`ASM-EXT-001`). Ответственный internal GPP component и interface contract пока не установлены, поэтому отдельные `IF-*` для них преждевременно не создаются.

## Temporal reconciliation каналов

Functional specification 2023 описывает `WebPortal` и mobile applications как infrastructure elements. Более позднее official CBA notice подтверждает прекращение `gpp.az` и mobile application с 10.01.2024.

Поэтому:

- `WebPortal` и mobile applications сохраняются в historical architecture;
- они не отображаются как действующие current AS-IS channels;
- текущий status `MobilApi` не выводится автоматически из закрытия client application;
- действующий user access подтвержден через integrated bank/non-bank PSP internet/mobile channels.

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
