---
title: SQL クエリのパフォーマンスと実行メトリックを取得する
description: Azure Cosmos DB 要求の SQL クエリの実行メトリックを取得し、SQL クエリのパフォーマンスをプロファイリングする方法について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: girobins
ms.openlocfilehash: 48b9a67de5c870a187ee008bd97265760ca6c341
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998361"
---
# <a name="get-sql-query-execution-metrics-and-analyze-query-performance-using-net-sdk"></a>SQL クエリの実行メトリックを取得し、.NET SDK を使用してクエリのパフォーマンスを分析する

この記事では、Azure Cosmos DB での SQL クエリのパフォーマンスをプロファイリングする方法を示します。 .NET SDK から取得された `QueryMetrics` を使用してこのプロファイリングを実行できます。それについて、ここで詳しく説明します。 [QueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.querymetrics.aspx) は、バックエンド クエリの実行に関する情報を持っている厳密に型指定されたオブジェクトです。 これらのメトリックの詳細については、[クエリ パフォーマンスのチューニング](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)に関する記事を参照してください。

## <a name="set-the-feedoptions-parameter"></a>FeedOptions パラメーターを設定する

[DocumentClient.CreateDocumentQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentquery.aspx) のすべてのオーバーロードで、省略可能な [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) パラメーターが使用されます。 このオプションは、クエリの実行で何をチューニングでき、何をパラメーター化できるかを指示します。 

SQL クエリの実行メトリックを収集するには、[FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.aspx) の [PopulateQueryMetrics](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.populatequerymetrics.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.PopulateQueryMetrics) パラメーターを `true` に設定する必要があります。 `PopulateQueryMetrics` を true に設定すると、関連する `QueryMetrics` が `FeedResponse` に格納されます。 

