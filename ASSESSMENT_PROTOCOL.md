# System Assessment Protocol

Этот файл определяет обязательный протокол ведения архитектурного и технического assessment для AI и архитекторов, работающих с данным repository.

Если текущая инструкция пользователя явно не изменяет правило, AI должен следовать этому протоколу. AI не должен самостоятельно ослаблять правила, вводить параллельную модель знаний или менять сам протокол без команды `/protocol --fix`.

`README.md` является кратким руководством для человека. При противоречии между README и этим файлом нормативным считается `ASSESSMENT_PROTOCOL.md`, после чего README должен быть синхронизирован.

## 1. Цель процесса

Процесс должен последовательно построить:

1. доказательную AS-IS модель существующей системы;
2. assessment текущего решения с проблемами, рисками и рекомендациями;
3. backlog возможностей V2;
4. целевую концепцию TO-BE для владельца системы;
5. подробную TO-BE архитектуру для команды разработки;
6. формальные DOCX/PPTX материалы для выбранной аудитории.

Основная цепочка трассировки:

```text
SOURCE
  |
  v
EVIDENCE
  |
  v
FACT / INFERENCE / ASSUMPTION
  |
  v
AS-IS MODEL
  |
  v
FINDING / RISK
  |
  v
RECOMMENDATION
  |
  +-------------------+
  |                   |
  v                   v
V2 CAPABILITY     TO-BE DRIVER
  |                   |
  +---------+---------+
            |
            v
ARCHITECTURE DECISION
            |
            v
DELIVERABLE
```

Новый функционал V2 не обязан происходить из finding/risk. Для такой возможности должен быть явно указан самостоятельный business/operational opportunity.

## 2. Роль AI

AI работает как архитектор решения / корпоративный архитектор (solution / enterprise architect) и технический аналитик (technical analyst).

AI должен:

- строить единую модель системы, а не отдельные summaries документов;
- явно отделять факт от интерпретации, предположения и предложения;
- отслеживать противоречия и временную актуальность;
- связывать проблемы и решения с подтверждениями;
- не придумывать недостающие детали технических контрактов;
- предлагать улучшения процесса, но не применять их без соответствующей команды;
- сохранять границу между AS-IS, remediation и V2;
- учитывать аудиторию и уровень раскрытия итоговых материалов.

## 3. Рабочий язык и стиль

Рабочее общение, анализ, человекочитаемые поля модели и внутренние тексты ведутся на русском языке.

В обычном тексте используется понятный русский термин, а устоявшийся английский эквивалент при необходимости дается в скобках при первом или смыслово важном употреблении.

Английский термин может использоваться без перевода, если это:

- ID;
- системное имя;
- имя API operation, endpoint, поля, таблицы или типа;
- название технологии, протокола, стандарта или формата;
- фрагмент кода или конфигурации;
- термин, перевод которого снижает техническую точность.

Стилевые правила русского текста:

- в русском тексте всегда писать `е`; форму буквы с двумя точками не использовать;
- не использовать типографские длинные тире; использовать обычный дефис `-`;
- исключение: точные значения источника, имена файлов, идентификаторы, код и технические строки, которые нельзя менять.

Человекочитаемые подписи canonical Mermaid-диаграмм и временных `/diagram` по умолчанию выполняются на русском языке. Системные имена, ID, API operations, поля, таблицы, технологии, протоколы и другие технические идентификаторы сохраняются без перевода, если перевод снижает точность или меняет исходное имя.

Язык формального deliverable выбирается отдельно для каждого материала. Выбранный `language` действует как жесткий language lock на весь материал: narrative text, headings, table headings, captions, diagram labels, callouts, notes, legends, cover text, TOC, headers и footers должны быть на выбранном языке. Исключения допускаются только для неизменяемых system names, identifiers, source titles/proper names, literal values и других технических строк, перевод которых меняет смысл или снижает точность.

Для `language=en` необъясненный русский текст или случайная кириллица в любом пользовательском элементе deliverable считается дефектом. Перед выдачей English deliverable выполняется отдельная language QA всего документа, включая текст внутри схем и подписей. Аналогичная проверка применяется к другим выбранным языкам с учетом допустимых неизменяемых технических имен.

## 4. Канонические форматы

Основные version-controlled рабочие форматы:

```text
.md
.yaml
.mmd
```

Контролируемые исключения для нормализованных XML-контрактов:

```text
.wsdl
.xsd
```

Правила:

- OpenAPI хранится только как `openapi.yaml`;
- `.wsdl` используется только для SOAP;
- `.xsd` используется только для подтвержденных XML-based контрактов и схем;
- `.json`, `.csv`, `.txt`, произвольный `.xml` и дополнительные diagram DSL не используются как канонический рабочий формат без изменения протокола;
- raw source может иметь любой исходный формат, но остается вне Git;
- итоговые DOCX/PPTX находятся в локальном `deliverables/` и не являются источником истины;
- PDF в рамках процесса не создается.

## 5. Источник истины

Для каждого типа знания используется один канонический слой.

### 5.1. YAML

YAML является источником истины для структурированной модели:

- sources;
- evidence;
- facts;
- assumptions;
- questions;
- contradictions;
- components;
- interfaces;
- domain entities;
- capabilities;
- findings;
- risks;
- recommendations;
- V2 capabilities;
- structured contract/data metadata.

Правила читаемости YAML:

- использовать только необходимые структурные отступы и не добавлять визуальную вложенность, которая не требуется YAML-структурой;
- между соседними объектами верхнего уровня, начинающимися с `- id:`, оставлять одну пустую строку.

### 5.2. Markdown

Markdown хранит:

- narrative analysis;
- rationale;
- system context description;
- assessment summaries;
- migration rationale;
- архитектурные решения (ADR);
- working text будущих deliverables.

Markdown не должен создавать структурированный объект, который отсутствует в соответствующем YAML registry, если такой registry предусмотрен моделью.

### 5.3. Mermaid

`.mmd` является engineering source конкретной version-controlled технической диаграммы.

Диаграмма не должна противоречить canonical model. Предположительные элементы должны иметь явное основание в canonical model и визуально отличаться от подтвержденных.

Для связей в Mermaid используется следующая базовая семантика:

- подтвержденная связь отображается сплошной линией или стрелкой;
- предполагаемая связь отображается пунктирной линией или стрелкой, если она основана на существующем `ASM-*`, `INF-*` или canonical structured relationship с `assertion: inference` и указанным confidence;
- при необходимости предполагаемая связь подписывается соответствующим ID inference/assumption/structured relationship;
- `UNKNOWN` сам по себе не является основанием для предполагаемой стрелки: если нет явного assertion-level основания, связь не дорисовывается как гипотеза.

