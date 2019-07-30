---
title: Azure Stream Analytics でのストリーミング ユニットの理解と調整
description: この記事では、Azure Stream Analytics のパフォーマンスに影響するストリーミング ユニットの設定とその他の要因について説明します。
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 54296f0b4aed22457a5218154111a42ad01ec262
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329337"
---
# <a name="understand-and-adjust-streaming-units"></a>ストリーミング ユニットの理解と調整

ストリーミング ユニット (SU) は、Stream Analytics ジョブを実行するために割り当てられているコンピューティング リソースを表しています。 SU 数が大きいほど、多くの CPU とメモリ リソースがジョブ用に割り当てられます。 この能力により、クエリ ロジックに集中することができ、Stream Analytics ジョブがタイミングよく実行されるようハードウェアを管理する必要がなくなります。

待ち時間が短いストリーミング処理を実現するために、Azure Stream Analytics ジョブはすべての処理をメモリ内で実行します。 メモリが不足した場合は、ストリーミング ジョブが失敗します。 そのため、実稼働ジョブでは、ストリーミング ジョブのリソース使用状況を監視し、ジョブの稼働を 24 時間、週 7 日維持するのに十分なリソースが割り当てられていることを確認することが重要です。

SU 使用率 (%) メトリックはワークロードのメモリ消費量を表し、その範囲は 0% から 100% となります。 最小フットプリントのストリーミング ジョブでは、このメトリックの範囲は、通常 10% から 20% です。 SU 使用率 (%) が低く、入力イベントにバックログが生じるようであれば、そのワークロードは、より多くのコンピューティング リソースが必要である可能性が高く、その場合は SU 数を増やす必要があります。 偶発的なスパイクを考慮して、SU メトリックを 80% 未満に維持することをお勧めします。 リソースの枯渇を防ぐために SU 使用率メトリックは 80% でアラートを設定することをお勧めします。 詳細については、[チュートリアル: Azure Stream Analytics ジョブのアラートを設定する](stream-analytics-set-up-alerts.md)」を参照してください。

