---
title: Azure Cosmos DB のコンテナーのスループットをプロビジョニングする
description: Azure Cosmos DB のコンテナー レベルでスループットをプロビジョニングする方法について説明します。
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: f195eaa0f5d22160de8c1e9e2f429073de001828
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986021"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Azure Cosmos コンテナー上でのスループットをプロビジョニングする

この記事では、Azure Cosmos DB のコンテナー (コレクション、グラフ、またはテーブル) のスループットをプロビジョニングする方法について説明します。 スループットは、単一のコンテナーを対象にプロビジョニングできるほか、[データベースを対象にプロビジョニング](how-to-provision-database-throughput.md)して、それをデータベース内の複数のコンテナーで共有することもできます。 コンテナーのスループットは、Azure portal、Azure CLI、Azure Cosmos DB SDK のいずれかを使用してプロビジョニングできます。

## <a name="provision-throughput-using-azure-portal"></a>Azure portal を使用してスループットをプロビジョニングする

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存の Azure Cosmos アカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコレクション]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * コンテナー (またはテーブル、グラフ) ID を入力します。
   * パーティション キーの値 (`/userid` など) を入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

![[新しいコレクション] が強調表示されている [データ エクスプローラー] のスクリーンショット](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>Azure CLI を使用してスループットをプロビジョニングする

```azurecli-interactive
# Create a container with a partition key and provision throughput of 400 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 400
```

## <a name="provision-throughput-using-powershell"></a>PowerShell を使用してスループットをプロビジョニングする

```azurepowershell-interactive
# Create a container with a partition key and provision throughput of 400 RU/s
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"= 400 }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

MongoDB 用 Azure Cosmos DB API を使用して構成された Azure Cosmos DB アカウントのコンテナーに対するスループットをプロビジョニングする場合は、パーティション キーのパスとして `/myShardKey` を使用します。 Cassandra API を使用して構成された Azure Cosmos アカウントのコンテナーに対するスループットをプロビジョニングする場合は、パーティション キーのパスとして `/myPrimaryKey` を使用します。

## <a name="provision-throughput-by-using-net-sdk"></a>.NET SDK を使用してスループットをプロビジョニングする

> [!Note]
> Cassandra API を除くすべての Cosmos DB API シリーズに対するスループットをプロビジョニングするには、SQL API 用の Cosmos SDK を使用します。

### <a id="dotnet-most"></a>SQL API、MongoDB API、Gremlin API、Table API
### <a name="net-v2-sdk"></a>.Net V2 SDK

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

### <a name="net-v3-sdk"></a>.Net V3 SDK
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>次の手順

Azure Cosmos DB でのスループットのプロビジョニングについては、次の記事を参照してください。

* [データベースのスループットをプロビジョニングする方法](how-to-provision-database-throughput.md)
* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)