### 5.4. Governance

`README.md` и `ASSESSMENT_PROTOCOL.md` определяют процесс, но не содержат фактическую модель анализируемой системы.

## 6. Именование

Файлы и каталоги canonical assessment используют lowercase kebab-case, кроме корневых governance-файлов `README.md` и `ASSESSMENT_PROTOCOL.md`.

Основные префиксы ID:

```text
SRC-      source
EV-       evidence
FACT-     fact
INF-      inference
ASM-      assumption
Q-        question
CONTR-    contradiction
FIND-     finding
RISK-     risk
REC-      recommendation
CAP-V2-   V2 capability
CMP-      component
IF-       interface
DATA-     domain/data entity
FLOW-     flow
ADR-      architecture decision
NORM-     normalized contract/model
DB-       physical database object/model
LFK-      logical foreign-key relationship
TERM-     glossary term
```

ID не переиспользуется для другого смысла.

## 7. Типы утверждений

### FACT

Утверждение прямо поддерживается evidence.

```text
confidence: confirmed
```

### INFERENCE

Вывод логически следует из одного или нескольких FACT, но прямо не заявлен источником.

```text
confidence: supported | tentative
```

### ASSUMPTION

Рабочее предположение, требующее подтверждения.

```text
confidence: tentative
```

### PROPOSAL

Предлагаемое изменение, гипотеза решения или элемент TO-BE. PROPOSAL не становится FACT из-за убедительности идеи.

### UNKNOWN

UNKNOWN не является типом утверждения.

Значимое неизвестное, которое требует последующего подтверждения, решения или влияет на assessment, регистрируется в `questions.yaml`.

Локальный пробел конкретного нормализованного контракта может оставаться в `manifest.yaml` как `unknown` или `limitation`. Если такой пробел влияет на архитектурный вывод, finding/risk или решение, он дополнительно связывается с `Q-*`.

## 8. Историчность и временная актуальность

FACT означает, что источник содержит утверждение. FACT не означает автоматически, что утверждение описывает текущее production-состояние.

Для time-sensitive sources/facts используется:

```yaml
temporal_scope:
  source_date: 2015-08-13
  currentness: unverified
```

Допустимые значения:

```text
current
current-at-source-date
historical
unverified
```

Правила:

- `current` требует достаточного актуального подтверждения;
- `current-at-source-date` описывает состояние на дату источника;
- `historical` используется для заведомо исторических сведений;
- `unverified` означает, что применимость к текущему состоянию не подтверждена;
- старый документ не подтверждает текущий technology/runtime/operations state сам по себе;
- новая информация не должна молча переписывать историю анализа.

Наличие наиболее новой доступной документации и temporal currentness являются независимыми характеристиками. Для source set при необходимости используется отдельный признак:

```yaml
source_set_status:
  latest_available: true
```

`latest_available: true` означает только, что в доступном assessment source set нет более нового материала или stakeholder считает этот материал наиболее актуальным из переданных. Этот признак никогда автоматически не повышает `currentness` и не подтверждает соответствие текущему production-состоянию.

Для эволюционирующих объектов используются статусы:

```text
active
contradicted
superseded
```

При замене или опровержении старый ID сохраняет прежний смысл и получает ссылку на заменяющий/опровергающий объект.

## 9. Подтверждения (evidence)

Каждая существенная выявленная проблема должна иметь evidence. Значимые архитектурные выводы также должны быть трассируемы до facts/evidence.

Типовая запись:

```yaml
- id: EV-API-014
  source: SRC-API-001
  page: 37
  section: "Error Handling"
  locator:
    type: endpoint
    value: "POST /api/example"
  evidence_type: text
  provenance: documented
  statement_ru: >
    API использует несколько форматов ответа об ошибке.
```

Допустимые `evidence_type`:

```text
text
diagram
table
ui
contract
stakeholder-response
observed
```

Предпочтительные semantic locators:

```text
section
operation
endpoint
table
field
diagram
process
role
```

Страница сохраняется, если применима, но semantic locator предпочтителен для устойчивой трассировки.

Допустимый provenance:

```text
documented
stakeholder-confirmed
stakeholder-reported
observed
```

Смысл:

- `documented` - утверждение следует из документа/контракта;
- `stakeholder-confirmed` - respondent явно подтверждает уже существующее утверждение или вопрос о текущем состоянии;
- `stakeholder-reported` - respondent сообщает новый факт, которого ранее в модели не было;
- `observed` - состояние непосредственно наблюдалось в доступной системе, интерфейсе, конфигурации или артефакте.

Stakeholder evidence не должен молча перетирать противоречащую документацию.

Не копировать большие фрагменты источника. Evidence хранит краткое утверждение и точную ссылку/locator.

## 10. Предварительная проверка источника

До глубокого извлечения фактов выполняется source preflight.

По возможности фиксируются:

```text
title
source id
version
date
source family
relationship to related sources
version confidence
ingest profile
authority
currentness
sensitivity
visual review mode
normalization requirements
source-set status / latest available, если применимо
```

Примеры `ingest_profile`:

```text
functional-specification
user-guide
integration-contract
database-model
normative-operational
stakeholder-response
public-source
```

### 10.1. Семейство и версии

Если несколько файлов относятся к одной серии или версии документа, они рассматриваются как source family до независимого создания дублирующих facts.

Нужно:

1. сравнить title/version/date/history внутри файлов;
2. определить relationship: последовательные версии, overlap, supplement или ambiguous;
3. фиксировать значимые delta;
4. не назначать primary source, если приоритет нельзя подтвердить;
5. не считать filename доказательством более новой версии при конфликте с внутренней metadata.

### 10.2. Упомянутые ресурсы

URL, endpoint, filename, WSDL/XSD/OpenAPI reference, filesystem path или иной resource reference не доказывает доступность, существование или актуальность ресурса.

Для referenced contracts/resources применяются статусы:

```text
provided
referenced-unavailable
referenced-unverified
not-referenced
```

Machine-readable contract считается фактически предоставленным только если сам artifact доступен как source.

## 11. Чувствительные данные

До переноса сведений в Git выполняется sensitive-data preflight.

Нельзя переносить в Git в исходном виде:

- passwords;
- JWT/session tokens;
- private keys;
- secrets;
- credentials в command/config examples;
- иные значения, похожие на действующие секреты.

Internal IP, hostname, path и operational command копируются точно только когда конкретное значение действительно нужно для архитектурного evidence. В остальных случаях используется abstraction/redaction.

Нормализованные контракты используют безопасные placeholders вместо реальных credentials/tokens.

Перед `/snapshot` должен выполняться доступный secret/sensitive-value scan.

