---
title: Azure Stream Analytics でのクエリの並列処理とスケールの使用
description: この記事では、Stream Analytics ジョブをスケールするために入力パーティションの構成、クエリ定義のチューニング、およびジョブのストリーミング ユニットの設定を行う方法について説明します。
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 0b68819ba032d7655433aadd30fe2852941096ce
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000548"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Azure Stream Analytics でのクエリの並列処理の活用
この記事では、Azure Stream Analytics で並列処理を活用する方法を示します。 入力パーティションの構成と分析クエリ定義のチューニングによって Stream Analytics ジョブをスケールする方法について説明します。
前提条件として、「[ストリーミング ユニットを効率的に使用できるようにジョブを最適化する](stream-analytics-streaming-unit-consumption.md)」で説明されているストリーミング ユニットの概念について理解しておく必要があります。

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Stream Analytics ジョブの構成について教えてください。
Stream Analytics のジョブ定義は、入力、クエリ、および出力で構成されます。 入力は、ジョブがデータ ストリームを読み取る場所です。 クエリは、データ入力ストリームを変換するために使用されます。出力は、ジョブ結果の送信先です。  

ジョブにはデータ ストリーミング用に少なくとも 1 つの入力ソースが必要です。 データ ストリームの入力ソースは、Azure イベント ハブまたは Azure Blob Storage に格納できます。 詳細については、「[Azure Stream Analytics の概要](stream-analytics-introduction.md)」および「[Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)」をご覧ください。

## <a name="partitions-in-sources-and-sinks"></a>ソースとシンク内のパーティション
Stream Analytics ジョブのスケーリングでは、入力または出力でパーティションを利用します。 パーティション分割すると、パーティション キーに基づいてデータをサブセットに分割できます。 データを使用するプロセス (Streaming Analytics ジョブなど) では、さまざまなパーティションを同時に使用し、書き込みを実行できるので、スループットが向上します。 

### <a name="inputs"></a>入力
Azure Stream Analytics のすべての入力では、パーティション分割を利用できます。
-   EventHub (PARTITION BY キーワードを使用してパーティション キーを明示的に設定する必要があります)
-   IoT Hub (PARTITION BY キーワードを使用してパーティション キーを明示的に設定する必要があります)
-   BLOB ストレージ

### <a name="outputs"></a>出力

Azure Stream Analytics を使用するときは、出力でパーティション分割を利用できます。
-   Azure Data Lake Storage
-   Azure Functions
-   Azure テーブル
-   Blob Storage (パーティション キーを明示的に設定できます)
-   Cosmos DB (パーティション キーを明示的に設定する必要があります)
-   Event Hubs (パーティション キーを明示的に設定する必要があります)
-   IoT Hub (パーティション キーを明示的に設定する必要があります)
-   Service Bus
- オプションのパーティション分割を使用した SQL および SQL Data Warehouse: 詳細については、[Azure SQL Database への出力](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf)に関するページを参照してください。

