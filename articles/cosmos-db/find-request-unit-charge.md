---
title: Azure Cosmos DB の要求ユニット (RU) 使用量を確認する
description: Azure Cosmos コンテナーに対して実行した操作の要求ユニット使用量を確認する方法について説明します。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/21/2019
ms.author: thweiss
ms.openlocfilehash: e3175ee136057c695ceef3cd1976b447a529c803
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2019
ms.locfileid: "59053042"
---
# <a name="find-the-request-unit-ru-charge-in-azure-cosmos-db"></a>Azure Cosmos DB の要求ユニット (RU) 使用量を確認する

この記事では、Azure Cosmos コンテナーに対して実行された操作の[要求ユニット](request-units.md)消費量を確認するさまざまな方法を紹介します。 この消費量は現在、Azure portal を使用して測定できるほか、いずれかの SDK を通じて Azure Cosmos DB から返された応答を調べることによって測定できます。

## <a name="core-api"></a>Core API

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

Azure portal では現在、SQL クエリについてのみ要求の使用量を確認できます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos DB アカウントを作成](create-sql-api-dotnet.md#create-account)してデータを取り込むか、既にデータが存在する既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて、操作の対象となるコンテナーを選択します。

1. **[新しい SQL クエリ]** をクリックします。

1. 有効なクエリを入力し、**[クエリの実行]** をクリックします。

1. **[Query Stats]\(クエリの統計\)** をクリックすると、直前に実行した要求の実際の使用量が表示されます。

![Azure portal で SQL クエリの要求の使用量を表示した画面のスクリーンショット](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>.NET SDK V2 の使用

[.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (使用法については、[こちらのクイック スタート](create-sql-api-dotnet.md)を参照) から返されるオブジェクトは、`RequestCharge` プロパティを公開しています。

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

### <a name="use-the-java-sdk"></a>Java SDK の使用

[Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (使用法については、[こちらのクイック スタート](create-sql-api-java.md)を参照) から返されるオブジェクトは、`getRequestCharge()` メソッドを公開しています。

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

### <a name="use-the-nodejs-sdk"></a>Node.js SDK の使用

[Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (使用法については、[こちらのクイック スタート](create-sql-api-nodejs.md)を参照) から返されるオブジェクトは、`headers` サブオブジェクトを公開しています。基になる HTTP API から返されるすべてのヘッダーが、このサブオブジェクトによってマップされます。 要求の使用量は、`x-ms-request-charge` キーで得られます。

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

### <a name="use-the-python-sdk"></a>Python SDK の使用

[Python SDK](https://pypi.org/project/azure-cosmos/) (使用法については、[こちらのクイック スタート](create-sql-api-python.md)を参照) の `CosmosClient` オブジェクトは、`last_response_headers` ディクショナリを公開しています。直前に実行された操作に関して、基になる HTTP API から返されるすべてのヘッダーが、このディクショナリによってマップされます。 要求の使用量は、`x-ms-request-charge` キーで得られます。

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API

要求ユニット使用量は、`getLastRequestStatistics` という名前のカスタム [データベース コマンド](https://docs.mongodb.com/manual/reference/command/)によって公開されます。 直前に実行された操作の名前を含んだドキュメント、その要求の使用量、その実行時間が、このコマンドから返されます。

### <a name="use-the-azure-portal"></a>Azure ポータルの使用

Azure portal では現在、クエリについてのみ要求の使用量を確認できます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos DB アカウント](create-mongodb-dotnet.md#create-a-database-account)を作成してデータを取り込むか、既にデータが存在する既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて、操作の対象となるコレクションを選択します。

1. **[新しいクエリ]** をクリックします。

1. 有効なクエリを入力し、**[クエリの実行]** をクリックします。

1. **[Query Stats]\(クエリの統計\)** をクリックすると、直前に実行した要求の実際の使用量が表示されます。

![Azure portal で MongoDB クエリの要求の使用量を表示した画面のスクリーンショット](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>MongoDB .NET ドライバーの使用

[公式 MongoDB .NET ドライバー](https://docs.mongodb.com/ecosystem/drivers/csharp/) (使用法については、[こちらのクイック スタート](create-mongodb-dotnet.md)を参照) を使用する場合は、`IMongoDatabase` オブジェクトで `RunCommand` メソッドを呼び出すことでコマンドを実行できます。 このメソッドには、`Command<>` 抽象クラスの実装が必要となります。

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

### <a name="use-the-mongodb-java-driver"></a>MongoDB Java ドライバーの使用

[公式 MongoDB Java ドライバー](http://mongodb.github.io/mongo-java-driver/) (使用法については、[こちらのクイック スタート](create-mongodb-java.md)を参照) を使用する場合は、`MongoDatabase` オブジェクトで `runCommand` メソッドを呼び出すことでコマンドを実行できます。

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

### <a name="use-the-mongodb-nodejs-driver"></a>MongoDB Node.js ドライバーの使用

[公式 MongoDB Node.js ドライバー](https://mongodb.github.io/node-mongodb-native/) (使用法については、[こちらのクイック スタート](create-mongodb-nodejs.md)を参照) を使用する場合は、`Db` オブジェクトで `command` メソッドを呼び出すことでコマンドを実行できます。

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

## <a name="cassandra-api"></a>Cassandra API

Azure Cosmos DB の Cassandra API に対して操作を実行すると、受信ペイロードの `RequestCharge` という名前のフィールドとして要求ユニット使用量が返されます。

### <a name="use-the-net-sdk"></a>.NET SDK を使用する

[.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/) (使用法については、[こちらのクイック スタート](create-cassandra-dotnet.md)を参照) を使用する場合は、`RowSet` オブジェクトの `Info` プロパティで受信ペイロードを取得できます。

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"], 0);
```

### <a name="use-the-java-sdk"></a>Java SDK の使用

[Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) (使用法については、[こちらのクイック スタート](create-cassandra-java.md)を参照) を使用する場合は、`ResultSet` オブジェクトで `getExecutionInfo()` メソッドを呼び出すことで受信ペイロードを取得できます。

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

## <a name="gremlin-api"></a>Gremlin API

### <a name="use-drivers-and-sdk"></a>ドライバーと SDK の使用

Gremlin API から返されるヘッダーは、Gremlin .NET と Java SDK で現在公開されているカスタムの状態属性にマップされます。 要求の使用量は、`x-ms-request-charge` キーで得られます。

### <a name="use-the-net-sdk"></a>.NET SDK を使用する

[Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/) (使用法については、[こちらのクイック スタート](create-graph-dotnet.md)を参照) を使用する場合は、`ResultSet<>` オブジェクトの `StatusAttributes` プロパティで状態属性が得られます。

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

### <a name="use-the-java-sdk"></a>Java SDK の使用

[Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) (使用法については、[こちらのクイック スタート](create-graph-java.md)を参照) を使用する場合は、`ResultSet` オブジェクトで `statusAttributes()` メソッドを呼び出すことで状態属性を取得できます。

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

## <a name="table-api"></a>テーブル API

現在、テーブル操作の要求ユニット使用量を返す SDK は、[.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) (使用法については、[こちらのクイック スタート](create-table-dotnet.md)を参照) だけです。 `TableResult` オブジェクトは、`RequestCharge` プロパティを公開しています。このオブジェクトを Azure Cosmos DB の Table API に対して使用すると、この SDK によってプロパティの値が設定されます。

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

## <a name="next-steps"></a>次の手順

要求ユニットの消費を最適化する方法については、次の記事を参照してください。

* [Azure Cosmos DB でプロビジョニング済みのスループット コストを最適化する](optimize-cost-throughput.md)
* [Azure Cosmos DB でのクエリ コストを最適化する](optimize-cost-queries.md)