## <a name="configure-stream-analytics-streaming-units-sus"></a>Stream Analytics のストリーミング ユニット (SU) を構成する
1. [Azure Portal](https://portal.azure.com/) にサインインします

2. リソースの一覧で、スケールする Stream Analytics ジョブを見つけて開きます。 

3. ジョブ ページの **[構成]** 見出しで、 **[スケーリング]** を選択します。 

    ![Azure Portal での Stream Analytics ジョブの構成][img.stream.analytics.preview.portal.settings.scale]
    
4. スライダーを使用してジョブの SU 数を設定します。 特定の SU 設定に限定されることに注意してください。 

## <a name="monitor-job-performance"></a>ジョブのパフォーマンスを監視する
Azure Portal を使用して、ジョブのスループットを追跡できます。

![Azure Stream Analytics のジョブの監視][img.stream.analytics.monitor.job]

ワークロードの予想されるスループットを計算します。 スループットが予想よりも低い場合は、入力パーティションを調整し、クエリをチューニングして、ジョブに SU を追加します。

## <a name="how-many-sus-are-required-for-a-job"></a>ジョブに必要な SU の数

特定のジョブに必要な SU の数の選択は、入力のパーティション構成と、ジョブで定義されたクエリによって異なります。 **[スケール]** ページでは、適切な SU の数を設定することができます。 SU は、必要な量より多めに割り当てておくことをお勧めします。 メモリをより多く割り当てると、Stream Analytics 処理エンジンによって待ち時間とスループットが最適化されます。

一般に、**PARTITION BY** を使用していないクエリであれば、6 個の SU から始めることがベスト プラクティスです。 そのうえで、試行錯誤により最適な数を検討します。具体的には、典型的な量のデータを渡して SU% 使用率のメトリックを確認し、SU の数を調整する作業を繰り返すことによって、最適な SU の数を割り出します。 Stream Analytics ジョブで使用できるストリーミング ユニットの最大数は、ジョブに定義されたクエリのステップ数と各ステップのパーティション数によって異なります。 制限の詳細については、[こちら](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job)を参照してください。

適切な SU 数の選択の詳細については、次のページを参照してください:[スループット向上のための Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)

> [!Note]
> 特定のジョブに必要な SU 数の選択は、入力のパーティション構成と、ジョブに定義されたクエリによって異なります。 1 つのジョブについて、クォータまでの SU 数を選択できます。 既定では、各 Azure サブスクリプションには、特定のリージョン内のすべての分析ジョブを対象に最大 500 個という SU のクォータが設定されています。 このクォータを超えてサブスクリプションの SU 数を増やす場合は、[Microsoft サポート](https://support.microsoft.com)までご連絡ください。 ジョブごとの SU 数の有効な値は 1、3、6 であり、6 ずつ増加します。

## <a name="factors-that-increase-su-utilization"></a>SU の使用率を上げる要因 

Stream Analytics によって提供されるステートフルな演算子のコア セットは、一時的な (時間指向の) クエリ要素です。 Stream Analytics では、サービスのアップグレード時の、メモリ消費量、回復性のチェックポイント処理、および状態の回復を管理することにより、ユーザーの代わりに内部的にこれらの操作の状態を管理します。 Stream Analytics では、状態を完全に管理していますが、ユーザーが考慮する必要のある推奨事項が多数があります。

複雑なクエリ ロジックを持つジョブは、入力イベントを継続的に受け取らない場合でも、高い SU 使用率 (%) になる可能性があることに注意してください。 これは、入出力イベントが突然急増した後に発生する可能性があります。 ジョブは、クエリが複雑な場合は、メモリ内に状態を保持し続けることがあります。

SU% 使用率は、予想されるレベルに戻る前に、短期間で突然 0 に低下することがあります。 この現象は、一時的なエラーや、システムによって開始されたアップグレードが原因で発生します。 クエリが[完全に並列](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)になっていない場合は、ジョブのストリーミング ユニットの数を増やしても、SU 使用率 (%) は減少しないことがあります。

## <a name="stateful-query-logicin-temporal-elements"></a>一時的な要素のステートフルなクエリ ロジック
Azure Stream Analytics ジョブの固有の機能の 1 つに、ウィンドウ集計、一時的な結合、一時的な分析関数などのステートフル処理の実行があります。 これらの各演算子によって状態情報が保持されます。 これらのクエリ要素の最大ウィンドウ サイズは 7 日間です。 

テンポラル ウィンドウの概念は、いくつかの Stream Analytics クエリ要素に現れます。
1. ウィンドウ集計:タンブリング ウィンドウ、ホッピング ウィンドウ、スライディング ウィンドウの GROUP BY

2. テンポラル結合:DATEDIFF 関数を使用した JOIN

3. テンポラル分析関数:LIMIT DURATION を使用した ISFIRST、LAST、および LAG

Stream Analytics ジョブが使用するメモリ (ストリーミング ユニット メトリックの一部) は、次の要因の影響を受けます。

## <a name="windowed-aggregates"></a>ウィンドウ集計
ウィンドウ集計用に使用されたメモリ (状態のサイズ) は、常にウィンドウのサイズと比例していません。 代わりに、使用されるメモリはデータの基数または各時間枠のグループ数と比例しています。


たとえば、次のクエリは、`clusterid` に関連付けられた数字がクエリの基数です。 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

前のクエリで大きな基数によって引き起こされる問題を軽減するために、`clusterid` でパーティション分割されたイベントをイベント ハブに送信し、次の例で示すように **PARTITION BY** を使用してシステムで各入力パーティションを個別に処理できるようにすることでクエリをスケールアウトできます。

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

クエリは、パーティション分割されると、複数のノードに広がります。 その結果、各ノードで受信される `clusterid` 値の数が削減されるため、group by 演算子の基数が減少します。 

削減手順の必要性をなくすには、イベント ハブ パーティションをグループ化キーでパーティション分割する必要があります。 詳細については、「[Event Hubs の概要](../event-hubs/event-hubs-what-is-event-hubs.md)」を参照してください。 

## <a name="temporal-joins"></a>一時的な結合
一時的な結合によって消費されるメモリ (状態サイズ) は、イベント入力速度にウィグル領域サイズを乗算した、結合の一時的なウィグル領域におけるイベント数に比例します。 つまり、結合によって消費されたメモリは、平均イベント率に DateDiff 時間の範囲を掛けたものと比例しています。

結合内の不一致のイベントの数は、クエリのメモリ使用率に影響します。 次のクエリは、クリックに結びつく広告のインプレッションを検索します。

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

この例では、多数の広告が表示されても、ほとんどのユーザーがクリックしていない可能性があり、時間枠内にすべてのイベントを保持する必要があります。 消費されるメモリは、時間枠のサイズおよびイベント レートに比例します。 

これを修復するには、結合キー (この場合は id) でパーティション分割されたイベントをイベント ハブに送信し、次に示すように **PARTITION BY** を使用して各入力パーティションをシステムが個別に処理できるようにすることでクエリをスケールアウトします。

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

クエリは、パーティション分割されると、複数のノードに広がります。 その結果、各ノードで受信されるイベントの数が減少するため、結合ウィンドウに保持される状態のサイズが減少します。 

## <a name="temporal-analytic-functions"></a>一時的な分析関数
一時的な分析関数で使用されたメモリ (状態サイズ) は、期間で乗算したイベント レートに比例します。 分析関数によって消費されるメモリは、時間枠のサイズではなく、むしろ各時間枠のパーティション数に比例しています。

修復は、一時的な結合と同様です。 **PARTITION BY** を使用してクエリをスケールアウトできます。 

## <a name="out-of-order-buffer"></a>誤順序バッファー 
ユーザーは、[イベント順序] 構成ウィンドウで誤順序バッファー サイズを構成できます。 バッファーは、ウィンドウの継続期間にわたって入力を保持し、それらを並べ替えるために使用されます。 バッファーのサイズは、誤順序ウィンドウのサイズを乗算したイベント入力速度に比例します。 既定のウィンドウ サイズは 0 です。 

誤順序バッファーのオーバーフローを修復するには、**PARTITION BY** を使用してクエリをスケールアウトします。 クエリは、パーティション分割されると、複数のノードに広がります。 その結果、各ノードで受信されるイベントの数が減少するため、各並べ替えバッファー内のイベント数が減少します。 

## <a name="input-partition-count"></a>入力パーティション数 
ジョブ入力の各入力パーティションにはバッファーがあります。 入力パーティション数が多いほど、ジョブで消費されるリソースが多くなります。 ストリーミング ユニットごとに、Azure Stream Analytics ではおおよそ 1 MB/s の入力を処理できます。 したがって、Stream Analytics のストリーミング ユニットの数と、イベント ハブのパーティション数を調整することにより、最適化することができます。 

通常、(イベント ハブの最小値である) 2 つのパーティションを使用するイベント ハブでは、1 つのストリーミング ユニットで構成されている 1 つのジョブで十分です。 イベント ハブにより多くのパーティションがある場合、Stream Analytics ジョブはより多くのリソースを消費しますが、イベント ハブによって提供される追加のスループットを必ずしも使用しません。 

6 つストリーミング ユニットがあるジョブでは、イベント ハブのパーティションは 4 または 8 必要な場合があります。 ただし、リソースの使用を抑えるために不要なパーティションは増やさないようにします。 たとえば、ストリーミング ユニットが 1 つある Stream Analytics ジョブで、イベント ハブのパーティションが 16 以上ある場合などです。 

## <a name="reference-data"></a>参照データ 
ASA の参照データは、高速検索を行うためにメモリに読み込まれます。 現在の実装では、同じ参照データで複数回結合する場合でも、参照データを使用する結合操作ごとに参照データのコピーがメモリに保持されます。 **PARTITION BY** を使用したクエリでは、各パーティションに参照データのコピーが 1 つあるため、パーティションは完全に分離されます。 相乗効果により、複数のパーティションで参照データを使用して複数回結合した場合、メモリ使用量がすぐに非常に高くなることがあります。  

### <a name="use-of-udf-functions"></a>UDF 関数の使用
UDF 関数を追加すると、Azure Stream Analytics は、JavaScript ランタイムをメモリに読み込みます。 これは SU% に影響します。

## <a name="next-steps"></a>次の手順
* [Azure Stream Analytics での並列化可能クエリの作成](stream-analytics-parallelization.md)
* [スループット向上のための Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
