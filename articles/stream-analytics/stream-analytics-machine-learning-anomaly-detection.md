---
title: Azure Stream Analytics での異常検出
description: この記事では、Azure Stream Analytics と Azure Machine Learning を併用して異常を検出する方法を説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: e2fd226f1c605821f0fd595832b2cbe26d994fb4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612338"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure Stream Analytics での異常検出

クラウドと Azure IoT Edge の両方で利用できます。Azure Stream Analytics には、機械学習をベースにした異常検出機能が組み込まれています。これを使用して、最も多く発生する 2 種類の異常 (一時的な異常と永続的な異常) を監視できます。 **AnomalyDetection_SpikeAndDip** と **AnomalyDetection_ChangePoint** 関数を使用して、異常検出を Stream Analytics ジョブ内で直接実行することができます。

機械学習モデルでは、均等にサンプリングされたタイム シリーズを想定しています。 タイム シリーズが均等でない場合は、異常検出を呼び出す前に、タンブリング ウィンドウを使って集計手順を挿入してもかまいません。

現時点で、機械学習の処理は、季節性の傾向や多変量の相関関係には対応していません。

## <a name="model-behavior"></a>モデルの動作

一般に、スライディング ウィンドウ内のデータが多いほどモデルの精度が向上します。 指定したスライディング ウィンドウ内のデータは、その期間の正常範囲の値の一部として扱われます。 モデルで現在のイベントの異常性を調べるときは、スライディング ウィンドウ内のイベント履歴のみが考慮されます。 スライディング ウィンドウが動くと、モデルのトレーニングから古い値が削除されます。

この機能は、これまでの履歴を基に一定の標準を確立することによって動作します。 外れ値は、信頼度レベル内で、確立された標準と比較することで識別されます。 異常が発生したときに認識できるように、ウィンドウのサイズは、正常な動作でモデルをトレーニングするのに必要な最小イベント数を基準にしてください。

履歴のサイズが大きくなると、比較対象となる過去のイベント数も増えるため、モデルの応答時間が増えます。 パフォーマンス向上のため、イベントは必要な数だけ含めることをお勧めします。

タイム シリーズにギャップがある場合、モデルが特定の時点のイベントを受け取っていないことが原因である可能性があります。 このような状況は、Stream Analytics の補完ロジックによって処理されます。 同じスライディング ウィンドウの履歴サイズと期間の両方を使用して、イベントの平均出現率が計算されます。

