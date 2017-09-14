---
title: "スループット向上のための Stream Analytics ジョブのスケーリング | Microsoft Docs"
description: "Stream Analytics ジョブをスケールするために入力パーティションの構成、クエリ定義のチューニング、およびジョブのストリーミング ユニットの設定を行う方法について説明します。"
keywords: "データ ストリーミング、ストリーミング データ処理、分析のチューニング"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 7e857ddb-71dd-4537-b7ab-4524335d7b35
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/22/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: f1e5e11e82d344508aa4375c42d509f96aaa1d00
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>ストリーム データ処理スループット向上のための Azure Stream Analytics ジョブのスケーリング
この記事では、Stream Analytics クエリをチューニングして、Streaming Analytics ジョブのスループットを向上させる方法について説明します。 入力パーティションの構成、分析クエリの定義の調整、ジョブの*ストリーミング ユニット* (SU) の計算と設定を行って、Stream Analytics ジョブをスケールする方法について説明します。 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Stream Analytics ジョブの構成について教えてください。
Stream Analytics のジョブ定義は、入力、クエリ、および出力で構成されます。 入力は、ジョブがデータ ストリームを読み取る場所です。 クエリは、データ入力ストリームを変換するために使用されます。出力は、ジョブ結果の送信先です。  

ジョブにはデータ ストリーミング用に少なくとも 1 つの入力ソースが必要です。 データ ストリームの入力ソースは、Azure イベント ハブまたは Azure Blob Storage に格納できます。 詳細については、「[Azure Stream Analytics の概要](stream-analytics-introduction.md)」および「[Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)」をご覧ください。

## <a name="partitions-in-event-hubs-and-azure-storage"></a>イベント ハブと Azure Storage 内のパーティション
Stream Analytics ジョブのスケーリングでは、入力または出力でパーティションを利用します。 パーティション分割すると、パーティション キーに基づいてデータをサブセットに分割できます。 データを使用するプロセス (Streaming Analytics ジョブなど) では、さまざまなパーティションを同時に使用し、書き込みを実行できるので、スループットが向上します。 Azure Stream Analytics を使用するときは、イベント ハブと Blob Storage でパーティション分割を利用できます。 

パーティションの詳細については、次の記事をご覧ください。

