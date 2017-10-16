---
title: "Azure での異常検出使用ガイド (プレビュー) | Microsoft Docs"
description: "Stream Analytics と Machine Learning を使って異常を検出します。"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 7ab489f6ae7da2640ba199b20e7727da60497918
ms.contentlocale: ja-jp
ms.lasthandoff: 09/20/2017

---

# <a name="using-the-anomalydetection-operator"></a>ANOMALYDETECTION 演算子を使用する

> [!IMPORTANT]
> この機能はプレビュー状態です。 運用環境での使用はお勧めできません。

**ANOMALYDETECTION** 演算子を使うと、イベント ストリーム内の異常を検出できます。
たとえば、空きメモリの量が長い時間をかけてゆっくり減るのはメモリ リークを示していることがあり、ある範囲で安定していた Web サービス要求の数が急激に増えたり減ったりすることがあります。

この演算子は、指定した期間について、次の種類の異常を調べます。

- 双方向のレベル変化
- ゆっくりした増加傾向
- ゆっくりした減少傾向

現在のイベントから遡って履歴を調べる必要がある期間は、**LIMIT DURATION** 句を使って制限します。 **WHEN** 句を使うと、特定のプロパティまたは条件に一致するイベントのみに **ANOMALYDETECTION** を制限することもできます。

また、必要に応じて、**PARTITION BY** 句で指定するキーに基づいて、イベントのグループを個別に処理できます。 トレーニングと予測は、パーティションごとに独立して行われます。

## <a name="syntax"></a>構文

`ANOMALYDETECTION(\<scalar_expression\>) OVER ([PARTITION BY \<partition key\>] LIMIT DURATION(\<unit\>, \<length\>) [WHEN boolean_expression])` 


## <a name="example-usage"></a>使用例

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id \> 100) FROM input`|


## <a name="arguments"></a>引数

- **scalar_expression**

  異常検出を実行する対象のスカラー式です。 これは、単一の (スカラー) 値を返す float 型または bigint 型の式です。 ワイルドカード式 **\*** は使用できません。 **scalar_expression** に他の分析関数または外部関数を含めることはできません。

- **OVER ( [ partition_by_clause ] limit_duration_clause [when_clause])**

- **partition_by_clause** 

  `PARTITION BY \<partition key\>` 句は、学習とトレーニングを個別のパーティションに分割します。 つまり、`\<partition key\>` の値ごとに異なるモデルが使われ、その値を持つイベントのみがそのモデルの学習とトレーニングに使われます。 たとえば、次のように入力します。

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

  この式は、同じセンサーの他の読み取りに対してのみ、読み取りをトレーニングおよびスコア付けします。

- **limit_duration clause** DURATION(\<unit\>, \<length\>)

  **ANOMALYDETECTION** の計算に使う、現在のイベントからの履歴の量を指定します。 サポートされている単位とその省略形について詳しくは、DATEDIFF をご覧ください。

- **when_clause** 

  **ANOMALYDETECTION** の計算で使うイベントのブール条件を指定します。

## <a name="return-types"></a>戻り値の型

この関数は、次の 3 つのスコアをすべて含むレコードを出力として返します。 異なる種類の異常検出機能に関連付けられているプロパティが呼び出されます。

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

レコードから個々の値を抽出するには、**GetRecordPropertyValue** 関数を使います。 For example:

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) \> 3.25` 


これらの異常スコアのいずれかがしきい値を超えると、特定の種類の異常が検出されます。 しきい値は、\>= 0 以上の任意の浮動小数点数です。 しきい値は、感度と信頼性のトレードオフになります。 たとえば、しきい値を低くすると、変化の検出感度が高くなり、生成されるアラートの数が増えます。しきい値を高くすると、検出の感度は低くなり、信頼性は高くなりますが、一部の異常が見落とされる可能性があります。 使うしきい値の正確な値は、シナリオによって異なります。 上限はありませんが、推奨される範囲は 3.25 から 5 です。

