---
title: Azure CLI を使用した Azure Cosmos DB Core (SQL) API リソースの管理
description: Azure CLI を使用して Azure Cosmos DB Core (SQL) API リソースを管理します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: b13f5bfffced9afd80663d606e30e028e52643ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563838"
---
# <a name="manage-azure-cosmos-core-sql-api-resources-using-azure-cli"></a>Azure CLI を使用した Azure Cosmos Core (SQL) API リソースの管理
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

次のガイドでは、Azure CLI を使用して Azure Cosmos DB のアカウント、データベース、コンテナーの管理を自動化するための共通のコマンドについて説明します。 Azure Cosmos DB CLI のすべてのコマンドのリファレンス ページは、[Azure CLI リファレンス](/cli/azure/cosmosdb)で確認できます。 [Azure Cosmos DB 向け Azure CLI サンプル](cli-samples.md)にも、MongoDB、Gremlin、Cassandra、Table API で Cosmos DB のアカウント、データベース、コンテナーを作成し、管理する方法などを含むその他の例があります。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.12.1 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

他の API の Azure CLI サンプルについては、[CLI Samples for Cassandra](cli-samples-cassandra.md)、[CLI Samples for MongoDB API](cli-samples-mongodb.md)、[CLI Samples for Gremlin](cli-samples-gremlin.md)、[CLI Samples for Table](cli-samples-table.md) を参照してください。

> [!IMPORTANT]
> Azure Cosmos DB リソースの名前を変更することはできません。これは、Azure Resource Manager とリソース URI が連携する方法に違反するためです。

## <a name="azure-cosmos-accounts"></a>Azure Cosmos アカウント

以下のセクションでは、Azure Cosmos アカウントの管理方法について説明します。