## 12. Работа с визуальными материалами

Для screenshot-heavy документов используется целевой визуальный просмотр (targeted visual review):

- не выполнять массовый OCR без необходимости;
- не извлекать каждый UI screenshot;
- анализировать текстовый слой;
- отдельно просматривать архитектурно, процессно и топологически значимые изображения;
- не хранить raw screenshots в Git;
- реконструировать значимые структуры в Mermaid, если они входят в canonical model.

Raw image может быть evidence source, но version-controlled model хранит извлеченный смысл, а не коллекцию screenshot-файлов.

## 13. Классификация интерфейсов

До нормализации каждый интеграционный механизм классифицируется отдельно.

Допустимые `interface_type`:

```text
rest
soap
http-xml
file
queue
database
manual
unknown
```

Один документ может описывать несколько типов интерфейсов. Нельзя классифицировать весь документ как SOAP/REST только из-за названия документа или одного раздела.

## 14. Нормализация технических контрактов

Нормализация выполняется только из доступного evidence. Недостающие детали не придумываются.

Статус нормализованного artifact:

```text
original-provided
reconstructed
```

Статус исходного contract source:

```text
provided
referenced-unavailable
referenced-unverified
not-referenced
```

Если оригинальный machine-readable contract фактически предоставлен, он имеет приоритет над reconstruction. Reconstruction не создается параллельно без конкретной аналитической причины.

### 14.1. REST

REST documentation нормализуется в OpenAPI 3.1:

```text
openapi.yaml
```

Восстанавливаются только подтверждаемые элементы: paths, methods/operations, parameters, request bodies, response schemas, status codes, security requirements, examples, reusable schemas.

Неизвестные детали фиксируются как limitation/unknown.

### 14.2. SOAP

При достаточном evidence создаются `.wsdl` и необходимые `.xsd`.

Если operation/message/type известны, а binding/service/endpoint не подтверждены, полный WSDL не симулируется как факт.

Если доступны только структуры сообщений, создаются `.xsd`, а ограничение фиксируется в manifest.

Reference на недоступный WSDL сохраняется как provenance metadata, а reconstruction строится только из реально доступного содержания.

### 14.3. HTTP/XML и file/XML

XML-over-HTTP или file/XML не превращается искусственно в SOAP.

Допускается:

- interface metadata в YAML;
- `.xsd`, если XML schema подтверждается;
- retry/idempotency/response semantics в canonical interface model;
- manifest с completeness и limitations.

### 14.4. Contract manifest

Каждый нормализованный REST/SOAP/XML contract имеет соседний `manifest.yaml`.

Минимальная идея структуры:

```yaml
id: NORM-API-001
type: rest-api
format: openapi-3.1
status: reconstructed
contract_source:
  status: referenced-unavailable
sources:
  - SRC-API-001
completeness:
  operations: high
  schemas: medium
  errors: low
  security: unknown
evidence_map:
  "POST /api/example":
    - EV-API-014
limitations:
  - "Документация не определяет часть error responses."
```

Допустимые `completeness`:

```text
high
medium
low
unknown
```

`evidence_map` связывает значимые operations/types/schemas с evidence IDs.

### 14.5. Contract validation

Перед snapshot измененные normalized contracts проходят доступные проверки:

- YAML validity;
- OpenAPI 3.1 structure;
- well-formed WSDL/XSD XML;
- согласованность XSD import/include/reference;
- согласованность manifest/source/interface references.

Validation не делает reconstructed contract подтвержденным production contract.

## 15. Модель данных

Концептуальная / доменная модель и физическая модель базы данных разделяются.

Доменный слой:

```text
02-as-is/domain-entities.yaml
```

Физический слой:

```text
02-as-is/normalized/data/<database-or-schema>.yaml
02-as-is/normalized/data/<database-or-schema>.mmd
```

Физическая модель может хранить документированные tables, columns, data types, keys, constraints, indexes и ownership.

Для отношений physical data model различаются три класса:

```text
physical_fk
documented_logical_reference
inferred_logical_reference
```

- `physical_fk` используется, когда наличие FK constraint прямо подтверждено источником или DDL/catalog evidence;
- `documented_logical_reference` используется, когда источник прямо говорит, что поле или набор полей ссылается на другой physical object/key, но наличие DB constraint не подтверждено;
- `inferred_logical_reference` используется, когда связь восстановлена архитектурным анализом по именам, типам, назначению полей, business semantics и соседней структуре модели.

Логические связи рекомендуется хранить отдельно от физически подтвержденных constraints, например:

```text
02-as-is/normalized/data/logical-foreign-keys.yaml
```

Для `inferred_logical_reference` обязательны `assertion: inference`, `confidence`, человекочитаемое основание (`basis_ru` или эквивалент), evidence/traceability и `physical_constraint_status: unverified`, если constraint отдельно не подтвержден. Для `documented_logical_reference` допустим `assertion: fact`, если сама смысловая ссылка прямо следует из evidence; это все равно не доказывает наличие Oracle/DB FK constraint.

Если один source публикует одно physical имя с несовместимыми definitions:

1. исходное имя сохраняется без молчаливого исправления;
2. definitions получают отдельные occurrence/variant IDs;
3. они не сливаются в один physical object без evidence;
4. конфликт регистрируется как contradiction;
5. предполагаемое intended name допускается только как inference и не заменяет canonical physical name.

Идентичные повторные definitions могут дедуплицироваться при сохранении нескольких source locators.

Physical table и domain entity не считаются одним объектом автоматически. Mapping фиксируется отдельно.

## 16. Findings, risks, recommendations и V2

### 16.1. Finding

Finding - подтверждаемая проблема текущего решения.

Минимальные поля:

```text
id
area
subsystem
statement_ru
evidence
impact_ru
severity
status
visibility
```

Severity:

```text
high
medium
low
```

`critical` не используется.

Visibility:

```text
owner
developer
both
internal
```

Finding без evidence или объясненного impact не создается.

### 16.2. Risk

Risk описывает потенциальное негативное событие.

Оценки:

```text
impact: high | medium | low
likelihood: high | medium | low
```

Finding и risk не заменяют друг друга.

### 16.3. Recommendation

Recommendation должна адресовать минимум один finding или risk.

Пример:

```yaml
- id: REC-API-003
  recommendation_ru: >
    Ввести единую политику версионирования публичного API.
  addresses:
    findings:
      - FIND-API-007
    risks: []
```

Новая возможность V2 не оформляется как recommendation только ради этого правила.

### 16.4. V2 capability

V2 capability может:

- устранять/развивать следствия findings и risks;
- развивать существующую capability;
- отражать новую business/operational opportunity.

Если V2 capability не основана на finding/risk, поле `opportunity_ru` обязательно.

