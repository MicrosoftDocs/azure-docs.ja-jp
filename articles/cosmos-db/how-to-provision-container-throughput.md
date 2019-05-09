---
title: Azure Cosmos DB のコンテナーのスループットをプロビジョニングする
description: Azure Cosmos DB のコンテナー レベルでスループットをプロビジョニングする方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 28060637db47b42db66f706815066d498032ec11
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258712"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Azure Cosmos コンテナー上でのスループットをプロビジョニングする

この記事では、Azure Cosmos DB のコンテナー (コレクション、グラフ、またはテーブル) のスループットをプロビジョニングする方法について説明します。 スループットは、単一のコンテナーを対象にプロビジョニングできるほか、[データベースを対象にプロビジョニング](how-to-provision-database-throughput.md)して、それをデータベース内の複数のコンテナーで共有することもできます。 コンテナーのスループットは、Azure portal、Azure CLI、Azure Cosmos DB SDK のいずれかを使用してプロビジョニングできます。

## <a name="provision-throughput-by-using-azure-portal"></a>Azure Portal を使用してスループットをプロビジョニングする

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos DB アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコレクション]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * コレクション ID (またはテーブル、グラフ) を入力します。
   * パーティション キーの値 (`/userid` など) を入力します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

![[新しいコレクション] が強調表示されている [データ エクスプローラー] のスクリーンショット](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-azure-cli"></a>Azure CLI を使用してスループットをプロビジョニングする

```azurecli-interactive
# Create a container with a partition key and provision throughput of 1000 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

MongoDB 用 Azure Cosmos DB API を使用して構成された Azure Cosmos DB アカウントのスループットをプロビジョニングする場合は、パーティション キーのパスとして `/myShardKey` を使用します。 Cassandra API 用に構成された Azure Cosmos DB アカウントのスループットをプロビジョニングする場合は、パーティション キーのパスとして `/myPrimaryKey` を使用します。

## <a name="provision-throughput-by-using-net-sdk"></a>.NET SDK を使用してスループットをプロビジョニングする

> [!Note]
> Cassandra API を除くすべての API は、SQL API を使用してスループットをプロビジョニングします。

### <a id="dotnet-most"></a>SQL API、MongoDB API、Gremlin API、Table API

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 1000 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>次の手順

Azure Cosmos DB でのスループットのプロビジョニングについては、次の記事を参照してください。

* [データベースのスループットをプロビジョニングする方法](how-to-provision-database-throughput.md)
* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)
