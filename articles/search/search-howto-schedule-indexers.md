---
title: インデクサー実行をスケジュールする
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search インデクサーのスケジュールを設定して、定期的に、または特定の時間にコンテンツのインデックスを作成します。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 8ae9a89ddba2010603ae5a5f6b812e3aa1e1e3a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100097978"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Azure Cognitive Search のインデクサーをスケジュールする方法

インデクサーは通常、その作成直後に 1 回実行されます。 その後、Azure portal、[インデクサー実行 (REST)](/rest/api/searchservice/run-indexer)、または Azure SDK のいずれかを使用して、必要に応じてもう一度実行できます。 あるいは、スケジュールに従って実行するようにインデクサーを構成することもできます。 インデクサーのスケジュール設定が役立つ状況には、次のものがあります。

* ソース データが時間の経過と共に変化するため、検索インデクサーによって差分を自動的に処理したい。

* ソース データが非常に大きく、時間の経過と共にインデクサーの処理を分散させたい。 インデクサー ジョブには、通常のデータ ソースの場合は 24 時間、スキルセットのあるインデクサーの場合は 2 時間の最大実行時間が課せられます。 この最大間隔内にインデックス作成を完了できない場合は、2 時間ごとに実行されるスケジュールを構成できます。 インデクサーは中断した箇所を自動的に取得できます。これは、インデックスが最後に終了した位置を示す内部の高基準値から分かります。 2 時間の繰り返しスケジュールでインデクサーを実行すると、1 つのジョブで許可されている間隔を超えて、大規模なデータ セット (膨大な数のドキュメント) を処理できます。 大量のデータのインデックスを作成する方法の詳細については、「[Azure Cognitive Search で大容量のデータ セットのインデックスを作成する方法](search-howto-large-index.md)」を参照してください。

* 複数のデータ ソースから検索インデックスが生成されるので、競合を減らすために、異なるタイミングでインデクサーが実行されるようにしたい。

視覚的には、スケジュールは次のようになることがあります。1 月 1 日に開始されて 50 分ごとに実行される。

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> スケジューラは Azure Cognitive Search の組み込みの機能です。 外部スケジューラはサポートされていません。

## <a name="schedule-property"></a>スケジュール プロパティ

スケジュールは、インデクサーの定義の一部です。 **schedule** プロパティを省略した場合、インデクサーは作成直後に 1 回だけ実行されます。 **schedule** プロパティを追加する場合、2 つの部分を指定します。

| プロパティ | 説明 |
|----------|-------------|
|**間隔** | (必須) 連続する 2 つのインデクサー実行の開始の時間間隔。 設定できる最小の間隔は 5 分、最長は 1440 分 (24 時間) です。 XSD "dayTimeDuration" 値 ([ISO 8601 期間](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)値の制限されたサブセット) として書式設定する必要があります。 使用されるパターンは、`P(nD)(T(nH)(nM))` です。 <br/><br/>たとえば、15 分ごとの場合は `PT15M`、2 時間ごとの場合は `PT2H` です。|
| **開始時刻 (UTC)** | (省略可能) スケジュールされた実行が開始する時刻を示します。 省略すると、現在の UTC 時刻が使用されます。 この時刻は過去でもかまいません。その場合、最初の実行はインデクサーが元の **startTime** から継続的に実行されているかのようにスケジュールされます。<br/><br/>例: `2021-01-01T00:00:00Z` の場合は 1 月 1 日の午前 0 時に開始し、`2021-01-05T22:28:00Z` の場合は 1 月 5 日の午後 10 時 28 分に開始します。|

## <a name="scheduling-behavior"></a>スケジュールの動作

特定のインデクサーを実行できるのは一度に 1 つだけです。 インデクサーの次の開始予定時間にそのインデクサーが既に実行されている場合、その実行は、次回予定されている時間まで延期されます。

さらに具体的な例を示します。 **[Interval] \(間隔\)** を 1 時間、 **[Start Time] \(開始時刻\)** を 2019 年 6 月 1 日午前 8 時 0 分 0 秒 (UTC) にしてインデクサーのスケジュールを構成するとします。 インデクサーの実行に 1 時間より長くかかると、次のことが起きる可能性があります。

* 最初のインデクサーの実行は、2019 年 6 月 1 日午前 8 時 (UTC) 前後に開始します。 この実行に 20 分 (または、1 時間未満) かかるものとします。
* 2 番目の実行は、2019 年 6 月 1 日午前 9 時 (UTC) 前後に開始します。 この実行に 70 分 (1 時間より長く) かかり、午前 10 時 10 分 (UTC) まで完了しないとします。
* 3 回目の実行は午前 10 時 (UTC) に開始するようにスケジュール設定されていますが、その時点で前の実行がまだ終わっていません。 このとき、このスケジュール設定された実行はスキップされます。 インデクサーの次の実行は午前 11 時 (UTC) まで開始しません。

> [!NOTE]
> インデクサーが特定のスケジュールに設定されているが、同じドキュメントに対して毎回繰り返し失敗する場合、進捗が再び正常化するまでの間、インデクサーの実行頻度が下がります (最大で 24 時間に 1 回以上の間隔)。 基になる問題を修正したと思われる場合は、手動でインデクサーを実行できます。インデックス作成が成功すると、インデクサーは通常のスケジュールに戻ります。

## <a name="schedule-using-rest"></a>REST を使用してスケジュールを設定する

インデクサーを作成または更新するときに、**schedule** プロパティを追加します。

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>.NET を使用してスケジュールを設定する

次の C# の例では、定義済みのデータ ソースとインデックスを使用して Azure SQL データベースのインデクサーを作成し、今後毎日 1 回実行するようにスケジュールを設定します。

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

このスケジュールは、[SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)を使用してインデクサーを作成または更新するときに、[IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) クラスを使用して定義されます。 **IndexingSchedule** コンストラクターでは、**TimeSpan** オブジェクトを使用して **Interval** パラメーターを指定する必要があります。 前に説明したように、設定できる最短の間隔値は 5 分、最長は 24 時間です。 **DateTimeOffset** オブジェクトとして指定された 2 番目の **StartTime** パラメーターは省略可能です。

## <a name="next-steps"></a>次のステップ

スケジュールに基づいて実行されるインデクサーの場合は、検索サービスから状態を取得することによって操作を監視するか、診断ログを有効にすることによって詳細情報を取得できます。

* [検索インデクサーの状態を監視する](search-howto-monitor-indexers.md)
* [ログ データを収集して分析する](search-monitor-logs.md)