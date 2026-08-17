# Source note: GPP operational rules 2015

## Source preflight

- Source: `SRC-OPS-001`.
- Title: `Hökumət Ödəniş Portalı vasitəsilə əməliyyatların aparılması Qaydaları`.
- Дата утверждения: 13.08.2015, Правление Центрального банка.
- Ingest profile: `normative-operational`.
- Currentness: `historical`.
- Stakeholder clarification: эта редакция была передана assessment как последняя доступная (`EV-STK-003`). Это не является доказательством того, что operational/technology details 2015 года остаются текущими.
- Filename содержит hint `V14`, но в документе нет соответствующей internal version metadata; filename не используется как version authority.
- Sensitivity: source содержит internal operational details. Canonical extraction абстрагирует hostnames, filesystem paths и executable commands, если точное значение не требуется для evidence.

## Архитектурная ценность

Source используется для:

- ролей участников, пользователей и администраторов;
- исторических access/security administration expectations;
- operational day и next-business-day settlement semantics;
- backup/archive process;
- disaster recovery topology и procedures;
- исторического infrastructure/technology context.

Source не используется как подтверждение current production technologies или deployment.

## Targeted visual review

Страница 13 Appendix 3 содержит архитектурно значимую DR/topology diagram. Визуальный просмотр подтверждает:

- Main и Alternative sites находятся внутри Main Center;
- оба используют один shared disk array;
- Backup site расположен ниже границы центра в отдельном Backup Center;
- Backup site связан со своим disk array;
- Oracle replication показана от main contour к backup contour.

Следующий текст добавляет failover/failback procedure semantics. Canonical Mermaid reconstruction сохранена в `02-as-is/diagrams/historical-dr-topology-2015.mmd`.

## Temporal handling

Все source-derived deployment, technology, security и operations facts имеют `source_date: 2015-08-13` и `currentness: historical`. Stakeholder confirmation, что это последняя переданная редакция, не повышает currentness этих фактов. Open questions отслеживают current operational day, security/access, backup и DR state.

## Sensitive-data handling

Raw source содержит concrete operational host/path/command examples. Они просмотрены в объеме, необходимом для понимания procedures, но не копируются в canonical evidence, facts, diagrams или analysis, поскольку точные значения не нужны для подтверждения архитектурного смысла.
