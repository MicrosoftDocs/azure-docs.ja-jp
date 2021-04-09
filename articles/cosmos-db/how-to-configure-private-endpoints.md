---
title: Azure Cosmos アカウントの Azure Private Link を構成する
description: 仮想ネットワークのプライベート IP アドレスを使用して Azure Cosmos アカウントにアクセスするように Azure Private Link を設定する方法について説明します。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thweiss
ms.custom: devx-track-azurecli
ms.openlocfilehash: d21943c90e1f77bd4a43cdfd27b183df018f6cc7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690670"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Azure Cosmos アカウントの Azure Private Link を構成する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Private Link を使用すると、プライベート エンドポイント経由で Azure Cosmos アカウントに接続できます。 プライベート エンドポイントは、仮想ネットワークのサブネットにある一組のプライベート IP アドレスです。 これで、プライベート IP アドレスを使用して Azure Cosmos アカウントへのアクセスを制限できるようになります。 Private Link を制限付き NSG ポリシーと結合することで、データ流出のリスクを軽減することができます。 プライベート エンドポイントの詳細については、[Azure Private Link](../private-link/private-link-overview.md) に関する記事を参照してください。

> [!NOTE]
> Azure Cosmos エンドポイントがパブリック DNS で解決されることが Private Link によって止められることはありません。 受信した要求のフィルタリングは、トランスポート レベルでもネットワーク レベルでもなく、アプリケーション レベルで行われます。

Private Link を使用すると、ユーザーは、仮想ネットワーク内から、またはピアリングされた任意の仮想ネットワークから Azure Cosmos アカウントにアクセスできます。 Private Link にマップされたリソースは、プライベート ピアリングを使用して、VPN または Azure ExpressRoute 経由でオンプレミスからアクセスすることもできます。

自動または手動の承認方法により、Private Link を使用して構成された Azure Cosmos アカウントに接続できます。 詳細については、Private Link のドキュメントの[承認ワークフロー](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow)に関するセクションを参照してください。

この記事では、Azure Cosmos DB トランザクション ストアのプライベート エンドポイントをセットアップする方法について説明します。 自動承認方法を使用していることを前提としています。 分析ストアを使用している場合は、[分析ストアのプライベート エンドポイント](analytical-store-private-endpoints.md)に関する記事を参照してください。

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Azure portal を使用してプライベート エンドポイントを作成する

Azure portal を使用して、次の手順で既存の Azure Cosmos アカウントのプライベート エンドポイントを作成します。

1. **[すべてのリソースの]** ペインで、Azure Cosmos アカウントを選択します。

1. 設定の一覧から **[プライベート エンドポイント接続]** を選択し、 **[プライベート エンドポイント]** を選択します。

   :::image type="content" source="./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png" alt-text="Azure portal でプライベート エンドポイントを作成するための選択":::

1. **[プライベート エンドポイントの作成 - 基本]** ペインで次の詳細を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | サブスクリプションを選択します。 |
    | Resource group | リソース グループを選択します。|
    | **インスタンスの詳細** |  |
    | 名前 | プライベート エンドポイントの名前を入力します。 この名前が使用済みの場合は、一意の名前を作成します。 |
    |リージョン| Private Link をデプロイするリージョンを選択します。 仮想ネットワークが存在する場所と同じ場所にプライベート エンドポイントを作成します。|
    |||
1. **[Next:リソース]** を選択します。
1. **[プライベート エンドポイントの作成 - リソース]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    |接続方法  | **[マイ ディレクトリ内の Azure リソースに接続します]** を選択します。 <br/><br/> これで、いずれかのリソースを選択して Private Link を設定できるようになります。 または、他のユーザーがあなたと共有したリソース ID またはエイリアスを使用して、そのユーザーのリソースに接続できます。|
    | サブスクリプション| サブスクリプションを選択します。 |
    | リソースの種類 | **AzureCosmosDB/databaseAccounts** を選択します。 |
    | リソース |自分の Azure Cosmos アカウントを選択します。 |
    |ターゲット サブリソース |マップする Azure Cosmos DB API の種類を選択します。 既定では SQL、MongoDB、Cassandra API では、1 つしか選択できません。 Gremlin と Table API のような API は SQL API と相互運用可能であるため、**SQL** を選択することもできます。 |
    |||