Power BI では、パーティション分割がサポートされていません。 ただし、[このセクション](#multi-step-query-with-different-partition-by-values)の説明に従って入力をパーティション分割することはできます 

パーティションの詳細については、次の記事をご覧ください。

* [Event Hubs の機能の概要](../event-hubs/event-hubs-features.md#partitions)
* [データのパーティション分割](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>驚異的並列ジョブ
*驚異的並列*ジョブは、Azure Stream Analytics において最もスケーラブルなシナリオです。 入力の 1 つのパーティションを、出力の 1 つのパーティションに対するクエリの 1 つのインスタンスに接続します。 この並列処理には次の要件があります。

1. クエリ ロジックが同じクエリ インスタンスによって処理される同じキーに依存する場合、イベントが入力の同じパーティションに送信されるようにする必要があります。 Event Hubs または IoT Hub の場合、イベント データに **PartitionKey** 値が設定されている必要があります。 代わりに、パーティション分割された送信元を使用することもできます。 Blob Storage の場合、イベントが同じパーティション フォルダーに送信される必要があります。 クエリ ロジックで、同じクエリ インスタンスによって処理される同じキーが不要の場合は、この要件を無視してかまいません。 このロジックの例として、単純な select-project-filter クエリがあります。  

2. データが入力側でレイアウトされている場合、クエリがパーティション分割されている必要があります。 そのためには、すべてのステップで **PARTITION BY** を使用する必要があります。 複数のステップが許可されますが、すべてのステップが同じキーでパーティション分割されている必要があります。 現時点では、完全な並列ジョブにするために、パーティション キーを **PartitionId** に設定する必要があります。  

3. ほとんどの出力でパーティション分割を利用できますが、ジョブのパーティション分割をサポートしない出力の種類を使用する場合、ジョブは完全には並列になりません。 詳しくは、「[出力](#outputs)」セクションをご覧ください。

4. 入力パーティションの数が出力パーティションの数と同じである必要があります。 Blob Storage 出力では、パーティションをサポートでき、アップストリーム クエリのパーティション構成を継承します。 Blob Storage のパーティション キーを指定すると、データが入力パーティションごとにパーティション分割されるため、結果も完全に並列になります。 完全な並列ジョブを可能にするパーティション値の例を次に示します。

   * 8 個のイベント ハブ入力パーティションと 8 個のイベント ハブ出力パーティション
   * 8 個のイベント ハブ入力パーティションと Blob Storage 出力
   * 8 個のイベント ハブ 入力パーティションと任意のカーディナリティのカスタム フィールドによってパーティション分割された Blob Storage 出力
   * 8 個の Blob Storage 入力パーティションと Blob Storage 出力
   * 8 個の Blob Storage 入力パーティションと 8 個のイベント ハブ出力パーティション

以下のセクションでは、驚異的並列であるシナリオの例を示します。

### <a name="simple-query"></a>単純なクエリ

* 次の内容を入力します。8 個のパーティションがあるイベント ハブ
* 出力:8 個のパーティションがあるイベント ハブ

クエリ:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

このクエリは単純なフィルターです。 そのため、イベント ハブに送信される入力のパーティション分割を気にする必要はありません。 このクエリには **PARTITION BY PartitionId** が含まれているので、前述の要件 2. を満たしています。 出力については、パーティション キーが **PartitionId** に設定されたイベント ハブ出力をジョブで構成する必要があります。 最後に、入力パーティションと出力パーティションの数が同じであることを確認します。

### <a name="query-with-a-grouping-key"></a>グループ化キーが含まれたクエリ

* 次の内容を入力します。8 個のパーティションがあるイベント ハブ
* 出力:BLOB ストレージ

クエリ:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

このクエリにはグループ化キーが含まれています。 そのため、グループ化されたイベントは、同じイベント ハブ パーティションに送信される必要があります。 この例では TollBoothID でグループ化するので、イベントがイベント ハブに送信されるときに、パーティション キーとして TollBoothID が使用されることを確認する必要があります。 その後 ASA で、**PARTITION BY PartitionId** を使用して、このパーティション構成から継承し、完全な並列処理を有効にすることができます。 出力は Blob Storage であるため、要件 4. に記載されているように、パーティション キー値の構成を気にする必要はありません。

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>驚異的並列では "*ない*" シナリオの例

前のセクションでは、驚異的並列のシナリオをいくつか紹介しました。 このセクションでは、驚異的並列のすべての要件を満たしているわけではないシナリオについて説明します。 

### <a name="mismatched-partition-count"></a>パーティション数の不一致
* 次の内容を入力します。8 個のパーティションがあるイベント ハブ
* 出力:32 個のパーティションがあるイベント ハブ

この場合、クエリの内容は問題ではありません。 入力パーティション数と出力パーティション数が一致しない場合、トポロジは驚異的並列ではありません。ただし、一部のレベルまたは並列処理は利用できます。

### <a name="query-using-non-partitioned-output"></a>パーティション分割されていない出力を使用したクエリ
* 次の内容を入力します。8 個のパーティションがあるイベント ハブ
* 出力:Power BI

現在、Power BI 出力ではパーティション分割がサポートされていません。 そのため、このシナリオは驚異的並列ではありません。

### <a name="multi-step-query-with-different-partition-by-values"></a>PARTITION BY 値が異なる複数ステップのクエリ
* 次の内容を入力します。8 個のパーティションがあるイベント ハブ
* 出力:8 個のパーティションがあるイベント ハブ

クエリ:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

ご覧のように、2 番目のステップは **TollBoothId** をパーティション キーとして使用しています。 このステップは最初のステップと異なるので、シャッフルを実行する必要があります。 

上記の各例では、驚異的並列トポロジに準拠する (または準拠していない) Stream Analytics ジョブを紹介しました。 驚異的並列トポロジに準拠するジョブは、最大スケールを実現できる可能性があります。 これらのどのプロファイルにも適合しないジョブについては、今後の更新でスケーリング ガイダンスを提供する予定です。 現時点では、以下のセクションに示す一般的なガイダンスを使用してください。

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>ジョブのストリーミング ユニットの最大数を計算する
Stream Analytics ジョブで使用できるストリーミング ユニットの合計数は、ジョブに定義されたクエリのステップ数と各ステップのパーティション数によって異なります。

### <a name="steps-in-a-query"></a>クエリでのステップ
1 つのクエリに 1 つ以上のステップを含めることができます。 各ステップは、**WITH** キーワードで定義されたサブクエリです。 次のクエリの **SELECT** ステートメントのように、**WITH** キーワードの外にあるクエリ (1 つのクエリのみ) もステップとしてカウントされます。

クエリ:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId
```

このクエリには 2 つのステップがあります。

> [!NOTE]
> このクエリについては、この記事で後ほど詳しく説明します。
>  

### <a name="partition-a-step"></a>ステップをパーティション分割する
ステップをパーティション分割するには、次の条件を満たす必要があります。

* 入力ソースはパーティション分割する。 
* クエリの **SELECT** ステートメントは、パーティション分割された入力ソースから読み取る。
* ステップ内のクエリに **PARTITION BY** キーワードを含める。

クエリをパーティション分割すると、入力イベントが処理されて個々のパーティション グループに集計され、グループごとに出力イベントが生成されます。 集計を結合する場合は、パーティション分割されていない 2 つ目のステップを集計用に作成する必要があります。

### <a name="calculate-the-max-streaming-units-for-a-job"></a>ジョブのストリーミング ユニットの最大数を計算する
パーティション分割されていないステップは、Stream Analytics ジョブの 6 個のストリーミング ユニット (SU) にスケールアップできます。 さらに、パーティション分割されたステップでパーティションごとに 6 SU を追加できます。
いくつかの**例**を次の表に示します。

| クエリ                                               | ジョブの最大 SU 数 |
| --------------------------------------------------- | ------------------- |
| <ul><li>クエリに 1 つのステップが含まれている。</li><li>ステップはパーティション分割されていない。</li></ul> | 6 |
| <ul><li>入力データ ストリームは 16 個にパーティション分割されている。</li><li>クエリに 1 つのステップが含まれている。</li><li>ステップはパーティション分割されていない。</li></ul> | 96 (6 * 16 パーティション) |
| <ul><li>クエリに 2 つのステップが含まれている。</li><li>どのステップもパーティション分割されていない。</li></ul> | 6 |
| <ul><li>入力データ ストリームは 3 つにパーティション分割されている。</li><li>クエリに 2 つのステップが含まれている。 入力ステップはパーティション分割されているが、2 番目のステップはされていない。</li><li><strong>SELECT</strong> ステートメントはパーティション分割された入力から読み取る。</li></ul> | 24 (パーティション分割されたステップ用に 18 + パーティション分割されていないステップ用に 6) |

### <a name="examples-of-scaling"></a>スケーリングの例

次のクエリでは、3 つのブースがある料金所を 3 分間に通過する車の台数を計算します。 このクエリは、最大 6 個の SU にスケールできます。

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

クエリに使用する SU を増やすには、入力データ ストリームとクエリの両方をパーティション分割する必要があります。 データ ストリーム パーティションが 3 に設定されているので、変更を加えた次のクエリを最大 18 個の SU にスケールできます。

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

クエリがパーティション分割されている場合、入力イベントは処理されて個々のパーティション グループに集計されます。 出力イベントは、それぞれのグループに対しても生成されます。 **GROUP BY** フィールドが入力データ ストリームのパーティション キーでない場合、パーティション分割を実行すると予期しない結果になることがあります。 たとえば、前のクエリの **TollBoothId** フィールドは **Input1** のパーティション キーではありません。 そのため、TollBooth 1 のデータが複数のパーティションに分散される可能性があります。

**Input1** の各パーティションは、Stream Analytics によって個別に処理されます。 その結果、同じタンブリング ウィンドウで同じ料金所ブースの複数の通過台数レコードが作成されます。 入力パーティション キーを変更できない場合は、次の例のように、パーティション分割されていないステップを追加してパーティション間の値を集計すると、この問題を解決できます。

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

このクエリは 24 個の SU にスケールできます。

> [!NOTE]
> 2 つのストリームを結合する場合は、結合に使用する列のパーティション キーでストリームがパーティション分割されていることを確認します。 また、両方のストリームに同じ数のパーティションがあることも確認します。
> 
> 





## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を参照してください。

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

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

