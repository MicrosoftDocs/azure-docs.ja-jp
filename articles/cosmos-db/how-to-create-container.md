---
title: Azure Cosmos DB にコンテナーを作成する
description: Azure Cosmos DB にコンテナーを作成する方法について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 8ce890500c31c1966254e5bca9d23c8fcdd7bb67
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258294"
---
# <a name="create-an-azure-cosmos-container"></a>Azure Cosmos コンテナーを作成する

この記事では、コンテナー (コレクション、テーブル、またはグラフ) を作成するいくつかの方法について説明します。 Azure portal、Azure CLI、またはサポートされている SDK を使用します。 この記事では、コンテナーを作成してパーティション キーを指定し、スループットをプロビジョニングする方法を紹介します。

## <a name="create-a-container-by-using-azure-portal"></a>Azure portal を使用してコンテナーを作成する

### <a id="portal-sql"></a>SQL API

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos DB アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコレクション]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * コレクション ID を入力します。
   * パーティション キーを入力します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

![[データ エクスプローラー] ウィンドウのスクリーンショット ([新しいコレクション] を強調表示したところ)](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>MongoDB 用 Azure Cosmos DB API

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos DB アカウントを作成する](create-mongodb-dotnet.md#create-a-database-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコレクション]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * コレクション ID を入力します。
   * **[無制限]** のストレージ容量を選択します。
   * シャード キーを入力します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

![MongoDB 用 Azure Cosmos DB API の [コレクションの追加] ダイアログ ボックスのスクリーンショット](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Cassandra API

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos DB アカウントを作成する](create-cassandra-dotnet.md#create-a-database-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいテーブル]** を選択します。 続けて、以下の詳細を指定してください。

   * 新しいキースペースを作成するか、既存のキースペースを使用するかを指定します。
   * テーブルの名前を入力します。
   * プロパティを入力し、主キーを指定します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

![Cassandra API の [テーブルの追加] ダイアログ ボックスのスクリーンショット](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Cassandra API では、主キーがパーティション キーとして使用されます。

### <a id="portal-gremlin"></a>Gremlin API

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos DB アカウントを作成する](create-graph-dotnet.md#create-a-database-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいグラフ]** を選択します。 続けて、以下の詳細を指定してください。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * グラフ ID を入力します。
   * **[無制限]** のストレージ容量を選択します。
   * 頂点のパーティション キーを入力します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

![Gremlin API の [グラフの追加] ダイアログ ボックスのスクリーンショット](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Table API

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos DB アカウントを作成する](create-table-dotnet.md#create-a-database-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいテーブル]** を選択します。 続けて、以下の詳細を指定してください。

   * テーブル ID を入力します。
   * **[無制限]** のストレージ容量を選択します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

![Table API の [テーブルの追加] ダイアログ ボックスのスクリーンショット](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Table API では、新しい行を追加するたびにパーティション キーが指定されます。

## <a name="create-a-container-by-using-azure-cli"></a>Azure CLI を使用してコンテナーを作成する

### <a id="cli-sql"></a>SQL API

```azurecli-interactive
# Create a container with a partition key and provision 1000 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-mongodb"></a>MongoDB 用 Azure Cosmos DB API

```azurecli-interactive
# Create a collection with a shard key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 1000
```

### <a id="cli-cassandra"></a>Cassandra API

```azurecli-interactive
# Create a table with a partition/primary key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 1000
```

### <a id="cli-gremlin"></a>Gremlin API

```azurecli-interactive
# Create a graph with a partition key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-table"></a>Table API

```azurecli-interactive
# Create a table with 1000 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 1000
```

## <a name="create-a-container-by-using-net-sdk"></a>.NET SDK を使用してコンテナーを作成する

### <a id="dotnet-sql-graph"></a>SQL API と Gremlin API

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>MongoDB 用 Azure Cosmos DB API

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB ワイヤ プロトコルには、要求ユニットという概念はありません。 スループットを指定して新しいコレクションを作成する場合は、Azure portal または SQL API を使用してください。

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB でのパーティション分割](partitioning-overview.md)
