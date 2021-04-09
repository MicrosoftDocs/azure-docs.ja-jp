---
title: 'クイックスタート: Azure PowerShell を使用して Azure プライベート リンク サービスを作成する'
description: このクイックスタートでは、Azure PowerShell を使用して Azure Private Link サービスを作成する方法について説明します
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/24/2021
ms.author: allensu
ms.openlocfilehash: 366be37135808a6d3d5cc1a277e2de3e3d6da8ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566404"
---
# <a name="quickstart-create-a-private-link-service-using-azure-powershell"></a>クイックスタート: Azure PowerShell を使用して Private Link サービスを作成する

自分のサービスを参照する Private Link サービスを作成してみましょう。  Private Link には、Azure Standard Load Balancer の背後にデプロイされたサービスやリソースへのアクセス権を付与することができます。  サービスのユーザーは、各自の仮想ネットワークからプライベートにアクセスすることができます

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、次のようにリソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivLinkService-rg' -Location 'eastus2'

```

## <a name="create-an-internal-load-balancer"></a>内部ロード バランサーを作成します。

このセクションでは、仮想ネットワークと内部 Azure Load Balancer を作成します。

### <a name="virtual-network"></a>仮想ネットワーク

このセクションでは、Private Link サービスにアクセスするロード バランサーのホストとなる仮想ネットワークとサブネットを作成します。

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して仮想ネットワークを作成します。

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    PrivateLinkServiceNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig
}
$vnet = New-AzVirtualNetwork @net

```

### <a name="create-standard-load-balancer"></a>Standard ロード バランサーを作成する

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。

* フロントエンド IP プールに対し、[New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) を使用して、次のようにフロントエンド IP を作成します。 ロード バランサーでは、この IP で受信トラフィックを受け取ります。

* ロード バランサーのフロントエンドから送信されるトラフィック用に、[New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) でバックエンド アドレス プールを作成します。 このプールが、バックエンド仮想マシンのデプロイ先となります。

* バックエンド VM インスタンスの正常性を調べる正常性プローブを [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) で作成します。

* 複数の VM に対してどのようにトラフィックを分散させるかを定義するロード バランサー規則を [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) で作成します。

* [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) を使用して、次のようにパブリック ロード バランサーを作成します。


```azurepowershell-interactive
## Place virtual network created in previous step into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create load balancer frontend configuration and place in variable. ##
$lbip = @{
    Name = 'myFrontEnd'
    PrivateIpAddress = '10.1.0.4'
    SubnetId = $vnet.subnets[0].Id
}
$feip = New-AzLoadBalancerFrontendIpConfig @lbip

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus2'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="create-a-private-link-service"></a>プライベート リンク サービスを作成する

このセクションでは、前の手順で作成した Standard Azure Load Balancer を使用するプライベート リンク サービスを作成します。

* [New-AzPrivateLinkServiceIpConfig](/powershell/module/az.network/new-azprivatelinkserviceipconfig) を使用してプライベート リンク サービスの IP 構成を作成します。

* [New-AzPrivateLinkService](/powershell/module/az.network/new-azprivatelinkservice) を使用してプライベート リンク サービスを作成します。

```azurepowershell
## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePrivLinkService-rg'

## Create the IP configuration for the private link service. ##
$ipsettings = @{
    Name = 'myIPconfig'
    PrivateIpAddress = '10.1.0.5'
    Subnet = $vnet.subnets[0]
}
$ipconfig = New-AzPrivateLinkServiceIpConfig @ipsettings

## Place the load balancer frontend configuration into a variable. ##
$par = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$fe = Get-AzLoadBalancer @par | Get-AzLoadBalancerFrontendIpConfig

## Create the private link service for the load balancer. ##
$privlinksettings = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    LoadBalancerFrontendIpConfiguration = $fe
    IpConfiguration = $ipconfig
}
New-AzPrivateLinkService @privlinksettings