1. **[Next:構成]** を選択します。
1. **[Create a private endpoint - Configuration]/(プライベート エンドポイントの作成 - 構成/)** で次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    |**ネットワーク**| |
    | 仮想ネットワーク| 仮想ネットワークを選択します。 |
    | Subnet | サブネットを選択します。 |
    |**プライベート DNS の統合**||
    |プライベート DNS ゾーンとの統合 |**[はい]** を選択します。 <br><br/> プライベート エンドポイントに非公開で接続するには、DNS レコードが必要です。 プライベート エンドポイントとプライベート DNS ゾーンを統合することをお勧めします。 また、独自の DNS サーバーを使用したり、仮想マシン上のホスト ファイルを使用して DNS レコードを作成したりすることもできます。 |
    |プライベート DNS ゾーン |**privatelink.documents.azure.com** を選択します。 <br><br/> プライベート DNS ゾーンは自動的に決定されます。 Azure portal を使用して変更することはできません。|
    |||

1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認と作成]** ページで、Azure によって構成が検証されます。
1. "**証に成功しました**" というメッセージが表示されたら、 **[作成]** を選択します。

Azure Cosmos アカウントの Private Link を承認すると、Azure portal の **[ファイアウォールと仮想ネットワーク]** ペインにある **[すべてのネットワーク]** オプションは使用できなくなります。

次の表は、さまざまな Azure Cosmos アカウント API の種類、サポートされているサブ リソース、および対応するプライベート ゾーン名間のマッピングを示しています。 SQL API を使用して Gremlin および Table API アカウントにアクセスすることもできるため、これらの API には 2 つのエントリがあります。

|Azure Cosmos アカウント API の種類  |サポートされているサブリソース (またはグループ ID) |プライベート ゾーン名  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|テーブル    |    テーブル     |   privatelink.table.cosmos.azure.com    |
|テーブル     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>プライベート IP アドレスを取り込む

プライベート エンドポイントがプロビジョニングされたら、IP アドレスにクエリを実行できます。 Azure portal から IP アドレスを表示するには、以下の手順に従います。

1. **[すべてのリソース]** を選択します。
1. 先ほど作成したプライベート エンドポイントを検索します。 この例では **cdbPrivateEndpoint3** です。
1. **[概要]** タブを選択して、DNS の設定と IP アドレスを表示します。

:::image type="content" source="./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png" alt-text="Azure portal のプライベート IP アドレス":::

プライベート エンドポイントごとに複数の IP アドレスが作成されます。

* 1 つは、Azure Cosmos アカウントのグローバルな (リージョンに依存しない) エンドポイント用です。
* もう 1 つは、Azure Cosmos アカウントがデプロイされているリージョン用です。

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Azure PowerShell を使用してプライベート エンドポイントを作成する

次の PowerShell スクリプトを実行して、既存の Azure Cosmos アカウントに "MyPrivateEndpoint" という名前のプライベート エンドポイントを作成します。 変数の値を実際の環境の詳細に置き換えます。

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>プライベート エンドポイントとプライベート DNS ゾーンを統合する

プライベート エンドポイントを作成した後は、次の PowerShell スクリプトを使用して、プライベート DNS ゾーンと統合できます。

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>プライベート IP アドレスを取り込む

プライベート エンドポイントがプロビジョニングされたら、次の PowerShell スクリプトを使用して、IP アドレスと FQDN のマッピングにクエリを実行できます。

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Azure CLI を使用してプライベート エンドポイントを作成する

次の Azure CLI スクリプトを実行して、既存の Azure Cosmos アカウントに "myPrivateEndpoint" という名前のプライベート エンドポイントを作成します。 変数の値を実際の環境の詳細に置き換えます。

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>プライベート エンドポイントとプライベート DNS ゾーンを統合する

