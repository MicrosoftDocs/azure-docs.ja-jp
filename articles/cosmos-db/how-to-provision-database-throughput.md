---
title: Azure Cosmos DB のデータベースのスループットをプロビジョニングする
description: Azure Cosmos DB のデータベース レベルでスループットをプロビジョニングする方法について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 93961b44f1c0d063774395ab384cb84b1aa05d99
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71812495"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Azure Cosmos DB のデータベースのスループットをプロビジョニングする

この記事では、Azure Cosmos DB のデータベースのスループットをプロビジョニングする方法について説明します。 スループットは、単一の[コンテナー](how-to-provision-container-throughput.md)を対象にプロビジョニングできるほか、データベースを対象にプロビジョニングして、それをデータベース内の複数のコンテナーで共有することもできます。 コンテナーレベルとデータベースレベルのスループットを使用するタイミングについては、[コンテナーとデータベースでのスループットのプロビジョニングのユース ケース](set-throughput.md)に関する記事を参照してください。 データベース レベルのスループットは、Azure portal または Azure Cosmos DB SDK を使用してプロビジョニングできます。

## <a name="provision-throughput-using-azure-portal"></a>Azure portal を使用してスループットをプロビジョニングする

### <a id="portal-sql"></a>SQL (Core) API

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存の Azure Cosmos アカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいデータベース]** を選択します。 以下の詳細を指定します。

   * データベース ID を入力します。
   * **[スループットのプロビジョニング]** を選択します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

    ![[新しいデータベース] ダイアログ ボックスのスクリーンショット](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Azure CLI または PowerShell を使用してスループットをプロビジョニングする

共有スループットのデータベースを作成するには、次を参照してください。

* [Azure CLI を使用してデータベースを作成する](manage-with-cli.md#create-a-database-with-shared-throughput)
* [PowerShell を使用してデータベースを作成する](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>.NET SDK を使用してスループットをプロビジョニング

> [!Note]
> SQL API 用の Cosmos SDK を使用して、すべての API のスループットをプロビジョニングできます。 Cassandra API では、必要に応じて以下の例を使用することもできます。

### <a id="dotnet-all"></a>すべての API

### <a name="net-v2-sdk"></a>.Net V2 SDK

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

### <a name="net-v3-sdk"></a>.Net V3 SDK

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>次の手順

Azure Cosmos DB のプロビジョニングされたスループットについては、次の記事を参照してください。

* [プロビジョニングされたスループットのグローバルなスケーリング](scaling-throughput.md)
* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)
* [特定のコンテナーに対してスループットをプロビジョニングする方法](how-to-provision-container-throughput.md)
* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)
