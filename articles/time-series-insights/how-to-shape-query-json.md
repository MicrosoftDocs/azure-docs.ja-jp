---
title: JSON を調整するためのベスト プラクティス - Azure Time Series Insights クエリ | Microsoft Docs
description: JSON を調整して Azure Time Series Insights クエリの効率を改善する方法について説明します。
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 3d611806d31719899d249b29ed4b0ea499280252
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894908"
---
# <a name="shape-json-to-maximize-query-performance"></a>クエリのパフォーマンスを最大化するための JSON の調整 

この記事では、Azure Time Series Insights でお使いのクエリを最大限に効率化できるよう JSON を調整する方法を説明します。

## <a name="video"></a>ビデオ

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>ストレージのニーズを満たすように JSON を調整するためのベスト プラクティスについて説明します。</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>ベスト プラクティス

Time Series Insights にイベントを送信する方法を検討します。 つまり、常に次のようになるようにします。

1. ネットワーク上でできるだけ効率的にデータを送信する。
1. 使用するシナリオに適した形式で集計できるようお使いのデータが格納されていることを確認する。
1. 以下の Time Series Insights の最大プロパティ制限を超えないことを確認する。
   - S1 環境で 600 プロパティ (列)
   - S2 環境で 800 プロパティ (列)

> [!TIP]
> Azure Time Series Insights プレビューの[制限と計画](time-series-insights-update-plan.md)をご確認ください。

次のガイダンスは最適なクエリ パフォーマンスの確保に役立ちます。

1. プロパティ名としてタグ ID などの動的なプロパティは使用しない。 このように使用すると、プロパティの最大制限に達します。
1. 不要なプロパティを送信しない。 クエリのプロパティが必要でない場合は、送信しないことをお勧めします。 このようにすることで、ストレージの制限に達することを避けることができます。
1. [参照データ](time-series-insights-add-reference-data-set.md)を使用して、ネットワーク上で静的なデータを送信しないようにします。
1. 複数のイベントでディメンションのプロパティを共有して、ネットワーク経由でのデータ送信を効率化します。
1. 深い入れ子の配列を使用しない。 Time Series Insights では、オブジェクトを含む入れ子配列は 2 つのレベルの入れ子までサポートされています。 Time Series Insights では、メッセージ内の配列は、プロパティ値のペアを使用する複数のイベントにフラット化されます。
1. すべて、またはほとんどのイベントに対して数個のメジャーのみが存在する場合は、これらのメジャーを同じオブジェクト内の個別のプロパティとして送信することをお勧めします。 個別に送信することでイベントの数が減少し、処理する必要があるイベントが少なくなるため、クエリのパフォーマンスが向上します。 複数のメジャーが存在する場合、1 つのプロパティの値として送信すると、最大プロパティ制限に達する可能性を最小限に抑えられます。

## <a name="example-overview"></a>サンプルの概要

次の 2 つの例では、前述した推奨事項のイベントの送信方法を説明しています。 各例の後には、推奨事項の適用方法を示しています。

例では、複数のデバイスがメジャーや信号を送信するシナリオを使用しています。 メジャーまたは信号には、フロー レート、エンジン オイル圧、温度、または湿度を使用できます。 最初の例では、すべてのデバイスにわたって数個のメジャーがあります。 2 番目の例には、デバイスが多数あり、各デバイスは数多くの一意のメジャーを送信しています。

## <a name="scenario-one-only-a-few-measurements-exist"></a>シナリオ 1:メジャーが少ししかない

> [!TIP]
> それぞれのメジャーや信号は、別のプロパティまたは列として送信することをお勧めします。

次の例では Azure IoT Hub メッセージが 1 つあり、外側の配列に共通のディメンション値の共有部分が含まれています。 外側の配列は、参照データを使用してメッセージの効率を改善します。 参照データには、イベントごとに変更されないが、データ解析に役立つプロパティを提供するデバイス メタデータが含まれています。 共通のディメンション値をバッチ処理し、かつ参照データを使用することで、ネットワーク上に送信されるバイト数を減らしてメッセージを効率化できます。

Azure クラウドに送信されるときに JSON にシリアル化される [IoT デバイス メッセージ オブジェクト](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet)を使用して、Time Series Insights GA 環境に送信される次の JSON ペイロードを検討します。


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
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

