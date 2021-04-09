---
title: PowerShell を使用して Azure Cosmos DB Core (SQL) API リソースを管理する
description: PowerShell を使用して Azure Cosmos DB Core (SQL) API リソースを管理します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 2d99a760e3bf44febcee05bf9827479616bf7bd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93333443"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-using-powershell"></a>PowerShell を使用して Azure Cosmos DB Core (SQL) API リソースを管理する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

以下のガイドでは、PowerShell を使用して、Cosmos アカウント、データベース、コンテナー、スループットなど、Azure Cosmos DB Core (SQL) API リソースの管理をスクリプト処理および自動化する方法について説明します。 他の API 用の PowerShell コマンドレットについては、[Cassandra 用の PowerShell サンプル](powershell-samples-cassandra.md)、[MongoDB API 用の PowerShell サンプル](powershell-samples-mongodb.md)、[Gremlin 用の PowerShell サンプル](powershell-samples-gremlin.md)、[Table 用の PowerShell サンプル](powershell-samples-table.md)に関する記述を参照してください。

> [!NOTE]
> この記事のサンプルでは、[Az.CosmosDB](/powershell/module/az.cosmosdb) 管理コマンドレットを使用します。 最新の変更については、[Az.CosmosDB](/powershell/module/az.cosmosdb) API リファレンス ページを参照してください。

