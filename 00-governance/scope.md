# Scope assessment GPP

## Текущая фаза

AS-IS evidence collection. Published baseline `SNAPSHOT-003` дополнен текущим WIP по ограниченному повторному public-source pass: уточнены external identification sources, system-level эксплуатация GPP в 2025 году и логическое централизованное хранение reference/master data.

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
- Внутренняя functional specification имеет внутреннюю дату 05.05.2023; технические утверждения из нее считаются `current-at-source-date`, а не автоматически current.
- Собственные пользовательские каналы `gpp.az` и mobile app прекращены с 10.01.2024 и не входят в текущий AS-IS как действующие каналы.
- Для `MobilApi` и остальных modules из specification 2023 года текущий production status требует подтверждения (`Q-CUR-001`).
- После прекращения собственных web/mobile channels платежи через GPP продолжают быть доступны через интегрированные internet/mobile services банков и небанковских PSP.
- System-level эксплуатация GPP подтверждена официальным обзором CBA за 2025 год: через GPP выполнено 114,4 млн платежей на сумму 8,6 млрд манатов. Это не подтверждает component-level currentness модулей из specification 2023 года.

## Source-family constraint

Файлы с filename hints `v005` и `v006` рассматриваются совместно как source family. Их filename order не используется как доказательство precedence: оба файла внутри содержат document id `GPP_System Functional Specification_v001`, history version `0.01` и дату `05.05.2023`.

Shared content консолидируется без дублирующих facts. Source-specific delta сохраняется в `01-evidence/source-notes/gpp-functional-specification-reconciliation.md` и через `Q-SRC-001`.

## Ограничения текущей итерации

- Authoritative/current редакция functional specification не определена.
- Внутренние sources 2023 года содержат operational values и screenshots; sensitive values не переносятся в canonical model без необходимости.
- Machine-readable contracts не предоставлены. Из specification извлекается только interface classification и confirmed relationship; REST/SOAP/file details не придумываются.
- Core payment processing, persistence, runtime/deployment topology и database architecture в текущем source family не описаны достаточно для подтвержденной модели.
- Findings по source quality не создаются как findings системы.
