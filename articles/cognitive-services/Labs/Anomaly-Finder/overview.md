---
title: Anomaly Finder とは - Microsoft Cognitive Services | Microsoft Docs
description: Anomaly Finder に備わる高度なアルゴリズムを使用すると、Microsoft Cognitive Services で、時系列データの異常を識別し、情報を返す助けになります。
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: 41d20cfd9821fbd2a34a66754e5c2da3d6439fd2
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56726815"
---
# <a name="what-is-anomaly-finder"></a>Anomaly Finder とは

[!INCLUDE [PrivatePreviewNote](../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Anomaly Finder では、産業、シナリオ、データ量を問わず、適切な統計モデルを自動的に適用することによってユーザー独自のデータに適応する機械学習を使用し、ある時間にわたってデータを監視し、異常を検出することができます。 Anomaly Finder API は時系列を入力として使用し、データ ポイントが異常かどうかを返します。また、予期される値と、視覚化の上下の境界を特定します。 ビルド済みの AI サービスである Anomaly Finder では、RESTful API の使用方法を理解すること以外に、機械学習の専門知識は必要とされません。 これにより、開発はシンプルで用途が広いものになります。それは、どのような時系列データでも扱うことができ、ストリーミング データ システムに組み込むこともできるためです。 Anomaly Finder は、広範にわたるユース ケースを網羅しています。たとえば、詐欺、盗難、変化する市場、および潜在的なビジネス インシデントを管理するための財務ツールや、匿名性を維持しながらの IoT デバイスのトラフィック監視です。 このソリューションは、データ、支出、投資収益率、またはユーザー アクティビティの変化を理解するための、エンド カスタマー向けサービスの一部として収益化することもできます。
Anomaly Finder API を試して、お持ちのデータについて理解を深めてください。 

この API で何をビルドできるかご確認ください。

* 時系列の履歴データに基づいて、予期される値の予測方法を学ぶ
* データ ポイントが履歴パターンから外れた異常であるかどうかを示す
* "正常" 値の範囲を視覚化するバンドを生成する

![Anomaly_Finder](./media/anomaly_detection1.png) 

図 1: 売上高の異常を検出する

![Anomaly_Finder](./media/anomaly_detection2.png)

図 2: サービス要求のパターンの変化を検出する

## <a name="requirements"></a>必要条件

- 入力時系列の最小データ: 明確な周期性がない時系列データの場合は少なくとも 13 のデータ ポイント。既知の周期性がある時系列の場合は少なくとも 4 サイクル分のデータ ポイント。 
- データ整合性: 時系列データ ポイントが同じ間隔で区切られていて、不足しているポイントがないこと。 

## <a name="identify-anomalies"></a>異常を識別する

異常検出の API は、任意の特定データ ポイントが異常かどうかの結果を返し、次のような追加情報を提供します
* Period - API が異常なポイントを検出するために使用した周期性。
* WarningText - ありえる警告の情報。
* ExpectedValue - 学習ベースのモデルによって予測された値
* IsAnomaly - データ ポイントが異常値かどうかに関する結果
* IsAnomaly_Neg - データ ポイントがマイナス方向の異常値 (急激な下落) であるかどうかに関する結果
* IsAnomaly_Pos - データ ポイントがプラス方向の異常値 (急激な上昇) であるかどうかに関する結果
* UpperMargin - ExpectedValue と UpperMargin の合計によって、データ ポイントがまだ正常であると判断される上の境界が決まります
* LowerMargin - ExpectedValue から LowerMargin を引いた値によって、データ ポイントがまだ正常であると判断される下の境界が決まります

> [!Note]
> UpperMargin と LowerMargin を、実際の時系列の周囲にバンドを生成するために使用すると、正常値の範囲を視覚化できます。 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>応答に対する後処理での上下の境界の調整

異常検出 API は、データ ポイントが異常かどうかの既定の結果を返し、ExpectedValue と UpperMargin/LowerMargin から上下の境界を計算することができます。 これらの既定値は、ほとんどの場合、適切に機能するはずです。 ただし、一部のシナリオでは、既定のものとは異なる境界が必要となります。 推奨されるやり方は、UpperMargin または LowerMargin に係数を適用して動的な境界を調整することです。

### <a name="examples-with-1152-as-coefficiency"></a>係数として 1/1.5/2 を適用した例

![既定の感度](./media/sensitivity_1.png)

![感度 1.5](./media/sensitivity_1.5.png)

![感度 2](./media/sensitivity_2.png)

サンプル データを含む要求

[!INCLUDE [Request](./includes/request.md)]

サンプルの JSON 応答

[!INCLUDE [Response](./includes/response.md)]
