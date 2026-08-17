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
