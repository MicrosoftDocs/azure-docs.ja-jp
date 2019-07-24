---
title: Azure Time Series Insights プレビューでのイベントの調整 | Microsoft Docs
description: Azure Time Series Insights プレビューでのイベントの調整を理解します。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: eb398ad621167ad9f9b245fb8aa98c6942b87668
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557429"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Azure Time Series Insights プレビューでのイベントの調整

この記事では、Azure Time Series Insights プレビュー クエリの効率を最大化するよう JSON ファイルを調整できます。

## <a name="best-practices"></a>ベスト プラクティス

Azure Time Series Insights プレビューにイベントを送信する方法について検討することが重要です。 具体的には、常に、以下を考慮する必要があります。

* ネットワーク上でできるだけ効率的にデータを送信する。
* 自分のシナリオにとってより適切に集計できる方法でデータを格納する。

クエリ パフォーマンスを最善に維持するには、次の手順に従ってください。

* 不要なプロパティを送信しない。 Time Series Insights プレビューは、従量課金制です。 クエリを実行するデータを格納して処理することをお勧めします。
* 静的データにインスタンス フィールドを使用する。 この手法によって、ネットワーク上で静的なデータを送信しないようにできます。 インスタンス フィールドは、タイム シリーズ モデルのコンポーネントであり、Time Series Insights GA サービスで参照データのように機能します。 インスタンス フィールドの詳細については、「[Time Series Models (タイム シリーズ モデル)](./time-series-insights-update-tsm.md)」を参照してください。
* 2 つ以上のイベント間でディメンション プロパティを共有します。 この手法によって、ネットワーク経由でより効率的にデータを送信できます。
* 深い入れ子の配列を使用しない。 Time Series Insights プレビューでは、オブジェクトを含む、入れ子になった最大 2 レベルの配列をサポートしています。 Time Series Insights プレビューでは、メッセージ内の配列は、プロパティ値のペアを使用する複数のイベントにフラット化されます。
* すべて、またはほとんどのイベントに対して数個のメジャーのみが存在する場合は、これらのメジャーを同じオブジェクト内の個別のプロパティとして送信することをお勧めします。 個別に送信することでイベントの数が減少し、処理する必要があるイベントが少なくなるため、クエリのパフォーマンスを改善できることがあります。

## <a name="example"></a>例

次の例では、2 つ以上のデバイスが、メジャーや信号を送信するシナリオを使用しています。 メジャーまたは信号には、*フロー レート*、*エンジン オイル圧*、*温度*、または*湿度*を使用できます。

次の例では IoT Hub メッセージが 1 つあり、外側の配列に共通のディメンション値の共有部分が含まれています。 外側の配列は、Time Series Instance データを使用してメッセージの効率を改善します。 Time Series Instance には、イベントごとに変更されないが、データ解析に役立つプロパティを提供するデバイス メタデータが含まれています。 ネットワーク経由で送信されるバイト数を削減し、メッセージをより効率的にするには、共通のディメンション値のバッチ処理と、Time Series Instance メタデータの使用を検討してください。

### <a name="example-json-payload"></a>JSON ペイロードの例

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

### <a name="time-series-instance"></a>Time Series Instance 
> [!NOTE]
> タイム シリーズ ID は*deviceId* です。

```JSON
{
    "timeSeriesId": [
      "FXXX"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "REVOLT SIMULATOR",
      "L2": "Battery System",
    }
  },
  {
    "timeSeriesId": [
      "FYYY"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "COMMON SIMULATOR",
      "L2": "Battery System",
    }
  },
```

Time Series Insights プレビューは、クエリ時に (フラット化の後に) テーブルを結合します。 このテーブルには、**Type** などの追加の列が含まれます。 次の例で、利用統計情報データの[調整](./time-series-insights-send-events.md#json)方法を示します。

| deviceId  | type | L1 | L2 | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | REVOLT SIMULATOR | Battery System | 2018-01-17T01:17:00Z |    1.0172575712203979 |    34.7 |
| `FXXX` | LINE_DATA    REVOLT | SIMULATOR |    Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

前の例では、次の点に注意してください。

* 静的プロパティは、ネットワーク経由で送信されるデータを最適化するために、Time Series Insights プレビューに格納されます。
* Time Series Insights プレビュー データは、インスタンスで定義されている *timeSeriesId* を使用して、クエリ時に結合されます。
* 2 層の入れ子が使用されます。これは、Time Series Insights プレビューでサポートされている最大です。 深く入れ子された配列は使用しないことが重要です。
* メジャーはほとんどないため、同じオブジェクト内の個別のプロパティとして送信されます。 たとえば、**series.Flow Rate psi**、**series.Engine Oil Pressure psi**、および **series.Flow Rate ft3/s** が一意な列です。

>[!IMPORTANT]
> インスタンス フィールドは、利用統計情報と共には格納されません。 **タイム シリーズ モデル**にメタデータと格納されます。
> 上記の表は、クエリ ビューを表しています。

## <a name="next-steps"></a>次の手順

これらのガイドラインを実践するには、[Azure Time Series Insights プレビューのクエリ構文](./time-series-insights-query-data-csharp.md)に関するページを参照してください。 Time Series Insights プレビュー データ アクセス REST API のクエリ構文の詳細について説明します。

サポートされている JSON の調整の詳細については、「[サポートされている JSON 構造](./time-series-insights-send-events.md#json)」を参照してください。