プライベート エンドポイントを作成した後は、次の Azure CLI スクリプトを使用して、プライベート DNS ゾーンと統合できます。

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用してプライベート エンドポイントを作成する

Private Link を設定するには、仮想ネットワーク サブネットにプライベート エンドポイントを作成します。 これを実現するには、Azure Resource Manager テンプレートを使用します。

次のコードを使用して、"PrivateEndpoint_template.json" という名前の Resource Manager テンプレートを作成します。 このテンプレートは、既存の仮想ネットワークにある既存の Azure Cosmos SQL API アカウントのプライベート エンドポイントを作成します。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**テンプレートのパラメーター ファイルを定義する**

テンプレートのパラメーター ファイルを作成し、"PrivateEndpoint_parameters.json" という名前を付けます。 次のコードをパラメーター ファイルに追加します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**PowerShell スクリプトを使用してテンプレートをデプロイする**

次のコードを使用して、PowerShell スクリプトを作成します。 スクリプトを実行する前に、サブスクリプション ID、リソース グループ名、その他の変数値を実際の環境の詳細に置き換えます。

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

PowerShell スクリプトでは、`GroupId` 変数に 1 つの値のみを含めることができます。 この値は、アカウントの API の種類です。 使用できる値は、`Sql`、`MongoDB`、`Cassandra`、`Gremlin`、`Table` です。 一部の Azure Cosmos アカウントの種類は、複数の API を使用してアクセスできます。 次に例を示します。

* Gremlin API アカウントは、Gremlin アカウントおよび SQL API アカウントの両方からアクセスできます。
* Table API アカウントは、Table アカウントおよび SQL API アカウントの両方からアクセスできます。

これらのアカウントについては、API の種類ごとに 1 つのプライベート エンドポイントを作成する必要があります。 対応する API の種類は `GroupId` 配列に指定されています。

テンプレートが正常にデプロイされると、次の図に示すような出力が表示されます。 プライベート エンドポイントが正しく設定されている場合、`provisioningState` 値は `Succeeded` です。

:::image type="content" source="./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png" alt-text="Resource Manager テンプレートのデプロイの出力":::

テンプレートがデプロイされると、プライベート IP アドレスがサブネット内で予約されます。 Azure Cosmos アカウントのファイアウォール規則は、プライベート エンドポイントからの接続のみを受け入れるように構成されています。

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>プライベート エンドポイントとプライベート DNS ゾーンを統合する

次のコードを使用して、"PrivateZone_template.json" という名前の Resource Manager テンプレートを作成します。 このテンプレートは、既存の仮想ネットワークにある既存の Azure Cosmos SQL API アカウントのプライベート DNS ゾーンを作成します。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

次のコードを使用して、"PrivateZoneRecords_template.json" という名前の Resource Manager テンプレートを作成します。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**テンプレートのパラメーター ファイルを定義する**

次の 2 つのパラメーター ファイルをテンプレートに作成します。 "PrivateZone_parameters" を作成します。 を、以下のコードに置き換えます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

"PrivateZoneRecords_parameters.json" を作成します。 を、以下のコードに置き換えます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**PowerShell スクリプトを使用してテンプレートをデプロイする**

次のコードを使用して、PowerShell スクリプトを作成します。 スクリプトを実行する前に、サブスクリプション ID、リソース グループ名、その他の変数値を実際の環境の詳細に置き換えます。

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>カスタム DNS の構成

プライベート DNS ゾーンは、プライベート エンドポイントを作成したサブネット内で使用する必要があります。 各プライベート IP アドレスが DNS エントリにマップされるようにエンドポイントを構成します (前述の応答の `fqdns` プロパティを参照してください)。

プライベート エンドポイントを作成するときに、Azure のプライベート DNS ゾーンと統合できます。 代わりにカスタム DNS ゾーンを使用する場合は、プライベート エンドポイント用に予約されているすべてのプライベート IP アドレスの DNS レコードを追加するように構成する必要があります。

