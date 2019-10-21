---
title: Azure Cosmos DB アカウント用の仮想ネットワークとサブネット ベースのアクセスを構成する
description: このドキュメントでは、Azure Cosmos DB の仮想ネットワーク サービス エンドポイントのセットアップに必要な手順について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 1c81045408a948820c8b9fef56e2c7d69cd39e08
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811912"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>仮想ネットワーク (VNet) からのアクセスの構成

Azure Cosmos DB アカウントを構成して、Azure 仮想ネットワークの特定のサブネットのみからアクセスを許可するよう構成できます。 Azure Cosmos DB アカウントへの仮想ネットワーク内のサブネットからの接続をアクセス制限するには、次を実行します。

1. サブネットが、Azure Cosmos DB にサブネットと仮想ネットワークの ID を送信できるようにします。 これは、特定のサブネット上で Azure Cosmos DB 用のサービス エンドポイントを有効にすることで実現できます。

1. Azure Cosmos DB アカウントにアクセスできるソースとしてサブネットを指定する規則を Azure Cosmos DB アカウントに追加します。

> [!NOTE]
> ご使用の Azure Cosmos DB アカウント用のサービス エンドポイントがサブネット上で有効になると、Azure Cosmos DB に到達するトラフィックのソースが、パブリック IP から、仮想ネットワークとサブネットに切り替わります。 このトラフィックの切り替えは、このサブネットからアクセスされるすべての Azure Cosmos DB アカウントに適用されます。 このサブネットを許可する IP ベースのファイアウォールがご使用の Azure Cosmos DB アカウントにある場合、サービスで有効なサブネットからの要求は IP ファイアウォール規則と一致しなくなり、要求は拒否されるようになります。
>
> 詳細については、この記事の「[IP ファイアウォール規則から VNET ACL への移行](#migrate-from-firewall-to-vnet)」セクションの手順を参照してください。

以下のセクションでは、Azure Cosmos DB アカウント用の仮想ネットワーク サービス エンドポイントを構成する方法について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a id="configure-using-portal"></a>Azure portal を使用してサービス エンドポイントを構成する

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>既存の Azure 仮想ネットワークとサブネット用のサービス エンドポイントを構成する

1. **[すべてのリソース]** ブレードで、セキュリティで保護する Azure Cosmos DB アカウントを見つけます。

1. 設定メニューの **[ファイアウォールと仮想ネットワーク]** を選択し、 **[選択されたネットワーク]** からアクセスを許可するように選択します。

1. 既存の仮想ネットワークにあるサブネットへのアクセスを許可するには、 **[仮想ネットワーク]** で **[Add existing Azure virtual network]\(既存の Azure 仮想ネットワークを追加\)** を選択します。

1. 追加する Azure 仮想ネットワークの**サブスクリプション**を選択します。 Azure Cosmos DB アカウントへのアクセスを提供する Azure **仮想ネットワーク**と**サブネット**を選択します。 次に、 **[有効化]** を選択して、サービス エンドポイントを持つ選択したネットワークを "Microsoft.AzureCosmosDB" に対して有効にします。 完了したら、 **[追加]** を選択します。

   ![仮想ネットワークとサブネットを選択する](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. 仮想ネットワークから Azure Cosmos DB アカウントへのアクセスを有効にすると、この選択したサブネットからのトラフィックのみが許可されます。 追加した仮想ネットワークとサブネットは、次のスクリーン ショットのように表示されます。

   ![正常に構成された仮想ネットワークとサブネット](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> 仮想ネットワーク サービス エンドポイントを有効にするには、次のサブスクリプションのアクセス許可が必要です。
>   * 仮想ネットワークのサブスクリプション:ネットワーク共同作成者
>   * Azure Cosmos DB アカウントのサブスクリプション:DocumentDB アカウント共同作成者
>   * 仮想ネットワークと Azure Cosmos DB アカウントが異なるサブスクリプションにある場合は、仮想ネットワークを持つサブスクリプションにも `Microsoft.DocumentDB` リソース プロバイダーが登録されていることを確認します。 リソース プロバイダーを登録するには、「[Azure リソース プロバイダーと種類](../azure-resource-manager/resource-manager-supported-services.md)」を参照してください。

リソース プロバイダーにサブスクリプションを登録するための手順を次に示します。

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>新しい Azure 仮想ネットワークとサブネット用のサービス エンドポイントを構成する

1. **[すべてのリソース]** ブレードで、セキュリティで保護する Azure Cosmos DB アカウントを見つけます。  

1. 設定メニューの **[Firewalls and Azure virtual networks]** \(ファイアウォールと Azure 仮想ネットワーク\) を選択し、 **[選択されたネットワーク]** からのアクセスを許可するように選択します。  

1. 新しい Azure 仮想ネットワークへのアクセスを許可するには、 **[仮想ネットワーク]** で **[新しい仮想ネットワークを追加]** を選択します。  

1. 新しい仮想ネットワークを作成するために必要な詳細を指定し、 **[作成]** を選択します。 "Microsoft.AzureCosmosDB" 用のサービス エンドポイントが有効になっているサブネットが作成されます。

   ![仮想ネットワークと新しい仮想ネットワークのサブネットを選択する](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

ご使用の Azure Cosmos DB アカウントが Azure Search のような他の Azure サービスによって使用されていたり、Stream Analytics または Power BI からアクセスされたりする場合は、 **[Accept connections from within global Azure datacenters]\(グローバル Azure データセンター内からの接続を受け入れる\)** をオンにしてアクセスを許可します。

ポータルから Azure Cosmos DB のメトリックにアクセスできるようにするには、 **[Azure Portal からのアクセスを許可する]** オプションを有効にする必要があります。 これらのオプションの詳細については、[IP ファイアウォールの構成](how-to-configure-firewall.md)に関する記事を参照してください。 アクセスを有効にしたら、 **[保存]** を選択して、設定を保存します。

## <a id="remove-vnet-or-subnet"></a>仮想ネットワークまたはサブネットを削除する

1. **[すべてのリソース]** ブレードで、サービス エンドポイントを割り当てた Azure Cosmos DB アカウントを見つけます。  

2. 設定メニューの **[ファイアウォールと仮想ネットワーク]** を選択します。  

3. 仮想ネットワークまたはサブネットの横にある **[...]** を選択し、 **[削除]** を選択し、仮想ネットワークまたはサブネットのルールを削除します。

   ![仮想ネットワークを削除する](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4. **[保存]** を選択して変更を保存します。

## <a id="configure-using-powershell"></a>Azure PowerShell を使用してサービス エンドポイントを構成する

> [!NOTE]
> PowerShell または Azure CLI を使用している場合、追加の必要があるもののみではなく、IP フィルターとの仮想ネットワーク ACL の完全な一覧をパラメーターに指定するようにしてください。

Azure PowerShell を使用して、Azure Cosmos DB アカウントへのサービス エンドポイントを構成するには、次の手順を実行します。  

1. [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) をインストールして Azure に[サインイン](https://docs.microsoft.com/powershell/azure/authenticate-azureps)します。  

1. 仮想ネットワークにある、既存のサブネット用のサービス エンドポイントを有効にします。  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
   ```

1. 仮想ネットワークの情報を取得します。

   ```powershell
   $vnProp = Get-AzVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. 次のコマンドレットを実行して、Azure Cosmos DB アカウントのプロパティを取得します。  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. 後で使用するために変数を初期化します。 既存のアカウントの定義からすべての変数を設定します。

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. 次のコマンドレットを実行して、Azure Cosmos DB アカウントのプロパティを新しい構成で更新します。 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. 次のコマンドを実行して、Azure Cosmos DB アカウントが、前の手順で構成した仮想ネットワーク サービス エンドポイントで更新されていることを確認します。

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Azure CLI を使用してサービス エンドポイントを構成する

Azure Cosmos アカウントは、サブネットが既に構成されている場合に、作成または後で更新されたときにサービス エンドポイント用に構成できます。 サービス エンドポイントは、サブネットがまだ構成されていない Cosmos アカウントで有効にすることもできます。その後、サブネットが後で構成されたときに機能し始めます。 Cosmos アカウントと仮想ネットワーク リソースの両方にアクセスできない管理者は、この柔軟性により、構成を互いに独立させることができます。

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>新しい Cosmos アカウントを作成し、新しい仮想ネットワークのバック エンド サブネットに接続する

この例では、仮想ネットワークとサブネットが作成され、両方のサービス エンドポイントが作成時に有効になります。

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Cosmos アカウントをバック エンドのサブネットに個別に接続して構成する

このサンプルは、サービス エンドポイント用のサブネットがまだ構成されていない既存の新しい仮想ネットワークに Azure Cosmos アカウントを接続する方法を示すことを目的としています。 これを行うには、`--ignore-missing-vnet-service-endpoint` パラメーターを使用します。 これにより、仮想ネットワークのサブネットへの構成が完了する前に、Cosmos アカウントの構成をエラーなしで完了することができます。 サブネットの構成が完了すると、構成されたサブネットを介して Cosmos アカウントにアクセスできるようになります。

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a id="migrate-from-firewall-to-vnet"></a>IP ファイアウォール規則から仮想ネットワーク ACL へ移行する

以下の手順は、サブネットを許可する既存の IP ファイアウォール規則が設定されている Azure Cosmos DB アカウントがあるときに、IP ファイアウォール規則ではなく仮想ネットワークとサブネットベースの ACL を使用する場合にのみ実行する必要があります。

サブネットで Azure Cosmos DB アカウントのサービス エンドポイントを有効にしたら、要求は、パブリック IP ではなく、仮想ネットワークおよびサブネット情報を含むソース付きで送信されます。 これらの要求は、IP フィルターと一致しません。 このソースの切り替えは、サービス エンドポイントが有効になっているサブネットからアクセスされるすべての Azure Cosmos DB アカウントで発生します。 ダウンタイムを回避するために、次の手順を実行します。

1. 次のコマンドレットを実行して、Azure Cosmos DB アカウントのプロパティを取得します。

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. 後で使用するために変数を初期化します。 既存のアカウントの定義からすべての変数を設定します。 サブネットからアクセスされるすべての Azure Cosmos DB アカウントに、`ignoreMissingVNetServiceEndpoint` フラグ付きで仮想ネットワーク ACL を追加します。

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. 次のコマンドレットを実行して、Azure Cosmos DB アカウントのプロパティを新しい構成で更新します。

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. サブネットからアクセスするすべての Azure Cosmos DB アカウントに、手順 1 - 3 を繰り返します。

1.  15 分待機し、サブネットを更新して、サービス エンドポイントを有効にします。

1.  仮想ネットワークにある、既存のサブネット用のサービス エンドポイントを有効にします。

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
    ```

1. サブネット用の IP ファイアウォール規則を削除します。

## <a name="next-steps"></a>次の手順

* Azure Cosmos DB 用のファイアウォールの構成については、[ファイアウォールのサポート](firewall-support.md)に関する記事を参照してください。