* [Event Hubs の機能の概要](../event-hubs/event-hubs-features.md#partitions)
* [データのパーティション分割](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)


## <a name="streaming-units-sus"></a>ストリーミング ユニット (SU)
ストリーミング ユニット (SU) は、Azure Stream Analytics ジョブを実行するために必要なリソースと処理能力を表します。 SU は、CPU、メモリ、および読み取りと書き込みのレートを組み合わせた測定に基づいて、相対的なイベントの処理能力を記述する方法を提供します。 各 SU は、約 1 MB/秒のスループットに相当します。 

特定のジョブに必要な SU 数の選択は、入力のパーティション構成と、ジョブに定義されたクエリによって異なります。 1 つのジョブについて、クォータまでの SU 数を選択できます。 既定では、各 Azure サブスクリプションには、特定のリージョン内のすべての分析ジョブを対象に最大 50 個という SU のクォータが設定されています。 このクォータを超えてサブスクリプションの SU 数を増やす場合は、[Microsoft サポート](http://support.microsoft.com)までご連絡ください。 ジョブごとの SU 数の有効な値は 1、3、6 であり、6 ずつ増加します。

## <a name="embarrassingly-parallel-jobs"></a>驚異的並列ジョブ
*驚異的並列*ジョブは、Azure Stream Analytics において最もスケーラブルなシナリオです。 入力の 1 つのパーティションを、出力の 1 つのパーティションに対するクエリの 1 つのインスタンスに接続します。 この並列処理には次の要件があります。

1. クエリ ロジックが同じクエリ インスタンスによって処理される同じキーに依存する場合、イベントが入力の同じパーティションに送信されるようにする必要があります。 イベント ハブの場合、イベント データに **PartitionKey** 値が設定されている必要があります。 代わりに、パーティション分割された送信元を使用することもできます。 Blob Storage の場合、イベントが同じパーティション フォルダーに送信される必要があります。 クエリ ロジックで、同じクエリ インスタンスによって処理される同じキーが不要の場合は、この要件を無視してかまいません。 このロジックの例として、単純な select-project-filter クエリがあります。  

2. データが入力側でレイアウトされている場合、クエリがパーティション分割されている必要があります。 そのためには、すべてのステップで **Partition By** を使用する必要があります。 複数のステップが許可されますが、すべてのステップが同じキーでパーティション分割されている必要があります。 現時点では、完全な並列ジョブにするために、パーティション キーを **PartitionId** に設定する必要があります。  

3. 現在、パーティション分割された出力をサポートしているのは、イベント ハブと Blob Storage だけです。 イベント ハブ出力では、パーティション キーを **PartitionId** として構成する必要があります。 Blob Storage 出力では、何もする必要はありません。  

4. 入力パーティションの数が出力パーティションの数と同じである必要があります。 現在、Blob Storage 出力ではパーティションをサポートしていませんが、 アップストリーム クエリのパーティション構成を継承するので問題ありません。 完全な並列ジョブを可能にするパーティション値の例を次に示します。  

   * 8 個のイベント ハブ入力パーティションと 8 個のイベント ハブ出力パーティション
   * 8 個のイベント ハブ入力パーティションと Blob Storage 出力  
   * 8 個の Blob Storage 入力パーティションと Blob Storage 出力  
   * 8 個の Blob Storage 入力パーティションと 8 個のイベント ハブ出力パーティション  

以下のセクションでは、驚異的並列であるシナリオの例を示します。

### <a name="simple-query"></a>単純なクエリ

* 入力: 8 個のパーティションがあるイベント ハブ
* 出力: 8 個のパーティションがあるイベント ハブ

クエリ:

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

このクエリは単純なフィルターです。 そのため、イベント ハブに送信される入力のパーティション分割を気にする必要はありません。 このクエリには **Partition By PartitionId** が含まれているので、前述の要件 2. を満たしています。 出力については、パーティション キーが **PartitionId** に設定されたイベント ハブ出力をジョブで構成する必要があります。 最後に、入力パーティションと出力パーティションの数が同じであることを確認します。

### <a name="query-with-a-grouping-key"></a>グループ化キーが含まれたクエリ

* 入力: 8 個のパーティションがあるイベント ハブ
* 出力: Blob Storage

クエリ:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

このクエリにはグループ化キーが含まれています。 そのため、同じキーを同じクエリ インスタンスで処理する必要があります。つまり、パーティション分割された方法でイベントをイベント ハブに送信する必要があります。 しかし、どのキーを使用すればよいのでしょうか。 **PartitionId** はジョブ ロジック概念です。 実際に考慮すべきキーは **TollBoothId** であるため、イベント データの **PartitionKey** 値を **TollBoothId** にする必要があります。 そのためには、クエリで **Partition By** を **PartitionId** に設定します。 出力は Blob Storage であるため、要件 4. に記載されているように、パーティション キー値の構成を気にする必要はありません。

### <a name="multi-step-query-with-a-grouping-key"></a>グループ化キーが含まれた複数ステップのクエリ
* 入力: 8 個のパーティションがあるイベント ハブ
* 出力: 8 個のパーティションがあるイベント ハブ インスタンス

クエリ:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

このクエリにはグループ化キーが含まれているので、同じキーを同じクエリ インスタンスで処理する必要があります。 前の例と同じ方法を使用できます。 この例では、クエリに複数のステップがあります。 各ステップに **Partition By PartitionId** があるので、 このクエリは要件 3. を満たしています。 出力については、前述のようにパーティション キーを **PartitionId** に設定する必要があります。 また、入力と同じ数のパーティションが必要です。

## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>驚異的並列では*ない*シナリオの例

前のセクションでは、驚異的並列のシナリオをいくつか紹介しました。 このセクションでは、驚異的並列のすべての要件を満たしているわけではないシナリオについて説明します。 

### <a name="mismatched-partition-count"></a>パーティション数の不一致
* 入力: 8 個のパーティションがあるイベント ハブ
* 出力: 32 個のパーティションがあるイベント ハブ

この場合、クエリの内容は問題ではありません。 入力パーティション数と出力パーティション数が一致しない場合、トポロジは驚異的並列ではありません。

### <a name="not-using-event-hubs-or-blob-storage-as-output"></a>出力としてイベント ハブまたは Blob Storage を使用していない
* 入力: 8 個のパーティションがあるイベント ハブ
* 出力: PowerBI

現在、PowerBI 出力ではパーティション分割をサポートしていません。 そのため、このシナリオは驚異的並列ではありません。

### <a name="multi-step-query-with-different-partition-by-values"></a>Partition By 値が異なる複数ステップのクエリ
* 入力: 8 個のパーティションがあるイベント ハブ
* 出力: 8 個のパーティションがあるイベント ハブ

クエリ:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId

ご覧のように、2 番目のステップは **TollBoothId** をパーティション キーとして使用しています。 このステップは最初のステップと異なるので、シャッフルを実行する必要があります。 

上記の各例では、驚異的並列トポロジに準拠する (または準拠していない) Stream Analytics ジョブを紹介しました。 驚異的並列トポロジに準拠するジョブは、最大スケールを実現できる可能性があります。 これらのどのプロファイルにも適合しないジョブについては、今後の更新でスケーリング ガイダンスを提供する予定です。 現時点では、以下のセクションに示す一般的なガイダンスを使用してください。

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>ジョブのストリーミング ユニットの最大数を計算する
Stream Analytics ジョブで使用できるストリーミング ユニットの合計数は、ジョブに定義されたクエリのステップ数と各ステップのパーティション数によって異なります。

### <a name="steps-in-a-query"></a>クエリでのステップ
1 つのクエリに 1 つ以上のステップを含めることができます。 各ステップは、**WITH** キーワードで定義されたサブクエリです。 次のクエリの **SELECT** ステートメントのように、**WITH** キーワードの外にあるクエリ (1 つのクエリのみ) もステップとしてカウントされます。

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

このクエリには 2 つのステップがあります。

> [!NOTE]
> このクエリについては、この記事で後ほど詳しく説明します。
>  

### <a name="partition-a-step"></a>ステップをパーティション分割する
ステップをパーティション分割するには、次の条件を満たす必要があります。

* 入力ソースはパーティション分割する。 
* クエリの **SELECT** ステートメントは、パーティション分割された入力ソースから読み取る。
* ステップ内のクエリに **Partition By** キーワードを含める。

クエリをパーティション分割すると、入力イベントが処理されて個々のパーティション グループに集計され、グループごとに出力イベントが生成されます。 集計を結合する場合は、パーティション分割されていない 2 つ目のステップを集計用に作成する必要があります。

### <a name="calculate-the-max-streaming-units-for-a-job"></a>ジョブのストリーミング ユニットの最大数を計算する
パーティション分割されていないステップは、Stream Analytics ジョブの 6 個のストリーミング ユニット (SU) にスケールアップできます。 SU を追加するには、ステップをパーティション分割する必要があります。 パーティションごとに 6 個の SU を設定できます。

<table border="1">
<tr><th>クエリ</th><th>ジョブの最大 SU 数</th></td>

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
<li><strong>SELECT</strong> ステートメントはパーティション分割された入力から読み取る。</li>
</ul>
</td>
<td>24 (パーティション分割されたステップ用に 18 + パーティションされていないステップ用に 6)</td></tr>
</table>

### <a name="examples-of-scaling"></a>スケーリングの例

次のクエリでは、3 つのブースがある料金所を 3 分間に通過する車の台数を計算します。 このクエリは、最大 6 個の SU にスケールできます。

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

クエリに使用する SU を増やすには、入力データ ストリームとクエリの両方をパーティション分割する必要があります。 データ ストリーム パーティションが 3 に設定されているので、変更を加えた次のクエリを最大 18 個の SU にスケールできます。

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

クエリがパーティション分割されている場合、入力イベントは処理されて個々のパーティション グループに集計されます。 出力イベントは、それぞれのグループに対しても生成されます。 **GROUP BY** フィールドが入力データ ストリームのパーティション キーでない場合、パーティション分割を実行すると予期しない結果になることがあります。 たとえば、前のクエリの **TollBoothId** フィールドは **Input1** のパーティション キーではありません。 そのため、TollBooth 1 のデータが複数のパーティションに分散される可能性があります。

**Input1** の各パーティションは、Stream Analytics によって個別に処理されます。 その結果、同じタンブリング ウィンドウで同じ料金所ブースの複数の通過台数レコードが作成されます。 入力パーティション キーを変更できない場合は、次の例のように、パーティション分割されていないステップを追加することで、この問題を解決できます。

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

このクエリは 24 個の SU にスケールできます。

> [!NOTE]
> 2 つのストリームを結合する場合は、結合に使用する列のパーティション キーでストリームがパーティション分割されていることを確認します。 また、両方のストリームに同じ数のパーティションがあることも確認します。
> 
> 

## <a name="configure-stream-analytics-streaming-units"></a>Stream Analytics のストリーミング ユニットを構成する

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. リソースの一覧で、スケールする Stream Analytics ジョブを見つけて開きます。
3. ジョブ ブレードで、**[構成]** の **[スケール]** をクリックします。

    ![Azure Portal での Stream Analytics ジョブの構成][img.stream.analytics.preview.portal.settings.scale]

4. スライダーを使用してジョブの SU 数を設定します。 特定の SU 設定に限定されることに注意してください。


## <a name="monitor-job-performance"></a>ジョブのパフォーマンスを監視する
Azure Portal を使用して、ジョブのスループットを追跡できます。

![Azure Stream Analytics のジョブの監視][img.stream.analytics.monitor.job]

ワークロードの予想されるスループットを計算します。 スループットが予想よりも低い場合は、入力パーティションを調整し、クエリをチューニングして、ジョブに SU を追加します。


## <a name="visualize-stream-analytics-throughput-at-scale-the-raspberry-pi-scenario"></a>スケール時の Stream Analytics のスループットの視覚化 - Raspberry Pi のシナリオ
Stream Analytics ジョブのスケールのしくみを理解できるように、Raspberry Pi デバイスからの入力に基づく実験を行いました。 この実験により、複数のストリーミング ユニットとパーティションのスループットへの影響がわかります。

このシナリオでは、デバイスがセンサー データ (クライアント) をイベント ハブに送信します。 Streaming Analytics がそのデータを処理し、出力としてアラートまたは統計を別のイベント ハブに送信します。 

クライアントは、センサー データを JSON 形式で送信します。 データ出力も JSON 形式です。 データは次のようになります。

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

次のクエリを使用して、ライトがオフになったときにアラートを送信します。

    SELECT AVG(lght),
     "LightOff" as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>スループットを測定する

このコンテキストでは、スループットは一定時間内に Stream Analytics によって処理された入力データの量です  (ここでは 10 分間測定)。入力データの最適な処理スループットを実現するために、データ ストリーム入力とクエリの両方をパーティション分割しました。 また、処理された入力イベントの数を測定するために、クエリに **COUNT()** を含めました。 ジョブが入力イベントの発生を待機しているだけにならないように、入力イベント ハブの各パーティションには、約 300 MB の入力データが事前に読み込まれています。

イベント ハブでストリーミング ユニット数と対応するパーティション数を増やしたときの結果を次の表に示します。  

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

次のグラフは、SU 数とスループットの関係を視覚的に示しています。

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