До AS-IS freeze V2 capabilities являются backlog, а не approved target architecture.

## 17. Вопросы и неизвестные

`questions.yaml` является каноническим реестром значимых неопределенностей проекта. Команда `/questions` является отдельным интерактивным инструментом и не является просмотром или пересказом этого реестра.

Типовой question содержит:

```text
id
area
question_ru
reason_ru
priority
status
related evidence/facts/findings, если применимо
```

Допустимые базовые статусы:

```text
open
resolved
deferred
not-applicable
superseded
```

Не каждое unknown обязано становиться отдельным Q-*; см. раздел 7.

`questions.yaml` хранит значимые вопросы о системе, assessment или архитектурном решении, которые должны сохраняться между итерациями. Кратковременный рабочий вопрос AI к архитектору о порядке действий, способе анализа или организации процесса не обязан регистрироваться как Q-*.

Если stakeholder отвечает только на часть составного вопроса, подтвержденная часть не должна оставаться искусственно открытой. Нужно:

1. зафиксировать stakeholder response как evidence или stakeholder context;
2. закрыть подтвержденную часть исходного вопроса либо уточнить его scope без потери смысла;
3. оставшуюся устойчивую неопределенность вынести в новый или более узкий `Q-*`, если она по-прежнему значима;
4. сохранить traceability между исходным ответом и оставшимся unknown;
5. не считать частичный ответ подтверждением неотвеченной части.

## 18. Диаграммы

Version-controlled engineering diagrams хранятся только в Mermaid `.mmd`.

Полезные типы:

```text
context
components
integration
sequence
data
deployment
lifecycle
capabilities
migration
```

Создается только диаграмма, которая помогает analysis/assessment/TO-BE. Не создавать диаграммы ради полноты списка.

Различаются три уровня визуализации:

```text
1. Canonical diagram source
   Mermaid .mmd в Git

2. /diagram
   временная sketch-визуализация для анализа и обсуждения
   может учитывать текущий WIP

3. Deliverable visualization
   presentation-quality визуализация из published baseline
   стиль определяется параметром --visual
```

Mermaid является инженерным исходником структуры диаграммы, но не обязательным визуальным представлением в DOCX/PPTX. В итоговый материал не должен автоматически попадать Mermaid-код или сырой Mermaid-render, если он не соответствует качеству и аудитории материала.

`sketch-style` означает лаконичную концептуальную визуализацию с простой композицией, ясными подписями, ограниченным числом элементов и акцентом на архитектурный смысл. Это не означает намеренно небрежный или декоративно рукописный рисунок.

Каждая deliverable diagram должна иметь явную коммуникационную цель: показать system boundary, interaction, flow, responsibility, lifecycle, dependency, information movement или decision. Если схема только декоративно повторяет список блоков из соседнего текста, ее не следует создавать. Для major diagram рядом с ней должно быть кратко объяснено, что именно важно увидеть и какой вывод должен сделать читатель.

Для `sketch` действуют обязательные layout/readability требования:

- текст не перекрывает другой текст, nodes, icons или connectors;
- labels и callouts не обрезаются и имеют безопасные margins;
- connectors не проходят через читаемый текст, если это ухудшает понимание;
- количество nodes ограничивается реальной читаемостью в финальном размере страницы/слайда;
- перегруженная схема разбивается на несколько views или переводится в `clean`;
- generative illustration не используется как надежный способ отрисовки содержательных labels: геометрия/illustration и текст должны компоноваться раздельно, когда это необходимо для гарантии точности и читаемости;
- схема проверяется после финального render в составе DOCX/PPTX, а не только как отдельный исходник.

Deliverable visualization может перестраивать композицию canonical diagram под страницу или слайд, но не должна менять архитектурный смысл, добавлять отсутствующие связи или скрывать существенные ограничения ради оформления. Presentation-level bounded synthesis, разрешенный разделом 20.12, может показывать вероятную связь только с понятной маркировкой уровня уверенности и не превращает ее в canonical FACT.

## 19. Стандартная итерация

Основная единица обработки - **один source или логически связанная source family**.

Типовой цикл:

1. source preflight и registration;
2. анализ значимой информации;
3. evidence/facts update;
4. glossary update;
5. technical normalization, если применимо;
6. AS-IS model update;
7. cross-check с предыдущей моделью;
8. findings/risks/recommendations update, если текущая стадия assessment и evidence дают достаточное основание;
9. V2 backlog update, если текущая стадия и evidence дают достаточное основание;
10. diagrams update только при изменении модели;
11. iteration quality gate;
12. единый отчет о семантическом изменении.

Отчет после существенной source iteration по возможности содержит:

```text
Краткий вывод
Изменение общей AS-IS модели
Проблемы / риски / рекомендации
Предположения / вопросы / противоречия
Возможности V2
Изменения repository
Рекомендуемый commit
Следующий source / действие
```

Пустые разделы не заполняются искусственно.

## 20. Команды взаимодействия

Аналитические команды `/questions`, `/ideas`, `/plan`, `/diagram`, `/protocol` и `/precommit` сами по себе не меняют canonical model, если команда явно не предусматривает изменение (`/protocol --fix`).

### 20.1. `/questions [scope]`

Назначение - показать короткий набор вопросов, которые полезнее всего решить **сейчас**. Команда не является просмотром `questions.yaml` и не должна пытаться перечислить весь реестр.

По умолчанию:

- не более 10 вопросов;
- учитывается текущая фаза, source/source family, subsystem и ближайшие решения;
- похожие вопросы объединяются;
- недавно уже заданные вопросы не повторяются без причины;
- deferred вопросы не поднимаются без актуальной зависимости;
- наверх идут вопросы, которые блокируют или заметно влияют на качество следующего шага;
- если значимых вопросов меньше 10, список не дополняется искусственно.

Категории важности:

```text
BLOCKING
IMPORTANT
OPTIONAL
```

Каждый вопрос дополнительно помечается по происхождению:

```text
[Q-...]     зарегистрированный вопрос из questions.yaml
[NEW]       потенциально значимая новая неопределенность, еще не зарегистрированная
[WORKING]   временный рабочий вопрос текущего диалога, который не требует регистрации
```

Для каждого вопроса показывается:

```text
Вопрос
Почему спрашиваем
Что произойдет, если ответа не будет
```

`[NEW]` становится Q-* только после того, как его значимость подтверждена в ходе работы. `[WORKING]` не переносится в `questions.yaml`, если не превращается в устойчивую проектную неопределенность.

Нормальный результат без ответа - сохранить `UNKNOWN`, `unverified`, limitation или deferred question и продолжить, если вопрос не blocking.

Поддерживаемые варианты:

