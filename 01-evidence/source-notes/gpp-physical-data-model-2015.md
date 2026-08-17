# SRC-DB-001 - физическая модель данных GPP 2015

## Предварительная проверка

- Внутреннее название: `Verilənlər Bazasının təsviri` (описание базы данных). Имя файла содержит другой поясняющий текст и hint `V2-1`; они не используются как authoritative название или версия.
- История версий: `001` от 04.08.2009, `002` от 09.06.2015, `003` от 09.06.2015; для `003` указано добавление схемы APUS.
- В DOCX присутствует один незакрытый reviewer comment от 04.11.2015 по `SC_SUPPORTED_IDEN_MTD.PREFIXED`. Это более поздняя редакционная аннотация, но не новая подтвержденная версия, поэтому `version_confidence: medium`.
- Профиль загрузки: `database-model`.
- Актуальность: `historical`; отдельные факты о схеме описываются как `current-at-source-date` на 09.06.2015 и не переносятся в текущее production-состояние.
- Статус в source set: по `SRC-STK-003` от 17.08.2026 это, вероятно, наиболее актуальная доступная документация по physical DB model среди переданных материалов. `latest_available` не означает `current` и не повышает temporal currentness схемы 2015 года.
- Чувствительность: `internal`; необходимости переносить credentials или secrets в canonical model не выявлено.

## Документированная физическая граница

Источник прямо разделяет структуру БД на две части:

1. `CF - Transaction DB` - основная область платежных операций и синхронизации данных для отчетности.
2. `APUS - Reporting DB` - отдельная область/instance для устойчивой отчетности.

Нормализация выполнена раздельно:

- `02-as-is/normalized/data/cf-transaction-db.yaml`;
- `02-as-is/normalized/data/cf-transaction-db.mmd`;
- `02-as-is/normalized/data/apus-reporting-db.yaml`;
- `02-as-is/normalized/data/apus-reporting-db.mmd`.

## CF Transaction DB

После сведения двух точных повторов (`MESSAGE`, `AMG_MSG`) и исключения простого повторного указателя на `PAYMENT_DATA` в разделе 3.7 источник содержит 27 различимых определений физических таблиц и 203 колонки.

Явные foreign keys из колонки `Xarici açar` сохранены буквально. Ссылки, которые описаны только текстом (`OPERATION.MESSAGE_ID -> MESSAGE.ID`, `PAYMENT_NOTIFICATION.PMT_NTFY_TYPE_ID -> PAYMENT_NOTIFICATION_TYPES.ID` и т.п.), хранятся отдельно как семантические ссылки, потому что источник не доказывает наличие физического ограничения FK.

PK также не достраиваются по соглашению `ID`: primary key фиксируется только там, где описание прямо называет поле `əsas açar`. Для других `ID`/serial fields сохраняется документированный идентификатор с `constraint_status: unverified`.

Таблицы `ORGANIZATION`, `BGUSER` и `PAYMENT_METHOD` встречаются как цели FK, но их определения в источнике отсутствуют; они учитываются как только упомянутые внешние таблицы.


## Логические FK и предполагаемые связи

Из-за ожидаемой ограниченности дальнейшей документации physical model дополнена отдельным слоем `logical-foreign-keys.yaml`. В модели различаются:

- `physical_fk` - ссылка прямо указана источником в колонке `Xarici açar` и рассматривается как документированный physical FK на дату источника;
- `documented_logical_reference` / `assertion: fact` - source прямо описывает смысловую ссылку на другую таблицу/ID, но не подтверждает наличие Oracle FK constraint;
- `inferred_logical_reference` / `assertion: inference` - связь восстановлена по согласованным именам колонок, типам, описаниям и назначению соседних таблиц. Для таких связей указывается `confidence: supported|tentative`, evidence и человекочитаемое основание.

Logical FK не изменяет документированную physical schema и не превращается в PK/UK/FK constraint без отдельного подтверждения. В частности, отдельно зафиксированы предполагаемые связи `SERVICE_TYPES` с организацией и группой услуг, `PAYMENT_DATA` с типом услуги/электронной подписью, `PAYMENT_NOTIFICATION` с платежом и batch-message, а также extension-связи `PAYMENT_EXT`/`STATEMENT_EXT`. В Mermaid ERD INFERENCE-связи показаны пунктиром.

После этого `Q-DB-004` закрыт как вопрос к stakeholder: отсутствие полного DDL не мешает формировать логическую relational model, если inferred relationships явно отделены от физических constraints. Коллизии `MESSAGE.OUTBOUND` и `PAYMENT_DATA.EPMT_STATE_CODE` остаются contradictions.

## APUS Reporting DB

Раздел 4 определяет:

- `PAYMENT_DATA_AGGR_DAY`;
- `PAYMENT_DATA_AGGR_LAST_DATE`.

При этом раздел 3.4 утверждает, что `CF.PAYMENT_DATA` предназначена для синхронизации с одноименной таблицей в другой отчетной области. Раздел 4 не содержит схемы этой `APUS.PAYMENT_DATA`, поэтому она сохранена только как упомянутая, но не определенная таблица (`CONTR-DB-002`, `Q-DB-003`).

## Неоднозначности источника

1. `SC_IDEN_MTD_PREFIX_MAP` определена дважды с несовместимыми наборами колонок (`CONTR-DB-001`). Вторая структура по полям и описанию похожа на справочник групп услуг, но canonical model **не переименовывает** ее без evidence.
2. `MESSAGE.OUTBOUND` в одном описании дважды связывает значение `0` с противоположными направлениями (`CONTR-DB-003`).
3. `PAYMENT_DATA.EPMT_STATE_CODE` использует code `2` для двух разных значений статуса (`CONTR-DB-004`).
4. `MESSAGE` и `AMG_MSG` продублированы с одинаковыми определениями; это обработано как повтор источника, а не как две физические таблицы.

## Целевой визуальный просмотр

DOCX отрендерен в 36 страниц. Визуально проверены:

- страницы 1-2 - внутреннее название и история версий;
- representative страницы схемы CF, включая конфликт `SC_IDEN_MTD_PREFIX_MAP`;
- страницы 34-36 - APUS Reporting DB и обе агрегатные таблицы.

Табличная структура при render согласуется с programmatic extraction.

## Сопоставление физической и доменной моделей

Сопоставление хранится отдельно в `physical-domain-mapping.yaml`. Оно не превращает физические таблицы в domain entities. В частности:

- `PAYMENT_DATA` связан и с `Payment`, и с денормализованным контекстом invoice/statement;
- `PAYMENT_EXT` - дополнительная физическая проекция `Payment`;
- `STATEMENT_EXT` - историческая физическая проекция `InvoiceStatement`;
- `SERVICE_TYPES` - физическая конфигурация для `ServiceType`;
- identification tables - физическая конфигурация для `IdentificationType`;
- `SC_SERVICE_FEE` - физическая конфигурация для `ServiceFeeTariff`;
- `PAYMENT_DATA_AGGR_DAY` - отчетная проекция `Payment`.

`PRE_SESSION_STATE`, категории организаций и журнал messages/operations не повышены до новых domain entities автоматически.

## Временная актуальность

Совпадение части concepts со specification 2023 года и имен сообщений (`INVOICE_PAY_APUS`, `DAY_PAYMENTS`) с контрактом 2025 года может поддерживать концептуальную преемственность, но **не** подтверждает сохранение физических таблиц 2015 года. Текущая схема остается открытым вопросом `Q-DB-001`.
