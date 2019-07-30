---
title: PowerShell を使用して Azure Cosmos DB を作成および管理する
description: Azure PowerShell を使用して Azure Cosmos DB アカウント、データベース、コンテナー、およびスループットを管理します。
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/09/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: b61c7bbc06d8d265e5dd5dddd31aceadce1f623b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797057"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>PowerShell を使用して Azure Cosmos DB SQL API リソースを管理する

以下のガイドでは、PowerShell を使用して、アカウント、データベース、コンテナー、スループットなど、Azure Cosmos DB リソースの管理をスクリプト処理および自動化する方法について説明します。 Azure Cosmos DB の管理は、AzResource コマンドレットを使って Azure Cosmos DB のリソース プロバイダーに対して直接行います。 Azure Cosmos DB リソース プロバイダーに対して PowerShell を使用して管理できるすべてのプロパティを確認するには、[Azure Cosmos DB リソース プロバイダーのスキーマ](/azure/templates/microsoft.documentdb/allversions)に関する記事を参照してください。

Azure Cosmos DB のクロスプラットフォーム管理には、[Azure CLI](manage-with-cli.md)、[REST API][rp-rest-api]、または [Azure portal](create-sql-api-dotnet.md#create-account) を使うことができます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Getting Started (概要)

[Azure PowerShell のインストールおよび構成方法][powershell-install-configure]に関する記事の指示に従ってインストールし、PowerShell で Azure アカウントにサインインします。

* 次のコマンドをユーザーの確認を要求せずに実行する場合は、`-Force` フラグをコマンドに追加します。
* 次のコマンドはすべて同期コマンドです。

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

### <a id="create-account"></a> Azure Cosmos アカウントを作成する

このコマンドでは、[複数リージョン][distribute-data-globally]および有界整合性制約の[一貫性ポリシー](consistency-levels.md)を使って、Azure Cosmos DB データベース アカウントが作成されます。

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` Azure Cosmos アカウントの名前。 小文字にする必要があります。英数字と "-" 文字を使用でき、長さは 3 から 31 文字です。
* `$location` Azure Cosmos アカウント リソースの場所。
* `$locations` データベース アカウントのレプリカ リージョン。 各データベース アカウントの書き込みリージョンは 1 つのみであり、フェールオーバー優先度の値は 0 です。
* `$consistencyPolicy` Azure Cosmos アカウントの既定の一貫性レベル。 詳細については、[Azure Cosmos DB の一貫性レベル](consistency-levels.md)に関する記事をご覧ください。
* `$CosmosDBProperties` アカウントをプロビジョニングするために Cosmos DB Azure Resource Manager プロバイダーに渡されるプロパティ値。

Azure Cosmos アカウントは、IP ファイアウォールと仮想ネットワーク サービス エンドポイントで構成できます。 Azure Cosmos DB 用に IP ファイアウォールを構成する方法については、[IP ファイアウォールの構成](how-to-configure-firewall.md)に関する記事を参照してください。  Azure Cosmos DB のサービス エンドポイントを有効にする方法について詳しくは、「[仮想ネットワークからのアクセスの構成](how-to-configure-vnet-service-endpoint.md)」をご覧ください。

### <a id="list-accounts"></a> サブスクリプション内のすべての Azure Cosmos アカウントの一覧を取得する

このコマンドでは、サブスクリプション内のすべての Azure Cosmos アカウントの一覧を取得できます。

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a id="get-account"></a> Azure Cosmos アカウントのプロパティを取得する

このコマンドを使用すると、既存の Azure Cosmos アカウントのプロパティを取得できます。

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Azure Cosmos アカウントを更新する

このコマンドでは、Azure Cosmos DB データベース アカウントのプロパティを更新できます。 更新できるプロパティは次のとおりです。

* 領域を追加または削除する
* 既定の一貫性ポリシーを変更する
* フェールオーバー ポリシーを変更する
* IP 範囲フィルターを変更する
* 仮想ネットワーク構成を変更する
* マルチマスターを有効にする

> [!NOTE]
> このコマンドでは、リージョンの追加および削除が可能ですが、フェールオーバー優先度を変更することはできません。 フェールオーバー優先度を変更するには、[Azure Cosmos アカウントのフェールオーバー優先度の変更](#modify-failover-priority)を参照してください。

```azurepowershell-interactive
# Update an Azure Cosmos Account and set Consistency level to Session

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$account.Properties.consistencyPolicy = $consistencyPolicy
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> Azure Cosmos アカウントを削除する

このコマンドを使用すると、既存の Azure Cosmos アカウントを削除できます。

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> Azure Cosmos アカウントのタグを更新する

次の例では、Azure Cosmos アカウントに [Azure リソース タグ][azure-resource-tags]を設定する方法について説明します。

> [!NOTE]
> このコマンドは、対応するパラメーターと `-Tags` フラグを追加することで、作成または更新のコマンドと組み合わせることができます。

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a> アカウント キーの一覧表示

Azure Cosmos DB アカウントを作成すると、2 つのマスター アクセス キーが生成されます。これらのアクセス キーは、Azure Cosmos DB アカウントにアクセスする際の認証に使用できます。 2 つのアクセス キーが提供されるので、Azure Cosmos DB アカウントを中断することなくキーを再生成できます。 読み取り専用操作を認証するための読み取り専用キーも使用できます。 2 つの読み取り/書き込みキー (プライマリおよびセカンダリ) と、2 つの読み取り専用キー (プライマリおよびセカンダリ) が存在します。

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a> 接続文字列の一覧表示

MongoDB アカウントの場合、MongoDB アプリをデータベース アカウントに接続する接続文字列は、次のコマンドで取得できます。

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a> アカウント キーを再生成する

Azure Cosmos アカウントへのアクセス キーは、接続をより安全に保つために定期的に再生成する必要があります。 アカウントにはプライマリ アクセス キーとセカンダリ アクセス キーが割り当てられます。 これにより、クライアントは他の再生成中もアクセスを維持できます。 Azure Cosmos アカウントには、4 種類のキー (Primary、Secondary、PrimaryReadonly、および SecondaryReadonly) があります。

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="modify-failover-priority"></a> フェールオーバー優先度を変更する

複数リージョンのデータベース アカウントでは、プライマリ書き込みレプリカでリージョン間フェールオーバーを行う必要があるときに、Cosmos アカウントでセカンダリ読み取りレプリカを昇格させる順序を変更できます。 `failoverPriority=0` のリージョンを変更するときは、このコマンドを使ってディザスター リカバリーの訓練を開始し、ディザスター リカバリー計画をテストすることもできます。

以下の例では、アカウントの現在のフェールオーバー優先度が westus=0 および eastus=1 であるものとして、リージョンを反転させます。

> [!CAUTION]
> `failoverPriority=0` に対する `locationName` を変更すると、Azure Cosmos アカウントの手動フェールオーバーがトリガーされます。 他の優先度を変更しても、フェールオーバーはトリガーされません。

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverPolicies = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Azure Cosmos データベース

以下のセクションでは、Azure Cosmos データベースの管理方法について説明します。

* [Azure Cosmos データベースを作成する](#create-db)
* [共有スループットで Azure Cosmos データベースを作成する](#create-db-ru)
* [Azure Cosmos データベースのスループットを取得する](#get-db-ru)
* [アカウント内のすべての Azure Cosmos データベースを一覧表示する](#list-db)
* [単一の Azure Cosmos データベースを取得する](#get-db)
* [Azure Cosmos データベースを削除する](#delete-db)

### <a id="create-db"></a>Azure Cosmos データベースを作成する

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>共有スループットで Azure Cosmos データベースを作成する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-db-ru"></a>Azure Cosmos データベースのスループットを取得する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a id="list-db"></a>アカウント内のすべての Azure Cosmos データベースを取得する

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>単一の Azure Cosmos データベースを取得する

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>Azure Cosmos データベースを削除する

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos コンテナー

以下のセクションでは、Azure Cosmos コンテナーの管理方法について説明します。

* [Azure Cosmos コンテナーを作成する](#create-container)
* [大きいパーティション キーで Azure Cosmos コンテナーを作成する](#create-container-big-pk)
* [Azure Cosmos コンテナーのスループットを取得する](#get-container-ru)
* [共有スループットを使用して Azure Cosmos コンテナーを作成する](#create-container-ru)
* [カスタム インデックス作成を使用して Azure Cosmos コンテナーを作成する](#create-container-custom-index)
* [インデックス作成を無効にして Azure Cosmos コンテナーを作成する](#create-container-no-index)
* [一意のキーと TTL を使用して Azure Cosmos コンテナーを作成する](#create-container-unique-key-ttl)
* [競合解決を使用して Azure Cosmos コンテナーを作成する](#create-container-lww)
* [データベース内のすべての Azure Cosmos コンテナーを一覧表示する](#list-containers)
* [データベース内の 1 つの Azure Cosmos コンテナーを取得する](#get-container)
* [Azure Cosmos コンテナーを削除する](#delete-container)

### <a id="create-container"></a>Azure Cosmos コンテナーを作成する

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
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
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-big-pk"></a>大きいパーティション キー サイズで Azure Cosmos コンテナーを作成する

```azurepowershell-interactive
# Create an Azure Cosmos container with a large partition key value (version = 2)
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
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="get-container-ru"></a>Azure Cosmos コンテナーのスループットを取得する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a id="create-container-ru"></a>共有スループットを使用して Azure Cosmos コンテナーを作成する

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>カスタム インデックス ポリシーを使用して Azure Cosmos コンテナーを作成する

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>インデックス作成を無効にして Azure Cosmos コンテナーを作成する

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>一意のキー ポリシーと TTL を使用して Azure Cosmos コンテナーを作成する

```azurepowershell-interactive
# Create a container with a unique key policy and TTL
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 100;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>競合解決を使用して Azure Cosmos コンテナーを作成する

ストアド プロシージャを使用する競合解決ポリシーを作成するには、`"mode"="custom"` を設定し、ストアド プロシージャの名前として解決パスを設定します (`"conflictResolutionPath"="myResolverStoredProcedure"`)。 すべての競合を ConflictsFeed に書き込み、別々に処理するには、`"mode"="custom"` と `"conflictResolutionPath"=""` を設定します。

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-containers"></a>データベース内のすべての Azure Cosmos コンテナーを一覧表示する

```azurepowershell-interactive
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>データベース内の 1 つの Azure Cosmos コンテナーを取得する

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>Azure Cosmos コンテナーを削除する

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>次の手順

* [すべての PowerShell サンプル](powershell-samples.md)
* [Azure Cosmos アカウントの管理方法](how-to-manage-database-account.md)
* [Azure Cosmos コンテナーを作成する](how-to-create-container.md)
* [Azure Cosmos DB の有効期限を構成する](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
