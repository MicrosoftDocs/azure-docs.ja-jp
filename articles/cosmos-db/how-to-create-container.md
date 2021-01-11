---
title: Azure Cosmos DB にコンテナーを作成する
description: Azure portal、.NET、Java、Python、Node.js、およびその他の SDK を使用して、Azure Cosmos DB で コンテナーを作成する方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 2362326bccd90af997aa9237ec5f14e39ae62c85
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019999"
---
# <a name="create-an-azure-cosmos-container"></a>Azure Cosmos コンテナーを作成する

この記事では、Azure portal、Azure CLI、PowerShell、またはサポートされている SDK を使用して Azure Cosmos コンテナー (コレクション、テーブル、またはグラフ) を作成するいくつかの方法について説明します。 この記事では、コンテナーを作成してパーティション キーを指定し、スループットをプロビジョニングする方法を紹介します。

> [!NOTE]
> コンテナーを作成するときは、同じ名前で大文字と小文字が異なる 2 つのコンテナーを作成しないようにしてください。 これは、Azure プラットフォームの一部で大文字と小文字が区別されないため、このような名前のコンテナーでテレメトリとアクションの混同や衝突が発生する可能性があるためです。

## <a name="create-a-container-using-azure-portal"></a>Azure portal を使用してコンテナーを作成する

### <a name="sql-api"></a><a id="portal-sql"></a>SQL API

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコンテナー]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * コンテナー ID を入力します。
   * パーティション キーを入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="[新しいコンテナー] が強調して示されている [データ エクスプローラー] ウィンドウのスクリーンショット":::

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>MongoDB 用 Azure Cosmos DB API

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-mongodb-dotnet.md#create-a-database-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコンテナー]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * コンテナー ID を入力します。
   * シャード キーを入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="MongoDB 用 Azure Cosmos DB API の [コンテナーの追加] ダイアログ ボックスのスクリーンショット":::

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>Cassandra API

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-cassandra-dotnet.md#create-a-database-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいテーブル]** を選択します。 次に、以下の詳細を指定します。

   * 新しいキースペースを作成するか、既存のキースペースを使用するかを指定します。
   * テーブルの名前を入力します。
   * プロパティを入力し、主キーを指定します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Cassandra API の [テーブルの追加] ダイアログ ボックスのスクリーンショット":::

> [!NOTE]
> Cassandra API では、主キーがパーティション キーとして使用されます。

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>Gremlin API

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-graph-dotnet.md#create-a-database-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいグラフ]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * グラフ ID を入力します。
   * **[無制限]** のストレージ容量を選択します。
   * 頂点のパーティション キーを入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Gremlin API の [グラフの追加] ダイアログ ボックスのスクリーンショット":::

### <a name="table-api"></a><a id="portal-table"></a>Table API

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-table-dotnet.md#create-a-database-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいテーブル]** を選択します。 次に、以下の詳細を指定します。

   * テーブル ID を入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Table API の [テーブルの追加] ダイアログ ボックスのスクリーンショット":::

> [!Note]
> Table API では、新しい行を追加するたびにパーティション キーが指定されます。

## <a name="create-a-container-using-azure-cli"></a>Azure CLI を使用してコンテナーを作成する<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

以下のリンクは、Azure CLI を使用して Azure Cosmos DB のコンテナー リソースを作成する方法を示すものです。

すべての Azure Cosmos DB API にわたる Azure CLI の全サンプルの一覧については、「[Azure Cosmos DB の Azure CLI サンプル](cli-samples.md)」を参照してください。

* [Azure CLI を使用してコンテナーを作成する](manage-with-cli.md#create-a-container)
* [Azure CLI を使用して MongoDB 用 Azure Cosmos DB API のコレクションを作成する](./scripts/cli/mongodb/create.md)
* [Azure CLI を使用して Cassandra テーブルを作成する](./scripts/cli/cassandra/create.md)
* [Azure CLI を使用して Gremlin グラフを作成する](./scripts/cli/gremlin/create.md)
* [Azure CLI を使用して Table API テーブルを作成する](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershell"></a>PowerShell を使用してコンテナーを作成する

以下のリンクは、PowerShell を使用して Azure Cosmos DB のコンテナー リソースを作成する方法を示すものです。

すべての Azure Cosmos DB API にわたる PowerShell の全サンプルの一覧については、「[Azure Cosmos DB 用 Azure PowerShell サンプル](powershell-samples.md)」を参照してください

* [PowerShell を使用してコンテナーを作成する](manage-with-powershell.md#create-container)
* [Powershell を使用して MongoDB 用 Azure Cosmos DB API のコレクションを作成する](./scripts/powershell/mongodb/create.md)
* [Powershell を使用して Cassandra テーブルを作成する](./scripts/powershell/cassandra/create.md)
* [Powershell を使用して Gremlin グラフを作成する](./scripts/powershell/gremlin/create.md)
* [Powershell を使用して Table API テーブルを作成する](./scripts/powershell/table/create.md)

## <a name="create-a-container-using-net-sdk"></a>.NET SDK を使用してコンテナーを作成する

コレクションの作成時にタイムアウト例外が発生した場合は、コレクションが正常に作成されたかどうかを確認する読み取り操作を実行します。 この読み取り操作では、コレクションの作成操作が成功するまで例外がスローされます。 作成操作でサポートされている状態コードの一覧については、記事「[Azure Cosmos DB の HTTP 状態コード](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)」を参照してください。

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>SQL API と Gremlin API

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

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>MongoDB 用 Azure Cosmos DB API

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB ワイヤ プロトコルでは、[要求ユニット](request-units.md)という概念は理解されません。 プロビジョニングされたスループットを持つ新しいコレクションを作成するには、Azure portal または SQL API 用の Cosmos DB SDK を使用します。

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB でのパーティション分割](partitioning-overview.md)
* [Azure Cosmos DB の要求ユニット](request-units.md)
* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)
* [Azure Cosmos アカウントの使用](account-overview.md)
