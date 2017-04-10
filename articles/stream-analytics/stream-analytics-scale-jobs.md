---
title: "スループット向上のための Stream Analytics ジョブのスケーリング | Microsoft Docs"
description: "Stream Analytics ジョブをスケールするために入力パーティションの構成、クエリ定義のチューニング、およびジョブのストリーミング ユニットの設定を行う方法について説明します。"
keywords: "データ ストリーミング、ストリーミング データ処理、分析のチューニング"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 7e857ddb-71dd-4537-b7ab-4524335d7b35
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: c0a0959a5484111ee5426204e15434300cb6a438
ms.lasthandoff: 12/08/2016


---
# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>ストリーム データ処理スループット向上のための Azure Stream Analytics ジョブのスケーリング
Stream Analytics の分析ジョブをチューニングし、 *ストリーミング ユニット* を計算する方法と、Stream Analytics ジョブをスケールするために入力パーティションの構成、分析クエリ定義のチューニング、およびジョブのストリーミング ユニットの設定を行う方法について説明します。 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Stream Analytics ジョブの構成について教えてください。
Stream Analytics のジョブ定義は、入力、クエリ、および出力で構成されます。 入力は、ジョブがデータ ストリームを読み取る場所です。クエリは、データ入力ストリームを変換するために使用します。出力は、ジョブがジョブ結果を送信する宛先です。  

ジョブにはデータ ストリーミング用に少なくとも 1 つの入力ソースが必要です。 データ ストリームの入力ソースは、Azure Service Bus Event Hub または Azure BLOB ストレージに格納できます。 詳細については、「[Azure Stream Analytics の概要](stream-analytics-introduction.md)」および「[Azure Stream Analytics の使用](stream-analytics-get-started.md)」をご覧ください。

## <a name="configuring-streaming-units"></a>ストリーミング ユニットの構成
ストリーミング ユニット (SU) は、Azure Stream Analytics ジョブを実行するために必要なリソースと処理能力を表します。 SU は、CPU、メモリ、および読み取りと書き込みのレートを組み合わせた測定に基づいて、相対的なイベントの処理能力を記述する方法を提供します。 各ストリーミング ユニットは、約 1 MB/秒のスループットに対応します。 

