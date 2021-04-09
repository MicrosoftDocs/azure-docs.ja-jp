---
title: JSON のフラット化とエスケープのルール - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 での JSON のフラット化、エスケープ、および配列の処理について説明します。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.openlocfilehash: 9f768982e69f785c146f026040a91f7a63eef64c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673453"
---
# <a name="json-flattening-escaping-and-array-handling"></a>JSON のフラット化、エスケープ、および配列処理

Azure Time Series Insights Gen2 環境では、特定の名前付け規則に従って、ウォームおよびコールド ストアの列が動的に作成されます。 イベントが取り込まれると、一連のルールが JSON ペイロードとプロパティ名に適用されます。 これには、特定の特殊文字のエスケープや、入れ子になった JSON オブジェクトのフラット化も含まれます。 JSON の構造がイベントの格納とクエリの方法にどのように影響するかを理解できるように、これらのルールについて知っておくことが重要です。 全ルールの一覧については、次の表を参照してください。 また、例 A および B で、配列内の複数の時系列を効率的にバッチ処理する方法を示しています。

> [!IMPORTANT]
>
> * 以下のルールを確認してから、[Time Series ID プロパティ](./how-to-select-tsid.md)やイベント ソースの [timestamp プロパティ](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)を選択してください。 TS ID または timestamp が入れ子になったオブジェクト内にある場合、または以下の特殊文字が 1 つ以上含まれている場合は、指定したプロパティ名が、取り込みルールが適用された "*後*" の列名と一致していることを確認することが重要です。 以下の例 [B](concepts-json-flattening-escaping-rules.md#example-b) を参照してください。

| ルール | JSON の例 | [時系列式の構文](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Parquet のプロパティ列名
|---|---|---|---|
| Azure Time Series Insights Gen2 データ型は、列名の末尾に "_\<dataType\>" と追加されます | ```"type": "Accumulated Heat"``` | `$event.type.String` |`type_string` |
| イベント ソースの [timestamp プロパティ](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)は、Azure Time Series Insights Gen2 でストレージに "timestamp" として保存され、値は UTC で保存されます。 イベント ソースの timestamp プロパティはご自分のソリューションのニーズに合わせてカスタマイズできますが、ウォームおよびコールド ストレージ内の列名は "timestamp" です。 イベント ソースのタイムスタンプではないその他の datetime JSON プロパティは、上記のルールで説明したように、列名に "_datetime" が付加されて保存されます。  | ```"ts": "2020-03-19 14:40:38.318"``` |  `$event.$ts` | `timestamp` |
| 特殊文字を含む JSON プロパティ名。 [  \ および ' を含む JSON プロパティ名は、[' と '] を使用してエスケープされます。  |  ```"id.wasp": "6A3090FD337DE6B"``` |  `$event['id.wasp'].String` | `['id.wasp']_string` |
| [' と '] の間には、単一引用符と円記号の追加のエスケープがあります。 単一引用符は \’ のように記述され、円記号は \\\ のように記述されます。 | ```"Foo's Law Value": "17.139999389648"``` | `$event['Foo\'s Law Value'].Double` | `['Foo\'s Law Value']_double` |
| 入れ子になった JSON オブジェクトは、区切り記号としてピリオドを使用してフラット化されます。 最大 10 レベルの入れ子がサポートされています。 |  ```"series": {"value" : 316 }``` | `$event.series.value.Long`、`$event['series']['value'].Long` または `$event.series['value'].Long` |  `series.value_long` |
| プリミティブ型の配列は動的な型として格納されます。 |  ```"values": [154, 149, 147]``` | 動的な型は [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) API でのみ取得可能 | `values_dynamic` |
| オブジェクトを含む配列には、オブジェクトの内容に応じて、次の 2 つの動作があります。TS ID または timestamp プロパティが配列内のオブジェクトの中にある場合は、最初の JSON ペイロードによって複数のイベントが生成されるように配列がロールアウトされます。 これにより、複数のイベントを 1 つの JSON 構造にまとめることができます。 配列と同等である最上位レベルのプロパティは、アンロールされたオブジェクトごとに保存されます。 TS ID と timestamp が配列内に "*ない*" 場合は、全体が動的な型として保存されます。 | 例 [A](concepts-json-flattening-escaping-rules.md#example-a)、[B](concepts-json-flattening-escaping-rules.md#example-b) および [C](concepts-json-flattening-escaping-rules.md#example-c) を参照してください。
| 混合要素を含む配列はフラット化できません。 |  ```"values": ["foo", {"bar" : 149}, 147]``` | 動的な型は [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) API でのみ取得可能 | `values_dynamic` |
| JSON プロパティ名には 512 文字の制限があります。 名前が 512 文字を超える場合は、512 に切り捨てられ、' '_<'hashCode'>' が追加されます。 **注意**: これは、入れ子になったオブジェクトのパスを示す、フラット化されたオブジェクトから連結されたプロパティ名にも適用されます。 |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` |`"$event.data.items.datapoints.values.telemetry<...continuing to include all chars>.Double"` | `data.items.datapoints.values.telemetry<...continuing to 512 chars>_912ec803b2ce49e4a541068d495ab570_double` |

## <a name="understanding-the-dual-behavior-for-arrays"></a>配列のデュアル動作について

オブジェクトの配列は、データをどのようにモデル化したかに応じて、全体が格納されるか、複数のイベントに分割されます。 これにより、配列を使用してイベントをバッチ処理し、ルート オブジェクト レベルで定義されているテレメトリ プロパティの繰り返しを避けることができます。 バッチ処理によって、送信される Event Hubs または IoT Hub メッセージの量が減るため、メリットが得られることがあります。

ただし、オブジェクトを含む配列は、他の値のコンテキストでのみ意味を持つ場合があります。 複数のイベントを作成すると、データが無意味になります。 オブジェクトの配列がそのまま、動的な型として格納されるようにするには、次のデータ モデリングのガイダンスに従い、[例 C](concepts-json-flattening-escaping-rules.md#example-c) を参照してください。

### <a name="how-to-know-if-my-array-of-objects-will-produce-multiple-events"></a>オブジェクトの配列が複数のイベントを生成するかどうかを確認する方法

1 つ以上の Time Series ID プロパティが配列内のオブジェクト内で入れ子になっている場合、"*または*" イベント ソースの timestamp ププロパティが入れ子になっている場合は、インジェスト エンジンによって分割され、複数のイベントが作成されます。 TS ID や timestamp に指定したプロパティ名は、上のフラット化ルールに従う必要があります。したがって、JSON の構造が示されます。 次の例を参照し、[Time Series ID プロパティを選択する](./how-to-select-tsid.md)方法に関するガイドをご覧ください。

### <a name="example-a"></a>例 A

オブジェクト ルートの Time Series ID と入れ子になった timestamp\
**環境の Time Series ID:** `"id"`\
**イベント ソースのタイムスタンプ:** `"values.time"`\
**JSON ペイロード:**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**Parquet ファイルの結果:** \
上記の構成とペイロードでは、3 つの列と 4 つのイベントが生成されます。

| timestamp  | id_string | values.value_double
| ---- | ---- | ---- |
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` |
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` |
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` |
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` |

### <a name="example-b"></a>例 B

1 つのプロパティが入れ子になった複合 Time Series ID\
**環境の Time Series ID:** `"plantId"` および `"telemetry.tagId"`\
**イベント ソースのタイムスタンプ:** `"timestamp"`\
**JSON ペイロード:**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**Parquet ファイルの結果:** \
上記の構成とペイロードでは、4 つの列と 6 つのイベントが生成されます。

| timestamp  | plantId_string | telemetry.tagId_string | telemetry.value_double
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31.149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20.560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A7`` | -30.9918 |  
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A4`` | 19.960796 |

### <a name="example-c"></a>例 C

オブジェクト ルートの Time Series ID と timestamp\
**環境の Time Series ID:** `"id"`\
**イベント ソースのタイムスタンプ:** `"timestamp"`\
**JSON ペイロード:**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
            "value": 120
        },
        {
            "value": 124
        }
    ]
}
```

**Parquet ファイルの結果:** \
上記の構成とペイロードでは、3 つの列と 1 つのイベントが生成されます。

| timestamp  | id_string | datapoints_dynamic  
| ---- | ---- | ---- |
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>次のステップ

* 使用環境の[スループットに関する制限事項](./concepts-streaming-ingress-throughput-limits.md)を理解する
