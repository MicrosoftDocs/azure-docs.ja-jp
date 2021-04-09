---
title: Azure Cosmos DB SQL API で自動スケーリングのスループットをプロビジョニングする
description: Azure portal、CLI、PowerShell、およびその他のさまざまな SDK を使用して、Azure Cosmos DB SQL API のコンテナーおよびデータベース レベルで自動スケーリングのスループットをプロビジョニングする方法について説明します。
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 52904296df77d9097a6180345388e8e702e2bca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97357628"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db---sql-api"></a>Azure Cosmos DB - SQL API のデータベースまたはコンテナーで、自動スケーリングのスループットをプロビジョニングする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、Azure Cosmos DB SQL API のデータベースまたはコンテナー (コレクション、グラフ、またはテーブル) で、自動スケーリングのスループットをプロビジョニングする方法について説明します。 自動スケーリングは、単一のコンテナーを対象に有効にできるほか、データベースを対象に自動スケーリングのスループットをプロビジョニングして、それをデータベースのすべてのコンテナーで共有することもできます。

別の API を使用している場合は、[MongoDB 用 API](how-to-provision-throughput-mongodb.md)、[Cassandra API](how-to-provision-throughput-cassandra.md)、[Gremlin API](how-to-provision-throughput-gremlin.md) のスループット プロビジョニングに関する記事を参照してください。

## <a name="azure-portal"></a>Azure portal

### <a name="create-new-database-or-container-with-autoscale"></a>自動スケーリングを使用する新しいデータベースまたはコンテナーを作成する

1. [Azure portal](https://portal.azure.com) または [Azure Cosmos DB Explorer](https://cosmos.azure.com/) にサインインします。

1. Azure Cosmos DB アカウントに移動して、 **[データ エクスプローラー]** タブを開きます。

1. **[新しいコンテナー]** を選択します。 データベース、コンテナー、およびパーティション キーの名前を入力します。 **[スループット]** で **[自動スケーリング]** オプションを選択し、データベースまたはコンテナーをスケーリングする、[最大スループット (RU/秒)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works) を設定します。

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="コンテナーの作成と、自動スケーリングによってプロビジョニングされたスループットの構成":::

1. **[OK]** を選択します。

共有スループット データベースで自動スケーリングをプロビジョニングするには、新しいデータベースを作成する際に **[Provision database throughput]\(データベース スループットをプロビジョニングする\)** オプションを選択します。 

### <a name="enable-autoscale-on-existing-database-or-container"></a>既存のデータベースまたはコンテナーで自動スケーリングを有効にする

> [!IMPORTANT]
> 現在のリリースでは、自動スケーリングと標準 (手動) のプロビジョニングされたスループットの間での移行は、Azure portal でのみ行うことができます。 

1. [Azure portal](https://portal.azure.com) または [Azure Cosmos DB Explorer](https://cosmos.azure.com/) にサインインします。

1. Azure Cosmos DB アカウントに移動して、 **[データ エクスプローラー]** タブを開きます。

1. コンテナーの **[Scale and Settings]\(スケーリングと設定\)** か、データベースの **[スケーリング]** を選択します。

1. **[スケーリング]** で、 **[自動スケーリング]** オプションを選択して **[保存]** します。

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="既存のコンテナーで自動スケーリングを有効にする":::

> [!NOTE]
> 既存のデータベースまたはコンテナーで自動スケーリングを有効にする場合、最大 RU/s の開始値は、現在の手動でプロビジョニングされたスループットの設定とストレージに基づいて、システムによって決定されます。 操作が完了したら、必要に応じて最大 RU/s を変更できます。 [詳細情報。](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) 

## <a name="azure-cosmos-db-net-v3-sdk"></a>Azure Cosmos DB .NET V3 SDK

[バージョン 3.9 以上](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)の Azure Cosmos DB .NET SDK for SQL API を使用して、自動スケーリング リソースを管理します。 

> [!IMPORTANT]
> .NET SDK を使用して新しい自動スケーリング リソースを作成できます。 この SDK は、自動スケーリングと標準 (手動) のスループットとの間と移行をサポートしていません。 現在、この移行シナリオは Azure portal でのみサポートされています。 

### <a name="create-database-with-shared-throughput"></a>共有スループットのデータベースを作成する

```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>専用スループットを持つコンテナーを作成する

```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>現在のスループット (RU/s) を読み取る

```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>自動スケーリングの最大スループット (RU/s) を変更する

```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk"></a>Azure Cosmos DB Java V4 SDK

[バージョン 4.0 以上](https://mvnrepository.com/artifact/com.azure/azure-cosmos)の Azure Cosmos DB Java SDK for SQL API を使用して、自動スケーリング リソースを管理できます。

> [!IMPORTANT]
> Java SDK を使用して新しい自動スケーリング リソースを作成できます。 この SDK は、自動スケーリングと標準 (手動) のスループットとの間と移行をサポートしていません。 現在、この移行シナリオは Azure portal でのみサポートされています。

### <a name="create-database-with-shared-throughput"></a>共有スループットのデータベースを作成する

#### <a name="async"></a>[非同期](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

#### <a name="sync"></a>[同期](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>専用スループットを持つコンテナーを作成する

#### <a name="async"></a>[非同期](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

#### <a name="sync"></a>[同期](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>現在のスループット (RU/s) を読み取る

#### <a name="async"></a>[非同期](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

#### <a name="sync"></a>[同期](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>自動スケーリングの最大スループット (RU/s) を変更する

#### <a name="async"></a>[非同期](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

#### <a name="sync"></a>[同期](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager テンプレートを使用すると、すべての Azure Cosmos DB API に対して、データベースまたはコンテナー レベルのリソースへの自動スケーリングのスループットをプロビジョニングできます。 サンプルについては、「[Azure Cosmos DB の Azure Resource Manager テンプレート](./templates-samples-sql.md)」を参照してください。

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用すると、すべての Azure Cosmos DB API に対して、データベースまたはコンテナー レベルのリソースへの自動スケーリングのスループットをプロビジョニングできます。 サンプルについては、「[Azure Cosmos DB の Azure CLI サンプル](cli-samples.md)」を参照してください。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用すると、すべての Azure Cosmos DB API に対して、データベースまたはコンテナー レベルのリソースへの自動スケーリングのスループットをプロビジョニングできます。 サンプルについては、「[Azure Cosmos DB 用 Azure PowerShell サンプル](powershell-samples.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [自動スケーリングでプロビジョニングされたスループットの利点](provision-throughput-autoscale.md#benefits-of-autoscale)について学ぶ。
* [手動と自動スケーリングのスループットのどちらを選択するか](how-to-choose-offer.md)について学ぶ。
* [自動スケーリングに関する FAQ](autoscale-faq.md) を確認する。
