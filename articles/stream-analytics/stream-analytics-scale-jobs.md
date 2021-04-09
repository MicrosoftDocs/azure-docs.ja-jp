---
title: Azure Stream Analytics ジョブでのスケールアップとスケールアウト
description: この記事では、入力データのパーティション分割、クエリのチューニング、ジョブ ストリーミング ユニットの設定によって、Stream Analytics ジョブをスケーリングする方法について説明します。
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: e3d4fd6b6b83681284278d10409a1c16394db31f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018685"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>スループット向上のために Azure Stream Analytics ジョブをスケーリングする
この記事では、Stream Analytics クエリをチューニングして、Streaming Analytics ジョブのスループットを向上させる方法について説明します。 次のガイドを使用して、高い負荷を処理し、より多くのシステム リソース (より多くの帯域幅、より多くの CPU リソース、より多くのメモリなど) を利用するようにジョブをスケーリングできます。
前提条件として、次の記事を読む必要があります。
-   [ストリーミング ユニットの理解と調整](stream-analytics-streaming-unit-consumption.md)
-   [並列化可能ジョブの作成](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>ケース 1 - 複数の入力パーティションでクエリが本質的に完全並列化可能な場合
入力のパーティション間でクエリが本質的に完全並列化可能な場合は、次の手順に従うことができます。
1.  **PARTITION BY** キーワードを使用して、クエリを驚異的並列として作成します。 詳しくは、[このページ](stream-analytics-parallelization.md)の驚異並列ジョブのセクションをご覧ください。
2.  クエリで使用される出力の種類に応じて、一部の出力は並列化できない可能性や、驚異的並列とするためにさらに構成が必要な場合があります。 たとえば、PowerBI の出力は並列化できません。 出力は、出力シンクに送信する前に常にマージされます。 BLOB、Table、ADLS、Service Bus、Azure 関数は自動的に並列化されます。 SQL と Azure Synapse Analytics の出力には、並列化のオプションがあります。 イベント ハブでは、PartitionKey 構成を **PARTITION BY** フィールド (通常は PartitionId) と一致するように設定する必要があります。 イベント ハブの場合は、パーティション間でのクロスオーバーを回避するために、すべての入力とすべての出力でパーティション数が一致することにも注意してください。 
3.  **6 SU** (1 つのコンピューティング ノードの全容量) でクエリを実行して達成可能な最大スループットを測定し、**GROUP BY** を使用する場合は、ジョブで処理できるグループ数 (カーディナリティ) を測定します。 ジョブがシステム リソース制限に達した場合の一般的な症状は次のとおりです。
    - SU % 使用率のメトリックが 80% を超えている。 これは、メモリ使用率が高いことを示します。 このメトリックの増加に影響する要因については、[こちら](stream-analytics-streaming-unit-consumption.md)をご覧ください。 
    -   出力タイムスタンプが実時間よりも遅れている。 クエリ ロジックによっては、出力タイムスタンプに、実時間からの論理オフセットがある場合があります。 ただし、それらはほぼ同じ速度で進行します。 出力タイムスタンプの遅れが徐々に増加している場合は、システムが過負荷になっていることを示しています。 ダウンストリーム出力のシンク調整、または高 CPU 使用率の結果である可能性があります。 現時点では CPU 使用率のメトリックを提供していないため、2 つを区別するのは困難な可能性があります。
        - 問題の原因がシンク調整の場合は、出力パーティション (また、ジョブを完全並列化可能に保つには入力パーティションも) の数を増やすか、シンクのリソースの量 (CosmosDB の要求ユニット数など) を増やすことが必要な場合があります。
    - ジョブ ダイアグラムには、各入力にパーティションごとのバックログ イベント メトリックがあります。 バックログ イベント メトリックが増加し続ける場合も、(出力シンクの調整または高い CPU 使用率が原因で) システム リソースが制約されていることを示しています。
4.  6 SU ジョブが到達する可能性のある制限を確認したら、特定のパーティションを "ホット" にするデータ スキューがないことを前提として、SU を追加するときにジョブの処理容量を線形に推定できます。

> [!NOTE]
> 適切なストリーミング ユニット数を選択します。Stream Analytics では、6 SU が追加されるごとに処理ノードが作成されるため、ノード数を入力パーティション数の除数として、パーティションがノードに均等に分散されるようにすることが最適です。
> たとえば、6 SU ジョブが 4 MB/秒の処理速度を達成できることを測定し、入力パーティション数が 4 であるとします。 約 8 MB/秒の処理速度を達成するには 12 SU、16 MB/秒を達成するには 24 SU でジョブを実行することを選択できます。 入力速度の関数として、ジョブの SU 数をいつどのような値に増やすかを決定できます。


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>ケース 2 - クエリが驚異的並列でない場合。
クエリが驚異的並列でない場合は、次の手順に従うことができます。
1.  まず **PARTITION BY** を指定しないでクエリを開始してパーティション分割の複雑さを回避し、次に [ケース 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) と同様に 6 SU でクエリを実行して最大負荷を測定します。
2.  スループットについて予想される負荷を達成できる場合は完了です。 または、3 SU と 1 SU で実行している同じジョブを測定して、シナリオに合った SU の最小数を調べることもできます。
3.  目的のスループットを達成できない場合は、可能であればクエリを複数のステップに分割し (複数ステップがまだない場合)、クエリの各ステップに最大 6 SU を割り当てます。 たとえば、3 つのステップがある場合は、[スケール] オプションで 18 SU を割り当てます。
4.  このようなジョブを実行する場合、Stream Analytics は、専用の 6 SU リソースを持つ独自のノードに各ステップを配置します。 
5.  負荷ターゲットをまだ達成していない場合は、入力により近いステップから開始して **PARTITION BY** を使用してみます。 自然にはパーティション分割できない可能性のある **GROUP BY** 演算子では、ローカル/グローバル集計パターンを使用して、パーティション分割された **GROUP BY** に続けてパーティション分割されていない **GROUP BY** を実行できます。 たとえば、3 分ごとに各料金所を通過する自動車数と、6 SU で処理できる量を超えるデータ量をカウントするとします。

クエリ:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
上記のクエリでは、パーティションごとに各料金所で自動車数をカウントしてから、すべてのパーティションのカウントを合計します。

パーティション分割されたら、ステップのパーティションごとに、最大 6 SU を割り当てます。6 SU を持つ各パーティションが最大であるため、各パーティションを独自の処理ノードに配置できます。

> [!Note]
> クエリがパーティション分割できない場合は、複数ステップ クエリで SU を追加しても、常にスループットが向上するとは限りません。 パフォーマンスを向上させる 1 つの方法は、手順 5. で説明したようにローカル/グローバル集計パターンを使用して初期ステップで量を削減することです。

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>ケース 3 - ジョブで大量の独立したクエリを実行している場合。
1 つのジョブで複数のテナントのデータを処理する方がコスト効果の高い特定の ISV ユース ケースでは、テナントごとに個別の入力と出力を使用すると、1 つのジョブ内で実行する独立したクエリがかなり少数 (20 など) になることがあります。 このような各サブクエリの負荷が比較的少ないことが前提です。 この場合は、以下の手順に従うことができます。
1.  この場合、クエリでは **PARTITION BY** を使用しないでください
2.  イベント ハブを使用している場合は、入力パーティション数を最小値の 2 に減らします。
3.  6 SU でクエリを実行します。 各サブクエリで予想される負荷で、ジョブがシステム リソースの制限に達するまで、できるだけ多くのサブクエリを追加します。 この状況が発生した場合の症状については、[ケース 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) をご覧ください。
4.  上記で測定したサブクエリの制限に達したら、新しいジョブへのサブクエリの追加を開始します。 負荷の傾斜がないと仮定した場合、独立したクエリの数の関数として実行するジョブの数は、かなり線形になります。 サービスを提供するテナント数の関数として、実行する必要がある 6 SU ジョブの数を予測できます。
5.  このようなクエリで参照データ結合を使用する場合は、同じ参照データで結合する前に入力を統合します。 そのうえで、必要に応じてイベントを分割してください。 そうしないと、各参照データ結合がメモリに参照データのコピーを保持し、メモリ使用量が不必要に増加する可能性があります。

> [!Note] 
> 各ジョブにはいくつのテナントを配置しますか。
> このクエリ パターンでは、多くの場合、サブクエリの数が多くなり、非常に大規模で複雑なトポロジになることがあります。 ジョブのコントローラーは、このような大規模トポロジを処理できないことがあります。 原則として、1 SU ジョブでは 40 テナント未満、3 SU および 6 SU ジョブでは 60 テナント未満にします。 コントローラーの容量を超過すると、ジョブは正常に開始しません。



## <a name="get-help"></a>ヘルプの参照
詳細については、[Azure Stream Analytics に関する Microsoft Q&A 質問ページ](/answers/topics/azure-stream-analytics.html)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](/rest/api/streamanalytics/)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/