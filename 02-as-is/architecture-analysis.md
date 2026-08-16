# Architecture analysis - первичный каркас подсистем GPP

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
- payment/service types and groups;
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

- `CMP-GPP-010` LVPCSS - XÖHKS information exchange;
- `CMP-GPP-011` IPSClient - Instant Payment System information exchange.

## 8. Core payment processing - неизвестная внутренняя область

System-level source подтверждает прием, обработку и settlement payments, а field tables содержат semantics account/invoice/statement/payment state.

Однако named module list не идентифицирует component(s), отвечающие за:

- central payment orchestration;
- account/invoice state;
- transaction persistence;
- core database;
- settlement state machine.

До появления evidence эта область не создается как `CMP-*`; она отслеживается через `Q-ARCH-001`.

## 9. Внешние identification systems - рабочая классификация

`IAMAS` и `AVIS` рассматриваются как external systems (`ASM-EXT-001`). Functional specification использует их как identification sources и описывает получение данных до последующего debt lookup, но не указывает конкретный GPP component, protocol или authoritative contract. Поэтому system-context nodes добавлены, а interface objects пока не создаются.

## Source-family caveat

Логическая карта не выбирает ни PDF v006, ни DOCX v005 как authoritative по filename или порядку передачи. Stakeholder сообщает, что DOCX v005 был передан позже и является более полным, однако допускается независимое происхождение документов (`ASM-SRC-001`). Shared facts строятся на overlap; source-specific delta сохраняет provenance до разрешения `Q-SRC-001`.


## 10. Первичная доменная модель конфигурации

`02-as-is/domain-entities.yaml` фиксирует conceptual/domain layer отдельно от будущей physical database model.

Подтвержденный каркас включает:

- `DATA-GPP-001` ParticipantOrganization;
- `DATA-GPP-002` OrganizationUnit / branch;
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

### INF-ARCH-002 - configuration-driven behavior

Organization properties и service-type parameters управляют значимой частью integration, routing, limits, payment state и fee/settlement behavior. Поэтому текущая модель поддерживает inference о configuration-driven характере существенной части поведения GPP. Это не finding и не утверждение, что вся business logic реализована конфигурацией.