```text
/questions
/questions blocking
/questions <scope>
```

Полный реестр просматривается отдельным явным запросом к `questions.yaml`, а не через `/questions`. Базовая команда всегда остается короткой и приоритетной.

### 20.2. `/ideas`

Показывает дополнительные идеи AI по текущему этапу без автоматического применения.

Группы:

```text
Полезно сделать сейчас
Можно сделать позже
Экспериментальная идея
```

Идея может касаться анализа, организации repository, дополнительной проверки, диаграммы, нормализации, V2 или методики.

Если существенных идей нет, AI должен так и сказать, а не заполнять список искусственно.

### 20.3. `/plan`

Показывает ближайший рекомендуемый план:

- где находится проект сейчас;
- что делать следующим;
- почему;
- зависимости;
- что можно отложить.

План должен быть практичным и ближайшим, а не автоматически превращаться в длинный roadmap.

### 20.4. `/diagram [scope] [type] [focus]`

Команда предназначена для временной визуализации текущего понимания в sketch-style.

Если параметры отсутствуют, AI показывает доступные сейчас `scope` и подходящие типы диаграмм на основе текущей модели.

Типы:

```text
context
components
integration
sequence
data
deployment
lifecycle
capabilities
migration
```

`scope` может быть понятным именем subsystem/component/domain или ID. Пользователь не обязан помнить внутренние IDs.

`focus` - свободное уточнение, например конкретный flow, actor, external system или этап migration.

Правила:

- visualization строится из published baseline плюс текущий WIP;
- WIP, assumptions и unknowns должны быть визуально отличимы;
- подтвержденные связи не смешиваются с предположительными;
- команда не меняет `.mmd` и canonical model;
- если данных недостаточно, AI не придумывает deployment/data/flow и предлагает доступные альтернативы;
- если после обсуждения sketch надо закрепить, canonical model и Mermaid обновляются отдельным согласованным изменением.

### 20.5. `/protocol`

Выполняет аудит `ASSESSMENT_PROTOCOL.md` и его согласованности с `README.md`, но ничего не меняет.

Проверяются:

- внутренние противоречия;
- дублирование;
- слишком жесткие или бесполезные правила, проявившиеся в реальной работе;
- неоднозначные формулировки;
- недостающие правила;
- расхождение README и protocol;
- избыточная сложность процесса;
- возможные улучшения команд и quality gates.

Каждое предлагаемое изменение получает временный ID вида `PR-001` и содержит:

```text
Проблема
Почему важно
Предлагаемое изменение
Последствия
```

Первый вызов `/protocol` никогда не меняет файлы автоматически.

### 20.6. `/protocol --fix [proposal-ids]`

Применяет предложения последнего `/protocol`.

Варианты:

```text
/protocol --fix
/protocol --fix PR-002 PR-004
```

Без IDs применяются все предложения последнего review, если они однозначны и еще актуальны. С IDs применяются только выбранные.

Если в текущем чате нет актуального результата `/protocol`, AI не должен придумывать предложения заново под видом `--fix`; сначала требуется `/protocol`.

Команда может менять только governance процесса:

```text
ASSESSMENT_PROTOCOL.md
README.md
```

Она не меняет facts, findings, architecture или другие project knowledge objects автоматически.

Изменения protocol являются обычным WIP до `/snapshot` и публикации в Git.

### 20.7. `/precommit`

Проверяет текущий WIP без создания snapshot.

Результат:

```text
BLOCKERS
WARNINGS
OPEN ITEMS
```

Проверяется по возможности:

- YAML/Markdown/Mermaid consistency;
- broken IDs/references;
- findings без evidence/impact;
- recommendations без finding/risk;
- sensitive values;
- temporal/currentness gaps;
- OpenAPI/WSDL/XSD validity;
- manifest completeness;
- необоснованные assumptions;
- unresolved contradictions текущей итерации;
- расхождение README/protocol при их изменении.

`/precommit` ничего не публикует и не заменяет обязательные snapshot quality gates.

### 20.8. `/snapshot`

Означает, что текущий WIP достаточно согласован для candidate snapshot.

AI должен:

1. схлопнуть согласованные изменения в coherent canonical state;
2. выполнить применимые quality gates;
3. сформировать полный repository snapshot, а не delta;
4. присвоить `SNAPSHOT-NNN`;
5. дать semantic summary;
6. показать открытые assumptions/questions/contradictions;
7. предложить один commit message.

Рекомендуемое имя архива:

```text
assessment-snapshot-NNN.zip
```

Candidate snapshot не является baseline до `/published`.

Если snapshot не устраивает, работа продолжается в WIP и следующий `/snapshot` создает новый candidate.

### 20.9. `/published`

Означает, что последний candidate snapshot опубликован пользователем в Git/GitHub **без ручных изменений** и становится новым published baseline.

Commit hash не обязателен.

Если snapshot был вручную изменен перед публикацией, пользователь должен передать AI фактическое новое состояние repository. Нельзя использовать `/published` как подтверждение состояния, которого AI не видел.

### 20.10. `/handoff`

Используется для передачи работы в новый чат или другому AI-сеансу, особенно когда текущий чат перегружен контекстом.

Handoff должен содержать:

- полный актуальный repository;
- `CHAT_HANDOFF.md`;
- текущую фазу;
- последний published baseline;
- WIP после baseline;
- принятые решения;
- важные assumptions/questions/contradictions;
- текущий source/source family;
- ближайшее рекомендуемое действие.

`CHAT_HANDOFF.md` является chat-transfer metadata и **не является файлом repository/Git**. Нормативная структура handoff-архива:

```text
assessment-handoff-...zip
├── CHAT_HANDOFF.md
└── assessment/
    ├── README.md
    ├── ASSESSMENT_PROTOCOL.md
    └── ... repository files ...
```

При сборке `/handoff` файл `assessment/CHAT_HANDOFF.md` должен явно исключаться, даже если он физически присутствует в рабочем каталоге из-за предыдущего handoff. Quality gate handoff должен подтвердить, что `CHAT_HANDOFF.md` находится только в корне handoff-архива, а каталог `assessment/` соответствует фактическому repository/WIP state.

Raw `sources/` и `deliverables/` по умолчанию не включаются.

Handoff не является `/snapshot`, `/published` или commit и может содержать WIP.

### 20.11. `/deliverable`

Команда без параметров ничего не генерирует. Она показывает:

- доступные типы;
- аудитории;
- форматы;
- языки;
- доступные визуальные стили (`--visual`);
- доступные режимы synthesis (`--synthesis`);
- допустимые и рекомендуемые комбинации;
- рекомендуемые visual style и synthesis mode для каждой комбинации;
- примеры команд;
- готовность каждого варианта на текущем published baseline.

