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
ms.date: 01/19/2021
ms.openlocfilehash: b0536a152797d17cba0930b3a142a7fb92eaf5ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685159"
---
# <a name="supported-data-types"></a>サポートされるデータ型

次の表に、Azure Time Series Insights Gen2 でサポートされているデータ型を示します

| データ型 | 説明 | 例 | [時系列式の構文](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Parquet のプロパティ列名
|---|---|---|---|---|
| **bool** | 2 つの状態 (`true` または `false`) のいずれかを持つデータ型。 | `"isQuestionable" : true` | `$event.isQuestionable.Bool` または `$event['isQuestionable'].Bool` | `isQuestionable_bool`
| **datetime** | 特定の時点を表します。通常、日時形式で表されます。 [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 形式で表されます。 datetime プロパティは、常に UTC 形式で格納されます。 正しく書式設定されている場合は、タイム ゾーン オフセットが適用され、その後 UTC に格納される値が適用されます。 環境のタイムスタンプ プロパティと datetime オフセットの詳細については、[この](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)セクションを参照してください。 | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` |  "eventProcessedLocalTime" がイベント ソースのタイムスタンプの場合: `$event.$ts`。 別の JSON プロパティの場合: `$event.eventProcessedLocalTime.DateTime` または `$event['eventProcessedLocalTime'].DateTime` | `eventProcessedLocalTime_datetime`
| **double** | 倍精度 64 ビット数  | `"value": 31.0482941` | `$event.value.Double` または `$event['value'].Double` |  `value_double`
| **long** | 符号付き 64 ビット整数  | `"value" : 31` | `$event.value.Long` または `$event['value'].Long` |  `value_long`
| **string** | テキスト値。有効な UTF-8 で構成されている必要があります。 null 値と空の文字列は同様に扱われます。 |  `"site": "DIM_MLGGG"`| `$event.site.String` または `$event['site'].String`| `site_string`
| **dynamic** | 配列またはプロパティ バッグ (ディクショナリ) で構成される複合 (非プリミティブ) 型。 現時点で dynamic として格納されるのは、プリミティブの文字列化された JSON 配列、または TS ID やタイムスタンプ プロパティが含まれないオブジェクトの配列のみです。 どのようにオブジェクトがフラット化され、配列がアンロールされるかについては、この[記事](./concepts-json-flattening-escaping-rules.md)を参照してください。 この型で保存されたペイロード プロパティには、Time Series Insights Explorer で `Explore Events` を選択して未加工のイベントを表示するか、クライアント側での解析のための [`GetEvents`](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) クエリ API を使用する方法でのみアクセスできます。 |  `"values": "[197, 194, 189, 188]"` | 時系列式での動的な型の参照はまだサポートされていません | `values_dynamic`

> [!NOTE]
> 64 ビット整数値がサポートされていますが、JavaScript の制限により、Azure Time Series Insights Explorer で安全に表現できる最大の数値は 9,007,199,254,740,991 (2^53-1) です。 これよりも大きい数値をデータ モデルで扱う場合、[時系列モデル変数](./concepts-variables.md#numeric-variables)を作成して値を[変換](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions)することによってサイズを縮小できます。

> [!NOTE]
> **String** 型で NULL 値は許可されません。
>
> * [時系列クエリ](/rest/api/time-series-insights/reference-query-apis)で表され、空の文字列 ( **''** ) の値を **NULL** と比較する [時系列式 (TSX)](/rest/api/time-series-insights/reference-time-series-expression-syntax) は同じように動作します。`$event.siteid.String = NULL` は `$event.siteid.String = ''` と同等です。
> * 元のイベントに空の文字列が含まれていた場合でも、API は **NULL** 値を返す可能性があります。
> * 比較または評価を行うためには、**String** 列に **NULL** 値が含まれていると想定せず、空の文字列と同じように扱ってください。

## <a name="sending-mixed-data-types"></a>データ型が混在しているデータの送信

Azure Time Series Insights Gen2 環境は、厳密に型指定されています。 デバイスまたはタグにより、あるデバイス プロパティの異なる複数のデータ型が送信されると、値は 2 つの別の列に格納されるので、API 呼び出しで時系列モデル変数の式を定義するときには [coalesce() 関数](/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions)を使用する必要があります。

Azure Time Series Insights Explorer には、同じデバイス プロパティの別個の列を自動結合する方法が用意されています。 下の例では、センサーから、Long または Double の両方の可能性がある `PresentValue` プロパティが送信されます。 `PresentValue` プロパティの、格納されているすべての値に対して (データ型とは関係なく) クエリを実行する場合は、`PresentValue (Double | Long)` を選択すると複数の列が自動的に結合されます。

[![Explorer の自動結合](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>オブジェクトと配列

オブジェクトや配列などの複合型は、イベント ペイロードの一部として送信できます。 入れ子になったオブジェクトはフラット化され、配列は、環境構成と JSON 構造に応じて、`dynamic` として格納されるか、またはフラット化されて複数のイベントが生成されます。 [JSON のフラット化とエスケープの規則](./concepts-json-flattening-escaping-rules.md)の詳細については、こちらをご覧ください。

## <a name="next-steps"></a>次のステップ

* [JSON のフラット化とエスケープのルール](./concepts-json-flattening-escaping-rules.md)を読み、イベントの格納方法を理解する。

* 使用環境の[スループットに関する制限事項](./concepts-streaming-ingress-throughput-limits.md)を理解する。

* ストリーミング データを取り込むための[イベント ソース](concepts-streaming-ingestion-event-sources.md)について学習する。
