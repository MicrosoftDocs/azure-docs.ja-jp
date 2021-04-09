---
title: Azure Cosmos アカウント用の仮想ネットワーク ベースのアクセスを構成する
description: このドキュメントでは、Azure Cosmos DB の仮想ネットワーク サービス エンドポイントのセットアップに必要な手順について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d63d21f4c49e3c7aef035208477ac9fc79f2e51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94637185"
---
# <a name="configure-access-to-azure-cosmos-db-from-virtual-networks-vnet"></a>仮想ネットワーク (VNet) からの Azure Cosmos DB へのアクセスを構成する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos アカウントは、仮想ネットワーク (VNet) の特定のサブネットからのアクセスのみを許可するように構成することができます。 [サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)が仮想ネットワーク内のサブネット上の Azure Cosmos DB にアクセスできるようにすることで、そのサブネットからのトラフィックは、サブネットと仮想ネットワークの ID を使用して Azure Cosmos DB に送信されます。 Azure Cosmos DB サービス エンドポイントが有効になると、Azure Cosmos アカウントにサブネットを追加することで、サブネットへのアクセスを制限できます。

既定で、有効な承認トークンと共に要求が送信される場合、任意のソースから Azure Cosmos アカウントにアクセスできます。 VNet 内に 1 つまたは複数のサブネットを追加した場合、それらのサブネットから送信された要求のみが有効な応答を受け取ります。 その他のソースから送信された要求は、403 (禁止) 応答を受け取ります。 

Azure Cosmos DB アカウントを構成して、Azure 仮想ネットワークの特定のサブネットのみからアクセスを許可するよう構成できます。 Azure Cosmos DB アカウントへの仮想ネットワーク内のサブネットからの接続をアクセス制限するには、次を実行します。

1. サブネットが、Azure Cosmos DB にサブネットと仮想ネットワークの ID を送信できるようにします。 これは、特定のサブネット上で Azure Cosmos DB 用のサービス エンドポイントを有効にすることで実現できます。

1. Azure Cosmos DB アカウントにアクセスできるソースとしてサブネットを指定する規則を Azure Cosmos DB アカウントに追加します。