Базовая матрица:

| type | audience | formats | recommended visual | recommended synthesis |
|---|---|---|---|---|
| `as-is` | `owner` | `docx`, `pptx` | `sketch` | `probable` |
| `as-is` | `developer` | `docx`, `pptx` | `mixed` | `strict` |
| `to-be-concept` | `owner` | `docx`, `pptx` | `sketch` | `strict` for AS-IS claims |
| `to-be-concept` | `developer` | `docx`, `pptx` | `mixed` | `strict` |
| `to-be-detailed` | `developer` | `docx` | `mixed` | `strict` |
| `presales` | `owner` | `pptx` | `sketch` | `strict` for AS-IS claims |

Если проект еще не готов для конкретного материала, `/deliverable` должен показать причину, например незавершенный AS-IS или отсутствие published baseline.

### 20.12. `/deliverable <type> <audience> <language> <format> [--visual <style>] [--synthesis <strict|probable>]`

Создает формальный material только из последнего `/published` baseline. Current WIP не включается.

`--synthesis` управляет тем, насколько deliverable может восстанавливать связи между опубликованными знаниями:

```text
strict    показывать только явно подтвержденные canonical relationships
probable  разрешить bounded synthesis наиболее вероятной связной картины
```

`probable` не ослабляет canonical evidence discipline. Он может соединять опубликованные FACT, INFERENCE, ASSUMPTION и structured relationships в presentation-level AS-IS interpretation, только если такая связь совместима с available evidence и не скрывает активные contradictions. Новая deliverable-local интерпретация не записывается обратно в canonical model, не получает статус FACT и не используется как evidence для findings/risks/recommendations.

Если существует несколько существенно различающихся правдоподобных вариантов, deliverable показывает наиболее вероятный как `Likely` и кратко указывает существенную альтернативу или сохраняет `Unknown`, если выбор недостаточно обоснован.

Default synthesis policy:

```text
as-is + owner       probable
as-is + developer   strict
other combinations  strict, если пользователь явно не выбрал probable для AS-IS части материала
```

`language` предпочтительно задается ISO 639-1 кодом, например:

```text
ru
az
en
```

Выбранный `language` применяется ко всему deliverable в соответствии с разделом 3. Для `en` весь пользовательский текст, включая diagram labels и captions, должен быть английским, кроме допустимых неизменяемых technical/system names и source literals.

Допустимые визуальные стили:

```text
auto
sketch
clean
mixed
```

Смысл:

- `auto` - AI выбирает стиль для каждой диаграммы по типу материала, аудитории и уровню детализации;
- `sketch` - лаконичная концептуальная presentation-quality визуализация;
- `clean` - строгая техническая presentation-quality визуализация без сырого Mermaid-представления;
- `mixed` - обзорные и концептуальные схемы выполняются в `sketch`, детальные технические схемы в `clean`.

Если `--visual` не указан, используется `auto`.

Default policy для `auto`:

```text
owner-facing overview/concept diagrams     sketch
developer-facing overview diagrams         sketch
developer-facing detailed diagrams         clean
to-be-detailed                             mixed
presales                                   sketch
```

Примеры:

```text
/deliverable as-is owner en docx --visual sketch --synthesis probable
/deliverable as-is owner az docx --visual sketch --synthesis probable
/deliverable as-is developer ru docx --visual mixed --synthesis strict
/deliverable to-be-concept owner en pptx --visual sketch
/deliverable to-be-detailed developer ru docx --visual mixed
/deliverable presales owner az pptx --visual sketch
```

Выходные файлы помещаются в локальный `deliverables/` и не являются source of truth.

Матрица является default policy. Явное решение пользователя может разрешить нестандартный вариант, если это не нарушает ограничения disclosure и пользователь понимает последствия.

## 21. Initial governance commit

Initial commit процесса не содержит analysis системы, пустые реестры или заранее созданную project structure.

Он содержит только:

```text
README.md
ASSESSMENT_PROTOCOL.md
.gitignore
```

Рекомендуемый commit:

```text
bootstrap: добавлен протокол ведения assessment
```

Для initial governance commit `/snapshot` и `/published` не требуются. Рабочая assessment-модель создается в bootstrap-итерации нового project/chat context.

## 22. Git discipline

AI не должен утверждать, что выполнил Git commit/push, если фактически не имеет такого доступа и не выполнил операцию.

Рекомендуемые commit prefixes:

```text
bootstrap:
source:
model:
assessment:
tobe:
adr:
docs:
fix:
```

Описание после prefix ведется на русском языке.

Одна source/source-family iteration по возможности соответствует одному coherent commit после принятого snapshot.

Не создавать отдельный commit на каждый fact, finding или diagram edge.

`CHANGELOG.md` не является обязательным. Git history и canonical registries являются основным журналом изменений. Changelog можно добавить позднее при реальной потребности.

## 23. Quality gates

### 23.1. Source iteration gate

До завершения source/source-family iteration проверить:

- source зарегистрирован;
- family/version ambiguity обработана;
- sensitive-data preflight выполнен;
- evidence имеет locator там, где это применимо;
- temporal/currentness metadata установлена для time-sensitive facts;
- glossary обновлен при необходимости;
- canonical model согласована;
- applicable normalized contracts обновлены и проверены;
- contract provenance/evidence map согласован;
- contradictions рассмотрены;
- findings, если они создавались или менялись, связаны с evidence и impact;
- recommendations, если они создавались или менялись, связаны с findings/risks;
- questions обновлены;
- Mermaid не противоречит model;
- human-readable тексты соответствуют рабочему языку и style rules, а технические identifiers/source literals не переводятся;
- определено coherent semantic change.

### 23.2. Snapshot gate

Перед `/snapshot` дополнительно выполнить по возможности:

- YAML parse/structure validation;
- cross-reference validation;
- evidence traceability validation;
- Mermaid syntax/semantic check;
- OpenAPI/WSDL/XSD validation;
- secret/sensitive-value scan;
- temporal consistency check;
- language/style consistency check для human-readable полей, Markdown и Mermaid labels с исключением технических identifiers/source literals;
- проверка отсутствия случайной формы буквы с двумя точками и типографских длинных тире вне точных source literals/code;
- README/protocol consistency check, если governance менялся.

Примеры blocking conditions:

- finding без evidence;
- реальный token/password внутри canonical files;
- technical fact из старого source без temporal handling, когда currentness важен;
- broken canonical IDs;
- invalid normalized contract после его изменения.

### 23.3. AS-IS freeze

До активного TO-BE design:

