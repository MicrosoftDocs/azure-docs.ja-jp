---
title: Azure Stream Analytics での構成可能なしきい値ベースのルール
description: この記事では、Azure Stream Analytics で、参照データを使用して、設定可能なしきい値ベースのルールを含むアラート ソリューションを実現する方法について説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: 2f9d132084f0254486be533daea6b54239f4e450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019977"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Azure Stream Analytics での、設定可能なしきい値に基づいたルールの処理
この記事では、Azure Stream Analytics で、参照データを使用して、設定可能なしきい値に基づいたルールを含むアラート ソリューションを実現する方法について説明します。

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>シナリオ:調整可能なルールしきい値ベースのアラート
ストリーム化された受信イベントが特定の値に達した、またはストリーム化された受信イベントに基づいた集計値が特定のしきい値を超えた場合にアラートを出力として生成する必要がある場合があります。 固定値の、または事前定義の静的しきい値と値を比較する Stream Analytics クエリを簡単に設定できます。 固定値のしきい値は、単純な数値比較 (より大きい、より小さい、および等値) を使用してストリーミング クエリ構文にハードコーディングすることができます。

場合によっては、しきい値の値が変化するたびにクエリの構文を編集する必要のない、より簡単な方法でしきい値の値を設定する必要があります。 また、異なる種類のデバイス上に異なるしきい値が設定された、複数のデバイスまたはユーザーを、同じクエリで処理する必要がある場合もあります。 

このパターンは、入力データをフィルター処理することにより、しきい値を適用するデバイスの種類を個別に選択したり、出力にどのフィールドを含めるかを選択して、しきい値を動的に設定するために使用できます。

## <a name="recommended-design-pattern"></a>推奨される設計パターン
アラートのしきい値のルックアップとして、Stream Analytics ジョブへの参照データ入力を使用します。
- 1 つのキーに 1 つの値を含む参照データにしきい値の値を格納します。
- キー列の参照データとストリーミング データ入力イベントを結合します。
- 参照データからのキー付きの値をしきい値として使用します。

## <a name="example-data-and-query"></a>サンプル データとクエリ
この例では、1 分間の時間枠内のデバイスからの受信ストリーミング データの集計値が、参照データとしてルール内に指定されている規定値と一致する場合にアラートが生成されます。

クエリでは、各 deviceId および、その deviceId の各 metricName ごとに、0 から 5 のディメンションを GROUP BY に設定できます。 フィルター値が一致するイベントのみがグループ化されます。 グループ化されると、60 秒のタンブリング ウィンドウについて Min、Max、Avg のウィンドウ集計が計算されます。 次に、参照データに設定されたしきい値に従って、集計値に対するフィルターが計算され、アラート出力イベントが生成されます。

たとえば、**rules** という名前の参照データ入力と、**metrics** という名前のストリーム データ入力を含む Stream Analytics ジョブがあったとします。 

## <a name="reference-data"></a>参照データ
この例の参照データは、しきい値ベースのルールを表現する方法を示しています。 JSON ファイルでは、Azure BLOB ストレージの中に参照データが格納されており、その BLOB ストレージ コンテナーは、**rules** という名前の参照データ入力として使用されています。 後で、ストリーム ジョブを停止したり開始したりすることなく、この JSON ファイルを上書きして、ルール設定を置き換えることができます。

- サンプル ルールは、CPU が値 `90` % を超えた (平均がより大きい、または等しい) 場合の調整可能なアラートを表すため使用されています。 `value` フィールドは、必要に応じて設定できます。
- ルールの中の **operator** フィールドは、この後の `AVGGREATEROREQUAL` で動的に解釈されるクエリ構文です。 
- このルールは、値が `C1` の特定のディメンション キー `2` のデータをフィルター処理します。 その他のフィールドは空の文字列で、このイベント フィールドを条件に、入力ストリームがフィルター処理されないことを示しています。 必要に応じて、他の一致するフィールドをフィルター処理するよう、追加の CPU ルールを設定できます。
- すべての列を出力アラート イベントに含める必要はありません。 この場合、`includedDim` のキー番号 `2` は `TRUE` に設定されており、ストリーム内のイベント データのフィールド番号 2 が、条件を満たす出力イベントに含まれることを示しています。 他のフィールドはアラート出力に含まれませんが、フィールド リストは調整することができます。


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>ストリーミング クエリの例
この例では、Stream Analytics クエリによって、上記の例の **rules** 参照データが、**metrics** という名前のデータの入力ストリームに結合されます。

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>入力イベント データのストリーミングの例
この例では、JSON データは、上記のストリーミング クエリで使用されている **metrics** 入力データを表します。 

- 1 分間の間に、値 `T14:50` の 3 つのサンプル イベントが表示されています。 
- 3 つすべてに同じ値 `978648` の `deviceId` が含まれています。
- イベントごとに CPU のメトリック値が異なり、それぞれ `98`、`95`、`80` となっています。 最初の 2 つのサンプル イベントのみが、ルールに規定されている CPU アラート ルールを超えています。
- アラート ルールの includeDim フィールドのキー番号は 2 でした。 サンプル イベントで対応するキー 2 フィールドの名前は `NodeName` です。 3 つのサンプル イベントの値はそれぞれ `N024`、`N024`、`N014` です。 高 CPU 使用率アラートの条件に一致する唯一のデータである、ノード `N024` のみが出力されます。 `N014` は、高 CPU しきい値を満たしていません。
- アラート ルールは、キー番号 2 の `filter` のみに対し設定でき、これは、サンプル イベントの `cluster` フィールドに対応します。 3 つのサンプル イベントのすべての値が `C1`で、フィルター条件に一致します。

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>出力例
この出力例の JSON データは、参照データに定義されている CPU しきい値ルールに基づいて、1 つのアラート イベントが生成されたことを示します。 出力イベントには、アラート名だけでなく、計算 (平均、最小、最大) に含まれたフィールドの集計値が含まれています。 出力イベント データには、ルールの設定に従って、フィールド キー番号 2 `NodeName` の値 `N024` が含まれています。 (JSON は、読みやすくするため、改行を表示するよう変更されています。)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
