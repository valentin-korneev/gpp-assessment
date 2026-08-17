# Scope assessment GPP

## Текущая фаза

AS-IS evidence collection. Текущая canonical state включает `SRC-DB-001` - historical physical database model version 003 от 09.06.2015. CF Transaction DB и APUS Reporting DB моделируются раздельно; документированные tables/columns/Oracle types/keys/references вынесены в normalized physical model и Mermaid ERD. Physical tables не отождествляются с domain entities, mapping хранится отдельно, а logical FK явно отделены от подтвержденных physical constraints. По сообщению stakeholder это, вероятно, наиболее актуальная доступная DB-документация в переданном source set, но current production applicability схемы 2015 года не подтверждена.

## Объект assessment

Government Payment Portal (GPP / HÖP) Азербайджана рассматривается как централизованная платежная инфраструктура для приема, обработки и расчетов по бюджетным и массовым платежам, а также для взаимодействия с подключенными организациями, payment channels и внешними расчетными системами.

Функциональная спецификация 05.05.2023 позволяет уточнить рабочую boundary:

- внутри GPP находятся именованные infrastructure modules `WebPortal`, `WebPortalVC`, `PacPmtProc`, mobile applications, `MobilApi`, `PaymentService`, `TellerWP (BankPayment)`, `AdminConsole`, `Loader Generators`, `LVPCSS`, `IPSClient`;
- KXM, бюджетные организации, банки, card processing centers, service centers и иные PSP рассматриваются как external participant organizations;
- XÖHKS и Instant Payment System рассматриваются как внешние payment/settlement systems;
- неназванные core processing/persistence components пока остаются неизвестными и отслеживаются через `Q-ARCH-001`.

Предыдущее рабочее предположение `ASM-SCOPE-001` superseded этой evidence-based boundary.

## Temporal scope

- CISMS зафиксирован публичным источником CBA как предшествующая инфраструктура, запущенная 11.07.2008.
- GPP зафиксирован CBA как портал, запущенный в 2012 году с использованием инфраструктурных возможностей CISMS.
- Правила проведения операций через GPP утверждены Правлением CBA 13.08.2015. Их roles, access/security, operational-day, backup/DR и technology/deployment сведения используются как `historical`, а не как current production state.
- Внутренняя functional specification имеет внутреннюю дату 05.05.2023; технические утверждения из нее считаются `current-at-source-date`, а не автоматически current.
- Собственные пользовательские каналы `gpp.az` и mobile app прекращены с 10.01.2024 и не входят в текущий AS-IS как действующие каналы.
- Для `MobilApi` и остальных modules из specification 2023 года текущий production status требует подтверждения (`Q-CUR-001`).
- После прекращения собственных web/mobile channels платежи через GPP продолжают быть доступны через интегрированные internet/mobile services банков и небанковских PSP.
- System-level эксплуатация GPP подтверждена официальным обзором CBA за 2025 год: через GPP выполнено 114,4 млн платежей на сумму 8,6 млрд манатов. Это не подтверждает component-level currentness модулей из specification 2023 года.
- Integration contract `SRC-INT-001` имеет внутреннюю дату 18.08.2025 и используется как `current-at-source-date` для documented interface semantics; он не доказывает runtime availability referenced URLs или production endpoints.
- Physical database source `SRC-DB-001` имеет internal history version `003` от 09.06.2015. Он используется как historical physical model: schema details подтверждены только на дату источника, а текущая CF/APUS topology, tables и constraints остаются unverified (`Q-DB-001`).

## Source-family constraint

Файлы с filename hints `v005` и `v006` рассматриваются совместно как source family. Их filename order не используется как доказательство precedence: оба файла внутри содержат document id `GPP_System Functional Specification_v001`, history version `0.01` и дату `05.05.2023`.

Shared content консолидируется без дублирующих facts. Source-specific delta сохраняется в `01-evidence/source-notes/gpp-functional-specification-reconciliation.md` и через `Q-SRC-001`.

## Ограничения текущей итерации

- Authoritative/current редакция functional specification не определена.
- Внутренние sources 2023 года содержат operational values и screenshots; sensitive values не переносятся в canonical model без необходимости.
- Authoritative WSDL artifacts для `ServiceCompanyWS`, `GPPPaymentWS` и `SCVirtualCabinetWS` не предоставлены. SOAP normalization ограничена abstract WSDL/XSD reconstruction из documented envelopes, operation tables и type fragments; binding/service/SOAPAction/production endpoints не придумываются. Для reconciliation HTTP/XML inline XSD восстанавливается отдельно и сохраняет source contradictions без молчаливого исправления.
- Historical sources 2015 года теперь подтверждают не только Oracle persistence/replication и three-site DR topology, но и детальную тогдашнюю physical schema CF/APUS. Это не подтверждает current core payment processing, current physical tables/constraints, runtime/deployment topology или database architecture; для physical model открыт `Q-DB-001`.
- Findings по source quality не создаются как findings системы.
