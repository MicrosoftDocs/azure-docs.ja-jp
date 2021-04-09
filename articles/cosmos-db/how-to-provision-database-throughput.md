---
title: Azure Cosmos DB SQL API のデータベースのスループットをプロビジョニングする
description: Azure portal、CLI、PowerShell、および他のさまざまな SDK を使用して、Azure Cosmos DB SQL API でデータベース レベルでスループットをプロビジョニングする方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4ecbee2260da735cd6ba74d3b9ffb55b4a652e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341998"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db---sql-api"></a>Azure Cosmos DB SQL API のデータベースに標準 (手動) のスループットをプロビジョニングする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、Azure Cosmos DB SQL API のデータベースに標準 (手動) のスループットをプロビジョニングする方法について説明します。 スループットは、単一の[コンテナー](how-to-provision-container-throughput.md)を対象にプロビジョニングできるほか、データベースを対象にプロビジョニングして、それをデータベース内の複数のコンテナーで共有することもできます。 コンテナーレベルとデータベースレベルのスループットを使用するタイミングについては、[コンテナーとデータベースでのスループットのプロビジョニングのユース ケース](set-throughput.md)に関する記事を参照してください。 データベース レベルのスループットは、Azure portal または Azure Cosmos DB SDK を使用してプロビジョニングできます。

別の API を使用している場合は、[API for MongoDB](how-to-provision-throughput-mongodb.md)、[Cassandra API](how-to-provision-throughput-cassandra.md)、[Gremlin API](how-to-provision-throughput-gremlin.md) のスループット プロビジョニングに関する記事を参照してください。

## <a name="provision-throughput-using-azure-portal"></a>Azure portal を使用してスループットをプロビジョニングする

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存の Azure Cosmos アカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいデータベース]** を選択します。 以下の詳細を指定します。

   * データベース ID を入力します。
   * **[Provision database throughput]\(データベース スループットをプロビジョニングする\)** オプションを選択します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-sql-api.png" alt-text="[新しいデータベース] ダイアログ ボックスのスクリーンショット":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Azure CLI または PowerShell を使用してスループットをプロビジョニングする

共有スループットのデータベースを作成するには、次を参照してください。

* [Azure CLI を使用してデータベースを作成する](manage-with-cli.md#create-a-database-with-shared-throughput)
* [PowerShell を使用してデータベースを作成する](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>.NET SDK を使用してスループットをプロビジョニング

> [!Note]
> SQL API 用の Azure Cosmos SDK を使用して、すべての API のスループットをプロビジョニングできます。 Cassandra API では、必要に応じて以下の例を使用することもできます。

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB のプロビジョニングされたスループットについては、次の記事を参照してください。

* [プロビジョニングされたスループットのグローバルなスケーリング](./request-units.md)
* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)
* [コンテナーに標準 (手動) スループットをプロビジョニングする方法](how-to-provision-container-throughput.md)
* [コンテナーに自動スケーリングのスループットをプロビジョニングする方法](how-to-provision-autoscale-throughput.md)
* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)