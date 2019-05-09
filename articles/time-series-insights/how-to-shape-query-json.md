---
title: Azure Time Series Insights クエリで JSON を調整するためのベスト プラクティス | Microsoft Docs
description: Azure Time Series Insights クエリの効率を改善する方法について説明します。
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/24/2018
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: c076d425a7740bd0eb6398e6b8720fa873f2fc30
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201263"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>クエリのパフォーマンスを最大化するための JSON の調整方法 

この記事では、Azure Time Series Insights (TSI) クエリの効率を最大化するよう JSON を調整するためのガイダンスを提供します。

## <a name="video"></a>ビデオ: 

### <a name="in-this-video-we-cover-best-practices-around-shaping-json-to-meet-your-storage-needsbr"></a>このビデオでは、ストレージ要件を満たすように、JSON の調整に関するベスト プラクティスについて説明します。</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>ベスト プラクティス

Azure Time Series Insights にイベントを送信する方法について検討することが重要です。 具体的には、常に、以下を考慮する必要があります。

1. ネットワーク上でできるだけ効率的にデータを送信する。
2. 使用するシナリオに適した集計を実行することができる形式でデータが格納されている。
3. 以下の TSI の最大プロパティ制限を超えないようにする。
   - S1 環境で 600 プロパティ (列)
   - S2 環境で 800 プロパティ (列)

クエリ パフォーマンスを最善に維持するためガイダンスは、次のとおりです。

1. プロパティ名としてタグ ID を使用するなどの動的なプロパティを使用しない。使用すると、プロパティの最大限度に達します。
2. 不要なプロパティを送信しない。 クエリのプロパティが必要でない場合は、ストレージの制限に達することを避けるため、送信しないことをお勧めします。
3. [参照データ](time-series-insights-add-reference-data-set.md)を使用して、ネットワーク上で静的なデータを送信しないようにします。
4. 複数のイベントでディメンションのプロパティを共有して、ネットワーク経由でのデータ送信を効率化します。
5. 深い入れ子の配列を使用しない。 TSI では、オブジェクトを含む入れ子配列は 2 つのレベルの入れ子までサポートされています。 TSI では、メッセージ内の配列は、プロパティ値のペアを使用する複数のイベントに平坦化されます。
6. すべて、またはほとんどのイベントに対して数個のメジャーのみが存在する場合は、これらのメジャーを同じオブジェクト内の個別のプロパティとして送信することをお勧めします。 個別に送信することでイベントの数が減少し、処理する必要があるイベントが少なくなるため、クエリのパフォーマンスを改善できることがあります。 複数のメジャーが存在する場合、1 つのプロパティの値として送信すると、最大プロパティ制限に達する可能性を最小限に抑えられます。

## <a name="examples"></a>例

次の 2 つの例では、前述の推奨事項を説明するため、イベントを送信しています。 各例の後には、推奨事項の適用方法を示しています。

例では、複数のデバイスがメジャーや信号を送信するシナリオを使用しています。 メジャーまたは信号には、フロー レート、エンジン オイル圧、温度または湿度を使用できます。 最初の例では、すべてのデバイスにわたって数個のメジャーがあります。 2 番目の例では、複数のデバイスがあり、複数の一意なメジャーをそれぞれ送信しています。

### <a name="scenario-only-a-few-measurementssignals-exist"></a>シナリオ: メジャー/信号が数個のみ存在

**推奨事項:** 各メジャーを個別のプロパティ/列として送信します。

次の例では IoT Hub メッセージが 1 つあり、外側の配列に共通のディメンション値の共有部分が含まれています。 外側の配列は、参照データを使用してメッセージの効率を改善します。 参照データには、イベントごとに変更されないが、データ解析に役立つプロパティを提供するデバイス メタデータが含まれています。 共通のディメンション値をバッチ処理し、かつ参照データを使用することで、ネットワーク上に送信されるバイト数を減らしてメッセージを効率化できます。

例の JSON ペイロード:

```json
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

参照データ テーブル (キー プロパティは deviceId):

| deviceId | messageId | deviceLocation |
| --- | --- | --- |
| FXXX | LINE\_DATA | EU |
| FYYY | LINE\_DATA | US |

Azure Time Series Insights テーブル、(平坦化後):

| deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| --- | --- | --- | --- | --- | --- |
| FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
| FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
| FYYY | LINE\_DATA | US | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

前の例では、次の点に注意してください。

- **deviceId** 列は、fleet 内のさまざまなデバイスの列見出しとして機能します。 deviceId 値を使用して独自のプロパティ名を作成すると、他の 5 つの列を含め合計デバイス数が 595 (S1 環境) または 795 (S2 環境) に制限されます。

- メーカーや型の情報などの不要なプロパティを回避できます。今後もクエリで使用されることはないため、これらを削除するとネットワークおよびストレージの効率性が向上します。

- 参照データを使用すると、ネットワーク経由で転送されるバイト数を減らせます。 **messageId** と **deviceLocation** の 2 つの属性、**deviceId** キー プロパティを使用して結合されます。 このデータは、受信時に利用統計情報と結合され、その後、クエリのために TSI に格納されます。

- TSI でサポートされる最大入れ子数である、2 つの入れ子層が使用されます。 深く入れ子された配列は使用しないことが重要です。

- 複数のメジャーがあるため、メジャーは、同じオブジェクト内の個別のプロパティとして送信されます。 ここでは、**series.Flow Rate psi** と **series.Engine Oil Pressure ft3/s** が一意な列です。

### <a name="scenario-several-measures-exist"></a>シナリオ: 複数のメジャーが存在

**推奨事項:** "type"、"unit"、"value" の 3 つ 1 組のメジャーとして送信します。

例の JSON ペイロード:

```json
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

参照データ (キー プロパティは、deviceId および series.tagId):

| deviceId | series.tagId | messageId | deviceLocation | type | unit |
| --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | LINE\_DATA | EU | Flow Rate | ft3/s |
| FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi |
| FYYY | pumpRate | LINE\_DATA | US | Flow Rate | ft3/s |
| FYYY | oilPressure | LINE\_DATA | US | Engine Oil Pressure | psi |

Azure Time Series Insights テーブル、(平坦化後):

| deviceId | series.tagId | messageId | deviceLocation | type | unit | timestamp | series.value |
| --- | --- | --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | LINE\_DATA | EU | Flow Rate | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
| FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | psi | 2018-01-17T01:17:00Z | 34.7 |
| FXXX | pumpRate | LINE\_DATA | EU | Flow Rate | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
| FXXX | oilPressure | LINE\_DATA | EU | Engine Oil Pressure | Psi | 2018-01-17T01:17:00Z | 49.2 |
| FYYY | pumpRate | LINE\_DATA | US | Flow Rate | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
| FYYY | oilPressure | LINE\_DATA | US | Engine Oil Pressure | psi | 2018-01-17T01:18:00Z | 22.2 |

前の例と、最初の例の以下に注意してください。

- **deviceId** および **series.tagId** 列は、fleet 内のさまざまなデバイスとタグの列見出しとして機能します。 それぞれを独自の属性として使用すると、他の 6 つの列を含めクエリの総デバイス数が 594 (S1 環境) または 794 (S2 環境) に制限されます。

- 最初の例で説明されている理由で、不要なプロパティを回避できます。

- 参照データを使用すると、**messageId** と **deviceLocation** が一意に組み合わされた **deviceId** を使用してネットワーク経由で転送されたバイト数を減らせます。 **type** と **unit** を表す複合キー、**series.tagId** が使用されています。 複合キーを使用することにより **messageId、deviceLocation、type** および **unit** の 4 つの値を参照する、**deviceId** および **series.tagId** ペアが使用できます。 このデータは、受信時に利用統計情報と結合され、その後、クエリのために TSI に格納されます。

- 最初の例に示されている理由で、2 層の入れ子が使用されています。

### <a name="for-both-scenarios"></a>両方のシナリオで

使用可能な値の数が多いプロパティの場合は、値ごとに新しい列を作成するのではなく、1 つの列に含まれた個別の値として送信することをお勧めします。 前の 2 つの例から:
  - 最初の例では、複数の値を持つ複数のプロパティがあるため、それぞれ個別のプロパティを設定することが適切です。 
  - ただし、2 番目の例では、メジャーは個別のプロパティとしてではなく、共通の一連のプロパティに収められた値/メジャーの配列として指定されています。 新しいキー **tagId** を送信すると、平坦化されたテーブルに、新しい列 **series.tagId** が作成されます。 参照データを使用して **type** および **unit** の新しいプロパティが作成され、プロパティ制限の超過が回避されます。

## <a name="next-steps"></a>次の手順

これらのガイドラインを実際に使用する、TSI データ アクセス REST API のクエリ構文の詳細については、[Azure Time Series Insights のクエリの構文](/rest/api/time-series-insights/ga-query-syntax)に関する記事を参照してください。