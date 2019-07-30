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
ms.openlocfilehash: 5eba5601a50640261fa1b488d959f606d4514737
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612214"
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

2. データが入力側でレイアウトされている場合、クエリがパーティション分割されている必要があります。 そのためには、すべてのステップで **PARTITION BY** を使用する必要があります。 複数のステップが許可されますが、すべてのステップが同じキーでパーティション分割されている必要があります。 互換性レベル 1.0 および 1.1 では、完全な並列ジョブにするために、パーティション キーを **PartitionId** に設定する必要があります。 互換性レベル 1.2 以降のジョブの場合、入力設定でカスタム列をパーティション キーとして指定でき、ジョブは PARTITION BY 句がなくても自動的に並列化されます。

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

このクエリは単純なフィルターです。 そのため、イベント ハブに送信される入力のパーティション分割を気にする必要はありません。 互換性レベルが 1.2 より前のジョブには、上記の要件 2 を満たすために、**PARTITION BY PartitionId** 句を含める必要があることに注意してください。 出力については、パーティション キーが **PartitionId** に設定されたイベント ハブ出力をジョブで構成する必要があります。 最後に、入力パーティションと出力パーティションの数が同じであることを確認します。

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

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>互換性レベル 1.2 - PARTITION BY 値が異なる複数ステップのクエリ 
* 次の内容を入力します。8 個のパーティションがあるイベント ハブ
* 出力:8 個のパーティションがあるイベント ハブ

クエリ:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

互換性レベル 1.2 では、既定で並列クエリの実行が可能です。 たとえば、前のセクションのクエリは、"TollBoothId" 列が入力パーティション キーとして設定されている限りパーティション分割されます。 PARTITION BY PartitionId 句は不要です。

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

| Query                                               | ジョブの最大 SU 数 |
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

## <a name="achieving-higher-throughputs-at-scale"></a>大規模な高いスループットの実現

大規模な高いスループットを維持するために、[驚異的並列](#embarrassingly-parallel-jobs)ジョブが必要ですが十分ではありません。 すべてのストレージ システムおよびその対応する Stream Analytics 出力では、可能な限り最適な書き込みスループットを実現する方法がそれぞれ異なっています。 あらゆる大規模シナリオと同じようにいくつかの課題があり、それらは適切な構成を使用して解決できます。 このセクションでは、いくつかの一般的な出力の構成について説明し、1 秒あたり 1,000、5,000、および 10,000 のイベントの取り込み率を実現するためのサンプルを提供します。

以下の観察ではステートレス (パススルー) クエリを使用する Stream Analytics ジョブが使用され、これは Event Hub、Azure SQL DB、または Cosmos DB に書き込む基本的な JavaScript UDF です。

#### <a name="event-hub"></a>イベント ハブ

|取り込み率 (1 秒あたりのイベント数) | ストリーミング ユニット数 | 出力リソース  |
|--------|---------|---------|
| 1,000     |    1    |  2 TU   |
| 5,000     |    6    |  6 TU   |
| 10,000    |    12   |  10 TU  |

[Event Hub](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) ソリューションは、ストリーミング ユニット (SU) とスループットの観点から直線的にスケールするため、Stream Analytics からのデータを分析およびストリーム配信するための最も効率的かつパフォーマンスの高い方法となります。 ジョブは最大 192 SU までスケールアップでき、これは概算して最大 200 MB/秒、あるいは 1 日あたり 19 兆個のイベントの処理に換算されます。

#### <a name="azure-sql"></a>Azure SQL
|取り込み率 (1 秒あたりのイベント数) | ストリーミング ユニット数 | 出力リソース  |
|---------|------|-------|
|    1,000   |   3  |  S3   |
|    5,000   |   18 |  P4   |
|    10,000  |   36 |  P6   |

[Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) では、パーティション分割の継承と呼ばれる並列書き込みがサポートされていますが、既定では有効になっていません。 ただし、パーティション分割の継承と完全な並列クエリを一緒に有効にしても、高いスループットを実現するには十分でない場合があります。 SQL 書き込みスループットは、SQL Azure データベース構成およびテーブル スキーマに大きく依存しています。 [SQL 出力パフォーマンス](./stream-analytics-sql-output-perf.md)に関する記事に、書き込みスループットを最大限に高めることができるパラメーターの詳細が記載されています。 [Azure SQL Database への Azure Stream Analytics 出力](./stream-analytics-sql-output-perf.md#azure-stream-analytics)に関する記事に記載されているように、このソリューションは 8 個のパーティションを超える完全な並列パイプラインとして直線的にスケールせず、SQL 出力の前に再分割が必要な場合があります ([INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) に関する記事を参照してください)。 高い IO 率と、数分おきに発生するログ バックアップからのオーバーヘッドを維持するために、Premium SKU が必要です。

#### <a name="cosmos-db"></a>Cosmos DB
|取り込み率 (1 秒あたりのイベント数) | ストリーミング ユニット数 | 出力リソース  |
|-------|-------|---------|
|  1,000   |  3    | 20,000 RU  |
|  5,000   |  24   | 60,000 RU  |
|  10,000  |  48   | 120,000 RU |

Stream Analytics から出力される [Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) は、[互換性レベル 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12) のネイティブ統合を使用するように更新されました。 互換性レベル 1.2 は、新しいジョブの既定の互換性レベルである 1.1 に比べてスループットが著しく高く、RU 消費量が低下しています。 ソリューションは /deviceId 上にパーティション分割された CosmosDB コンテナーを使用し、ソリューションの残りの部分は同じように構成されています。

[Azure の大規模なストリーミングのサンプル](https://github.com/Azure-Samples/streaming-at-scale)はどれも、負荷をシミュレートするテスト クライアントによってデータが取り込まれるイベント ハブを入力として使用します。 各入力イベントは 1KB の JSON ドキュメントであるため、構成された取り込み率からスループット レートに簡単に換算できます (1MB/秒、5MB/秒、および10MB/秒)。 イベントは、最大 1,000 台のデバイス向けに以下の JSON データ (短縮された形式) を送信する IoT デバイスをシミュレートします。

```
{
    "eventId": "b81d241f-5187-40b0-ab2a-940faf9757c0",
    "complexData": {
        "moreData0": 51.3068118685458,
        "moreData22": 45.34076957651598
    },
    "value": 49.02278128887753,
    "deviceId": "contoso://device-id-1554",
    "type": "CO2",
    "createdAt": "2019-05-16T17:16:40.000003Z"
}
```

> [!NOTE]
> 構成は、ソリューションで使用されるさまざまなコンポーネントによって変更される可能性があります。 見積もりの精度を高めるには、ご使用のシナリオに合わせてサンプルをカスタマイズしてください。

### <a name="identifying-bottlenecks"></a>ボトルネックの特定

Azure Stream Analytics ジョブの [メトリックス] ウィンドウを使用して、パイプラインのボトルネックを特定します。 スループットについての **[Input/Output Events]\(入出力イベント\)** および [[透かしの遅延]](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) または **[Backlogged Events]\(バックログされたイベント\)** を確認して、ジョブが入力速度に対応しているかどうかを確認します。 イベント ハブのメトリックスについては、 **[Throttled Requests] (スロットルされた要求数)** を検索し、必要に応じてしきい値ユニットを調整します。 Cosmos DB メトリックスについては、スループットの下の **[パーティション キーの範囲ごとの使用された最大 RU/秒]** を確認して、パーティション キーの範囲が均一に消費されていることを確認します。 Azure SQL DB については、 **[ログ IO]** および **[CPU]** を監視します。

## <a name="get-help"></a>問い合わせ

さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
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

