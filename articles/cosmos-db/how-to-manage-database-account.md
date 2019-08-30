---
title: Azure Cosmos DB でデータベース アカウントを管理する方法について
description: Azure Cosmos DB でデータベース アカウントを管理する方法について
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: db7746bc91935c0385e97d494a45d34819665ced
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093400"
---
# <a name="manage-an-azure-cosmos-account"></a>Azure Cosmos アカウントを管理する

この記事では、Azure portal、Azure PowerShell、Azure CLI、Azure Resource Manager テンプレートを使用し、Azure Cosmos アカウントでさまざまなタスクを管理する方法を紹介します。

## <a name="create-an-account"></a>アカウントの作成

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```azurecli-interactive
# Create an account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case and < 31 characters

az cosmosdb create \
   --name $accountName \
   --resource-group $resourceGroupName \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=WestUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=EastUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations true
```

### <a id="create-database-account-via-ps"></a>Azure PowerShell
```azurepowershell-interactive
# Create an Azure Cosmos account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case and < 31 characters

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
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
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="create-database-account-via-arm-template"></a>Azure Resource Manager テンプレート

この Azure Resource Manager テンプレートでは、2 つのリージョンと、整合性レベル、自動フェールオーバー、およびマルチマスターを選択するオプションで構成された、サポート対象の任意の API に対する Azure Cosmos アカウントが作成されます。 このテンプレートをデプロイするには、[Azure Cosmos アカウントの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account)に関する readme ページで [Deploy to Azure]\(Azure に配置する\) をクリックします。

## <a name="addremove-regions-from-your-database-account"></a>データベース アカウントのリージョンの追加/削除

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. [Azure ポータル](https://portal.azure.com)にサインインします。 

1. お使いの Azure Cosmos アカウントに移動して、 **[データをグローバルにレプリケートする]** メニューを開きます。

1. リージョンを追加するには、目的のリージョンに対応する六角形 ( **+** ラベルが付いたもの) をマップ上で選択します。 別の方法でリージョンを追加するには、 **[+ リージョンの追加]** オプションを選択し、ドロップダウン メニューからリージョンを選択します。

1. リージョンを削除するには、チェック マークの付いた青い六角形をマップ上で選択して、1 つ以上のリージョンを消去します。 または、右側で、リージョンの横にある "ごみ箱" (🗑) アイコンを選択します。

1. 変更を保存するには、 **[OK]** を選択します。

   ![リージョンの追加や削除を行うメニュー](./media/how-to-manage-database-account/add-region.png)

単一リージョン書き込みモードでは、書き込みリージョンを削除できません。 その現在の書き込みリージョンを削除する前に、別のリージョンへのフェールオーバーを行う必要があります。

複数リージョン書き込みモードでは、少なくとも 1 つのリージョンがあれば、どのリージョンも追加または削除できます。

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case and <31 characters

# Create an account with 1 region
az cosmosdb create --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False --locations regionName=EastUS failoverPriority=1 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False
```

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Create an account with 1 region
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case and <31 characters

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( 
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East Us"; "failoverPriority"=1 } 
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="configure-multiple-write-regions"></a>複数の書き込みリージョンの構成

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

**[データをグローバルにレプリケートする]** タブを開き、 **[有効]** を選択して複数リージョンの書き込みを有効にします。 複数リージョンの書き込みを有効にすると、アカウントで現在用意されているすべての読み取りリージョンが読み書きリージョンになります。 

> [!NOTE]
> 複数リージョンの書き込みを有効にした後、無効にすることはできません。 

![Azure Cosmos アカウントのマルチマスター構成のスクリーンショット](./media/how-to-manage-database-account/single-to-multi-master.png)

この機能に関して他に質問がございましたら、askcosmosdb@microsoft.com エイリアスにご連絡ください。 

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'
az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Update an Azure Cosmos account from single to multi-master

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager テンプレート

アカウントと設定 `enableMultipleWriteLocations: true` の作成に使用された Resource Manager テンプレートをデプロイすることで、アカウントをシングルマスターからマルチマスターに移行できます。 次の Azure Resource Manager テンプレートは、2 つのリージョンと複数の書き込み場所を有効にして、SQL API 用の Azure Cosmos アカウントをデプロイする最小限のテンプレートです。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Azure Cosmos アカウントでの自動フェールオーバーの有効化

自動フェールオーバーを選択すると、あるリージョンが利用できなくなったとき、フェールオーバーの優先順位が最も高いリージョンに Azure Cosmos DB がフェールオーバーされます。ユーザー側の操作は必要ありません。 自動フェールオーバーが有効になっているとき、リージョン優先順位を変更できます。 自動フェールオーバーを有効にするには、アカウントに 2 つ以上のリージョンを用意する必要があります。

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. お使いの Azure Cosmos アカウントで、 **[データをグローバルにレプリケートする]** ウィンドウを開きます。

2. ウィンドウの上部で、 **[自動フェールオーバー]** を選択します。

   ![[データをグローバルにレプリケートする] メニュー](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **[自動フェールオーバー]** ウィンドウで、 **[自動フェールオーバーの有効化]** を **[ON]** に設定してください。 

4. **[保存]** を選択します。

   ![自動フェールオーバーのポータル メニュー](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```azurecli-interactive
# Enable automatic failover on an existing account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-automatic-failover true
```

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Azure Cosmos アカウントでのフェールオーバーの優先度の設定

Cosmos アカウントに自動フェールオーバーを構成した後、リージョンのフェールオーバーの優先順位を変更できます。

> [!IMPORTANT]
> アカウントに自動フェールオーバーを構成しているとき、書き込みリージョン (フェールオーバーの優先順位がゼロ) は変更できません。 書き込みリージョンを変更するには、自動フェールオーバーを無効にし、手動フェールオーバーを実行する必要があります。

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. お使いの Azure Cosmos アカウントで、 **[データをグローバルにレプリケートする]** ウィンドウを開きます。

2. ウィンドウの上部で、 **[自動フェールオーバー]** を選択します。

   ![[データをグローバルにレプリケートする] メニュー](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **[自動フェールオーバー]** ウィンドウで、 **[自動フェールオーバーの有効化]** を **[ON]** に設定してください。

4. フェールオーバーの優先度を変更するには、カーソルを置くと行の左側に表示される 3 つのドットを使い、読み込みリージョンをドラッグします。

5. **[保存]** を選択します。

   ![自動フェールオーバーのポータル メニュー](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```azurecli-interactive
# Assume region order is initially eastus=0 westus=1 southeastasia=2 on account creation
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb failover-priority-change --name $accountName --resource-group $resourceGroupName --failover-policies eastus=0 southeastasia=1 westus=2
```

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1, Southeast Asia = 2
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$failoverPolicies = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="Southeast Asia"; "failoverPriority"=1 },
    @{ "locationName"="East US"; "failoverPriority"=2 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a id="manual-failover"></a>Azure Cosmos アカウントで手動フェールオーバーを実行する

> [!IMPORTANT]
> この操作を成功させるには、Azure Cosmos アカウントに手動フェールオーバーを構成する必要があります。

手動フェールオーバーを実行するプロセスには、アカウントの書き込みリージョン (フェールオーバーの優先順位 = 0) をそのアカウントに構成されている別のリージョンに変更することが含まれます。

> [!NOTE]
> マルチマスター アカウントは手動でフェールオーバーできません。 Azure Cosmos SDK を使用するアプリケーションの場合、SDK はリージョンが利用できなくなったときにそれを検出し、SDK でマルチホーミング API を使用している場合、最も近くにあるリージョンに自動的にリダイレクトします。

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. お使いの Azure Cosmos アカウントに移動して、 **[データをグローバルにレプリケートする]** メニューを開きます。

2. メニューの上部で、 **[手動フェールオーバー]** を選択します。

   ![[データをグローバルにレプリケートする] メニュー](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **[手動フェールオーバー]** メニューで、新しい書き込みリージョンを選択します。 チェック ボックスをオンにして、このオプションによって書き込みリージョンが変更されることを理解していることを示します。

4. フェールオーバーをトリガーするには、 **[OK]** を選択します。

   ![手動フェールオーバーのポータル メニュー](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```azurecli-interactive
# Assume account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations regionName=westus failoverPriority=0 isZoneRedundant=False --locations regionName=eastus failoverPriority=1 isZoneRedundant=False
```

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

$account.Properties.locations=$locations;
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="next-steps"></a>次の手順

Azure Cosmos アカウント、データベース、コンテナーの管理方法に関する詳細と例については、次の記事をお読みください。

* [Azure PowerShell を使用した Azure Cosmos DB の管理](manage-with-powershell.md)
* [Azure CLI を使用した Azure Cosmos DB の管理](manage-with-cli.md)