> [!IMPORTANT]
> 要求の DNS 解決により、これらの要求がプライベート エンドポイントを経由するか、標準のパブリック ルートを利用するかが決まります。 プライベート エンドポイントによってマップされたプライベート IP アドレスをローカル DNS が正しく参照するようにしてください。

## <a name="private-link-combined-with-firewall-rules"></a>Private Link とファイアウォール規則の組み合わせ

Private Link とファイアウォール規則を組み合わせて使用すると、次のような状況と結果になる可能性があります。

* ファイアウォール規則を構成していない場合、既定では、すべてのトラフィックが Azure Cosmos アカウントにアクセスできます。

* パブリック トラフィックまたはサービス エンドポイントを構成し、プライベート エンドポイントを作成する場合、さまざまな種類の受信トラフィックが、対応する種類のファイアウォール規則によって承認されます。 プライベート エンドポイントが、サービス エンドポイントも構成されているサブネットで構成されている場合は、次のようになります。
  * プライベート エンドポイントによってマップされたデータベース アカウントへのトラフィックは、プライベート エンドポイントを介してルーティングされます。
  * サブネットからの他のデータベース アカウントへのトラフィックは、サービス エンドポイントを介してルーティングされます。

* パブリック トラフィックまたはサービス エンドポイントを構成せずにプライベート エンドポイントを作成する場合、Azure Cosmos アカウントにはプライベート エンドポイントからのみアクセスできます。 パブリック トラフィックまたはサービス エンドポイントを構成しない場合、すべての承認済みプライベート エンドポイントが拒否または削除されると、PublicNetworkAccess が Disabled に設定されていない限り、アカウントはネットワーク全体に対して開かれます (以下のセクションを参照してください)。

## <a name="blocking-public-network-access-during-account-creation"></a>アカウントの作成中のパブリック ネットワーク アクセスのブロック

前のセクションで説明されているように、特定のファイアウォール規則が設定されていない限り、プライベート エンドポイントを追加すると、Azure Cosmos アカウントはそのプライベート エンドポイント経由でのみアクセス可能になります。 つまり、パブリック トラフィックから Azure Cosmos アカウントに到達できるのは、それが作成されてから、プライベート エンドポイントが追加されるまでの間になります。 プライベート エンドポイントの作成の前であってもパブリック ネットワーク アクセスが確実に無効になるようにするには、アカウントの作成中に `publicNetworkAccess` フラグを `Disabled` に設定することができます。 このフラグはあらゆる IP または仮想ネットワークの規則より優先されることにご留意ください。このフラグが `Disabled` に設定されているとき、ファイアウォール構成でソース IP または仮想ネットワークが許可される場合でも、パブリックおよび仮想のネットワーク トラフィックはすべてブロックされます。

このフラグの使用方法を示す例については、[この Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/)を参照してください。

## <a name="adding-private-endpoints-to-an-existing-cosmos-account-with-no-downtime"></a>ダウンタイムが発生しないように既存の Cosmos アカウントにプライベート エンドポイントを追加する

既定では、プライベート エンドポイントを既存のアカウントに追加すると、約 5 分という短いダウンタイムが発生します。 このダウンタイムを回避するには、以下の指示に従います。

1. IP または仮想ネットワークの規則をファイアウォール構成に追加し、クライアント接続を明示的に許可します。
1. 構成が確実に更新されるよう、10 分間待ちます。
1. 新しいプライベート エンドポイントを構成します。
1. 手順 1 で設定したファイアウォール規則を削除します。

## <a name="port-range-when-using-direct-mode"></a>直接モードを使用する場合のポートの範囲

直接モード接続を介して Azure Cosmos アカウントで Private Link を使用している場合は、TCP ポートの全範囲 (0-65535) が開いていることを確認する必要があります。

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>リージョンの追加または削除時にプライベート エンドポイントを更新する

プライベート DNS ゾーン グループを使用していない限り、Azure Cosmos アカウントにリージョンを追加または削除する場合、そのアカウントの DNS エントリを追加または削除する必要があります。 リージョンが追加または削除された後で、サブネットのプライベート DNS ゾーンを更新し、追加または削除された DNS エントリとそれに対応するプライベート IP アドレスを反映できるようになります。

