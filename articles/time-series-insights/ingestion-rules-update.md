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
ms.date: 08/12/2020
ms.custom: lyhughes
ms.openlocfilehash: cdf90614e1f766fc37e04a1416081bd35e19b74e
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168203"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>新しい環境における JSON フラット化とエスケープの規則に関する今後の変更

**これらの変更は*新しく作成された* Azure Time Series Insights Gen2 環境にのみ適用されます。これらの変更は、Gen1 環境には適用されません。**

Azure Time Series Insights Gen2 環境では、特定の一連の名前付け規則に従って、ストレージ列が動的に作成されます。 イベントが取り込まれると、一連のルールが JSON ペイロードとプロパティ名に適用されます。 JSON データのフラット化の方法および格納方法に対する変更は、新しい Azure Time Series Insights Gen2 環境に、2020 年 7 月に適用されます。 この変更は、次のような場合に影響を及ぼします。

* JSON ペイロードに入れ子になったオブジェクトが含まれている場合
* JSON ペイロードに配列が含まれている場合
* JSON プロパティ名に次の 4 つの特殊文字のいずれかが使用されている場合: [ \ . '
* 1 つ以上の TS ID プロパティが入れ子になったオブジェクト内にある場合。

新しい環境を作成し、イベント ペイロードに上記のケースが 1 つ以上該当する場合、データは異なる方法でフラット化、および格納されます。 変更の概要を次に示します。

| 現在の規則 | [新しい規則] | JSON の例 | 以前の列名 | 新しい列名
|---|---| ---| ---|  ---|
| 入れ子になった JSON は、区切り文字にアンダースコアを使用してフラット化されます |入れ子になった JSON は、区切り文字にピリオドを使用してフラット化されます  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| 特殊文字はエスケープされません | 特殊文字 . [  \ および ' を含む JSON プロパティ名は、[' および '] を使用してエスケープされます。 [' and '] の間には、単一引用符と円記号の追加のエスケープがあります。 単一引用符は \' のように記述され、円記号は \\\ のように記述されます  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` |
| プリミティブの配列は文字列として格納されます | プリミティブ型の配列は動的な型として格納されます  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
オブジェクトの配列は常にフラット化され、複数のイベントを生成します | 配列内のオブジェクトに TS ID または timestamp プロパティのいずれも存在しない場合、オブジェクトの配列全体は動的な型として格納されます | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>新しい環境で推奨される変更

### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>TS ID または timestamp プロパティがオブジェクト内で入れ子になっている場合

* 新しいデプロイでは、新しい取り込み規則を一致させる必要があります。 たとえば、TS ID が `telemetry_tagId` である場合は、`telemetry.tagId` を環境の TS ID として構成するために、Azure Resource Manager (ARM) テンプレートまたは自動展開スクリプトを更新する必要があります。 この変更は、入れ子になった JSON のイベント ソースのタイムスタンプにも必要です。

### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>ペイロードに入れ子になった JSON または特殊文字が含まれており、[時系列モデル](.\time-series-insights-update-tsm.md)変数式の作成が自動化されている場合:

* 新しい取り込み規則に一致するように [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) を実行しているクライアント コードを更新します。 たとえば、前の `"value": {"tsx": "$event.series_value.Double"}` の[時系列式](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)は、次のいずれかのオプションに更新する必要があります。
  * `"value": {"tsx": "$event.series.value.Double"}`
  * `"value": {"tsx": "$event['series']['value'].Double"}`

## <a name="next-steps"></a>次のステップ

* [Azure Time Series Insights Gen2 のストレージとイングレス](./time-series-insights-update-storage-ingress.md)に関するページをご覧ください。

* [タイム シリーズ クエリ API](./concepts-query-overview.md) を使用してデータをクエリする方法の詳細をご覧ください。

* [新しいタイム シリーズ式の構文](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)の詳細をご覧ください。
