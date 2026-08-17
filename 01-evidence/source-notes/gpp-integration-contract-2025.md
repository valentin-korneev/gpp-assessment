# SRC-INT-001 - интеграционный контракт 1.16 (18.08.2025)

## Предварительная проверка

- Внутренний ID документа: `CMA-SS 550-19 TN 070 1.16`.
- Внутренняя версия/дата: `1.16`, `18.08.2025`; уверенность в версии высокая, поскольку титульный блок и история версий согласованы.
- Профиль загрузки: `integration-contract`.
- Авторитетность: внутренняя интеграционная спецификация.
- Актуальность: только `current-at-source-date`; документ задает ожидаемый контракт и сам по себе не доказывает доступность указанных URL во время выполнения.
- Чувствительность: `internal`. Внутренние IP-адреса не перенесены в canonical model, поскольку они не нужны для подтверждения семантики интерфейсов.
- Выполнен целевой визуальный просмотр: схема бизнес-процесса на странице 7 и таблицы/схема сверки на страницах 78-83.

## Обработка ссылок на ресурсы

Документ содержит URL на WSDL, но сами WSDL artifacts не входят в предоставленный источник. Поэтому они не считаются исходными machine-readable contracts.

| Ресурс | Роль в документе | Статус в assessment |
|---|---|---|
| `ServiceCompanyWS.xml` | ссылка на WSDL SOAP service HÖP -> XT | `referenced-unavailable` |
| `GPPPaymentWS.xml` | ссылка на WSDL SOAP service XT -> HÖP | `referenced-unavailable` |
| `SCVirtualCabinetWS.xml` | ссылка на WSDL callback service HÖP -> XT | `referenced-unavailable` |
| test URL GPPPaymentWS | документированная ссылка на test endpoint | `referenced-unverified` |
| production endpoints | должны предоставляться отдельно после приемки тестирования | не предоставлены |

## Классификация интерфейсов до нормализации

| Canonical ID | Документированный механизм | Направление | Классификация | Основание |
|---|---|---|---|---|
| `IF-GPP-009` | ServiceCompanyWS | HÖP -> XT | `soap` | SOAP 1.1 envelopes, messageHeader, operations, примеры SOAP Fault |
| `IF-GPP-008` | GPPPaymentWS | XT -> HÖP | `soap` | SOAP 1.1 envelopes, operations, ссылка на WSDL и test endpoint |
| `IF-GPP-010` | SCVirtualCabinetWS | HÖP -> XT | `soap` | SOAP operations обратного вызова `CompletePayment` / `CompletePaymentWithError` |
| `IF-GPP-011` | платежная информация для сверки | HÖP -> XT | `http-xml` | явный HTTP POST + XML body + custom media type; inline XSD |
| `IF-GPP-012` | уведомление банка XT о зачислении | Bank -> XT | `unknown` | формат информации рекомендован, но transport/method отсутствуют |
| `IF-GPP-013` | идентификация плательщика через IAMAS | HÖP -> IAMAS | `unknown` | документирован real-time вызов внешнего web-service, protocol не указан |
| `IF-GPP-014` | идентификация плательщика через AVIS | HÖP -> AVIS | `unknown` | документирован real-time вызов внешнего web-service, protocol не указан |

Типы debt-notification на страницах 75-77 **не повышаются до отдельного interface**, поскольку источник содержит фрагменты data model без имени operation, привязки к service и направления.

## Реконструкция SOAP flows

### ServiceCompanyWS - HÖP -> XT

Operations в real-time режиме:

1. `GetAccountInfo`
2. `GetAccountInfoByDocumentNum`
3. `GetInvoiceInfo`
4. `GetInvoiceInfoByDocumentNum`
5. `GetInvoiceInfoByServiceCodeList`
6. `GetPayableAmountDetail`
7. `NotifyAboutPayment`
8. `CheckIfPaymentCancelAllowed`
9. `NotifyAboutPaymentCancel`
10. `NotifyAboutError`

Запросы используют SOAP `messageHeader`. `transactionID` является значением корреляции на уровне процесса, общим для связанных сообщений, и генерируется стороной, начинающей процесс. Для operations обычно документируются HTTP 200/500, successful response и SOAP Fault/`WSException`.

### GPPPaymentWS - XT -> HÖP

Operations в real-time режиме:

- `GetAccountInfo` - необязателен в процессе, если у XT уже есть необходимые данные FIN/VÖEN;
- `InitiatePayment` - запускает обработку платежа и в показанном XML response возвращает redirect URL;
- `NotifyAboutError` - сообщает об ошибках валидации данных, полученных от HÖP;
- `GetBankList` - получает банковские данные, необходимые при инициации платежа.

### SCVirtualCabinetWS - callback HÖP -> XT

Business process явно асинхронен относительно `InitiatePayment`: XT инициирует платеж, затем HÖP позднее вызывает callback service, размещенный на стороне XT.

- `CompletePayment` - callback успешного завершения; пустой response подтверждает прием.
- `CompletePaymentWithError` - callback неуспешного завершения с error payload.

Сами callback calls остаются SOAP request/response exchanges. Политика SOAP retry/backoff не документирована.

## HTTP/XML сверка

Это отдельный non-SOAP contract.

- Метод: `POST`.
- Тип media: `application/gpp.message+xml`.
- Сообщения: `INVOICE_PAY_APUS`, `DAY_PAYMENTS`, `DAY_PAYMENTS_FROM_BANK`.
- Периодичность: первое сообщение имеет настраиваемый интервал, в источнике указано 30 секунд на дату документа; суточные сообщения формируются в конце операционного дня HÖP.
- Response body: не требуется.
- Подтверждение: HTTP 200 означает, что получатель может обработать или сохранить сообщение.
- Retry: любой non-200 приводит к повторной отправке в следующем цикле генерации.
- Уникальность платежа: `receiptNumber`.
- Идентификатор/reference сообщения: `UserID + SerialNumber`.
- PDF содержит inline XSD для `Data/Header/Msg/Rows/Row`; он реконструирован как `NORM-XML-001`, а не считается предоставленным отдельным XSD artifact.

Endpoint, профиль authentication/TLS, timeout, предел числа retry и backoff policy не определены.

## Сохраненные противоречия источника

1. Для `DAY_PAYMENTS` и `DAY_PAYMENTS_FROM_BANK` в Content-Type показан параметр `type=INVOICE_PAY_APUS`, хотя XML `Msg/Type` использует собственные имена сообщений (`CONTR-INT-001`).
2. Для `CompletePaymentWithError` показан `CompletePaymentResponse` как wrapper успешного ответа (`CONTR-INT-002`).
3. XML response `InitiatePayment` содержит `redirectURL`, тогда как соседняя таблица описывает `return` как `ServiceAccount` (`CONTR-INT-003`).
4. XSD сверки делает `BranchID` обязательным и ограничивает `amount` двумя знаками после запятой и десятью цифрами всего, тогда как field table/examples противоречат этим ограничениям (`CONTR-INT-004`).

Нормализация сохраняет наблюдаемые формы источника и фиксирует ограничения, а не исправляет их молча.
