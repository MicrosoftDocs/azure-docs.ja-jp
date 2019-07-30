---
title: Azure Cosmos DB の要求ユニット (RU) 使用量を確認する
description: Azure Cosmos コンテナーに対して実行した操作の要求ユニット (RU) 使用量を確認する方法について説明します。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/14/2019
ms.author: thweiss
ms.openlocfilehash: 96c36067456a49a5760d6fde488dcb4ad8311a90
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356465"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Azure Cosmos DB の要求ユニット使用量を確認する

この記事では、Azure Cosmos DB のコンテナーに対して実行された任意の操作の[要求ユニット](request-units.md) (RU) 消費量を確認するさまざまな方法を紹介します。 この消費量は現在、Azure portal を使用するか、いずれかの SDK を通じて Azure Cosmos DB から返された応答を調べることによってのみ測定できます。

## <a name="sql-core-api"></a>SQL (Core) API

SQL API を使用している場合、Azure Cosmos コンテナーに対する操作の RU 消費量をいくつかの方法で確認できます。

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

現在のところ、SQL クエリについてのみ、Azure portal で要求の使用量を確認できます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成](create-sql-api-dotnet.md#create-account)してデータを取り込むか、既にデータが存在する既存の Azure Cosmos アカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウに進み、操作の対象となるコンテナーを選択します。

1. **[新しい SQL クエリ]** を選択します。

1. 有効なクエリを入力し、 **[クエリの実行]** を選択します。

1. **[Query Stats]\(クエリの統計\)** を選択すると、実行した要求の実際の使用量が表示されます。

![Azure portal で SQL クエリの要求の使用量を表示した画面のスクリーンショット](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>.NET SDK を使用する
### <a name="net-v2-sdk"></a>.Net V2 SDK

[.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) から返されたオブジェクトにより `RequestCharge` プロパティが公開されます。

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="net-v3-sdk"></a>.Net V3 SDK

[.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) から返されたオブジェクトにより `RequestCharge` プロパティが公開されます。

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

詳細については、「[クイック スタート: Azure Cosmos DB の SQL API アカウントを使用して .NET Web アプリをビルドする](create-sql-api-dotnet.md)」を参照してください。

### <a name="use-the-java-sdk"></a>Java SDK の使用

[Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) から返されたオブジェクトにより `getRequestCharge()` メソッドが公開されます。

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

詳細については、「[クイック スタート: Azure Cosmos DB SQL API アカウントを使用して Java アプリケーションを構築する](create-sql-api-java.md)」を参照してください。

### <a name="use-the-nodejs-sdk"></a>Node.js SDK の使用

[Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) から返されるオブジェクトは、`headers` サブオブジェクトを公開します。基になる HTTP API から返されるすべてのヘッダーが、このサブオブジェクトによってマップされます。 要求の使用量は、`x-ms-request-charge` キーで得られます。

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

詳細については、「[クイック スタート: Azure Cosmos DB SQL API アカウントを使用して Node.js アプリを構築する](create-sql-api-nodejs.md)」を参照してください。 

### <a name="use-the-python-sdk"></a>Python SDK の使用

[Python SDK](https://pypi.org/project/azure-cosmos/) の `CosmosClient` オブジェクトは、`last_response_headers` ディクショナリを公開します。直前に実行された操作に関して、基になる HTTP API から返されるすべてのヘッダーが、このディクショナリによってマップされます。 要求の使用量は、`x-ms-request-charge` キーで得られます。

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

詳細については、「[クイック スタート: Azure Cosmos DB SQL API アカウントを使用して Python アプリケーションを構築する](create-sql-api-python.md)」を参照してください。 

## <a name="azure-cosmos-db-api-for-mongodb"></a>MongoDB 用 Azure Cosmos DB API

RU 使用量は、`getLastRequestStatistics` という名前のカスタム [データベース コマンド](https://docs.mongodb.com/manual/reference/command/)によって公開されます。 最後に実行された操作の名前を含んだドキュメント、その要求の使用量、その実行時間が、このコマンドから返されます。 Azure Cosmos DB API for MongoDB を使用している場合、RU 使用量をいくつかの方法で取得できます。

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

現在のところ、クエリについてのみ、Azure portal で要求の使用量を確認できます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成](create-mongodb-dotnet.md#create-a-database-account)してデータを取り込むか、既にデータが存在する既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウに進み、操作の対象となるコレクションを選択します。

1. **[新しいクエリ]** を選択します。

1. 有効なクエリを入力し、 **[クエリの実行]** を選択します。

1. **[Query Stats]\(クエリの統計\)** を選択すると、実行した要求の実際の使用量が表示されます。

![Azure portal で MongoDB クエリの要求の使用量を表示した画面のスクリーンショット](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>MongoDB .NET ドライバーの使用

[公式の MongoDB .NET ドライバー](https://docs.mongodb.com/ecosystem/drivers/csharp/)を使用するとき、`RunCommand` メソッドを `IMongoDatabase` オブジェクトで呼び出すことでコマンドを実行できます。 このメソッドには、`Command<>` 抽象クラスの実装が必要となります。

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

詳細については、「[クイック スタート: Azure Cosmos DB の MongoDB 用 API を使用して .NET Web アプリを構築する](create-mongodb-dotnet.md)」を参照してください。

### <a name="use-the-mongodb-java-driver"></a>MongoDB Java ドライバーの使用


[公式の MongoDB Java ドライバー](https://mongodb.github.io/mongo-java-driver/)を使用するとき、`runCommand` メソッドを `MongoDatabase` オブジェクトで呼び出すことでコマンドを実行できます。

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

詳細については、「[クイック スタート: Azure Cosmos DB の MongoDB 用 API と Java SDK を使用して Web アプリを構築する](create-mongodb-java.md)」を参照してください。

### <a name="use-the-mongodb-nodejs-driver"></a>MongoDB Node.js ドライバーの使用

[公式の MongoDB Node.js ドライバー](https://mongodb.github.io/node-mongodb-native/)を使用するとき、`command` メソッドを `db` オブジェクトで呼び出すことでコマンドを実行できます。

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

詳細については、「[クイック スタート: 既存の MongoDB Node.js Web アプリを Azure Cosmos DB に移行する](create-mongodb-nodejs.md)」を参照してください。

## <a name="cassandra-api"></a>Cassandra API

Azure Cosmos DB の Cassandra API に対して操作を実行すると、受信ペイロードの `RequestCharge` という名前のフィールドとして RU 使用量が返されます。 RU 使用量はいくつかの方法で取得できます。

### <a name="use-the-net-sdk"></a>.NET SDK を使用する

[.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/) を使用するとき、`RowSet` オブジェクトの `Info` プロパティの下で、入ってくるペイロードを取得できます。

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

詳細については、「[クイック スタート: .NET SDK と Azure Cosmos DB を使用して Cassandra アプリを構築する](create-cassandra-dotnet.md)」を参照してください。

### <a name="use-the-java-sdk"></a>Java SDK の使用

[Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) を使用するとき、`ResultSet` オブジェクトで `getExecutionInfo()` メソッドを呼び出すことで、入ってくるペイロードを取得できます。

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

詳細については、「[クイック スタート: Java SDK と Azure Cosmos DB を使用して Cassandra アプリをビルドする](create-cassandra-java.md)」を参照してください。

## <a name="gremlin-api"></a>Gremlin API

Gremlin API を使用するとき、Azure Cosmos コンテナーに対する操作の RU 消費量をいくつかの方法で確認できます。 

### <a name="use-drivers-and-sdk"></a>ドライバーと SDK の使用

Gremlin API から返されるヘッダーは、Gremlin .NET と Java SDK で現在公開されているカスタムの状態属性にマップされます。 要求の使用量は、`x-ms-request-charge` キーで得られます。

### <a name="use-the-net-sdk"></a>.NET SDK を使用する

[Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/) を使用するとき、`ResultSet<>` オブジェクトの `StatusAttributes` プロパティで状態属性が得られます。

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

詳細については、「[クイック スタート: Azure Cosmos DB Gremlin API アカウントを使用して .NET Framework アプリケーションまたは Core アプリケーションを構築する](create-graph-dotnet.md)」を参照してください。

### <a name="use-the-java-sdk"></a>Java SDK の使用

[Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) を使用するとき、`ResultSet` オブジェクトで `statusAttributes()` メソッドを呼び出すことで状態属性を取得できます。

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

詳細については、「[クイック スタート: Azure Cosmos DB で Java SDK を使用してグラフ データベースを作成する](create-graph-java.md)」を参照してください。

## <a name="table-api"></a>テーブル API

現在のところ、テーブル操作に対する RU 使用量を返す唯一の SDK が [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) です。 `TableResult` オブジェクトは、`RequestCharge` プロパティを公開します。このオブジェクトを Azure Cosmos DB の Table API に対して使用すると、この SDK によってプロパティの値が設定されます。

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

詳細については、「[クイック スタート: .NET SDK と Azure Cosmos DB を使用して Table API アプリをビルドする](create-table-dotnet.md)」を参照してください。

## <a name="next-steps"></a>次の手順

RU 使用量を最適化する方法については、次の記事をご覧ください。

* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)
* [Azure Cosmos DB でのプロビジョニングされたスループット コストを最適化する](optimize-cost-throughput.md)
* [Azure Cosmos DB でのクエリ コストを最適化する](optimize-cost-queries.md)
* [プロビジョニングされたスループットのグローバルなスケーリング](scaling-throughput.md)
* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)
* [特定のコンテナーに対してスループットをプロビジョニングする](how-to-provision-container-throughput.md)
* [Azure Cosmos DB のメトリックを使用した監視とデバッグ](use-metrics.md)
