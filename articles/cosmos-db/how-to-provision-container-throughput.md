---
title: Azure Cosmos DB SQL API のコンテナーのスループットをプロビジョニングする
description: Azure portal、CLI、PowerShell および他のさまざまな SDK を使用して、Azure Cosmos DB SQL API のコンテナー レベルでスループットをプロビジョニングする方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4caf43cb972b44dd1482b9e6e467e41cae294708
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93100101"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>Azure Cosmos コンテナーに標準 (手動) のスループットをプロビジョニングする - SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、Azure Cosmos DB SQL API のコンテナーに標準 (手動) のスループットをプロビジョニングする方法について説明します。 スループットは、単一のコンテナーを対象にプロビジョニングできるほか、[データベースを対象にプロビジョニング](how-to-provision-database-throughput.md)して、それをデータベース内の複数のコンテナーで共有することもできます。 コンテナーのスループットは、Azure portal、Azure CLI、Azure Cosmos DB SDK のいずれかを使用してプロビジョニングできます。

別の API を使用している場合は、[API for MongoDB](how-to-provision-throughput-mongodb.md)、[Cassandra API](how-to-provision-throughput-cassandra.md)、[Gremlin API](how-to-provision-throughput-gremlin.md) のスループット プロビジョニングに関する記事を参照してください。

## <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存の Azure Cosmos アカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコンテナー]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * コンテナー ID を入力します。
   * パーティション キーの値 (`/ItemID` など) を入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="[新しいコレクション] が強調表示されている [データ エクスプローラー] のスクリーンショット":::

## <a name="azure-cli-or-powershell"></a>Azure CLI または PowerShell

専用スループットを持つコンテナーの作成については、以下を参照してください。

* [Azure CLI を使用してコンテナーを作成する](manage-with-cli.md#create-a-container)
* [PowerShell を使用してコンテナーを作成する](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Cassandra および MongoDB API を除くすべての Cosmos DB API に対するスループットをプロビジョニングするには、SQL API 用の Cosmos SDK を使用します。

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

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB でのスループットのプロビジョニングについては、次の記事を参照してください。

* [データベースに標準 (手動) のスループットをプロビジョニングする方法](how-to-provision-database-throughput.md)
* [データベースに自動スケーリングのスループットをプロビジョニングする方法](how-to-provision-autoscale-throughput.md)
* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)
