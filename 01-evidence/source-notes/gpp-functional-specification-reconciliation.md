# Сверка семейства источников функциональной спецификации GPP

## Источники

- `SRC-FS-001` - DOCX с filename hint `v005`.
- `SRC-FS-002` - PDF с filename hint `v006`.

Оба артефакта рассматриваются как одно source family до разрешения `Q-SRC-001`.

## Внутренняя metadata и имена файлов

Оба файла внутри содержат одинаковую идентификацию документа:

- название: `HÖKUMƏT ÖDƏNİŞ PORTALI - SİSTEMİN FUNKSİONAL TƏSVİRİ`;
- ID документа: `GPP_System Functional Specification_v001`;
- version history: `0.01`;
- date: `05.05.2023`;
- примечание в истории: initial version.

Поэтому filename hints `v005` и `v006` сами по себе не доказывают последовательность или authority.

DOCX содержит reviewer comments и следы рабочего редактирования после исходной даты документа. PDF container metadata показывает генерацию в 2026 году, однако container timestamp не доказывает business/version precedence и не заменяет внутреннюю version history.

## Фактическая связь между источниками

Relationship классифицирован как **ambiguous overlap with unresolved precedence**. После stakeholder clarification известно, что DOCX `v005` был передан позже и является более полным, но это не считается доказательством version authority. Рабочая гипотеза `ASM-SRC-001` допускает, что это независимые рабочие документы, а не последовательные версии.

Общая часть достаточно велика для единой модели:

- назначение GPP;
- перечень infrastructure modules;
- типы participant organizations;
- базовый AdminConsole workflow;
- поля конфигурации организаций-участников;
- конфигурация типов платежей/услуг;
- identification type configuration;
- конфигурация тарифов service fee.

Shared content не создает параллельные facts на каждый файл.

## Значимые различия

| Область | DOCX / filename v005 | PDF / filename v006 | Обработка |
|---|---|---|---|
| Organization administration | Подробные отдельные sections для KXM, budget organization, bank, card processing center, service center, branches и service center departments | Сохраняет создание пяти top-level organization types, затем консолидирует fields в общую таблицу с applicability by organization type | Общая capability одна; branch/subdivision administration остается source-specific evidence из DOCX |
| Organization fields | Fields повторяются по organization-specific sections | Fields сведены в `3.1.2 Təşkilatın yaradılmasında istifadə olunan sahələrin təsviri` | PDF удобнее как data dictionary, но не получает authority автоматически |
| Service/payment types | Detailed UI CRUD для service codes и groups + field table | Section 3.2 сокращен до field/data table | Capability и data semantics консолидируются; UI procedure сохраняется как DOCX-specific detail |
| Identification types | Detailed UI creation/editing, включая composite identification | Section 3.3 сокращен до field/data table | Одна capability; UI workflow остается source-specific |
| Service fee tariffs | Detailed UI workflow | Section 3.4 сокращен до field table | Одна capability |
| New/changed config fields | `NotificationRetryRestricted` и `swiftIbanValidation` не найдены при reconciliation | Эти fields присутствуют в consolidated organization table | Сохраняются как PDF-specific delta `EV-FS-016`; current/authoritative semantics не повышаются |
| Editorial state | 35 reviewer comments в DOCX, в том числе предложения по реструктуризации fields/UI | Reviewer comments не представлены тем же способом, но в body сохранены red editorial notes и вопросы типа `Hazırda istifadə olunurmu?` | Ни один artifact не считается final/authoritative только по форме |

## Визуальный просмотр PDF

Выполнен targeted visual review screenshot-heavy PDF:

- page 7, Figure 2 - основной экран AdminConsole подтверждает top-level domains: reports, payment audit, management tools, security, queries, monitoring;
- page 8, Figure 3 - Management tools показывает participant management, BIN data, service fee tariffs, payment/service types and groups, registered payers, permissions, dual authorization, identification type management;
- pages 9-33 - screenshots подтверждают UI patterns создания top-level participant organizations;
- pages 33-46 - основной смысл находится в consolidated tables, а не screenshots;
- pages 46-51 - tables 3.2-3.4 содержат configuration semantics и unresolved editorial notes.

Массовый OCR screenshots не выполнялся.

## Проверка чувствительных данных

Raw sources содержат internal operational URL/IP, UI usernames/имена, example bank/account data и screenshots.

Canonical model сохраняет только архитектурный смысл. Конкретные internal addresses, account examples и персональные значения не копируются, если они не нужны для evidence. Явных passwords/tokens/private keys в canonical extraction не переносилось.

## Вывод для последующих итераций по источникам

Текущий family дает каркас, но не доказывает:

- current production status большинства modules после 2023;
- component(s) основной обработки платежей;
- persistence/database topology;
- deployment topology;
- authoritative контракты API/services;
- authoritative precedence между v005/v006.

Следующие sources должны присоединяться к текущим `CMP-*`, `IF-*`, `CAP-ASIS-*` и открытым `Q-*`, а не создавать отдельную модель документа.

## Уточнения stakeholder после /questions

- DOCX с filename hint `v005` был передан позже PDF и воспринимается как более полный; это delivery/completeness context, а не version precedence.
- `XÖHK` подтвержден как опечатка для `XÖHKS`; canonical model использует только `XÖHKS`.
- `IAMAS` и `AVIS` до обратного evidence считаются external systems GPP; точные interface/component ownership остаются неизвестными.
- Reviewer/editorial comments считаются важными leads. Технически значимые комментарии сгруппированы в `Q-ID-001`, `Q-CFG-001`, `Q-PAY-001`, `Q-SETTLE-001`, `Q-IPS-001`, `Q-FEE-001`, `Q-PAY-002`, `Q-PAY-003`, `Q-PSP-001`, `Q-BIN-001`. Чисто UI-предложения (например, popup/layout) не превращаются автоматически в findings или architecture proposals.


## Повторный question-driven pass - 17.08.2026

Повторная сверка reviewer-enriched configuration tables закрыла на дату source четыре semantic gaps: centralized identification types vs organization properties (`Q-ID-001`), base `MaxPaymentAmount` precedence (`Q-PAY-001`), `IpsTransitAccountConsent`/`SignedPain001` semantics (`Q-IPS-001`) и `supported_psp_code_list` (`Q-PSP-001`).

Дополнительно подтверждены intended role `VirtualBranchID`, влияние `IncludeServiceCode`, base semantics `IgnoreServiceFee`/`IgnoreVatCalculationOnReport`, `Whole_payment` и BIN field format. Эти элементы не повышаются до current 2026 автоматически: reviewer comments о желаемом изменении/удалении остаются editorial, а runtime currentness отслеживается отдельными questions.

Для discount/penalty source по-прежнему сам содержит вопрос об актуальном использовании без ответа. Это сохраняется как explicit unknown, а не как неполнота extraction.