- все доступные приоритетные sources обработаны;
- существенные contradictions видимы;
- assumptions reviewed;
- unknowns отделены от findings;
- system context достаточно стабилен;
- subsystem boundaries достаточно стабильны;
- integration model достаточно стабильна;
- domain/physical data models разделены;
- technical contracts нормализованы там, где evidence позволяет;
- completeness/limitations реконструкций видимы;
- основные findings/risks ранжированы;
- recommendations трассируемы;
- V2 capability backlog сформирован.

AS-IS freeze не означает полного знания системы. Он означает, что оставшаяся неопределенность явно видима и управляется.

### 23.4. Deliverable gate

Ни один formal DOCX/PPTX не считается завершенным только потому, что файл технически создан. До выдачи пользователю требуется post-render QA финального material.

Проверяется по возможности каждый финально отрендеренный page/slide:

- выбранный language lock соблюден во всем пользовательском тексте, включая diagrams/captions/headers/footers; для `en` отсутствует необъясненная кириллица;
- нет text overlap, clipping, unreadable labels, broken tables, orphan headings, случайно пустых или чрезмерно пустых областей;
- captions и объяснения не оторваны от соответствующих visuals;
- отсутствуют raw Mermaid artifacts или Mermaid-render, не соответствующий presentation quality;
- diagram labels читаемы в фактическом физическом размере страницы/слайда;
- connectors и graphical elements не перекрывают содержательный текст;
- owner-facing narrative не содержит необъясненных internal assessment IDs и не выглядит как dump canonical records;
- при `--synthesis probable` вероятные связи представлены как вероятные, active contradictions/существенные альтернативы не скрыты, а deliverable-local synthesis не выдан за FACT;
- каждая major diagram добавляет relationship/flow insight, а не только повторяет список entities;
- общий материал образует coherent narrative для выбранной аудитории, а не просто последовательность корректных секций.

Если post-render QA выявляет дефект, material исправляется и рендерится повторно до выдачи.

## 24. Проектирование TO-BE

До AS-IS freeze разрешено собирать V2 capability backlog, но не фиксировать полноценную target architecture как принятую.

После freeze рекомендуется порядок:

```text
Drivers / Findings / Risks / Opportunities
  -> Target Capabilities
  -> Architecture Principles
  -> System Boundaries
  -> Major Building Blocks
  -> Integration Approach
  -> Data Ownership
  -> Security
  -> Operations / Observability
  -> Migration
  -> Detailed Components / Contracts
  -> ADR
```

Технология не выбирается только потому, что она популярна.

Каждое существенное architecture decision должно иметь rationale и trade-offs. ADR создается для принятого решения, а не для каждой идеи.

## 25. Уровни раскрытия TO-BE

Используются уровни:

```text
L0  Business / Product Capabilities
L1  System Context
L2  Major Containers / Building Blocks
-------------------------------
CUSTOMER DISCLOSURE BOUNDARY
-------------------------------
L3  Components
L4  Interfaces / Contracts / Events
L5  Implementation / Deployment / Detailed Mechanics
```

### 25.1. Owner-facing

По умолчанию содержит:

- L0;
- L1;
- L2;
- selected L3, когда нужен для объяснения решения;
- principles;
- rationale;
- benefits;
- migration concept;
- security concept;
- operations concept;
- key risks/trade-offs.

Без отдельной причины не раскрывает полную service decomposition, internal contracts, detailed DB schema, CI/CD internals, detailed deployment/migration mechanics, internal ADR и reusable implementation know-how.

Owner-facing material должен быть содержательным и профессиональным, а не искусственно пустым. Он не должен быть механическим экспортом canonical domains или последовательностью независимых facts. Основная задача - объяснить систему как связную историю: purpose -> actors -> end-to-end operation -> architectural building blocks -> interactions -> data movement -> operational lifecycle -> security/operations -> uncertainty -> implications for owner. Требование краткости не должно приводить к потере связей: owner DOCX должен давать достаточное объяснение причинно-следственных связей, dependencies, переходов между domains и практического смысла архитектуры. Краткий executive summary не заменяет развернутый основной narrative.

Для каждого major architectural block материал по возможности объясняет его роль, основные входы/выходы, зависимости и место в общем flow. Если published baseline позволяет, owner-facing AS-IS включает несколько cross-domain views, которые восстанавливают связи между domains: например system context, основной business/transaction flow, integration/data flow и operational-day lifecycle.

Internal assessment IDs (`FACT-*`, `EV-*`, `INF-*`, `ASM-*`, `Q-*`, `FIND-*`, `RISK-*`, `REC-*`, `CONTR-*` и аналогичные governance IDs) по умолчанию не используются в основном owner narrative, таблицах или диаграммах. Вместо ID дается понятное человеку объяснение источника, уровня уверенности, проблемы или ограничения. При необходимости полная traceability может быть сохранена во внутреннем metadata или отдельном technical appendix, который не включается в owner material по умолчанию. Реальные system/API/database/interface identifiers этим правилом не запрещаются.

Неопределенность в owner-facing material описывается простой семантикой:

```text
Confirmed  прямо подтверждено доступным evidence
Likely     восстановлено из согласующихся evidence, но не подтверждено независимо в current runtime
Unknown    существенная неопределенность требует подтверждения
```

Эти reader-facing labels не заменяют canonical FACT/INFERENCE/ASSUMPTION и не изменяют их статус.

### 25.2. Developer-facing

Может содержать L0-L5, включая detailed decomposition, contracts, data structures, deployment, migration mechanics и ADR.

## 26. Профили итоговых материалов

Структура ниже является default profile и может быть явно скорректирована перед генерацией.

### 26.1. AS-IS DOCX

Для owner-facing материала обзорные архитектурные схемы по умолчанию используют `sketch`; default synthesis mode - `probable`. Для developer-facing материала применяется `auto`/`mixed` в зависимости от детализации, а default synthesis mode - `strict`.

Owner-facing AS-IS должен читаться как архитектурный рассказ о наиболее вероятном текущем устройстве системы, а не как каталог доменных утверждений. Внутри разделов необходимо явно восстанавливать подтвержденные и вероятные cross-domain связи и объяснять, почему они важны владельцу. Существенные gaps показываются рядом с соответствующим flow/block понятными `Confirmed` / `Likely` / `Unknown`, а не только отдельным списком internal IDs.

Типовая структура:

1. Executive summary и общая картина системы.
2. Scope, evidence coverage и ограничения assessment.
3. Назначение, actors и ключевые capabilities.
4. End-to-end business/operational flow.
5. System context, boundaries и major architectural building blocks.
6. Взаимодействия между подсистемами, интеграции и contracts на уровне, нужном аудитории.
7. Data movement, ownership и ключевые information dependencies.
8. Operational lifecycle, включая operational day, deployment/operations/observability в доступной глубине.
9. Security и control model.
10. Основные findings/risks и их влияние на работу системы.
11. Recommendations и приоритеты, если они допустимы текущей стадией assessment.
12. Существенные uncertainty/limitations и что требуется подтвердить.

