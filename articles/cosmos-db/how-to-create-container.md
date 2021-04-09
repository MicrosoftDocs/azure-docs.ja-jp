---
title: Azure Cosmos DB SQL API にコンテナーを作成する
description: Azure portal、.NET、Java、Python、Node.js、およびその他の SDK を使用して、Azure Cosmos DB SQL API でコンテナーを作成する方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 302c5d6e8e523a11b8773f10bb6089e3bea09bdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006850"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API にコンテナーを作成する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、Azure Cosmos DB SQL API にコンテナーを作成するいくつかの方法について説明します。 Azure portal、Azure CLI、PowerShell、またはサポートされている SDK を使用して、コンテナーを作成する方法が示されます。 この記事では、コンテナーを作成してパーティション キーを指定し、スループットをプロビジョニングする方法を紹介します。

この記事では、Azure Cosmos DB SQL API にコンテナーを作成するいくつかの方法について説明します。 別の API を使用している場合、コンテナーを作成するには、[MongoDB の API](how-to-create-container-mongodb.md)、[Cassandra API](how-to-create-container-cassandra.md)、[Gremlin API](how-to-create-container-gremlin.md)、[Table API](how-to-create-container-table.md) に関する記事を参照してください。

> [!NOTE]
> コンテナーを作成するときは、同じ名前で大文字と小文字が異なる 2 つのコンテナーを作成しないようにしてください。 これは、Azure プラットフォームの一部で大文字と小文字が区別されないため、このような名前のコンテナーでテレメトリとアクションの混同や衝突が発生する可能性があるためです。

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Azure portal を使用してコンテナーを作成する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコンテナー]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * コンテナー ID を入力します。
   * パーティション キーを入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="[新しいコンテナー] が強調して示されている [データ エクスプローラー] ウィンドウのスクリーンショット":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>Azure CLI を使用してコンテナーを作成する

[Azure CLI を使用してコンテナーを作成します](manage-with-cli.md#create-a-container)。 すべての Azure Cosmos DB API にわたる Azure CLI の全サンプルの一覧については、「[Azure Cosmos DB の Azure CLI サンプル](cli-samples.md)」を参照してください。

## <a name="create-a-container-using-powershell"></a>PowerShell を使用してコンテナーを作成する

[PowerShell を使用してコンテナーを作成します](manage-with-powershell.md#create-container)。 すべての Azure Cosmos DB API にわたる PowerShell の全サンプルの一覧については、「[Azure Cosmos DB 用 Azure PowerShell サンプル](powershell-samples.md)」を参照してください

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>.NET SDK を使用してコンテナーを作成する

コレクションの作成時にタイムアウト例外が発生した場合は、コレクションが正常に作成されたかどうかを確認する読み取り操作を実行します。 この読み取り操作では、コレクションの作成操作が成功するまで例外がスローされます。 作成操作でサポートされている状態コードの一覧については、記事「[Azure Cosmos DB の HTTP 状態コード](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)」を参照してください。

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

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB でのパーティション分割](partitioning-overview.md)
* [Azure Cosmos DB の要求ユニット](request-units.md)
* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)
* [Azure Cosmos アカウントの使用](./account-databases-containers-items.md)