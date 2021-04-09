---
title: Azure Cosmos DB の SQL クエリの要求ユニット (RU) 使用量を確認する
description: Azure Cosmos コンテナーに対して実行した SQL クエリの要求ユニット (RU) 使用量を確認する方法について説明します。 Azure portal、.NET、Java、Python、Node.js の各言語を使用して、RU 使用量を調べることができます。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: f716245d93727a0447bd1c67924ce7577c70b503
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201293"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API で実行された操作の要求ユニット使用量の確認
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB では、多くの API (SQL、MongoDB、Cassandra、Gremlin、Table など) がサポートされています。 各 API には、固有のデータベース操作のセットがあります。 これらの操作の範囲は、単純なポイント読み取り/書き込みから、複雑なクエリにまで及びます。 各データベース操作は、それらの操作の複雑さに基づいて、システム リソースを消費します。

すべてのデータベース操作のコストは Azure Cosmos DB によって正規化され、要求ユニット (RU) によって表されます。 要求の料金は、すべてのデータベース操作で使用される要求ユニットです。 RU は、Azure Cosmos DB によってサポートされるデータベース操作を実行するために必要な CPU、IOPS、メモリなどのシステム リソースを抽象化する、パフォーマンスの通貨と考えることができます。 Azure Cosmos コンテナーの操作にどの API 使用するかに関係なく、コストは RU によって測定されます。 データベース操作が書き込み、ポイント読み取り、またはクエリのいずれの場合でも、コストは常に RU で測定されます。 詳細については、[要求ユニットとその考慮事項](request-units.md)に関する記事を参照してください。

この記事では、Azure Cosmos DB SQL API のコンテナーに対して実行された任意の操作の[要求ユニット](request-units.md) (RU) 消費量を確認するさまざまな方法を紹介します。 別の API を使用している場合は、RU の使用量を確認する方法について、[MongoDB の API](find-request-unit-charge-mongodb.md)、[Cassandra API](find-request-unit-charge-cassandra.md)、[Gremlin API](find-request-unit-charge-gremlin.md)、[Table API](find-request-unit-charge-table.md) の記事を参照してください。

この消費量は現在、Azure portal を使用するか、いずれかの SDK を通じて Azure Cosmos DB から返された応答を調べることによってのみ測定できます。 SQL API を使用している場合、Azure Cosmos コンテナーに対する操作の RU 消費量をいくつかの方法で確認できます。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成](create-sql-api-dotnet.md#create-account)してデータを取り込むか、既にデータが存在する既存の Azure Cosmos アカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウに進み、操作の対象となるコンテナーを選択します。

1. **[新しい SQL クエリ]** を選択します。

1. 有効なクエリを入力し、 **[クエリの実行]** を選択します。

1. **[Query Stats]\(クエリの統計\)** を選択すると、実行した要求の実際の使用量が表示されます。

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Azure portal で SQL クエリの要求の使用量を表示した画面のスクリーンショット":::

## <a name="use-the-net-sdk"></a>.NET SDK を使用する

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) から返されたオブジェクトにより `RequestCharge` プロパティが公開されます。

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

詳細については、「[クイック スタート: Azure Cosmos DB の SQL API アカウントを使用して .NET Web アプリをビルドする](create-sql-api-dotnet.md)」を参照してください。

---

## <a name="use-the-java-sdk"></a>Java SDK の使用

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

## <a name="use-the-nodejs-sdk"></a>Node.js SDK の使用

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

## <a name="use-the-python-sdk"></a>Python SDK の使用

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

## <a name="next-steps"></a>次のステップ

RU 使用量を最適化する方法については、次の記事をご覧ください。

* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)
* [Azure Cosmos DB でのプロビジョニングされたスループット コストを最適化する](optimize-cost-throughput.md)
* [Azure Cosmos DB でのクエリ コストを最適化する](./optimize-cost-reads-writes.md)
* [プロビジョニングされたスループットのグローバルなスケーリング](./request-units.md)
* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)
* [特定のコンテナーに対してスループットをプロビジョニングする](how-to-provision-container-throughput.md)
* [Azure Cosmos DB のメトリックを使用した監視とデバッグ](use-metrics.md)