特定のジョブに必要な SU の数の選択は、入力のパーティション構成と、ジョブに定義されたクエリによって異なります。 Azure クラシック ポータルを使用して、1 つのジョブにクォータまでのストリーミング ユニットを設定できます。 また、1 つの Azure サブスクリプションの既定のクォータでは、ストリーミング ユニットの最大数は特定のリージョン内のすべての分析ジョブを合わせて 50 個です。 サブスクリプションのストリーミング ユニット数を増やすには、 [Microsoft サポート](http://support.microsoft.com)までご連絡ください。

ジョブで使用できるストリーミング ユニットの数は、入力のパーティション構成とジョブに定義されているクエリによって異なります。 ストリームのユニットの有効な値を使用する必要がある点にも注意してください。 次に示すように、有効な値は 1、3、6 から開始し、増分単位 6 で増加します。

![Azure Stream Analytics のストリーミング ユニットのスケーリング][img.stream.analytics.streaming.units.scale]

この記事では、分析ジョブのスループットを向上させるための計算とクエリ チューニングの方法について説明します。

## <a name="embarrassingly-parallel-job"></a>驚異的並列ジョブ
驚異的並列ジョブは、Azure Stream Analytics において最もスケーラブルなシナリオです。 入力の 1 つのパーティションを、出力の 1 つのパーティションに対するクエリの 1 つのインスタンスに接続します。 この並列性を実現するには、次の条件を満たす必要があります。

1. クエリのロジックが同じクエリ インスタンスによって処理される同じキーに依存する場合、イベントが入力の同じパーティションに送られるようにする必要があります。 Event Hubs の場合、これは、イベント データが **PartitionKey** セットを持つ必要があること、またはパーティション分割された送信側を使用できることを意味します。 BLOB の場合、これはイベントが同じパーティション フォルダーに送信されることを意味します。 クエリのロジックで、同じキーが同じクエリ インスタンスによって処理される必要ない場合は、この要件を無視できます。 この要件の例は、単純な選択/プロジェクト/フィルター クエリです。  
2. データが入力側で必要なレイアウトになっている場合、クエリがパーティション分割されている必要があります。 そのためには、すべてのステップで **Partition By** を使用する必要があります。 複数のステップが許可されますが、そのすべてが同じキーによってパーティション分割されている必要があります。 他に注意する必要があることとして、現時点では、完全な並列ジョブにするにはパーティション キーを **PartitionId** に設定する必要があります。  
3. 現在、パーティション分割された出力をサポートしているのは Event Hubs と BLOB だけです。 Event Hubs 出力の場合は、**PartitionKey** フィールドを **PartitionId** に構成する必要があります。 BLOB の場合は、何もする必要はありません。  
4. もう 1 つ注意すべきことは、入力パーティションの数が出力パーティションの数と一致している必要があります。 現在は BLOB 出力はパーティションをサポートしていませんが、アップストリーム クエリのパーティション方式を継承するため問題ありません。    完全な並列ジョブを許可するパーティション値の例:  
   1. 8 Event Hubs 入力パーティションと 8 Event Hubs 出力パーティション
   2. 8 Event Hubs 入力パーティションと BLOB 出力  
   3. 8 BLOB 入力パーティションと BLOB 出力  
   4. 8 BLOB 入力パーティションと 8 Event Hubs 出力パーティション  

驚異的並列であるシナリオの例を次に示します。

### <a name="simple-query"></a>単純なクエリ
入力 – 8 パーティションのEvent Hubs、出力 – 8 パーティションの Event Hubs

**クエリ:**

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

このクエリは単純なフィルターであり、そのため Event Hubs に送信する入力のパーティション分割について心配する必要はありません。 クエリには **PartitionId** の **Partition By** があるため、前記の 2 番目の要件を満たします。 出力では、**PartitionKey** フィールドが **PartitionId** に設定されるようにジョブで Event Hubs 出力を構成する必要があります。 最後に入力パーティションと出力パーティションは同じ数です。 このトポロジは驚異的並列です。

### <a name="query-with-grouping-key"></a>グループ化キーが含まれるクエリ
入力 – 8 パーティションの Event Hubs、出力 – BLOB

**クエリ:**

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

このクエリはグループ化キーがあるので、同じキーを同じクエリ インスタンスで処理する必要があります。 つまり、パーティション分割された方法でイベントを Event Hubs に送信する必要があります。 どのキーが重要なのでしょうか。 **PartitionId** はジョブ ロジック概念であり、実際に考慮すべきキーは **TollBoothId** です。 つまり、Event Hubs に送信するイベント データの **PartitionKey** を、イベントの **TollBoothId** になるように設定する必要があります。 クエリの **PartitionId** に **Partition By** があるため問題ありません。 出力については、BLOB であるため、 **PartitionKey**の構成について心配する必要はありません。 要件 4 については、これは BLOB なので、心配する必要はありません。 このトポロジは驚異的並列です。

### <a name="multi-step-query-with-grouping-key"></a>グループ化キーが含まれる複数ステップのクエリ
入力 – 8 パーティションのEvent Hubs、出力 – 8 パーティションの Event Hubs

**クエリ:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

このクエリはグループ化キーがあるので、同じキーを同じクエリ インスタンスで処理する必要があります。 前のクエリと同じ戦略を使用できます。 クエリには複数のステップがあります。 各ステップの **PartitionId** に **Partition By** があるため、 問題ありません。 出力については、先述のように **PartitionKey** を **PartitionId** に設定する必要があり、入力と同じ数のパーティションがあります。 このトポロジは驚異的並列です。

## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>驚異的並列ではないシナリオの例
### <a name="mismatched-partition-count"></a>パーティション数の不一致
入力 – 8 パーティションのEvent Hubs、出力 – 32 パーティションの Event Hubs

入力パーティションと出力パーティションの数が異なるので、この場合はクエリが何かは関係ありません。

### <a name="not-using-event-hubs-or-blobs-as-output"></a>出力として Event Hubs または BLOB を使用していない
入力 – 8 パーティションの Event Hubs、出力 – PowerBI

現在、PowerBI 出力はパーティション分割をサポートしていません。

### <a name="multi-step-query-with-different-partition-by-values"></a>Partition By 値が異なる複数ステップのクエリ
入力 – 8 パーティションのEvent Hubs、出力 – 8 パーティションの Event Hubs

**クエリ:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId

ご覧のように、2 番目のステップは **TollBoothId** をパーティション キーとして使用しています。 これは 1 番目のステップと異なるので、シャッフルを行う必要があります。 

驚異的並列トポロジを実現できて最大スケールになる可能性のある Stream Analytics ジョブの例と反例があります。 これらのどのプロファイルにも当てはまらないジョブについては、将来の更新で、他の標準的 Stream Analytics シナリオの最大限のスケーリングについて詳しく説明します。

現時点では、一般的なガイダンスを次に示しておきます。

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>ジョブのストリーミング ユニットの最大数を計算する
Stream Analytics ジョブで使用できるストリーミング ユニットの合計数は、ジョブに定義されたクエリのステップ数と各ステップのパーティション数によって異なります。

### <a name="steps-in-a-query"></a>クエリでのステップ
1 つのクエリに 1 つ以上のステップを含めることができます。 各ステップは、 **WITH** キーワードを使用して定義されたサブクエリです。 **WITH** キーワードの外にあるクエリだけが、ステップとしてカウントされます (例: 次のクエリの **SELECT** ステートメント)。

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

上のクエリのステップは 2 つです。

> [!NOTE]
> このサンプル クエリについては、記事の後半で説明します。
> 
> 

### <a name="partition-a-step"></a>ステップをパーティション分割する
ステップをパーティション分割するには、次の条件を満たす必要があります。

* 入力ソースはパーティション分割する。 詳細については、「 [Event Hubs のプログラミング ガイド](../event-hubs/event-hubs-programming-guide.md)」を参照してください。
* クエリの **SELECT** ステートメントは、パーティション分割された入力ソースから読み取る。
* ステップ内のクエリに **Partition By** 句を含める。

クエリがパーティション分割されている場合、入力イベントは処理されて個々のパーティション グループに集計され、そのグループごとに出力イベントが生成されます。 結合された集計が望ましい場合は、パーティション分割されていない 2 つ目のステップを集計用に作成する必要があります。

### <a name="calculate-the-max-streaming-units-for-a-job"></a>ジョブのストリーミング ユニットの最大数を計算する
Stream Analytics ジョブのパーティション分割されていないステップは、最大 6 個のストリーミング ユニットにスケールアップできます。 ストリーミング ユニットを追加するには、ステップをパーティション分割する必要があります。 各パーティションには 6 個のストリーミング ユニットを設定できます。

<table border="1">
<tr><th>ジョブのクエリ</th><th>ジョブの最大ストリーミング ユニット数</th></td>

<tr><td>
<ul>
<li>クエリに 1 つのステップが含まれている。</li>
<li>ステップはパーティション分割されていない。</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>入力データ ストリームは 3 つにパーティション分割されている。</li>
<li>クエリに 1 つのステップが含まれている。</li>
<li>ステップはパーティション分割されていない。</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>クエリに 2 つのステップが含まれている。</li>
<li>どのステップもパーティション分割されていない。</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>入力データ ストリームは 3 つにパーティション分割されている。</li>
<li>クエリに 2 つのステップが含まれている。 入力ステップはパーティション分割されているが、2 番目のステップはされていない。</li>
<li>SELECT ステートメントはパーティション分割された入力から読み取る。</li>
</ul>
</td>
<td>24 (パーティション分割されたステップ用に 18 + パーティションされていないステップ用に 6)</td></tr>
</table>

### <a name="examples-of-scale"></a>スケールの例
次のクエリは、3 つのブースがある高速道路の料金所を 3 分間に通過する車の台数を計算します。 このクエリを最大 6 個のストリーミング ユニットにスケールアップできます。

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

クエリに使用するストリーミング ユニットを増やすには、入力データ ストリームとクエリの両方をパーティション分割する必要があります。 データ ストリームのパーティションが 3 つに設定されている場合、変更を加えた次のクエリを最大 18 個のストリーミング ユニットにスケールアップできます。

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

クエリがパーティション分割されている場合、入力イベントは処理されて個々のパーティション グループに集計されます。 出力イベントは、それぞれのグループに対しても生成されます。 **Group-by** フィールドが入力データ ストリームのパーティション キーでない場合、パーティション分割を実行すると予期しない結果になることがあります。 たとえば、前のサンプル クエリの **TollBoothId** フィールドは Input1 のパーティション キーではありません。 TollBooth 1 のデータは、複数のパーティションに分散できます。

Input1 の各パーティションは Stream Analytics によって個別に処理され、同じタンブリング ウィンドウで同じ料金所の複数の通過台数レコードが作成されます。 入力パーティション キーを変更できない場合は、パーティション分割されていないステップを追加することで、この問題を解決できます。たとえば次のようにします。

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

このクエリは 24 個のストリーミング ユニットにスケールできます。

> [!NOTE]
> 2 つのストリームを結合している場合は、結合を実行した列のパーティション キーでストリームがパーティション分割され、両方のストリームに同じ数のパーティションが作成されていることを確認してください。
> 
> 

## <a name="configure-stream-analytics-job-partition"></a>Stream Analytics ジョブのパーティションを構成する
**ジョブのストリーミング ユニットを調整するには**

1. [管理ポータル](https://manage.windowsazure.com)にサインインします。
2. 左側のウィンドウで **[Stream Analytics]** をクリックします。
3. スケールする Stream Analytics ジョブをクリックします。
4. ページの上部にある **[スケール]** をクリックします。

![Azure Stream Analytics のストリーミング ユニットのスケーリング][img.stream.analytics.streaming.units.scale]

Azure ポータルの [設定] からスケールの設定にアクセスできます。

![Azure ポータルでの Stream Analytics ジョブの構成][img.stream.analytics.preview.portal.settings.scale]

## <a name="monitor-job-performance"></a>ジョブのパフォーマンスを監視する
管理ポータルを使用して、ジョブのスループット (イベント数/秒) を追跡できます。

![Azure Stream Analytics のジョブの監視][img.stream.analytics.monitor.job]

ワークロードのスループット予測 (イベント数/秒) を計算します。 スループットが予測よりも小さい場合、入力パーティションのチューニングとクエリのチューニングを実行し、ジョブにストリーミング ユニットを追加してください。

## <a name="stream-analytics-throughput-at-scale---raspberry-pi-scenario"></a>スケールごとの Stream Analytics のスループット - Raspberry Pi のシナリオ
複数のストリーミング ユニット間での処理スループットを考察する標準的なシナリオで、Stream Analytics ジョブがどのようにスケールするかを理解するために、ここでセンサー データ (クライアント) を Event Hub に送信し、そのデータを処理して、別の Event Hub に出力としてアラートまたは統計を送信するというテストをします。

クライアントは Stream Analytics 向けの合成されたセンサー データを  JSON 形式で Event Hubs に送信し、ASA からのデータ出力も JSON 形式とします。  サンプル データは次のようになります。  

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

クエリ: "電灯がオフになった場合にアラートを送信する"  

    SELECT AVG(lght),
     “LightOff” as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

スループットの測定: このコンテキストではスループットは、固定時間内 (10 分間) で Stream Analytics が処理した入力データの量です。 入力データの最適な処理スループットを実現するために、入力データ ストリームとクエリの両方をパーティション分割する必要があります。 さらに、入力イベントの処理数を測定するために、 **COUNT()**をクエリに含める必要があります。 ジョブが単に着信する入力イベントを待機しているだけとならないように、入力 Event Hub の各パーティションには、十分な入力データ (約 300 MB) が事前に読み込まれています。  

以下の結果は、Event Hubs でのストリーミング ユニット数の増加に対応するパーティション数を示しています。  

<table border="1">
<tr><th>入力パーティション数</th><th>出力パーティション数</th><th>ストリーミング ユニット数</th><th>持続スループット
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/秒</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/秒</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/秒</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/秒</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/秒</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/秒</td>
</tr>
</table>

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


