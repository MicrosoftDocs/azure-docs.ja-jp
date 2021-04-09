---
title: Azure Time Series Insights Gen2 での取り込みおよびフラット化ルールの今後の変更 | Microsoft Docs
description: 取り込み規則の変更
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: lyhughes
ms.openlocfilehash: 56a1d5aab2f665f9c5bd8f6fa322f35e55483c7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995235"
---
# <a name="upcoming-changes-to-json-flattening-and-escaping-rules-for-new-environments"></a>新しい環境での JSON フラット化とエスケープ規則に関する今後の変更

> [!IMPORTANT]
> これらの変更は、"*新しく作成された*" Microsoft Azure Time Series Insights Gen2 環境にのみ該当します。 この変更は、Gen1 環境には該当しません。

Azure Time Series Insights Gen2 環境では、特定の一連の名前付け規則に従って、ストレージ列が動的に作成されます。 イベントが取り込まれると、Time Series Insights により JSON ペイロードとプロパティ名に一連の規則が適用されます。 JSON データのフラット化および格納方法の変更は、新しい Azure Time Series Insights Gen2 環境に、2020 年 7 月から適用されています。 この変更は、次のような場合に影響を及ぼします。

* お使いの JSON ペイロードに入れ子になったオブジェクトが含まれている。
* お使いの JSON ペイロードに配列が含まれている。
* JSON プロパティ名に、`[` `\` `.` `'` の 4 つの特殊文字のいずれかが使用されている。
* 1 つ以上のお使いの Time Series (TS) ID プロパティが入れ子になったオブジェクト内にある。

新しい環境を作成し、お使いのイベント ペイロードに上記のケースが 1 つ以上該当する場合、お使いのデータは異なる方法でフラット化され、格納されます。 次の表に変更をまとめています。

| 現在の規則 | 新しい規則 | JSON の例 | 以前の列名 | 新しい列名
|---|---| ---| ---|  ---|
| 入れ子になった JSON は、区切り文字にアンダースコアが使用されフラット化されます。 |入れ子になった JSON は、区切り文字にピリオドが使用されフラット化されます。  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| 特殊文字はエスケープされません。 | 特殊文字 `.` `[`  `\` および `'` を含む JSON プロパティ名は、`['` と `']` を使用してエスケープされます。 `['` と `']` の間には、単一引用符とバックスラッシュのエスケープが追加されます。 単一引用符は `\'` と記述され、バックスラッシュは `\\` と記述されます。  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| プリミティブの配列は文字列として格納されます。 | プリミティブ型の配列は動的な型として格納されます。  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
オブジェクトの配列は常にフラット化され、複数のイベントを生成します。 | 配列内のオブジェクトに TS ID または timestamp プロパティのいずれも存在しない場合、オブジェクトの配列全体は動的な型として格納されます。 | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>新しい環境で推奨される変更

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>TS ID または timestamp プロパティがオブジェクト内で入れ子になっている場合

新しいデプロイでは、新しい取り込み規則を使用する必要があります。 たとえば、お使いの TS ID が `telemetry_tagId` である場合は、Azure Resource Manager テンプレートまたは自動展開スクリプトをすべて更新して、`telemetry.tagId` を環境の TS ID として構成する必要があります。 入れ子になった JSON のイベント ソースのタイムスタンプでも、この変更を行う必要があります。

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>ペイロードに入れ子になった JSON または特殊文字が含まれており、[時系列モデル](./concepts-model-overview.md)変数式の作成が自動化されている場合:

新しい取り込み規則が使用されるよう、[TypesBatchPut](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) を実行するお使いのクライアント コードを更新します。 たとえば、前の `"value": {"tsx": "$event.series_value.Double"}` の[タイム シリーズ式](/rest/api/time-series-insights/reference-time-series-expression-syntax)を、次のいずれかのオプションに更新します。

* `"value": {"tsx": "$event.series.value.Double"}`
* `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>次の手順

* [Azure Time Series Insights Gen2 のストレージとイングレス](./concepts-ingestion-overview.md)に関するページをご覧ください。

* [タイム シリーズ クエリ API](./concepts-query-overview.md) を使用してお使いのデータをクエリする方法をご覧ください。

* [新しいタイム シリーズ式の構文](/rest/api/time-series-insights/reference-time-series-expression-syntax)の詳細をご覧ください。