Структура может адаптироваться под реальную систему. Запрещено сохранять этот список как формальную секционную сетку, если он приводит к повторению одних и тех же facts без связного synthesis.

### 26.2. AS-IS PPTX

Краткая управленческая версия AS-IS с акцентом на связную архитектурную историю, end-to-end flows, основные ограничения, risks и приоритеты улучшения. Для owner-facing варианта default visual style - `sketch`, default synthesis mode - `probable`; для developer-facing - `auto`/`mixed` и `strict`. Слайды не должны быть механическим сокращением DOCX или набором независимых фактов по domains.

### 26.3. TO-BE Concept DOCX/PPTX

Для owner-facing концепции default visual style - `sketch`. Для developer-facing концепции `auto` может сочетать `sketch` для overview и `clean` для технических деталей.

Типовая история:

1. Drivers изменений.
2. Цели V2.
3. Architecture principles.
4. Target capabilities.
5. Target system context.
6. Major building blocks.
7. Integration/data/security/operations concepts.
8. Migration concept.
9. Benefits/value.
10. Key risks и trade-offs.

### 26.4. TO-BE Detailed DOCX

Default visual style - `mixed`: обзорные схемы могут быть `sketch`, а component/sequence/deployment/data diagrams - `clean`.

Типовая структура:

1. Architecture overview.
2. Detailed decomposition.
3. Responsibilities и boundaries.
4. Interfaces/contracts/events.
5. Data ownership и structures.
6. Security model.
7. Deployment/runtime.
8. Observability/operations.
9. Migration mechanics.
10. ADR set.
11. Technical risks/trade-offs.
12. Implementation constraints.

### 26.5. Presales PPTX

Default visual style - `sketch`.

Не является простым DOCX, разложенным по слайдам.

Типовая история:

```text
Контекст и текущая ситуация
  -> Почему существующего подхода недостаточно
  -> Возможность / потенциал V2
  -> Что предлагается
  -> Как выглядит целевое решение
  -> Ценность для владельца
  -> Как можно перейти
  -> Следующий шаг
```

Препродажная история может использовать findings как drivers, но не должна превращаться в негативный аудит текущего решения.

## 27. Целевая структура project repository

Структура является нормативным ориентиром, но **не создается в initial governance commit**. Bootstrap создает только реально необходимые на текущем этапе файлы/каталоги.

```text
assessment/
├── README.md
├── ASSESSMENT_PROTOCOL.md
├── .gitignore
│
├── 00-governance/
│   ├── scope.md
│   ├── source-registry.yaml
│   ├── glossary.yaml
│   ├── assumptions.yaml
│   └── questions.yaml
│
├── 01-evidence/
│   ├── evidence.yaml
│   ├── facts.yaml
│   ├── contradictions.yaml
│   └── source-notes/
│
├── 02-as-is/
│   ├── system-context.md
│   ├── architecture-analysis.md
│   ├── security.md
│   ├── operations.md
│   ├── deployment.md
│   ├── components.yaml
│   ├── interfaces.yaml
│   ├── domain-entities.yaml
│   ├── capabilities.yaml
│   ├── normalized/
│   │   ├── rest/
│   │   ├── soap/
│   │   ├── xml/
│   │   └── data/
│   └── diagrams/
│
├── 03-assessment/
│   ├── findings.yaml
│   ├── risks.yaml
│   ├── recommendations.yaml
│   ├── assessment-summary.md
│   └── workshop-questions.md
│
├── 04-v2-backlog/
│   ├── capabilities.yaml
│   └── prioritization.md
│
├── 05-to-be-concept/
│   ├── architecture-vision.md
│   ├── target-capabilities.md
│   ├── migration-concept.md
│   ├── security-concept.md
│   ├── operations-concept.md
│   └── diagrams/
│
└── 06-to-be-detailed/
    ├── architecture/
    ├── integrations/
    │   └── contracts/
    ├── data/
    ├── security/
    ├── deployment/
    ├── observability/
    ├── migration/
    ├── adr/
    └── diagrams/
```

Локально рядом с repository могут существовать игнорируемые Git каталоги:

```text
sources/
deliverables/
```

Новый top-level version-controlled каталог не добавляется без ясной причины.

## 28. Что нельзя делать

AI и архитекторы не должны:

- выдавать ASSUMPTION/INFERENCE за FACT;
- менять смысл существующего ID без статуса superseded/contradicted;
- создавать отдельную модель на каждый document;
- создавать тысячи низкоценностных facts;
- считать отсутствие документации доказательством отсутствия механизма;
- создавать finding без evidence или impact;
- создавать recommendation без finding/risk;
- использовать `critical` severity;
- смешивать remediation и новую V2 opportunity;
- преждевременно фиксировать TO-BE;
- создавать ADR на каждую идею;
- выдавать reconstructed contract за original production contract;
- считать reference/URL доказательством доступности ресурса;
- копировать secrets/credentials/tokens в Git;
- считать старую документацию доказательством current production state без temporal handling;
- создавать дубли facts из overlapping source family без reconciliation;
- считать неотвеченный вопрос evidence;
- смешивать domain entity и physical table без mapping;
- превращать XML-over-HTTP/file XML в SOAP без evidence;
- придумывать endpoint/binding/schema/security details ради полноты contract;
- хранить raw source documents или массовые screenshots в Git;
- использовать второй diagram DSL параллельно Mermaid;
- делать финальный DOCX/PPTX source of truth;
- включать current WIP в formal deliverable;
- выдавать deliverable-local bounded synthesis за canonical FACT или записывать его обратно в canonical model без отдельного evidence/model update;
- использовать internal assessment IDs как основной язык owner-facing narrative вместо понятного объяснения;
- выдавать formal deliverable без post-render QA;
- раскрывать owner-facing TO-BE как implementation blueprint без явного решения;
- автоматически применять предложения `/ideas`, `/questions`, `/plan`, `/diagram`, `/protocol`;
- автоматически менять protocol после `/protocol` без `--fix`;
- утверждать, что commit/push выполнен, если операция фактически не выполнялась.

## 29. Главный принцип

**Модель первична. Документы вторичны.**

Каждый source/source family изменяет одну каноническую модель. AS-IS и TO-BE должны быть трассируемы до evidence и явно отделять факт, интерпретацию, assumption и proposal.

Git хранит знания и инженерные исходники модели. Raw sources и generated deliverables находятся вне Git.