[ここ](https://aka.ms/asaanomalygenerator)で公開されている異常ジェネレーターは、IoT Hub にさまざまな異常パターン データをフィードするために使用できます。 ASA ジョブをこの異常検知機能と合わせて設定すると、この IoT Hub から読み取って異常を検出することができます。

## <a name="spike-and-dip"></a>スパイクとディップ

タイム シリーズ イベント ストリーム内の一時的な異常は、スパイクとディップと呼ばれます。 スパイクとディップは、機械学習をベースにした演算子 [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
) を使って監視できます。

![スパイクとディップの異常の例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

同じスライディング ウィンドウ内の 2 番目のスパイクが 1 番目のものよりも小さい場合、小さい方のスパイクに対して計算されるスコアが、指定した信頼度レベル内の最初のスパイクのスコアに比べて十分な大きさにならないことがあります。 このような異常を検知するには、モデルの信頼度レベルの設定を低くしてみてください。 ただし、それによってアラートの数が増えすぎてしまう場合は、より高い信頼区間を使用できます。

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

## <a name="performance-characteristics"></a>パフォーマンス特性

これらのモデルのパフォーマンスは、履歴のサイズ、ウィンドウ期間、イベントの負荷、関数レベルのパーティション分割を使用しているかどうかによります。 このセクションでは、これらの構成について説明し、1 秒あたり 1,000 件、5,000 件、1 万件のイベントの取り込み速度を維持する方法のサンプルを提供します。

* **履歴のサイズ** - これらのモデルは、**履歴のサイズ**に対して直線性を有します。 履歴のサイズが大きくなるほど、モデルが新しいイベントをスコア付けするためにかかる時間が長くなります。 これは、モデルが新しいイベントと履歴バッファー内にある過去の各イベントを比較するためです。
* **ウィンドウ期間** - **ウィンドウ期間**は、履歴のサイズで指定された数のイベントの受信にかかる時間を反映する必要があります。 ウィンドウ内に多くのイベントがない場合、Azure Stream Analytics は欠損値を補完します。 そのため、CPU 使用量は履歴のサイズの関数となります。
* **イベント負荷** - **イベント負荷**が大きいほどモデルの作業量が増え、CPU 使用量に影響を与えます。 ビジネス ロジックで入力パーティションを増やせると仮定すれば、ジョブを驚異的並列にすることでスケール アウトできます。
* **関数レベルのパーティション分割** - **関数レベルのパーティション分割**は、異常検出関数呼び出し内で ```PARTITION BY``` を使用して行われます。 この種類のパーティション分割は、同時に複数のモデルで状態を維持する必要があるため、オーバーヘッドが増加します。 関数レベルのパーティション分割は、デバイス レベルのパーティション分割などのシナリオで使用されます。

### <a name="relationship"></a>リレーションシップ
履歴のサイズ、ウィンドウ期間、およびイベント負荷の合計は、次のように関連します。

ウィンドウ期間 (ミリ秒) = 1000 * 履歴のサイズ / (1 秒あたりの入力イベントの合計 / 入力パーティション数)

関数を deviceId でパーティション分割する場合は、異常検出関数呼び出しに「PARTITION BY deviceId」を追加します。

### <a name="observations"></a>観測値
次の表には、パーティション分割されていない場合の 1 つのノード (6 SU) のスループット観測値が含まれています。

| 履歴のサイズ (イベント) | ウィンドウ期間 (ミリ秒) | 1 秒あたりの入力イベントの合計 |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6,000 | 10,910 | 1,100 |

次の表には、パーティション分割されている場合の 1 つのノード (6 SU) のスループット観測値が含まれています。

| 履歴のサイズ (イベント) | ウィンドウ期間 (ミリ秒) | 1 秒あたりの入力イベントの合計 | デバイス数 |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6,000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6,000 | 2,181,819 | <550 | 100 |

上記のパーティション分割されていない構成を実行するサンプル コードは、Azure サンプルの [streaming-at-scale リポジトリ](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh)にあります。 このコードでは、関数レベルのパーティション分割が行われていないストリーム分析ジョブを作成し、入力および出力として Event Hub を使用しています。 入力の負荷は、テスト クライアントを使用して生成されます。 各入力イベントは、1 KB の json ドキュメントです。 イベントは、JSON データを送信する IoT デバイスをシミュレートします (最大 1,000 台のデバイス)。 履歴のサイズ、ウィンドウ期間、およびイベント負荷の合計は、2 つの入力パーティションで異なります。

> [!Note]
> 見積もりの精度を高めるには、ご使用のシナリオに合わせてサンプルをカスタマイズしてください。

### <a name="identifying-bottlenecks"></a>ボトルネックの特定
Azure Stream Analytics ジョブの [メトリックス] ウィンドウを使用して、パイプラインのボトルネックを特定します。 スループットについての **[Input/Output Events]\(入出力イベント\)** および [[透かしの遅延]](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) または **[Backlogged Events]\(バックログされたイベント\)** を確認して、ジョブが入力速度に対応しているかどうかを確認します。 イベント ハブのメトリックスについては、 **[Throttled Requests] (スロットルされた要求数)** を検索し、必要に応じてしきい値ユニットを調整します。 Cosmos DB メトリックスについては、スループットの下の **[パーティション キーの範囲ごとの使用された最大 RU/秒]** を確認して、パーティション キーの範囲が均一に消費されていることを確認します。 Azure SQL DB については、 **[ログ IO]** および **[CPU]** を監視します。

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Azure Stream Analytics で機械学習を使用した異常検出

次の動画では、Azure Stream Analytics の機械学習機能を使ってリアルタイムで異常を検出する方法を示します。 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>次の手順

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

