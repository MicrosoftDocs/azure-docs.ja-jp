---
title: Azure Cosmos DB にコンテナーを作成する
description: Azure Cosmos DB にコンテナーを作成する方法について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 911f56cef58e5d9c161a3ba947850bcfff073200
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242402"
---
# <a name="create-an-azure-cosmos-container"></a>Azure Cosmos コンテナーを作成する

この記事では、Azure Cosmos コンテナー (コレクション、テーブル、またはグラフ) を作成するいくつかの方法について説明します。 これには、Azure portal、Azure CLI、またはサポートされている SDK を使用します。 この記事では、コンテナーを作成してパーティション キーを指定し、スループットをプロビジョニングする方法を紹介します。

## <a name="create-a-container-using-azure-portal"></a>Azure portal を使用してコンテナーを作成する

### <a id="portal-sql"></a>SQL API

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコンテナー]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * コンテナー ID を入力します。
   * パーティション キーを入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    ![[新しいコンテナー] が強調して示されている [データ エクスプローラー] ウィンドウのスクリーンショット](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>MongoDB 用 Azure Cosmos DB API

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-mongodb-dotnet.md#create-a-database-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコンテナー]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * コンテナー ID を入力します。
   * シャード キーを入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    ![MongoDB 用 Azure Cosmos DB API の [コンテナーの追加] ダイアログ ボックスのスクリーンショット](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Cassandra API

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-cassandra-dotnet.md#create-a-database-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいテーブル]** を選択します。 続けて、以下の詳細を指定してください。

   * 新しいキースペースを作成するか、既存のキースペースを使用するかを指定します。
   * テーブルの名前を入力します。
   * プロパティを入力し、主キーを指定します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    ![Cassandra API の [テーブルの追加] ダイアログ ボックスのスクリーンショット](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Cassandra API では、主キーがパーティション キーとして使用されます。

### <a id="portal-gremlin"></a>Gremlin API

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-graph-dotnet.md#create-a-database-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいグラフ]** を選択します。 続けて、以下の詳細を指定してください。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * グラフ ID を入力します。
   * **[無制限]** のストレージ容量を選択します。
   * 頂点のパーティション キーを入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    ![Gremlin API の [グラフの追加] ダイアログ ボックスのスクリーンショット](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>Table API

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-table-dotnet.md#create-a-database-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいテーブル]** を選択します。 次に、以下の詳細を指定します。

   * テーブル ID を入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    ![Table API の [テーブルの追加] ダイアログ ボックスのスクリーンショット](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Table API では、新しい行を追加するたびにパーティション キーが指定されます。

## Azure CLI を使用してコンテナーを作成する<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

以下のリンクは、Azure CLI を使用して Azure Cosmos DB のコンテナー リソースを作成する方法を示すものです。

すべての Azure Cosmos DB API にわたる Azure CLI の全サンプルの一覧については、[SQL API](cli-samples.md)、[Cassandra API](cli-samples-cassandra.md)、[MongoDB API](cli-samples-mongodb.md)、[Gremlin API](cli-samples-gremlin.md)、[Table API](cli-samples-table.md) に関する記事を参照してください。

* [Azure CLI を使用してコンテナーを作成する](manage-with-cli.md#create-a-container)
* [Azure CLI を使用して MongoDB 用 Azure Cosmos DB API のコレクションを作成する](./scripts/cli/mongodb/create.md)
* [Azure CLI を使用して Cassandra テーブルを作成する](./scripts/cli/cassandra/create.md)
* [Azure CLI を使用して Gremlin グラフを作成する](./scripts/cli/gremlin/create.md)
* [Azure CLI を使用して Table API テーブルを作成する](./scripts/cli/table/create.md)

## PowerShell を使用してコンテナーを作成する<a id="ps-sql"></a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"><a id="ps-table"></a>

以下のリンクは、PowerShell を使用して Azure Cosmos DB のコンテナー リソースを作成する方法を示すものです。

すべての Azure Cosmos DB API にわたる Azure CLI の全サンプルの一覧については、[SQL API](powershell-samples-sql.md)、[Cassandra API](powershell-samples-cassandra.md)、[MongoDB API](powershell-samples-mongodb.md)、[Gremlin API](powershell-samples-gremlin.md)、[Table API](powershell-samples-table.md) に関する記事を参照してください。

* [PowerShell を使用してコンテナーを作成する](manage-with-powershell.md#create-container)
* [Powershell を使用して MongoDB 用 Azure Cosmos DB API のコレクションを作成する](./scripts/powershell/mongodb/ps-mongodb-create.md)
* [Powershell を使用して Cassandra テーブルを作成する](./scripts/powershell/cassandra/ps-cassandra-create.md)
* [Powershell を使用して Gremlin グラフを作成する](./scripts/powershell/gremlin/ps-gremlin-create.md)
* [Powershell を使用して Table API テーブルを作成する](./scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>.NET SDK を使用してコンテナーを作成する

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
> MongoDB ワイヤ プロトコルでは、[要求ユニット](request-units.md)という概念は理解されません。 プロビジョニングされたスループットを持つ新しいコレクションを作成するには、Azure portal または SQL API 用の Cosmos DB SDK を使用します。

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>次の手順

* [Azure Cosmos DB でのパーティション分割](partitioning-overview.md)
* [Azure Cosmos DB の要求ユニット](request-units.md)
* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)
* [Azure Cosmos アカウントの使用](account-overview.md)
