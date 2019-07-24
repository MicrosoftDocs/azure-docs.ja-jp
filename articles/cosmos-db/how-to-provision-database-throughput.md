---
title: Azure Cosmos DB のデータベースのスループットをプロビジョニングする
description: Azure Cosmos DB のデータベース レベルでスループットをプロビジョニングする方法について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: c0a947c9cf2c1d4a11d310b9bab0774a70534a1e
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259433"
---
# <a name="provision-throughput-for-a-database-in-azure-cosmos-db"></a>Azure Cosmos DB のデータベースのスループットをプロビジョニングする

この記事では、Azure Cosmos DB のデータベースのスループットをプロビジョニングする方法について説明します。 スループットは、単一の[コンテナー](how-to-provision-container-throughput.md)を対象にプロビジョニングできるほか、データベースを対象にプロビジョニングして、それをデータベース内の複数のコンテナーで共有することもできます。 コンテナーレベルとデータベースレベルのスループットを使用するタイミングについては、[コンテナーとデータベースでのスループットのプロビジョニングのユース ケース](set-throughput.md)に関する記事を参照してください。 データベース レベルのスループットは、Azure portal または Azure Cosmos DB SDK を使用してプロビジョニングできます。

## <a name="provision-throughput-by-using-azure-portal"></a>Azure Portal を使用してスループットをプロビジョニングする

### <a id="portal-sql"></a>SQL (Core) API

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos DB アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいデータベース]** を選択します。 以下の詳細を指定します。

   * データベース ID を入力します。 
   * **[スループットのプロビジョニング]** を選択します。
   * スループットを入力します (例: 1000 RU)。
   * **[OK]** を選択します。

![[新しいデータベース] ダイアログ ボックスのスクリーンショット](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-net-sdk"></a>.NET SDK を使用してスループットをプロビジョニングする

> [!Note]
> すべての API は、SQL API を使用してスループットをプロビジョニングします。 Cassandra API では、必要に応じて以下の例を使用することもできます。

### <a id="dotnet-all"></a>すべての API

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>次の手順

Azure Cosmos DB でのスループットのプロビジョニングについては、次の記事を参照してください。

* [特定のコンテナーに対してスループットをプロビジョニングする方法](how-to-provision-container-throughput.md)
* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)
