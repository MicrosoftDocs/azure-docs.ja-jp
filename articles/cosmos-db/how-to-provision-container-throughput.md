---
title: Azure Cosmos DB のコンテナーのスループットをプロビジョニングする
description: Azure portal、CLI、PowerShell および他のさまざまな SDK を使用して、Azure Cosmos DB のコンテナー レベルでスループットをプロビジョニングする方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/13/2019
ms.author: mjbrown
ms.custom: devx-track-javascript, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: cf2bd616fc739d233826769baaa14298d0fcec2d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017126"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container"></a>Azure Cosmos コンテナーに標準 (手動) のスループットをプロビジョニングする

この記事では、Azure Cosmos DB のコンテナー (コレクション、グラフ、またはテーブル) に標準 (手動) スループットをプロビジョニングする方法について説明します。 スループットは、単一のコンテナーを対象にプロビジョニングできるほか、[データベースを対象にプロビジョニング](how-to-provision-database-throughput.md)して、それをデータベース内の複数のコンテナーで共有することもできます。 コンテナーのスループットは、Azure portal、Azure CLI、Azure Cosmos DB SDK のいずれかを使用してプロビジョニングできます。

## <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存の Azure Cosmos アカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコレクション]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * コンテナー (またはテーブル、グラフ) ID を入力します。
   * パーティション キーの値 (`/userid` など) を入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png" alt-text="[新しいコレクション] が強調表示されている [データ エクスプローラー] のスクリーンショット":::

## <a name="azure-cli-or-powershell"></a>Azure CLI または PowerShell

専用スループットを持つコンテナーの作成については、以下を参照してください。

* [Azure CLI を使用してコンテナーを作成する](manage-with-cli.md#create-a-container)
* [PowerShell を使用してコンテナーを作成する](manage-with-powershell.md#create-container)

> [!Note]
> MongoDB 用 Azure Cosmos DB API を使用して構成された Azure Cosmos DB アカウントのコンテナーに対するスループットをプロビジョニングする場合は、パーティション キーのパスとして `/myShardKey` を使用します。 Cassandra API を使用して構成された Azure Cosmos アカウントのコンテナーに対するスループットをプロビジョニングする場合は、パーティション キーのパスとして `/myPrimaryKey` を使用します。

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Cassandra および MongoDB API を除くすべての Cosmos DB API に対するスループットをプロビジョニングするには、SQL API 用の Cosmos SDK を使用します。

### <a name="sql-gremlin-and-table-apis"></a><a id="dotnet-most"></a>SQL API、Gremlin API、Table API

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

## <a name="javascript-sdk"></a>JavaScript SDK

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

### <a name="mongodb-api"></a><a id="dotnet-mongodb"></a>MongoDB API

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API

同様のコマンドは、CQL に準拠している任意のドライバーを使用して発行できます。

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Cassandra テーブルのスループットの変更

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>次のステップ

Azure Cosmos DB でのスループットのプロビジョニングについては、次の記事を参照してください。

* [データベースに標準 (手動) のスループットをプロビジョニングする方法](how-to-provision-database-throughput.md)
* [データベースに自動スケーリングのスループットをプロビジョニングする方法](how-to-provision-autoscale-throughput.md)
* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)
