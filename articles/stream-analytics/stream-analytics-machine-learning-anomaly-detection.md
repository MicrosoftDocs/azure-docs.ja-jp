---
title: Azure Stream Analytics での異常検出 (プレビュー)
description: この記事では、Azure Stream Analytics と Azure Machine Learning を併用して異常を検出する方法を説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.custom: seodec18
ms.openlocfilehash: 9ea9cc116a13aac2dca9edf8ba86c933310b5198
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269639"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure Stream Analytics での異常検出

クラウドと Azure IoT Edge の両方で利用できます。Azure Stream Analytics には、機械学習をベースにした異常検出機能が組み込まれています。これを使用して、最も多く発生する 2 種類の異常 (一時的な異常と永続的な異常) を監視できます。 **AnomalyDetection_SpikeAndDip** と **AnomalyDetection_ChangePoint** 関数を使用して、異常検出を Stream Analytics ジョブ内で直接実行することができます。

機械学習モデルでは、均等にサンプリングされたタイム シリーズを想定しています。 タイム シリーズが均等でない場合は、異常検出を呼び出す前に、タンブリング ウィンドウを使って集計手順を挿入してもかまいません。

機械学習の処理は、季節性の傾向や多変量の相関関係には対応していません。

## <a name="model-accuracy-and-performance"></a>モデルの精度とパフォーマンス

一般に、スライディング ウィンドウ内のデータが多いほどモデルの精度が向上します。 指定したスライディング ウィンドウ内のデータは、その期間の正常範囲の値の一部として扱われます。 モデルで現在のイベントの異常性を調べるときは、スライディング ウィンドウ内のイベント履歴のみが考慮されます。 スライディング ウィンドウが動くと、モデルのトレーニングから古い値が削除されます。

この機能は、これまでの履歴を基に一定の標準を確立することによって動作します。 外れ値は、信頼度レベル内で、確立された標準と比較することで識別されます。 異常が発生したときに認識できるように、ウィンドウのサイズは、正常な動作でモデルをトレーニングするのに必要な最小イベント数を基準にしてください。

履歴のサイズが大きくなると、比較対象となる過去のイベント数も増えるため、モデルの応答時間が増えることに注意してください。 パフォーマンス向上のため、イベントは必要な数だけ含めることをお勧めします。

タイム シリーズにギャップがある場合、モデルが特定の時点のイベントを受け取っていないことが原因である可能性があります。 このような状況は、Stream Analytics の補完機能によって処理されます。 同じスライディング ウィンドウの履歴サイズと期間の両方を使用して、イベントの平均出現率が計算されます。

## <a name="spike-and-dip"></a>スパイクとディップ

タイム シリーズ イベント ストリーム内の一時的な異常は、スパイクとディップと呼ばれます。 スパイクとディップは、機械学習をベースにした演算子 [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
) を使って監視できます。

![スパイクとディップの異常の例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

同じスライディング ウィンドウ内の 2 番目のスパイクが 1 番目のものよりも小さい場合、小さい方のスパイクに対して計算されるスコアが、指定した信頼度レベル内の最初のスパイクのスコアに比べて十分な大きさにならないことがあります。 このような異常をキャッチするには、モデルの信頼度レベルの設定を低くしてみてください。 ただし、それによってアラートの数が増えすぎてしまう場合は、より高い信頼区間を使用できます。

次のサンプル クエリでは、120 件のイベント履歴を持つ 2 分間のスライディング ウィンドウで、1 秒あたり 1 件という一定の割合でイベントの入力があるものとします。 最後の SELECT ステートメントで、95% の信頼度レベルでスコアと異常状態を抽出して出力します。

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>変化点

タイム シリーズ イベント ストリーム内の永続的な異常は、レベルの変化や傾向の変化などのような、イベント ストリーム内の値の分布内で起きる変化です。 Stream Analytics では、このような異常を機械学習をベースにした [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) 演算子を使って検出します。

永続的な変化は、スパイクやディップよりもはるかに長期間継続するため、致命的なイベントを示している可能性があります。 永続的な変化は、肉眼では確認できないことがほとんどですが、**AnomalyDetection_ChangePoint** 演算子を使えば検出できます。

次の画像はレベルの変化の例です。

![レベルの変化の異常の例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

次の画像は傾向の変化の例です。

![傾向の変化の異常の例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

次のサンプル クエリでは、1,200 件のイベント履歴サイズを持つ 20 分間のスライディング ウィンドウで、1 秒あたり 1 件という一定の割合でイベントの入力があるものとします。 最後の SELECT ステートメントで、80% の信頼度レベルでスコアと異常状態を抽出して出力します。

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="next-steps"></a>次の手順

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

