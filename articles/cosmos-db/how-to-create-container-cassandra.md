---
title: Azure Cosmos DB Cassandra API でコンテナーを作成する
description: Azure portal、.NET、Java、Python、Node.js、およびその他の SDK を使用して、Azure Cosmos DB Cassandra API でコンテナーを作成する方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 01030a563c15eee7786058c2eae30d23803dfe42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101648"
---
# <a name="create-a-container-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API でコンテナーを作成する
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

この記事では、Azure Cosmos DB Cassandra API でコンテナーを作成するさまざまな方法について説明します。 Azure portal、Azure CLI、PowerShell、またはサポートされている SDK を使用してコンテナーを作成する方法を示します。 この記事では、コンテナーを作成してパーティション キーを指定し、スループットをプロビジョニングする方法を紹介します。

この記事では、Azure Cosmos DB Cassandra API でコンテナーを作成するさまざまな方法について説明します。 異なる API を使用している場合は、[MongoDB 用の API](how-to-create-container-mongodb.md)、[Cassandra API](how-to-create-container-gremlin.md)、[Table API](how-to-create-container-table.md)、[SQL API](how-to-create-container.md) に関する記事を参照してコンテナーを作成してください。

> [!NOTE]
> コンテナーを作成するときは、同じ名前で大文字と小文字が異なる 2 つのコンテナーを作成しないようにしてください。 これは、Azure プラットフォームの一部で大文字と小文字が区別されないため、このような名前のコンテナーでテレメトリとアクションの混同や衝突が発生する可能性があるためです。

## <a name="create-using-azure-portal"></a><a id="portal-cassandra"></a>Azure portal を使用した作成

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

## <a name="create-using-net-sdk"></a><a id="dotnet-cassandra"></a>.NET SDK を使用して作成する

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

コレクションの作成時にタイムアウト例外が発生した場合は、コレクションが正常に作成されたかどうかを確認する読み取り操作を実行します。 この読み取り操作では、コレクションの作成操作が成功するまで例外がスローされます。 作成操作でサポートされている状態コードの一覧については、記事「[Azure Cosmos DB の HTTP 状態コード](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)」を参照してください。

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Azure CLI を使用して作成する

[Azure CLI を使用して Cassandra テーブルを作成します](./scripts/cli/cassandra/create.md)。 すべての Azure Cosmos DB API にわたる Azure CLI の全サンプルの一覧については、「[Azure Cosmos DB の Azure CLI サンプル](cli-samples.md)」を参照してください。

## <a name="create-using-powershell"></a>PowerShell を使用して作成する

[Powershell を使用して Cassandra テーブルを作成します](./scripts/powershell/cassandra/create.md)。 すべての Azure Cosmos DB API にわたる PowerShell の全サンプルの一覧については、「[Azure Cosmos DB 用 Azure PowerShell サンプル](powershell-samples.md)」を参照してください

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB でのパーティション分割](partitioning-overview.md)
* [Azure Cosmos DB の要求ユニット](request-units.md)
* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)
* [Azure Cosmos アカウントの使用](./account-databases-containers-items.md)