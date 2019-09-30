---
title: Azure PowerShell を使用して Azure プライベート リンク サービスを作成する | Microsoft Docs
description: Azure PowerShell を使用して Azure プライベート リンク サービスを作成する方法について説明します。
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 09158a935aac023382049d3aa9ce23a711972023
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104758"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Azure PowerShell を使用してプライベート リンク サービスを作成する
この記事では、Azure PowerShell を使用して Azure でプライベート リンク サービスを作成する方法について説明します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をローカルにインストールして使用することを選択する場合、この記事では最新の Azure PowerShell モジュール バージョンが必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループの作成

プライベート リンクを作成する前に、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用してリソース グループを作成する必要があります。 次の例では、*myResourceGroup* という名前のリソース グループを場所 *WestCentralUS* に作成します。

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>仮想ネットワークの作成
[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用してプライベート リンク用の仮想ネットワークを作成します。 次の例では、フロントエンド (*frontendSubnet*)、バックエンド (*backendSubnet*)、プライベート リンク (*otherSubnet*) 用のサブネットを含む、*myvnet* という名前の仮想ネットワークを作成します。

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"  

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"  

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled" 

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet 
```
## <a name="create-internal-load-balancer"></a>内部ロード バランサーを作成する
[New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) を使用して、内部 Standard Load Balancer を作成します。 次の例では、前の手順で作成したフロントエンド IP 構成、プローブ、ルール、およびバックエンド プールを使用して 内部 Standard Load Balancer を作成します。

```azurepowershell

$lbBackendName = "LB-backend" 
$lbFrontName = "LB-frontend" 
$lbName = "lb"
 
#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id 
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName 
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard 
```
## <a name="create-a-private-link-service"></a>プライベート リンク サービスを作成する
[AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer) を使用してプライベート リンク サービスを作成します。  この例では、*myResourceGroup* という名前のリソース グループ内で Standard Load Balancer を使用して、*myPLS* という名前のプライベート リンク サービスを作成します。 
```azurepowershell

$plsIpConfigName = "PLS-ipconfig" 
$plsName = "pls"
$peName = "pe" 
  
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5 

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig 

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP`
-IpConfiguration $IPConfig 
```

### <a name="get-private-link-service"></a>プライベート リンク サービスを取得する
次のように [AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) を使用してプライベート リンク サービスの詳細を取得します。

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

この段階で、プライベート リンク サービスが正常に作成され、トラフィックを受信する準備が整いました。 上の例では、PowerShell を使用したプライベート リンク サービスの作成を示すことのみを目的としていることに注意してください。  ロード バランサーのバックエンド プールまたはバックエンド プール上のアプリケーションがトラフィックをリッスンするようには構成されていません。 エンド ツー エンドのトラフィック フローを確認する場合は、お使いの Standard Load Balancer の背後にアプリケーションを構成することを強くお勧めします。 

次に、PowerShell を使用して、異なる VNet のプライベート エンドポイントにこのサービスをマップする方法について説明します。 ここでも、例は、プライベート エンドポイントの作成と上記で作成したプライベート リンク サービスへの接続のみを示しています。 シナリオを構築するために、仮想ネットワークに仮想マシンを作成して、プライベート エンドポイントに対してトラフィックを送受信できます。 

## <a name="create-a-private-endpoint"></a>プライベート エンドポイントを作成する
### <a name="create-a-virtual-network"></a>仮想ネットワークの作成
[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用してプライベート エンドポイント用の仮想ネットワークを作成します。 この例では、*myResourceGroup*という名前のリソース グループ内に  *vnetPE*  という名前の仮想ネットワークを作成します。
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName myResourceGroup `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```
### <a name="create-a-private-endpoint"></a>プライベート エンドポイントを作成する
上記で作成したプライベート リンク サービスを使用するためのプライベート エンドポイントを仮想ネットワーク内に作成します。
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
 ```
### Get private endpoint
Get the IP address of the private endpoint with `Get-AzPrivateEndpoint` as follows:

```azurepowershell

# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 
 ```
  
### <a name="approve-the-private-endpoint-connection"></a>プライベート エンドポイント接続を承認する
Approve-AzPrivateEndpointConnection を使用してプライベート リンク サービスへのプライベート エンドポイント接続を承認します。

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 
 ``` 
## <a name="next-steps"></a>次の手順
- [Azure Private Link](private-link-overview.md) の詳細
 