クロスプラットフォームの Azure Cosmos DB の管理には、[クロスプラットフォームの PowerShell](/powershell/scripting/install/installing-powershell) での `Az` コマンドレットと `Az.CosmosDB` コマンドレットのほか、[Azure CLI](manage-with-cli.md)、[REST API][rp-rest-api]、[Azure portal](create-sql-api-dotnet.md#create-account) を使用できます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>作業の開始

[Azure PowerShell のインストールおよび構成方法][powershell-install-configure]に関する記事の指示に従ってインストールし、PowerShell で Azure アカウントにサインインします。

> [!IMPORTANT]
> Azure Cosmos DB リソースの名前を変更することはできません。これは、Azure Resource Manager がリソース URI と連携する方法に違反するためです。

## <a name="azure-cosmos-accounts"></a>Azure Cosmos アカウント

以下のセクションでは、Azure Cosmos アカウントの管理方法について説明します。

* [Azure Cosmos アカウントを作成する](#create-account)
* [Azure Cosmos アカウントを更新する](#update-account)
* [サブスクリプション内のすべての Azure Cosmos アカウントの一覧を取得する](#list-accounts)
* [Azure Cosmos アカウントを取得する](#get-account)
* [Azure Cosmos アカウントを削除する](#delete-account)
* [Azure Cosmos アカウントのタグを更新する](#update-tags)
* [Azure Cosmos アカウントのキーを一覧表示する](#list-keys)
* [Azure Cosmos アカウントのキーを再生成する](#regenerate-keys)
* [Azure Cosmos アカウントの接続文字列を一覧表示する](#list-connection-strings)
* [Azure Cosmos アカウントのフェールオーバーの優先順位を変更する](#modify-failover-priority)
* [Azure Cosmos アカウントの手動フェールオーバーをトリガーする](#trigger-manual-failover)
* [Azure Cosmos DB アカウントのリソース ロックを一覧表示する](#list-account-locks)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> Azure Cosmos アカウントを作成する

このコマンドでは、[複数リージョン][distribute-data-globally]、[自動フェールオーバー](how-to-manage-database-account.md#automatic-failover)、有界整合性制約の[一貫性ポリシー](consistency-levels.md)を使って、Azure Cosmos DB データベース アカウントが作成されます。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName` Cosmos アカウントのデプロイ先となる Azure リソース グループ。 あらかじめ存在している必要があります。
* `$locations` データベース アカウントのリージョン。`FailoverPriority 0` を持つリージョンが書き込みリージョンです。
* `$accountName` Azure Cosmos アカウントの名前。 小文字で一意の名前にする必要があります。使用できるのは、英数字と "-" 文字のみで、長さは 3 から 31 文字としてください。
* `$apiKind` 作成する Cosmos アカウントの種類。 詳細については、[Cosmos DB の API](introduction.md#simplified-application-development) に関するページを参照してください。
* `$consistencyPolicy`、`$maxStalenessInterval`、`$maxStalenessPrefix` Azure Cosmos アカウントの既定の一貫性レベルと設定。 詳細については、[Azure Cosmos DB の一貫性レベル](consistency-levels.md)に関する記事をご覧ください。

Azure Cosmos アカウントは、IP ファイアウォール、仮想ネットワーク サービス エンドポイント、プライベート エンドポイントを使用して構成できます。 Azure Cosmos DB 用に IP ファイアウォールを構成する方法については、[IP ファイアウォールの構成](how-to-configure-firewall.md)に関する記事を参照してください。 Azure Cosmos DB のサービス エンドポイントを有効にする方法については、「[仮想ネットワークからのアクセスの構成](how-to-configure-vnet-service-endpoint.md)」を参照してください。 Azure Cosmos DB のプライベート エンドポイントを有効にする方法については、「[プライベート エンドポイントからのアクセスを構成する](how-to-configure-private-endpoints.md)」を参照してください。

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a> リソース グループ内のすべての Azure Cosmos アカウントを一覧表示する

このコマンドは、リソース グループ内のすべての Azure Cosmos アカウントを一覧表示します。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a> Azure Cosmos アカウントのプロパティを取得する

このコマンドを使用すると、既存の Azure Cosmos アカウントのプロパティを取得できます。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a> Azure Cosmos アカウントを更新する

このコマンドでは、Azure Cosmos DB データベース アカウントのプロパティを更新できます。 更新できるプロパティは次のとおりです。

* 領域を追加または削除する
* 既定の一貫性ポリシーを変更する
* IP 範囲フィルターを変更する
* 仮想ネットワーク構成を変更する
* 複数リージョンの書き込みを有効にする

> [!NOTE]
> Azure Cosmos アカウントに対し、リージョン (`locations`) の追加 (または削除) と他のプロパティの変更を同時に行うことはできません。 リージョンの変更は、アカウントに対する他のあらゆる変更とは別の操作として実行する必要があります。
> [!NOTE]
> このコマンドでは、リージョンの追加および削除が可能ですが、フェールオーバー優先度を変更したり手動フェールオーバーをトリガーしたりすることはできません。 「[フェールオーバー優先度を変更する](#modify-failover-priority)」と「[手動フェールオーバーをトリガーする](#trigger-manual-failover)」を参照してください。

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

# Create the Cosmos DB account
New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Add a region to the account
$locationObject2 = @()
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 2 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject2

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove West US region from the account
$locationObject3 = @()
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 1 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject3

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```

### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-region-writes"></a>Azure Cosmos アカウントの複数の書き込みリージョンを有効にする

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-region writes
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a> Azure Cosmos アカウントを削除する

このコマンドは、既存の Azure Cosmos アカウントを削除します。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -PassThru:$true
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a> Azure Cosmos アカウントのタグを更新する

このコマンドは、Azure Cosmos アカウントに [Azure リソース タグ][azure-resource-tags]を設定します。 タグは、アカウントの作成時に `New-AzCosmosDBAccount` を使用して設定できるほか、アカウントの更新時に `Update-AzCosmosDBAccount` を使用して設定することもできます。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a> アカウント キーの一覧表示

Azure Cosmos アカウントを作成すると、Azure Cosmos アカウントにアクセスする際の認証に使用できる 2 つのプライマリ アクセス キーが生成されます。 また、読み取り専用操作を認証するための読み取り専用キーも生成されます。
アクセス キーが 2 つ提供されるため、Azure Cosmos アカウントを中断することなく、キーの再生成とローテーションを 1 つずつ行うことができます。
Cosmos DB アカウントには、2 つの読み取り/書き込みキー (プライマリおよびセカンダリ) と、2 つの読み取り専用キー (プライマリおよびセカンダリ) が存在します。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a> 接続文字列の一覧表示

次のコマンドは、Cosmos DB アカウントにアプリを接続するための接続文字列を取得します。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a> アカウント キーを再生成する

Azure Cosmos アカウントへのアクセス キーは、接続を安全に保つために定期的に再生成する必要があります。 アカウントにはプライマリ アクセス キーとセカンダリ アクセス キーが割り当てられます。 これにより、キーを 1 つずつ再生成し、その間も、クライアントはアクセスを維持できます。
Azure Cosmos アカウントには、4 種類のキー (Primary、Secondary、PrimaryReadonly、および SecondaryReadonly) があります。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a>自動フェールオーバーを有効にする

次のコマンドは、プライマリ リージョンが使用できなくなった場合に自動的にそのセカンダリ リージョンにフェールオーバーするように Cosmos DB アカウントを設定します。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-region writes - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a> フェールオーバー優先度を変更する

自動フェールオーバーを使用して構成されたアカウントでは、プライマリが利用不可になった場合に、Cosmos がどのような順序でセカンダリ レプリカをプライマリに昇格するかを変更することができます。

以下の例では、現在のフェールオーバー優先度が `West US 2 = 0`、`East US 2 = 1`、`South Central US = 2` であることを想定しています。 それが、このコマンドによって `West US 2 = 0`、`South Central US = 1`、`East US 2 = 2` に変更されます。

> [!CAUTION]
> `failoverPriority=0` の場所を変更すると、Azure Cosmos アカウントの手動フェールオーバーがトリガーされます。 他の優先度を変更しても、フェールオーバーはトリガーされません。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a> 手動フェールオーバーをトリガーする

手動フェールオーバーを使用して構成されたアカウントでは、`failoverPriority=0` に変更することでフェールオーバーを実行し、任意のセカンダリ レプリカをプライマリに昇格することができます。 この操作は、ディザスター リカバリーの訓練を開始し、ディザスター リカバリー計画をテストする際に使用できます。

以下の例では、アカウントの現在のフェールオーバー優先度が `West US 2 = 0` および `East US 2 = 1` であるものとして、リージョンを反転させます。

> [!CAUTION]
> `failoverPriority=0` に対する `locationName` を変更すると、Azure Cosmos アカウントの手動フェールオーバーがトリガーされます。 他の優先度を変更しても、フェールオーバーはトリガーされません。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="list-resource-locks-on-an-azure-cosmos-db-account"></a><a id="list-account-locks"></a> Azure Cosmos DB アカウントのリソース ロックを一覧表示する

リソース ロックは、データベースやコレクションなどの Azure Cosmos DB リソースに設定できます。 次の例は、Azure Cosmos DB アカウントのすべての Azure リソース ロックを一覧表示する方法を示しています。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceTypeAccount = "Microsoft.DocumentDB/databaseAccounts"
$accountName = "mycosmosaccount"

Get-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceTypeAccount `
    -ResourceName $accountName
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB データベース

以下のセクションでは、Azure Cosmos DB データベースの管理方法について説明します。

* [Azure Cosmos DB データベースを作成する](#create-db)
* [共有スループットで Azure Cosmos DB データベースを作成する](#create-db-ru)
* [Azure Cosmos DB データベースのスループットを取得する](#get-db-ru)
* [データベースのスループットを自動スケーリングに移行する](#migrate-db-ru)
* [アカウント内のすべての Azure Cosmos DB データベースを一覧表示する](#list-db)
* [単一の Azure Cosmos DB データベースを取得する](#get-db)
* [Azure Cosmos DB データベースを削除する](#delete-db)
* [Azure Cosmos DB データベースにリソース ロックを作成して削除を防ぐ](#create-db-lock)
* [Azure Cosmos DB データベースのリソース ロックを削除する](#remove-db-lock)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Azure Cosmos DB データベースを作成する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>共有スループットで Azure Cosmos DB データベースを作成する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Azure Cosmos DB データベースのスループットを取得する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="migrate-database-throughput-to-autoscale"></a><a id="migrate-db-ru"></a>データベースのスループットを自動スケーリングに移行する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Invoke-AzCosmosDBSqlDatabaseThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -ThroughputType Autoscale
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>アカウント内のすべての Azure Cosmos DB データベースを取得する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>単一の Azure Cosmos DB データベースを取得する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Azure Cosmos DB データベースを削除する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-a-resource-lock-on-an-azure-cosmos-db-database-to-prevent-delete"></a><a id="create-db-lock"></a>Azure Cosmos DB データベースにリソース ロックを作成して削除を防ぐ

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-database"></a><a id="remove-db-lock"></a>Azure Cosmos DB データベースのリソース ロックを削除する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB コンテナー

以下のセクションでは、Azure Cosmos DB コンテナーの管理方法について説明します。

* [Azure Cosmos DB コンテナーを作成する](#create-container)
* [自動スケーリングを使用して Azure Cosmos DB コンテナーを作成する](#create-container-autoscale)
* [大きいパーティション キーで Azure Cosmos DB コンテナーを作成する](#create-container-big-pk)
* [Azure Cosmos DB コンテナーのスループットを取得する](#get-container-ru)
* [コンテナーのスループットを自動スケーリングに移行する](#migrate-container-ru)
* [カスタム インデックス作成を使用して Azure Cosmos DB コンテナーを作成する](#create-container-custom-index)
* [インデックス作成を無効にして Azure Cosmos DB コンテナーを作成する](#create-container-no-index)
* [一意のキーと TTL を使用して Azure Cosmos DB コンテナーを作成する](#create-container-unique-key-ttl)
* [競合解決を使用して Azure Cosmos DB コンテナーを作成する](#create-container-lww)
* [データベース内のすべての Azure Cosmos DB コンテナーを一覧表示する](#list-containers)
* [データベース内の 1 つの Azure Cosmos DB コンテナーを取得する](#get-container)
* [Azure Cosmos DB コンテナーを削除する](#delete-container)
* [Azure Cosmos DB コンテナーにリソース ロックを作成して削除を防ぐ](#create-container-lock)
* [Azure Cosmos DB コンテナーのリソース ロックを削除する](#remove-container-lock)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Azure Cosmos DB コンテナーを作成する

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$throughput = 400 #minimum = 400

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -Throughput $throughput
```

### <a name="create-an-azure-cosmos-db-container-with-autoscale"></a><a id="create-container-autoscale"></a>自動スケーリングを使用して Azure Cosmos DB コンテナーを作成する

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and autoscale throughput at 4000 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$autoscaleMaxThroughput = 4000 #minimum = 4000

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -AutoscaleMaxThroughput $autoscaleMaxThroughput
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>大きいパーティション キー サイズで Azure Cosmos DB コンテナーを作成する

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Azure Cosmos DB コンテナーのスループットを取得する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="migrate-container-throughput-to-autoscale"></a><a id="migrate-container-ru"></a>コンテナーのスループットを自動スケーリングに移行する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Invoke-AzCosmosDBSqlContainerThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -ThroughputType Autoscale
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>カスタム インデックス ポリシーを使用して Azure Cosmos DB コンテナーを作成する

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>インデックス作成を無効にして Azure Cosmos DB コンテナーを作成する

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>一意のキー ポリシーと TTL を使用して Azure Cosmos DB コンテナーを作成する

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>競合解決を使用して Azure Cosmos DB コンテナーを作成する

すべての競合を ConflictsFeed に書き込み、別々に処理するには、`-Type "Custom" -Path ""` を渡します。

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

ストアド プロシージャを使用する競合の解決ポリシーを作成するには、`New-AzCosmosDBSqlConflictResolutionPolicy` を呼び出して、パラメーター `-Type` および `-ConflictResolutionProcedure` を渡します。

```azurepowershell-interactive
# Create container with custom conflict resolution policy using a stored procedure
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionSprocName = "mysproc"

$conflictResolutionSproc = "/dbs/$databaseName/colls/$containerName/sprocs/$conflictResolutionSprocName"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type Custom `
    -ConflictResolutionProcedure $conflictResolutionSproc

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```


### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>データベース内のすべての Azure Cosmos DB コンテナーを一覧表示する

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>データベース内の 1 つの Azure Cosmos DB コンテナーを取得する

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Azure Cosmos DB コンテナーを削除する

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```
### <a name="create-a-resource-lock-on-an-azure-cosmos-db-container-to-prevent-delete"></a><a id="create-container-lock"></a>Azure Cosmos DB コンテナーにリソース ロックを作成して削除を防ぐ

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-container"></a><a id="remove-container-lock"></a>Azure Cosmos DB コンテナーのリソース ロックを削除する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="next-steps"></a>次のステップ

* [すべての PowerShell サンプル](powershell-samples.md)
* [Azure Cosmos アカウントの管理方法](how-to-manage-database-account.md)
* [Azure Cosmos DB コンテナーを作成する](how-to-create-container.md)
* [Azure Cosmos DB の有効期限を構成する](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: /powershell/azure/
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: ../azure-resource-manager/management/overview.md#resource-groups
[azure-resource-tags]: ../azure-resource-manager/management/tag-resources.md
[rp-rest-api]: /rest/api/cosmos-db-resource-provider/