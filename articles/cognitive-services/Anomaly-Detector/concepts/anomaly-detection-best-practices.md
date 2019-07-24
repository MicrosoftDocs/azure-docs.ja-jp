---
title: Anomaly Detector API の利用に関するベスト プラクティス
description: Anomaly Detector API を使用して異常を検出する場合のベスト プラクティスについて説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 467ac4e475a1c23e25b62c76cfbc959e7ed49465
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484043"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Anomaly Detector API の使用に関するベスト プラクティス

Anomaly Detector API はステートレスな異常検出サービスです。 結果の精度とパフォーマンスは、次の事項によって影響を受けます。

* 時系列データの準備方法。
* 使用した Anomaly Detector API パラメーター。
* API 要求のデータ ポイント数。 

この記事では、この API を使用してデータに関する最良の結果を得るためのベスト プラクティスについて説明します。 

## <a name="data-preparation"></a>データの準備

Anomaly Detector API では、JSON 要求オブジェクトへフォーマットされた時系列データを受け入れます。 時系列データは、一定期間にわたって順番に記録された数値データです。 API のパフォーマンスを改善するために、複数の時間枠の時系列データを Anomaly Detector API エンドポイントに送信できます。 送信できるデータ ポイントの最小数は 12 ポイントで、最大数は 8640 ポイントです。 

Anomaly Detector API に送信されるデータ ポイントには、有効な協定世界時 (UTC) のタイムスタンプと、数値が含まれている必要があります。 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

### <a name="missing-data-points"></a>欠落データ ポイント

均等に分散された時系列データ セットでは、データ ポイントの欠落はよくあることです。特に細分化されたデータ (たとえば、 数分ごとなどの短いサンプリング期間にサンプリングされたデータ) でよく見られます。 データで予想されるデータ ポイント数の不足が 10% 以下の場合、検出結果に悪影響はありません。 前の期間のデータ ポイントとの置き換え、線状補間、または移動平均などにより、その特徴に基づいて、データの不足部分を埋めることを検討します。

### <a name="aggregate-distributed-data"></a>分散データを集計する

Anomaly Detector API は均等に分散された時系列に最適です。 データがランダムに分散されている場合は、時間の単位 (たとえば、分単位、時間単位、日単位など) 別に集計する必要があります。

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>季節性パターンを使用したデータの異常検出

時系列データに季節性パターン (定期的に発生するパターン) があることが分かっている場合は、精度と API の応答時間を改善できます。 

JSON 要求を作成するときに `period` を指定すると、異常検出の待機時間を最大で 50% 短縮できます。 `period` は、時系列でパターンが繰り返されるまでのデータ ポイント数を大まかに指定する整数です。 たとえば、1 日あたり 1 つのデータ ポイントを持つ時系列の `period` は `7` となり、1 時間あたり 1 つのデータ ポイントを (毎週同じパターンで) 持つ時系列の `period` は `7*24` となります。 データのパターンが不明な場合は、このパラメーターを指定する必要はありません。

最良の結果を得るには、4 つの`period` に相当するデータ ポイントと、追加で 1 つのデータ ポイントを指定します。 たとえば、上記で説明した毎週のパターンを持つ時間単位のデータでは、要求本文で 673 個のデータ ポイント (`7 * 24 * 4 + 1`) を指定する必要があります。

### <a name="sampling-data-for-real-time-monitoring"></a>リアルタイム監視用のデータのサンプリング

ストリーミング データを短期間 (秒単位や分単位など) でサンプリングした場合、推奨される数のデータ ポイントを送信すると、Anomaly Detector API で許容される最大数 (8640 データ ポイント) を超える可能性があります。 データの季節性パターンが安定している場合、より長期間 (時間単位など) で時系列データのサンプルを送信することを検討してください。 このようにデータをサンプリングすると、API の応答時間も大幅に短縮できます。 

## <a name="next-steps"></a>次の手順

* [Anomaly Detector API とは](../overview.md)
* [クイック スタート:Anomaly Detector REST API を使用し、時系列データ内の異常を検出する](../quickstarts/detect-data-anomalies-csharp.md)
