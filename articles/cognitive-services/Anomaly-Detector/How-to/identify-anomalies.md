---
title: 時系列データで Anomaly Detector API を使用する方法
titleSuffix: Azure Cognitive Services
description: バッチとして、またはストリーミング データ上でデータ内の異常を検出する方法について説明します。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: mbullwin
ms.openlocfilehash: 74f891ba7f5b400b5782565e670539167f4e2464
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97703434"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>方法:時系列データに Anomaly Detector API を使用する  

[Anomaly Detector API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) には、異常検出の方法が 2 つあります。 時系列全体でバッチとして異常を検出するか、最新のデータ ポイントの異常状態を検出することによりデータが生成されるときに異常を検出することができます。 検出モデルは、異常結果と共に、各データ ポイントの予期される値と異常検出の上限および下限値を返します。 これらの値を使用して、通常の値の範囲およびデータの異常を視覚化することができます。

## <a name="anomaly-detection-modes"></a>異常検出モード 

Anomaly Detector API には、バッチとストリーミングの検出モードが用意されています。

> [!NOTE]
> 次の要求 URL は、サブスクリプションに対して適切なエンドポイントと組み合わせる必要があります。 例: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>バッチ検出

特定の時間範囲にわたるデータ ポイントのバッチ全体で異常を検出するには、次の要求 URI を時系列データと共に使用します。 

`/timeseries/entire/detect`. 

時系列データを一度に送信することにより、API は、シリーズ全体を使用してモデルを生成し、そのモデルで各データ ポイントを分析します。  

### <a name="streaming-detection"></a>ストリーミング検出

ストリーミング データの異常を継続的に検出するには、最新のデータ ポイントと共に次の要求 URI を使用します。 

`/timeseries/last/detect'`. 

新しいデータ ポイントを生成するときにそれらを送信することによって、データをリアル タイムで監視できます。 モデルは、送信したデータ ポイントで生成され、API は、時系列の最新のポイントが異常であるかどうかを判断します。

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>異常検出の下限値と上限値の調整

既定では、異常検出の上限値と下限値は `expectedValue`、`upperMargin`、および `lowerMargin` を使用して計算されます。 別の限界値が必要な場合は、`marginScale` を `upperMargin` または `lowerMargin` に適用することをお勧めします。 限界値は次のように計算されます。

|限界値  |計算  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

次の例は、さまざまな感度での Anomaly Detector API の結果を示しています。

### <a name="example-with-sensitivity-at-99"></a>99 の感度による例

![既定の感度](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>95 の感度による例

![99 の感度](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>85 の感度による例

![85 の感度](../media/sensitivity_85.png)

## <a name="next-steps"></a>次の手順

* [Anomaly Detector API とは](../overview.md)
* [クイック スタート: Anomaly Detector を使用して時系列データ内の異常を検出する](../quickstarts/client-libraries.md)