## <a name="get-query-metrics-with-asdocumentquery"></a>AsDocumentQuery() を使用してクエリのメトリックを取得する
次のコード例では、[AsDocumentQuery()](https://msdn.microsoft.com/library/microsoft.azure.documents.linq.documentqueryable.asdocumentquery.aspx) メソッドを使用したメトリックの取得方法を示します。

```csharp
// Initialize this DocumentClient and Collection
DocumentClient documentClient = null;
DocumentCollection collection = null;

// Setting PopulateQueryMetrics to true in the FeedOptions
FeedOptions feedOptions = new FeedOptions
{
    PopulateQueryMetrics = true
};

string query = "SELECT TOP 5 * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    
    // At this point you have QueryMetrics which you can serialize using .ToString()
    foreach (KeyValuePair<string, QueryMetrics> kvp in partitionIdToQueryMetrics)
    {
        string partitionId = kvp.Key;
        QueryMetrics queryMetrics = kvp.Value;
        
        // Do whatever logging you need
        DoSomeLoggingOfQueryMetrics(query, partitionId, queryMetrics);
    }
}
```
## <a name="aggregating-querymetrics"></a>QueryMetrics の集計

前のセクションでは、[ExecuteNextAsync](https://msdn.microsoft.com/library/azure/dn850294.aspx) メソッドへの呼び出しが複数回あったことに注目してください。 各呼び出しで `QueryMetrics` のディクショナリを持つ `FeedResponse` オブジェクトが返されました (クエリの継続ごとに 1 つ)。 次の例では、これらの `QueryMetrics` を LINQ を使用して集計する方法を示します。

```csharp
List<QueryMetrics> queryMetricsList = new List<QueryMetrics>();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    queryMetricsList.AddRange(partitionIdToQueryMetrics.Values);
}

// Aggregate the QueryMetrics using the + operator overload of the QueryMetrics class.
QueryMetrics aggregatedQueryMetrics = queryMetricsList.Aggregate((curr, acc) => curr + acc);
Console.WriteLine(aggregatedQueryMetrics);
```

## <a name="grouping-query-metrics-by-partition-id"></a>パーティション ID によるクエリ メトリックのグループ化

パーティション ID によって `QueryMetrics` をグループ化できます。 パーティション ID によってグループ化することで、他と比較したときに、特定のパーティションがパフォーマンス上の問題の原因となっているかどうかを確認できます。 次の例では、LINQ を使用して `QueryMetrics` をグループ化する方法を示します。

```csharp
List<KeyValuePair<string, QueryMetrics>> partitionedQueryMetrics = new List<KeyValuePair<string, QueryMetrics>>();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    
    // This dictionary is maps the partitionId to the QueryMetrics of that query
    IReadOnlyDictionary<string, QueryMetrics> partitionIdToQueryMetrics = feedResponse.QueryMetrics;
    partitionedQueryMetrics.AddRange(partitionIdToQueryMetrics.ToList());
}

// Now we are able to group the query metrics by partitionId
IEnumerable<IGrouping<string, KeyValuePair<string, QueryMetrics>>> groupedByQueryMetrics = partitionedQueryMetrics
    .GroupBy(kvp => kvp.Key);

// If we wanted to we could even aggregate the groupedby QueryMetrics
foreach(IGrouping<string, KeyValuePair<string, QueryMetrics>> grouping in groupedByQueryMetrics)
{
    string partitionId = grouping.Key;
    QueryMetrics aggregatedQueryMetricsForPartition = grouping
        .Select(kvp => kvp.Value)
        .Aggregate((curr, acc) => curr + acc);
    DoSomeLoggingOfQueryMetrics(query, partitionId, aggregatedQueryMetricsForPartition);
}
```

## <a name="linq-on-documentquery"></a>DocumentQuery の LINQ

`AsDocumentQuery()` メソッドを使用して、LINQ クエリから `FeedResponse` を取得することもできます。

```csharp
IDocumentQuery<Document> linqQuery = client.CreateDocumentQuery(collection.SelfLink, feedOptions)
    .Take(1)
    .Where(document => document.Id == "42")
    .OrderBy(document => document.Timestamp)
    .AsDocumentQuery();
FeedResponse<Document> feedResponse = await linqQuery.ExecuteNextAsync<Document>();
IReadOnlyDictionary<string, QueryMetrics> queryMetrics = feedResponse.QueryMetrics;
```

## <a name="expensive-queries"></a>コストの高いクエリ

各クエリで使用された要求単位をキャプチャして、コストの高いクエリや高スループットを消費するクエリを調査することができます。 `FeedResponse` で [RequestCharge](https://msdn.microsoft.com/library/azure/dn948712.aspx) プロパティを使用することで、要求の使用量を取得できます。 Azure portal と各種の SDK を使用して要求の使用量を取得する方法の詳細については、[要求ユニット使用量の検出](find-request-unit-charge.md)に関する記事を参照してください。

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();

while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    double requestCharge = feedResponse.RequestCharge
    
    // Log the RequestCharge how ever you want.
    DoSomeLogging(requestCharge);
}
```

## <a name="get-the-query-execution-time"></a>クエリの実行時間を取得する

クライアント側でクエリを実行するために必要だった時間を計算するときは、`ExecuteNextAsync` メソッドの呼び出し時間のみが含まれ、コード ベースのその他の部分の呼び出し時間は含まれていないことを確認します。 例に示すように、これらの呼び出しだけが、クエリを実行するためにかかった時間を計算するために役立ちます。

```csharp
string query = "SELECT * FROM c";
IDocumentQuery<dynamic> documentQuery = documentClient.CreateDocumentQuery(Collection.SelfLink, query, feedOptions).AsDocumentQuery();
Stopwatch queryExecutionTimeEndToEndTotal = new Stopwatch();
while (documentQuery.HasMoreResults)
{
    // Execute one continuation of the query
    queryExecutionTimeEndToEndTotal.Start();
    FeedResponse<dynamic> feedResponse = await documentQuery.ExecuteNextAsync();
    queryExecutionTimeEndToEndTotal.Stop();
}

// Log the elapsed time
DoSomeLogging(queryExecutionTimeEndToEndTotal.Elapsed);
```

## <a name="scan-queries-commonly-slow-and-expensive"></a>スキャン クエリ (通常は低速かつコストがかかります)

スキャン クエリとは、インデックスによるサービスの提供がないために、結果セットを返す前に多数のドキュメントを読み込むクエリを指します。

スキャン クエリの例を次に示します。

```sql
SELECT VALUE c.description 
FROM   c 
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

このクエリのフィルターでは、インデックスからサービスを提供されないシステム関数 UPPER が使用されています。 このクエリを大規模なコレクションに対して実行すると、最初の継続で次のクエリ メトリックが生成されます。

```
QueryMetrics

Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

このクエリのメトリック 出力に含まれる次の値に注意してください。

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
```

このクエリでは、合計で 399,998,938 バイトになる 60,951 のドキュメントが読み込まれました。 この大量のバイトの読み込みは、高いコストや要求ユニット使用量につながります。 さらに、クエリの実行に長い時間がかかりますが、これは、次の費やされた時間の合計を示すプロパティによって明確に示されています。

```
Total Query Execution Time               :        4,500.34 milliseconds
```

これは、クエリの実行に 4.5 秒かかったことを意味しています (しかもこれは 1 つの継続だけのものです)。

この例のクエリを最適化するには、フィルターで UPPER を使用しないようにします。 代わりに、ドキュメントが作成または更新されたときに、すべての大文字に `c.description` 値を挿入する必要があります。 クエリは次のようになります。 

```sql
SELECT VALUE c.description 
FROM   c 
WHERE c.description = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
```

これで、このクエリでは、インデックスからサービスが提供されるようになりました。

クエリ パフォーマンスのチューニングの詳細については、[クエリ パフォーマンスのチューニング](https://docs.microsoft.com/azure/cosmos-db/documentdb-sql-query-metrics)に関する記事を参照してください。

## <a id="References"></a>参考資料

- [Azure Cosmos DB SQL の仕様](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 

## <a name="next-steps"></a>次の手順

- [クエリ パフォーマンスのチューニング](sql-api-query-metrics.md)
- [インデックス作成の概要](index-overview.md)
- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmos-dotnet-v3)