## <a name="algorithm"></a>アルゴリズム

**ANOMALYDETECTION** はスライディング ウィンドウ セマンティクスを使います。つまり、関数に入ってくるイベントごとに計算が実行され、そのイベントに対してスコアが生成されます。 計算は Exchangeability Martingales に基づきます。これは、イベント値の分布が変化したかどうかをチェックすることで動作します。 変化した場合は、異常が検出された可能性があります。 返されるスコアは、その異常の信頼レベルを示す値です。 内部の最適化として、**ANOMALYDETECTION** は *d* から *2d* に相当するイベントに基づいてイベントの異常スコアを計算します。*d* は指定された検出ウィンドウ サイズです。

**ANOMALYDETECTION** は、入力時系列が一定であるものと想定します。 イベント ストリームは、タンブリング ウィンドウまたはホッピング ウィンドウについて集計することにより一定にできます。 イベント間のギャップが集計ウィンドウより常に小さいシナリオでは、時系列を一定にするにはタンブリング ウィンドウで十分です。 ギャップが大きくなる可能性がある場合は、ホッピング ウィンドウを使って最後の値を繰り返すことにより、ギャップを埋めることができます。 どちらのシナリオも、後で示す例を使って処理できます。 現時点では、`FillInMissingValuesStep` のステップをスキップすることはできません。 このステップがないと、コンパイル エラーが発生します。

## <a name="performance-guidance"></a>パフォーマンスに関するガイダンス

- ジョブには 6 SU を使います。 
- 少なくとも 1 秒間隔でイベントを送信します。
- **ANOMALYDETECTION** 関数を使うパーティション分割されていないクエリでは、平均約 25 ミリ秒の計算待機時間で結果が生成される可能性があります。
- パーティション分割されたクエリで発生する待機時間は、計算の数が増えるので、パーティションの数によって若干異なります。 ただし、全パーティションのイベントの総数が等しい場合は、待機時間はパーティション分割されていない場合とほぼ同じです。
- リアルタイムではないデータを読み取るときは、大量のデータが短時間で取り込まれます。 現在は、このデータを処理すると速度が大幅に低下します。 このようなシナリオでの待機時間は、ウィンドウ サイズやイベントの間隔ではなく、ウィンドウ内のデータ ポイントの数に比例して増加することが判明しています。 リアルタイムではない場合の待機時間を減らすには、ウィンドウ サイズを小さくすることを検討してください。 または、現在の時刻からジョブを開始することを検討してください。 パーティション分割されていないクエリの待機時間に関するいくつかの例: 
    - 検出ウィンドウ内のデータ ポイントの数が 60 の場合、3 MBps のスループットで 10 秒の待機時間になります。 
    - データ ポイントの数が 600 では、0.4 MBps のスループットで待機時間は約 80 秒になります。

## <a name="example"></a>例

次のクエリを使うと、異常が検出された場合にアラートを出力できます。
入力ストリームが一定でないときは、集計手順が時系列を一定にするのに役立ちます。 この例では **AVG** を使いますが、どのような種類の集計を使うかはユーザーのシナリオによって異なります。 さらに、時系列に集計ウィンドウより大きいギャップがある場合は、(スライディング ウィンドウ セマンティクスに従って) 異常検出をトリガーするイベントが時系列に存在しません。 その結果、次のイベントが到着した時点で、一様性の想定が崩れます。 このような場合は、時系列のギャップを埋める手段が必要です。 可能な方法の 1 つは、次に示すように、すべてのホップ ウィンドウで最後のイベントを取得することです。

前述したように、現時点では、`FillInMissingValuesStep` のステップをスキップしないでください。 このステップを省略すると、コンパイル エラーが発生します。

    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) \>= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) \>=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) \>=
       3.25

## <a name="references"></a>参照

* [異常検出 – Machine Learning を使って時系列データの異常を検出する](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Machine Learning 異常検出 API](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [時系列の異常検出](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="get-support"></a>サポートを受ける
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