たとえば、次の 3 つのリージョンに Azure Cosmos アカウントをデプロイするとします。"米国西部"、"米国中部"、および "西ヨーロッパ"。 アカウントのプライベート エンドポイントを作成すると、4 つのプライベート IP がサブネットで予約されます。 3 つのリージョンのそれぞれに 1 つの IP があり、グローバル/リージョンに依存しないエンドポイントに 1 つの IP があります。

Azure Cosmos アカウントに新しいリージョン ("米国東部" など) を追加する場合は、後で行います。 新しいリージョンを追加した後で、プライベート DNS ゾーンかカスタム DNS のどちらかに、対応する DNS レコードを追加する必要があります。

リージョンを削除する際も同じステップを使用できます。 新しいリージョンを削除した後で、プライベート DNS ゾーンかカスタム DNS のどちらかから、対応する DNS レコードを削除する必要があります。

## <a name="current-limitations"></a>現在の制限

Azure Cosmos アカウントで Private Link を使用する場合は、次の制限事項が適用されます。

* 1 つの Azure Cosmos アカウントに 200 を超えるプライベート エンドポイントを設定することはできません。

* ダイレクト モード接続を介して Azure Cosmos アカウントで Private Link を使用している場合は、TCP プロトコルのみを使用できます。 HTTP プロトコルは現在サポートされていません。

* MongoDB アカウント用の Azure Cosmos DB の API を使用する場合、プライベート エンドポイントは、サーバーのバージョンが 3.6 のアカウントでのみサポートされます (つまり、`*.mongo.cosmos.azure.com` 形式でエンドポイントを使用するアカウント)。 サーバーのバージョンが 3.2 のアカウント (`*.documents.azure.com` の形式でエンドポイントを使用するアカウント) では、Private Link はサポートされていません。 Private Link を使用するには、古いアカウントを新しいバージョンに移行する必要があります。

* ご使用の Azure Cosmos DB の MongoDB 用 API アカウントに Private Link が存在する場合、適切に接続するためには、ツールまたはライブラリが SNI (Service Name Identification) をサポートするか、接続文字列から `appName` パラメーターを渡す必要があります。 一部の古いツールまたはライブラリは、Private Link 機能の使用と互換性がない場合があります。

* 自動承認されるプライベート エンドポイントを作成するには、Azure Cosmos アカウント スコープで少なくとも `Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action` アクセス許可がネットワーク管理者に付与されている必要があります。

### <a name="limitations-to-private-dns-zone-integration"></a>プライベート DNS ゾーンの統合に関する制限事項

プライベート DNS ゾーン グループを使用していない限り、プライベート DNS ゾーンの DNS レコードは、プライベート エンドポイントを削除しても、Azure Cosmos アカウントのリージョンを削除しても、自動的には削除されません。 以下の操作の前に、DNS レコードを手動で削除する必要があります。

* このプライベート DNS ゾーンにリンクされる新しいプライベート エンドポイントを追加する。
* このプライベート DNS ゾーンにリンクされるプライベート エンドポイントがあるすべてのデータベース アカウントに新しいリージョンを追加する。

DNS レコードをクリーンアップしないと、予期しないデータ プレーンの問題が発生する可能性があります。 こうした問題には、プライベート エンドポイントの削除、リージョンの削除後に追加されたリージョンへのデータ停止などがあります。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB のセキュリティ機能の詳細については、次の記事を参照してください。

* Azure Cosmos DB 用のファイアウォールを構成するには、[ファイアウォールのサポート](how-to-configure-firewall.md)を参照してください。

* Azure Cosmos アカウントの仮想ネットワーク サービス エンドポイントを構成する方法については、「[仮想ネットワークからのアクセスの構成](how-to-configure-vnet-service-endpoint.md)」を参照してください。

* Private Link の詳細については、[Azure Private Link](../private-link/private-link-overview.md) に関するドキュメントを参照してください。