```

プライベート リンク サービスが作成され、トラフィックを受信できるようになります。 トラフィック フローを確認する場合は、ご利用のアプリケーションを Standard ロード バランサーの背後に構成します。

## <a name="create-private-endpoint"></a>プライベート エンドポイントの作成

このセクションでは、プライベート リンク サービスをプライベート エンドポイントにマップします。 仮想ネットワークには、プライベート リンク サービス用のプライベート エンドポイントが含まれています。 この仮想ネットワークには、ご利用のプライベート リンク サービスにアクセスするリソースが含まれています。

### <a name="create-private-endpoint-virtual-network"></a>プライベート エンドポイント仮想ネットワークを作成する

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して仮想ネットワークを作成します。

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'mySubnetPE'
    AddressPrefix = '11.1.0.0/24'
    PrivateEndpointNetworkPolicies = 'Disabled'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create the virtual network ##
$net = @{
    Name = 'myVNetPE'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    AddressPrefix = '11.1.0.0/16'
    Subnet = $subnetConfig
}
$vnetpe = New-AzVirtualNetwork @net

```

### <a name="create-endpoint-and-connection"></a>エンドポイントと接続を作成する

* 前に作成したプライベート リンク サービスの構成を後で使用できるように変数に配置するには、[Get-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) を使用します。

* 接続構成を作成するには、[New-AzPrivateLinkServiceConnection](/powershell/module/az.network/new-azprivatelinkserviceconnection) を使用します。

* エンドポイントを作成するには、[New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) を使用します。



```azurepowershell-interactive
## Place the private link service configuration into variable. ##
$par1 = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$pls = Get-AzPrivateLinkService @par1

## Create the private link configuration and place in variable. ##
$par2 = @{
    Name = 'myPrivateLinkConnection'
    PrivateLinkServiceId = $pls.Id
}
$plsConnection = New-AzPrivateLinkServiceConnection @par2

## Place the virtual network into a variable. ##
$par3 = @{
    Name = 'myVNetPE'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$vnetpe = Get-AzVirtualNetwork @par3

## Create private endpoint ##
$par4 = @{
    Name = 'MyPrivateEndpoint'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Location = 'eastus2'
    Subnet = $vnetpe.subnets[0]
    PrivateLinkServiceConnection = $plsConnection
}
New-AzPrivateEndpoint @par4 -ByManualRequest
```

### <a name="approve-the-private-endpoint-connection"></a>プライベート エンドポイント接続を承認する

このセクションでは、前の手順で作成した接続を承認します。

* 接続を承認するには、[Approve-AzPrivateEndpointConnection](/powershell/module/az.network/approve-azprivateendpointconnection) を使用します。

```azurepowershell-interactive
## Place the private link service configuration into variable. ##
$par1 = @{
    Name = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
}
$pls = Get-AzPrivateLinkService @par1

$par2 = @{
    Name = $pls.PrivateEndpointConnections[0].Name
    ServiceName = 'myPrivateLinkService'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    Description = 'Approved'
}
Approve-AzPrivateEndpointConnection @par2

```

### <a name="ip-address-of-private-endpoint"></a>プライベート エンドポイントの IP アドレス

このセクションでは、ロード バランサーとプライベート リンク サービスに対応するプライベート エンドポイントの IP アドレスについて説明します。

* IP アドレスを取得するには、[Get-AzPrivateEndpoint](/powershell/module/az.network/get-azprivateendpoint) を使用します。

```azurepowershell-interactive
## Get private endpoint and the IP address and place in a variable for display. ##
$par1 = @{
    Name = 'myPrivateEndpoint'
    ResourceGroupName = 'CreatePrivLinkService-rg'
    ExpandResource = 'networkinterfaces'
}
$pe = Get-AzPrivateEndpoint @par1

## Display the IP address by expanding the variable. ##
$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress
```

```bash
❯ $pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress
11.1.0.4
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなれば、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループやロード バランサー、その他のリソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreatePrivLinkService-rg'
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは次の作業を行います。

* 仮想ネットワークと内部 Azure Load Balancer を作成しました。
* Private Link サービスを作成しました。

Azure プライベート エンドポイントについてさらに学習するには、次の記事に進んでください。
> [!div class="nextstepaction"]
> [クイックスタート: Azure PowerShell を使用してプライベート エンドポイントを作成する](create-private-endpoint-powershell.md)