* [Azure Cosmos アカウントを作成する](#create-an-azure-cosmos-db-account)
* [リージョンの追加または削除](#add-or-remove-regions)
* [複数リージョンの書き込みを有効にする](#enable-multiple-write-regions)
* [リージョンのフェールオーバーの優先度を設定する](#set-failover-priority)
* [自動フェールオーバーを有効にする](#enable-automatic-failover)
* [手動フェールオーバーをトリガーする](#trigger-manual-failover)
* [アカウント キーを一覧表示する](#list-account-keys)
* [読み取り専用のアカウント キーを一覧表示する](#list-read-only-account-keys)
* [接続文字列の一覧表示](#list-connection-strings)
* [アカウント キーを再生成する](#regenerate-account-key)

### <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを作成する

Azure Cosmos DB アカウントを SQL API で、米国西部 2 と米国東部 2 リージョンのセッション整合性で作成します。

> [!IMPORTANT]
> Azure Cosmos アカウント名は、44 文字未満の小文字である必要があります。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 44 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

### <a name="add-or-remove-regions"></a>リージョンを追加または削除する

リージョンが 2 つある Azure Cosmos アカウントの作成、リージョンの追加、リージョンの削除を行います。

> [!NOTE]
> Azure Cosmos アカウントに対し、リージョン (`locations`) の追加 (または削除) と他のプロパティの変更を同時に行うことはできません。 リージョンの変更は、アカウント リソースに対する他のあらゆる変更とは別の操作として実行する必要があります。
> [!NOTE]
> このコマンドでは、リージョンの追加および削除が可能ですが、フェールオーバー優先度を変更したり手動フェールオーバーをトリガーしたりすることはできません。 [フェールオーバーの優先度](#set-failover-priority)と[手動フェールオーバーのトリガー](#trigger-manual-failover)に関する説明を参照してください。

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName="South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False
```

### <a name="enable-multiple-write-regions"></a>複数の書き込みリージョンを有効にする

Cosmos アカウントの複数リージョンの書き込みを有効にする

```azurecli-interactive
# Update an Azure Cosmos account from single write region to multiple write regions
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

### <a name="set-failover-priority"></a>フェールオーバー優先度を設定する

Azure Cosmos アカウントに、自動フェールオーバーのフェールオーバー優先度を設定します

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2=0' 'South Central US=1' 'East US 2=2'
```

### <a name="enable-automatic-failover"></a>自動フェールオーバーを有効にする

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

### <a name="trigger-manual-failover"></a>手動フェールオーバーをトリガーする

> [!CAUTION]
> リージョンの優先度を = 0 に変更すると、Azure Cosmos アカウントの手動フェールオーバーがトリガーされます。 他の優先度を変更しても、フェールオーバーはトリガーされません。

```azurecli-interactive
# Assume region order is initially 'West US 2=0' 'East US 2=1' 'South Central US=2' for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2=0' 'South Central US=1' 'West US 2=2'
```

### <a name="list-all-account-keys"></a><a id="list-account-keys"></a> すべてのアカウント キーを一覧表示する

Cosmos アカウントのすべてのキーを取得します。

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

### <a name="list-read-only-account-keys"></a>読み取り専用のアカウント キーを一覧表示する

Cosmos アカウントの読み取り専用キーを取得します。

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

### <a name="list-connection-strings"></a>接続文字列の一覧表示

Cosmos アカウントの接続文字列を取得します。

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

### <a name="regenerate-account-key"></a>アカウント キーの再生成

Cosmos アカウントの新しいキーを再生成します。

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB データベース

以下のセクションでは、Azure Cosmos DB データベースの管理方法について説明します。

* [データベースの作成](#create-a-database)
* [共有スループットのデータベースを作成する](#create-a-database-with-shared-throughput)
* [データベースを自動スケーリングのスループットに移行する](#migrate-a-database-to-autoscale-throughput)
* [データベースのスループットを変更する](#change-database-throughput)
* [データベースが削除されないようにする](#prevent-a-database-from-being-deleted)

### <a name="create-a-database"></a>データベースを作成する

Cosmos データベースを作成します。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

### <a name="create-a-database-with-shared-throughput"></a>共有スループットのデータベースを作成する

共有スループットの Cosmos データベースを作成します。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

### <a name="migrate-a-database-to-autoscale-throughput"></a>データベースを自動スケーリングのスループットに移行する

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

# Migrate to autoscale throughput
az cosmosdb sql database throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    -t 'autoscale'

# Read the new autoscale max throughput
az cosmosdb sql database throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -n $databaseName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv
```

### <a name="change-database-throughput"></a>データベースのスループットを変更する

Cosmos データベースのスループットを秒あたり 1000 RU 増やします。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

### <a name="prevent-a-database-from-being-deleted"></a>データベースが削除されないようにする

削除されないように、データベースに Azure リソースの削除ロックを配置します。 この機能を使用するには、データ プレーン SDK による Cosmos アカウントの変更をロックする必要があります。 詳細については、[SDK からの変更の防止](role-based-access-control.md#prevent-sdk-changes)に関する記事を参照してください。 Azure リソース ロックは、`ReadOnly` のロックの種類を指定することで、リソースが変更されないようにすることもできます。 Cosmos データベースの場合、これを使用して、スループットが変更されないようにすることができます。

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
databaseLockName="$databaseName-Lock"

# Create a delete lock on database
az lock create --name $databaseLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/sqlDatabases \
    --lock-type $lockType \
    --parent $databaseParent \
    --resource $databaseName

# Delete lock on database
lockid=$(az lock show --name $databaseLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/sqlDatabases \
        --resource $databaseName \
        --parent $databaseParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB コンテナー

以下のセクションでは、Azure Cosmos DB コンテナーの管理方法について説明します。

* [コンテナーの作成](#create-a-container)
* [自動スケーリングを使用してコンテナーを作成する](#create-a-container-with-autoscale)
* [TTL が有効なコンテナーを作成する](#create-a-container-with-ttl)
* [カスタム インデックス ポリシーを持つコンテナーの作成](#create-a-container-with-a-custom-index-policy)
* [コンテナーのスループットを変更する](#change-container-throughput)
* [コンテナーを自動スケーリングのスループットに移行する](#migrate-a-container-to-autoscale-throughput)
* [コンテナーが削除されないようにする](#prevent-a-container-from-being-deleted)

### <a name="create-a-container"></a>コンテナーを作成する

既定のインデックス ポリシー、パーティション キー、および 400 の秒あたりの RU を使用して Cosmos コンテナーを作成します。

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

### <a name="create-a-container-with-autoscale"></a>自動スケーリングを使用してコンテナーを作成する

既定のインデックス ポリシー、パーティション キー、および 4000 自動スケーリング RU/秒を使用して Cosmos コンテナーを作成します。

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
maxThroughput=4000

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --max-throughput $maxThroughput
```

### <a name="create-a-container-with-ttl"></a>TTL を使用したコンテナーを作成する

TTL が有効になっている Cosmos コンテナーを作成します。

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl=86400
```

### <a name="create-a-container-with-a-custom-index-policy"></a>カスタム インデックス ポリシーを持つコンテナーを作成する

カスタム インデックス ポリシー、空間インデックス、複合インデックス、パーティション キーを持ち、RU が秒あたり 400 の Cosmos コンテナーを作成します。

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

### <a name="change-container-throughput"></a>コンテナーのスループットを変更する

Cosmos コンテナーのスループットを秒あたり 1000 RU 増やします。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

### <a name="migrate-a-container-to-autoscale-throughput"></a>コンテナーを自動スケーリングのスループットに移行する

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

# Migrate to autoscale throughput
az cosmosdb sql container throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    -t 'autoscale'

# Read the new autoscale max throughput
az cosmosdb sql container throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv
```

### <a name="prevent-a-container-from-being-deleted"></a>コンテナーが削除されないようにする

削除されないように、コンテナーに Azure リソースの削除ロックを配置します。 この機能を使用するには、データ プレーン SDK による Cosmos アカウントの変更をロックする必要があります。 詳細については、[SDK からの変更の防止](role-based-access-control.md#prevent-sdk-changes)に関する記事を参照してください。 Azure リソース ロックは、`ReadOnly` のロックの種類を指定することで、リソースが変更されないようにすることもできます。 Cosmos コンテナーの場合、これを使用して、スループットやその他のプロパティが変更されないようにすることができます。

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
containerParent="databaseAccounts/$accountName/sqlDatabases/$databaseName"
containerLockName="$containerName-Lock"

# Create a delete lock on container
az lock create --name $containerLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/containers \
    --lock-type $lockType \
    --parent $containerParent \
    --resource $containerName

# Delete lock on container
lockid=$(az lock show --name $containerLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/containers \
        --resource-name $containerName \
        --parent $containerParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、次をご覧ください。

* [Azure CLI のインストール](/cli/azure/install-azure-cli)
* [Azure CLI リファレンス](/cli/azure/cosmosdb)
* [Azure Cosmos DB の追加 Azure CLI サンプル](cli-samples.md)