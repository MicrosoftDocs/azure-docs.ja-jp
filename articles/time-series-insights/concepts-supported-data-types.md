---
title: サポートされているデータ型 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 でサポートされているデータ型について説明します。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: e6fd405d1969a2f40a5f0c3466a57fbec60723e9
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141161"
---
# <a name="supported-data-types"></a>サポートされるデータ型

次の表に、Azure Time Series Insights Gen2 でサポートされているデータ型を示します

| データ型 | 説明 | 例 | Parquet のプロパティ列名
|---|---|---|---|
| **bool** | 2 つの状態 (`true` または `false`) のいずれかを持つデータ型。 | `"isQuestionable" : true` | isQuestionable_bool
| **datetime** | 特定の時点を表します。通常、日時形式で表されます。 [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 形式で表されます。 datetime プロパティは、常に UTC 形式で格納されます。 正しく書式設定されている場合は、タイム ゾーン オフセットが適用され、その後 UTC に格納される値が適用されます。 環境のタイムスタンプ プロパティと datetime オフセットの詳細については、[この](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)セクションを参照してください。 | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` | eventProcessedLocalTime_datetime
| **double** | 倍精度 64 ビット数  | `"value": 31.0482941` | value_double
| **long** | 符号付き 64 ビット整数  | `"value" : 31` | value_long
| **string** | テキスト値。有効な UTF-8 で構成されている必要があります。 null 値と空の文字列は同様に扱われます。 |  `"site": "DIM_MLGGG"` | site_string
| **dynamic** | 配列またはプロパティ バッグ (ディクショナリ) で構成される複合 (非プリミティブ) 型。 現時点で dynamic として格納されるのは、プリミティブの文字列化された JSON 配列、または TS ID やタイムスタンプ プロパティが含まれないオブジェクトの配列のみです。 どのようにオブジェクトがフラット化され、配列がアンロールされるかについては、この[記事](./concepts-json-flattening-escaping-rules.md)を参照してください。 この型として格納されているペイロード プロパティには、Azure Time Series Insights Gen2 Explorer と `GetEvents` クエリ API を通してアクセスできます。 |  `"values": "[197, 194, 189, 188]"` | values_dynamic

## <a name="sending-mixed-data-types"></a>データ型が混在しているデータの送信

Azure Time Series Insights Gen2 環境は、厳密に型指定されています。 デバイスまたはタグにより、あるデバイス プロパティの異なる複数のデータ型が送信されると、値は 2 つの別の列に格納されるので、API 呼び出しで時系列モデル変数の式を定義するときには [coalesce() 関数](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions)を使用する必要があります。

Azure Time Series Insights Explorer には、同じデバイス プロパティの別個の列を自動結合する方法が用意されています。 下の例では、センサーから、Long または Double の両方の可能性がある `PresentValue` プロパティが送信されます。 `PresentValue` プロパティの、格納されているすべての値に対して (データ型とは関係なく) クエリを実行する場合は、`PresentValue (Double | Long)` を選択すると複数の列が自動的に結合されます。

[![Explorer の自動結合](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>オブジェクトと配列

オブジェクトや配列などの複合型は、イベント ペイロードの一部として送信できます。 入れ子になったオブジェクトはフラット化され、配列は、環境構成と JSON 構造に応じて、`dynamic` として格納されるか、またはフラット化されて複数のイベントが生成されます。 [JSON のフラット化とエスケープの規則](./concepts-json-flattening-escaping-rules.md)の詳細については、こちらをご覧ください。

## <a name="next-steps"></a>次のステップ

* [JSON のフラット化とエスケープのルール](./concepts-json-flattening-escaping-rules.md)を読み、イベントの格納方法を理解する。

* 使用環境の[スループットに関する制限事項](./concepts-streaming-ingress-throughput-limits.md)を理解する。

* ストリーミング データを取り込むための[イベント ソース](concepts-streaming-ingestion-event-sources.md)について学習する。