* キー プロパティ **deviceId** がある参照データ テーブル:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | US |

* Time Series Insights イベント テーブル (フラット化後):

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | US | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - **deviceId** 列は、fleet 内のさまざまなデバイスの列見出しとして機能します。 **deviceId** 値をその独自のプロパティ名にすると、デバイスの合計数は 595 (S1 環境) または 795 (S2 環境) に制限されます。他に 5 つの列があります。
> - 不要なプロパティは回避されます (メーカーとモデルの情報など)。 プロパティは今後もクエリで使用されることはないため、これらがないことによってネットワークおよびストレージの効率性が向上します。
> - 参照データを使用すると、ネットワーク経由で転送されるバイト数を減らせます。 **messageId** と **deviceLocation** の 2 つの属性は、**deviceId** のキー プロパティを使用して結合されます。 このデータは、イングレス時に利用統計情報と結合され、その後、クエリのために Time Series Insights に格納されます。
> - Time Series Insights でサポートされる最大入れ子数である、2 つの入れ子層が使用されます。 深く入れ子された配列は使用しないことが重要です。
> - メジャーの数が少ないため、同じオブジェクト内の個別のプロパティとして送信されます。 ここでは、**series.Flow Rate psi** と **series.Engine Oil Pressure ft3/s** が一意な列です。

## <a name="scenario-two-several-measures-exist"></a>シナリオ 2:メジャーがいくつかある

> [!TIP]
> 測定値は、"type"、"unit"、"value" の 3 つ 1 組のメジャーとして送信することをお勧めします。

例の JSON ペイロード:

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* キー プロパティ **deviceId** および **series.tagId** がある参照データ テーブル:

   | deviceId | series.tagId | messageId | deviceLocation | type | unit |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Flow Rate | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi |
   | FYYY | pumpRate | LINE\_DATA | US | Flow Rate | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | US | Engine Oil Pressure | psi |

* Time Series Insights イベント テーブル (フラット化後):

   | deviceId | series.tagId | messageId | deviceLocation | type | unit | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | Flow Rate | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | Flow Rate | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | US | Flow Rate | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | US | Engine Oil Pressure | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - **deviceId** および **series.tagId** 列は、fleet 内のさまざまなデバイスとタグの列見出しとして機能します。 それぞれを独自の属性として使用すると、クエリの総デバイス数が 594 (S1 環境) または 794 (S2 環境) と他の 6 つの列に制限されます。
> - 最初の例で説明されている理由で、不要なプロパティを回避できます。
> - 参照データを使用すると、**messageId** と **deviceLocation** の一意の組み合わせである **deviceId** を使用してネットワーク経由で転送されるバイト数を減らすことができます。 複合キーの **series.tagId** は、**type** と **unit** の一意の組み合わせに対して使用されています。 複合キーを使用することにより **messageId、deviceLocation、type** および **unit** の 4 つの値を参照する、**deviceId** および **series.tagId** ペアが使用できます。 このデータは、イングレス時に、利用統計情報と結合されます。 その後、クエリ用に Time Series Insights に格納されます。
> - 最初の例に示されている理由で、2 層の入れ子が使用されています。

### <a name="for-both-scenarios"></a>両方のシナリオで

使用可能な値の数が多いプロパティの場合は、値ごとに新しい列を作成するのではなく、1 つの列に個別の値を含めて送信することをお勧めします。 前の 2 つの例から:

  - 最初の例では、少数のプロパティにいくつか値があります。そのため、それぞれを別のプロパティにすることが適切です。
  - 2 番目の例では、メジャーは個々のプロパティとして指定されていません。 代わりに、共通の一連のプロパティに収められた値またはメジャーの配列として指定されています。 新しいキー **tagId** を送信すると、フラット化されたテーブルに、新しい列 **series.tagId** が作成されます。 参照データを使用して **type** および **unit** の新しいプロパティが作成され、プロパティ制限の超過が回避されます。

## <a name="next-steps"></a>次の手順

- [IoT Hub デバイス メッセージをクラウド](../iot-hub/iot-hub-devguide-messages-construct.md)に送信する方法の詳細を参照してください。

- Time Series Insights データ アクセス REST API のクエリ構文の詳細については、[Azure Time Series Insights のクエリの構文](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax)に関する記事を参照してください。

- [イベントを調整する方法](./time-series-insights-send-events.md)について説明します。