> [!NOTE]
> ご使用の Azure Cosmos DB アカウント用のサービス エンドポイントがサブネット上で有効になると、Azure Cosmos DB に到達するトラフィックのソースが、パブリック IP から、仮想ネットワークとサブネットに切り替わります。 このトラフィックの切り替えは、このサブネットからアクセスされるすべての Azure Cosmos DB アカウントに適用されます。 このサブネットを許可する IP ベースのファイアウォールがご使用の Azure Cosmos DB アカウントにある場合、サービスで有効なサブネットからの要求は IP ファイアウォール規則と一致しなくなり、要求は拒否されるようになります。
>
> 詳細については、この記事の「[IP ファイアウォール規則から VNET ACL への移行](#migrate-from-firewall-to-vnet)」セクションの手順を参照してください。

以下のセクションでは、Azure Cosmos DB アカウント用の仮想ネットワーク サービス エンドポイントを構成する方法について説明します。

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Azure portal を使用してサービス エンドポイントを構成する

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>既存の Azure 仮想ネットワークとサブネット用のサービス エンドポイントを構成する

1. **[すべてのリソース]** ブレードで、セキュリティで保護する Azure Cosmos DB アカウントを見つけます。

1. 設定メニューの **[ファイアウォールと仮想ネットワーク]** を選択し、 **[選択されたネットワーク]** からアクセスを許可するように選択します。

1. 既存の仮想ネットワークにあるサブネットへのアクセスを許可するには、 **[仮想ネットワーク]** で **[Add existing Azure virtual network]\(既存の Azure 仮想ネットワークを追加\)** を選択します。

1. 追加する Azure 仮想ネットワークの **サブスクリプション** を選択します。 Azure Cosmos DB アカウントへのアクセスを提供する Azure **仮想ネットワーク** と **サブネット** を選択します。 次に、 **[有効化]** を選択して、サービス エンドポイントを持つ選択したネットワークを "Microsoft.AzureCosmosDB" に対して有効にします。 完了したら、 **[追加]** を選択します。

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="仮想ネットワークとサブネットを選択する":::

1. 仮想ネットワークから Azure Cosmos DB アカウントへのアクセスを有効にすると、この選択したサブネットからのトラフィックのみが許可されます。 追加した仮想ネットワークとサブネットは、次のスクリーン ショットのように表示されます。

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="正常に構成された仮想ネットワークとサブネット":::

> [!NOTE]
> 仮想ネットワーク サービス エンドポイントを有効にするには、次のサブスクリプションのアクセス許可が必要です。
>   * 仮想ネットワークのサブスクリプション:ネットワーク共同作成者
>   * Azure Cosmos DB アカウントのサブスクリプション:DocumentDB アカウント共同作成者
>   * 仮想ネットワークと Azure Cosmos DB アカウントが異なるサブスクリプションにある場合は、仮想ネットワークを持つサブスクリプションにも `Microsoft.DocumentDB` リソース プロバイダーが登録されていることを確認します。 リソース プロバイダーを登録するには、「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。

リソース プロバイダーにサブスクリプションを登録するための手順を次に示します。

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>新しい Azure 仮想ネットワークとサブネット用のサービス エンドポイントを構成する

1. **[すべてのリソース]** ブレードで、セキュリティで保護する Azure Cosmos DB アカウントを見つけます。  

1. 設定メニューの **[Firewalls and Azure virtual networks]** \(ファイアウォールと Azure 仮想ネットワーク\) を選択し、 **[選択されたネットワーク]** からのアクセスを許可するように選択します。  

1. 新しい Azure 仮想ネットワークへのアクセスを許可するには、 **[仮想ネットワーク]** で **[新しい仮想ネットワークを追加]** を選択します。  

1. 新しい仮想ネットワークを作成するために必要な詳細を指定し、 **[作成]** を選択します。 "Microsoft.AzureCosmosDB" 用のサービス エンドポイントが有効になっているサブネットが作成されます。

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="仮想ネットワークと新しい仮想ネットワークのサブネットを選択する":::

ご使用の Azure Cosmos DB アカウントを Azure Cognitive Search などのその他の Azure サービスで使用していたり、それが Stream Analytics または Power BI からアクセスされる場合は、 **[Accept connections from within global Azure datacenters]** \(グローバル Azure データセンター内からの接続を受け入れる\) をオンにしてアクセスを許可します。

ポータルから Azure Cosmos DB のメトリックにアクセスできるようにするには、 **[Azure Portal からのアクセスを許可する]** オプションを有効にする必要があります。 これらのオプションの詳細については、[IP ファイアウォールの構成](how-to-configure-firewall.md)に関する記事を参照してください。 アクセスを有効にしたら、 **[保存]** を選択して、設定を保存します。

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>仮想ネットワークまたはサブネットを削除する

1. **[すべてのリソース]** ブレードで、サービス エンドポイントを割り当てた Azure Cosmos DB アカウントを見つけます。  

1. 設定メニューの **[ファイアウォールと仮想ネットワーク]** を選択します。  

1. 仮想ネットワークまたはサブネットの横にある **[...]** を選択し、 **[削除]** を選択し、仮想ネットワークまたはサブネットのルールを削除します。

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="仮想ネットワークを削除する":::

1. **[保存]** を選択して変更を保存します。

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Azure PowerShell を使用してサービス エンドポイントを構成する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell を使用して、Azure Cosmos DB アカウントへのサービス エンドポイントを構成するには、次の手順を実行します。  

1. [Azure PowerShell](/powershell/azure/install-Az-ps) をインストールして Azure に[サインイン](/powershell/azure/authenticate-azureps)します。  

1. 仮想ネットワークにある、既存のサブネット用のサービス エンドポイントを有効にします。  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

   > [!NOTE]
   > PowerShell または Azure CLI を使用している場合、追加の必要があるもののみではなく、IP フィルターとの仮想ネットワーク ACL の完全な一覧をパラメーターに指定するようにしてください。

1. 仮想ネットワークの情報を取得します。

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Cosmos DB 仮想ネットワーク規則を準備する

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. 仮想ネットワーク エンドポイントの新しい構成を使用して、Azure Cosmos DB アカウントのプロパティを更新します。 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. 次のコマンドを実行して、Azure Cosmos DB アカウントが、前の手順で構成した仮想ネットワーク サービス エンドポイントで更新されていることを確認します。

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Azure CLI を使用してサービス エンドポイントを構成する

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

## <a name="port-range-when-using-direct-mode"></a>直接モードを使用する場合のポートの範囲

ダイレクト モード接続を介して Azure Cosmos アカウントでサービス エンドポイントを使用している場合は、10000 から 20000 までの TCP ポート範囲が開いていることを確保する必要があります。

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>IP ファイアウォール規則から仮想ネットワーク ACL へ移行する

Azure Cosmos DB アカウントを移行するには、IP ファイアウォール規則を使用することから、仮想ネットワーク サービス エンドポイントを使用することまで、以下の手順に従います。

サブネットのサービス エンドポイントに対して Azure Cosmos DB アカウントが構成されると、そのサブネットからの要求は、ソースのパブリック IP アドレスではなく、仮想ネットワークとサブネットのソース情報を使用して Azure Cosmos DB に送信されます。 これらの要求は、Azure Cosmos DB アカウントで構成された IP フィルターと一致しなくなります。それが理由で、ダウンタイムを回避するために以下の手順が必要です。

続行する前に、上記の「仮想ネットワークにある、既存のサブネット用のサービス エンドポイントを有効にします」で示した手順に従って、仮想ネットワークとサブネットで Azure Cosmos DB サービス エンドポイントを有効にします。

1. 仮想ネットワークとサブネットの情報を取得します。

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Azure Cosmos DB アカウントの新しい仮想ネットワーク規則オブジェクトを準備します。

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Azure Cosmos DB アカウントを更新して、サブネットからのサービス エンドポイント アクセスを有効にします。

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. サブネットからアクセスされるすべての Azure Cosmos DB アカウントについて、前の手順を繰り返します。

1. Azure Cosmos DB アカウントのファイアウォール規則から、サブネットの IP ファイアウォール規則を削除します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

仮想ネットワークからのアクセスを構成する場合についてよく寄せられる質問を次に示します。

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Notebook と Mongo/Cassandra シェルは、現在、Virtual Network が有効になっているアカウントと互換性がありますか。

現在、Cosmos DB Data Explorer での [Mongo シェル](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/)と [Cassandra シェル](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/)統合、および [Jupyter Notebook サービス](./cosmosdb-jupyter-notebooks.md)は、VNET アクセスではサポートされていません。 これは現在開発が進められています。

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Azure Cosmos アカウントに対して仮想ネットワーク サービス エンドポイントと IP アクセス制御ポリシーの両方を指定できますか。 

Azure Cosmos アカウントに対して、仮想ネットワーク サービス エンドポイントと IP アクセス制御ポリシー (ファイアウォールとも呼ばれます) の両方を有効にすることができます。 この 2 つの機能は補完的であり、組み合わせることで Azure Cosmos アカウントの分離とセキュリティを達成できます。 IP ファイアウォールを使用すると、確実に静的 IP からアカウントにアクセスできます。 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>仮想ネットワーク内のサブネットへのアクセスを制限するにはどうすればよいですか。 

サブネットから Azure Cosmos アカウントへのアクセスを制限するには、2 つの手順が必要です。 まず、サブネットからのトラフィックで、そのサブネットおよび仮想ネットワーク ID を Azure Cosmos DB に伝達できるようにします。 このために、サブネット上で Azure Cosmos DB のサービス エンドポイントを有効にします。 次に、Azure Cosmos アカウントにアクセスできるソースとしてサブネットを指定する規則を Azure Cosmos アカウントに追加します。

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>仮想ネットワーク ACL と IP ファイアウォールは要求または接続を拒否しますか。 

IP ファイアウォールまたは仮想ネットワークのアクセス規則が追加されると、許可されたソースからの要求のみが有効な応答を受け取ります。 他の要求は 403 (禁止) で拒否されます。 Azure Cosmos アカウントのファイアウォールを接続レベルのファイアウォールと区別することが重要です。 ソースは引き続きサービスに接続でき、接続自体は拒否されません。

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>サブネット上の Azure Cosmos DB に対してサービス エンドポイントを有効にすると、要求がブロックされ始めました。 なぜでしょうか?

Azure Cosmos DB 用のサービス エンドポイントがサブネット上で有効になると、アカウントに到達するトラフィックのソースが、パブリック IP から、仮想ネットワークとサブネットに切り替わります。 IP ベースのファイアウォールのみが Azure Cosmos アカウントに設定されている場合、サービスで有効なサブネットからのトラフィックは IP ファイアウォール規則と一致しなくなるため、拒否されるようになります。 IP ベースのファイアウォールから仮想ネットワーク ベースのアクセス制御にシームレスに移行するための手順を実行してください。

### <a name="are-additional-azure-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Azure Cosmos アカウントと VNET サービス エンドポイントの組み合わせには、追加の Azure RBAC アクセス許可が必要ですか。

VNet サービス エンドポイントを Azure Cosmos アカウントに追加した後、アカウント設定を変更する場合、自分の Azure Cosmos アカウントで構成されているすべての VNET について、`Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` アクションの権限が必要になります。 このアクセス許可が必要になるのは、あらゆるプロパティを評価する前に、リソース (データベースや仮想ネットワーク リソースなど) へのアクセス権限が認証プロセスで検証されるためです。
 
この認証では、ユーザーが Azure CLI を使用して VNET ACL を指定しない場合でも、VNet リソース アクションのアクセス許可が検証されます。 現在のところ、Azure Cosmos アカウントのコントロール プレーンでは、Azure Cosmos アカウントの完全な状態を設定できます。 コントロール プレーン呼び出しのパラメーターの 1 つは `virtualNetworkRules` です。 このパラメーターが指定されていない場合、Azure CLI では、`virtualNetworkRules` を取得する目的でデータベースの取得呼び出しを行い、更新呼び出しでこの値を使用します。

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>ピアリングされた仮想ネットワークも Azure Cosmos アカウントにアクセスできますか。 
Azure Cosmos アカウントに追加された仮想ネットワークとそのサブネットのみがアクセスできます。 ピアリングされた仮想ネットワーク内のサブネットをアカウントに追加するまで、ピアリングされた VNet はアカウントにアクセスできません。

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>1 つの Cosmos アカウントにアクセスできるサブネットの最大数はいくつですか。 
現在、Azure Cosmos アカウントでは最大 256 のサブネットが許可されています。

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>VPN と Express Route からのアクセスを有効にすることはできますか。 
オンプレミスから Express Route 経由で Azure Cosmos アカウントにアクセスするには、Microsoft ピアリングを有効にする必要があります。 IP ファイアウォールまたは仮想ネットワーク アクセス規則を設定すると、Azure Cosmos アカウントの IP ファイアウォールに Microsoft ピアリングに使用するパブリック IP アドレスを追加して、オンプレミス サービスが Azure Cosmos アカウントにアクセスできるようにすることができます。 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>ネットワーク セキュリティ グループ (NSG) の規則を更新する必要はありますか。 
NSG 規則は、仮想ネットワークを使用したサブネットとの接続を制限するために使用されます。 Azure Cosmos DB のサービス エンドポイントをサブネットに追加する場合、Azure Cosmos アカウントのために NSG で送信接続を開く必要はありません。 

### <a name="are-service-endpoints-available-for-all-vnets"></a>サービス エンドポイントはすべての VNet に使用できますか。
いいえ。サービス エンドポイントを有効にすることができるのは Azure Resource Manager 仮想ネットワークのみです。 クラシック仮想ネットワークはサービス エンドポイントをサポートしていません。

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Azure Cosmos DB のサービス エンドポイント アクセスが有効になっているときに "パブリック Azure データセンター内からの接続を受け入れる" ことはできますか?  
これは、Azure Data Factory、Azure Cognitive Search、または特定の Azure リージョンにデプロイされるサービスのような他の Azure ファースト パーティ サービスによって Azure Cosmos DB アカウントがアクセスされる場合にのみ必要です。

## <a name="next-steps"></a>次のステップ

* Azure Cosmos DB 用のファイアウォールの構成については、[ファイアウォールのサポート](how-to-configure-firewall.md)に関する記事を参照してください。
