---
title: Azure Cosmos DB Cassandra API リソース スループットのプロビジョニング
description: Azure Cosmos DB Cassandra API リソースでコンテナー、データベース、自動スケールのスループットをプロビジョニングする方法について説明します。 Azure portal、CLI、PowerShell、その他のさまざまな SDK を使用します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 63b633ed67c03a006a154bc69a1aafb4cb4aa6d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93086280"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>Azure Cosmos DB Cassandra API リソースのデータベース、コンテナー、または自動スケール スループットのプロビジョニング
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

この記事では、Azure Cosmos DB Cassandra API のスループットをプロビジョニングする方法について説明します。 標準 (手動) または自動スケーリングのスループットは、コンテナーを対象にプロビジョニングできるほか、データベースを対象にプロビジョニングして、それをデータベース内の複数のコンテナーで共有することもできます。 スループットは、Azure portal、Azure CLI、Azure Cosmos DB SDK のいずれかを使用してプロビジョニングできます。

別の API を使用している場合は、[SQL API](how-to-provision-container-throughput.md)、[API for MongoDB](how-to-provision-throughput-mongodb.md)、[Gremlin API](how-to-provision-throughput-gremlin.md) のスループット プロビジョニングに関する記事を参照してください。

## <a name="azure-portal"></a><a id="portal-cassandra"></a> Azure portal

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-mongodb-dotnet.md#create-a-database-account)か、既存の Azure Cosmos アカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいテーブル]** を選択します。 次に、以下の詳細を指定します。

   * 新しいキースペースを作成するか、既存のキースペースを使用するかを指定します。 キースペース レベルでスループットをプロビジョニングする場合は、 **[データベースのスループットのプロビジョニング]** オプションを選択します。
   * CQL コマンド内でテーブル ID を入力します。
   * 主キーの値 (`/userrID` など) を入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="データベース レベルのスループットで新しいコレクションを作成しているときの、データ エクスプローラーのスクリーンショット":::

> [!Note]
> Cassandra API を使用して構成された Azure Cosmos アカウントのコンテナーに対するスループットをプロビジョニングする場合は、パーティション キーのパスとして `/myPrimaryKey` を使用します。

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> .NET SDK

### <a name="provision-throughput-for-a-cassandra-table"></a>Cassandra テーブルに対してスループットをプロビジョニングする

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
同様のコマンドは、CQL に準拠している任意のドライバーを使用して発行できます。

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>Cassandra テーブルのスループットを変更する

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

同様のコマンドは、CQL に準拠している任意のドライバーを使用して実行できます。

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager テンプレートを使用すると、すべての Azure Cosmos DB API に対して、データベースまたはコンテナー レベルのリソースへの自動スケーリングのスループットをプロビジョニングできます。 サンプルについては、「[Azure Cosmos DB の Azure Resource Manager テンプレート](templates-samples-cassandra.md)」を参照してください。

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用すると、すべての Azure Cosmos DB API に対して、データベースまたはコンテナー レベルのリソースへの自動スケーリングのスループットをプロビジョニングできます。 サンプルについては、「[Azure Cosmos DB の Azure CLI サンプル](cli-samples-cassandra.md)」を参照してください。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用すると、すべての Azure Cosmos DB API に対して、データベースまたはコンテナー レベルのリソースへの自動スケーリングのスループットをプロビジョニングできます。 サンプルについては、「[Azure Cosmos DB 用 Azure PowerShell サンプル](powershell-samples-cassandra.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB でのスループットのプロビジョニングについては、次の記事を参照してください。

* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)