---
title: PowerShell を使用して Azure の内部ロード バランサーを作成する
titlesuffix: Azure Load Balancer
description: Azure Resource Manager と Azure PowerShell モジュールを使用して、内部ロード バランサーを作成する方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: b53225334c6a7d61fcee70327df5979af1e424ee
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275403"
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>Azure PowerShell モジュールを使用した内部ロード バランサーの作成

> [!div class="op_single_selector"]
> * [Azure Portal](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [テンプレート](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>構成の概要

この記事では、Azure Resource Manager と Azure PowerShell モジュールを使用して、内部ロード バランサーを作成する方法について説明します Resource Manager デプロイ モデルでは、内部ロード バランサーの作成に必要なオブジェクトは個々に構成されます。 オブジェクトを作成して構成した後は、結合して 1 つのロード バランサーを作成します。

ロード バランサーをデプロイするには、次のオブジェクトを作成する必要があります。

* フロントエンド IP プール:すべての受信ネットワーク トラフィック用のプライベート IP アドレス。
* バックエンド アドレス プール:フロントエンドの IP アドレスから負荷分散されたトラフィックを受け取るネットワーク インターフェイス。
* 負荷分散ルール:ロード バランサーの (ソースおよびローカルの) ポート構成。
* プローブ構成:仮想マシンの正常性状態のプローブ。
* 受信 NAT ルール:仮想マシンに対する直接アクセスのポート ルール。

ロード バランサー コンポーネントの詳細については、「[Azure Load Balancer での Azure Resource Manager のサポートの使用](load-balancer-arm.md)」を参照してください。

次の手順では、2 つの仮想マシンの間でロード バランサーを構成する方法について説明します。

## <a name="set-up-powershell-to-use-resource-manager"></a>Resource Manager を使用するための PowerShell をセットアップ

最新の製品版 Azure PowerShell モジュールを使用していることを確認します。 PowerShell は、ご利用の Azure サブスクリプションにアクセスできるように正しく構成する必要があります。

### <a name="step-1-start-powershell"></a>手順 1:PowerShell を起動する

Azure Resource Manager 用の PowerShell モジュールを起動します。

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="step-2-view-your-subscriptions"></a>手順 2:サブスクリプションを表示する

使用できる Azure サブスクリプションを確認します。

```azurepowershell-interactive
Get-AzSubscription
```

認証を求められたら、資格情報を入力します。

### <a name="step-3-select-the-subscription-to-use"></a>手順 3:使用するサブスクリプションを選択します

ロード バランサーのデプロイに使用する Azure サブスクリプションを選択します。

```azurepowershell-interactive
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>手順 4:ロード バランサー用のリソース グループを選択する

ロード バランサー用に新しいリソース グループを作成します。 既存のリソース グループを使用する場合は、この手順をスキップしてください。

```azurepowershell-interactive
New-AzResourceGroup -Name NRP-RG -location "West US"
```

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 この場所は、そのリソース グループ内のすべてのリソースの既定の保存先として使用されます。 ロード バランサーの作成に関連するすべてのコマンドに必ず同じリソース グループを使用してください。

この例では、米国西部の場所に **NRP RG** というリソース グループを作成しました。

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>フロントエンド IP プール用に仮想ネットワークと IP アドレスを作成する

仮想ネットワークのサブネットを作成し、変数 **$backendSubnet** に割り当てます。

```azurepowershell-interactive
$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

仮想ネットワークを作成します。

```azurepowershell-interactive
$vnet= New-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

仮想ネットワークが作成されます。 **LB-Subnet-BE** サブネットを **NRPVNet** 仮想ネットワークに追加します。 これらの値は **$vnet** 変数に割り当てられます。

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>フロントエンド IP プールとバックエンド アドレス プールを作成する

ロード バランサー トラフィックを受け取るために、受信トラフィックとバックエンド アドレス プール用のフロントエンド IP プールを作成します。

### <a name="step-1-create-a-front-end-ip-pool"></a>手順 1:フロントエンド IP プールを作成する

サブネット 10.0.2.0/24 用にプライベート IP アドレスが 10.0.2.5 のフロントエンド IP プールを作成します。 このアドレスは、受信ネットワーク トラフィック エンドポイントです。

```azurepowershell-interactive
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>手順 2:バックエンド アドレス プールを作成する

フロントエンド IP プールから受信トラフィックを受け取るために、バックエンド アドレス プールを作成します。

```powershell
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>構成ルール、プローブ、ロード バランサーを作成する

フロントエンド IP プールとバックエンド アドレス プールを作成したら、ロード バランサー リソースのルールを指定します。

### <a name="step-1-create-the-configuration-rules"></a>手順 1:構成ルールを作成する

この例では、次に示す 4 つのルール オブジェクトを作成します。

* リモート デスクトップ プロトコル (RDP) 用のインバウンド NAT ルール:ポート 3441 に対するすべての受信トラフィックをポート 3389 にリダイレクトします。
* RDP 用の第 2 インバウンド NAT ルール:ポート 3442 に対するすべての受信トラフィックをポート 3389 にリダイレクトします。
* 正常性プローブ ルール:HealthProbe.aspx パスの正常性状態を確認します。
* ロード バランサー ルール:パブリック ポート 80 上のすべての受信トラフィックをバックエンド アドレス プールのローカル ポート 80 に負荷分散します。

```azurepowershell-interactive
$inboundNATRule1= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

### <a name="step-2-create-the-load-balancer"></a>手順 2:ロード バランサーを作成する

ロード バランサーを作成し、ルール オブジェクトを結合します (RDP 用の受信 NAT、ロード バランサー、および正常性プローブ)。

```azurepowershell-interactive
$NRPLB = New-AzLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-the-network-interfaces"></a>ネットワーク インターフェイスを作成する

内部ロード バランサーの作成後、負荷分散された受信ネットワーク トラフィック、NAT ルール、およびプローブを受信するネットワーク インターフェイス (NIC) を定義します。 各ネットワーク インターフェイスは個別に構成され、後で仮想マシンに割り当てられます。

### <a name="step-1-create-the-first-network-interface"></a>手順 1:最初のネットワーク インターフェイスを作成する

リソースの仮想ネットワークとサブネットを取得します。 これらの値は、ネットワーク インターフェイスの作成に使用されます。

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

**lb-nic1-be** という名前で、最初のネットワーク インターフェイスを作成します。 このインターフェイスをロード バランサーのバックエンド プールに割り当てます。 最初の RDP 用 NAT ルールをこの NIC に割り当てます。

```azurepowershell-interactive
$backendnic1= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>手順 2:2 つ目のネットワーク インターフェイスを作成する

**lb-nic2-be** という名前で 2 つ目のネットワーク インターフェイスを作成します。 2 つ目のインターフェイスを、最初のインターフェイスと同じロード バランサー バックエンド プールに割り当てます。 2 つ目の NIC を 2 つ目の RDP 用 NAT ルールに関連付けます。

```azurepowershell-interactive
$backendnic2= New-AzNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

構成を確認します。

    $backendnic1

設定は次のようになります。

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/[Id]/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3-assign-the-nic-to-a-vm"></a>手順 3:NIC を VM に割り当てる

`Add-AzVMNetworkInterface` コマンドを使用して、NIC を仮想マシンに割り当てます。

仮想マシンを作成して NIC を割り当てる詳しい手順については、「[PowerShell で Windows 仮想マシンを作成する](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)」を参照してください。

## <a name="add-the-network-interface"></a>ネットワーク インターフェイスの追加

仮想マシンが作成されたら、ネットワーク インターフェイスを追加します。

### <a name="step-1-store-the-load-balancer-resource"></a>手順 1:ロード バランサーのリソースを格納する

変数にロード バランサーのリソースを格納します (まだ実行していない場合)。 ここでは、 **$lb** という変数名を使用しています。スクリプトの属性名には、前の手順で作成したロード バランサー リソースの名前を使用します。

```azurepowershell-interactive
$lb = Get-AzLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>手順 2:バックエンド構成を格納する

バックエンド構成を **$backend** 変数に格納します。

```azurepowershell-interactive
$backend = Get-AzLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>手順 3:ネットワーク インターフェイスを格納する

別の変数にネットワーク インターフェイスを格納します。 このインターフェイスは、「手順 1: ネットワーク インターフェイスを作成する」で作成されたものです。 ここでは、 **$nic1** という変数名を使用しています。 前の例と同じネットワーク インターフェイス名を使用します。

```azurepowershell-interactive
$nic = Get-AzNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>手順 4:バックエンド構成を変更する

ネットワーク インターフェイス上のバックエンド構成を変更します。

```azurepowershell-interactive
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>手順 5: ネットワーク インターフェイス オブジェクトを保存する

ネットワーク インターフェイス オブジェクトを保存します。

```azurepowershell-interactive
Set-AzNetworkInterface -NetworkInterface $nic
```

インターフェイスをバックエンド プールに追加すると、ネットワーク トラフィックの負荷はルールに従って分散されます。 これらのルールは、「構成ルール、プローブ、ロード バランサーを作成する」で構成されたものです。

## <a name="update-an-existing-load-balancer"></a>既存のロード バランサーの更新

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>手順 1:ロード バランサー オブジェクトを変数に割り当てる

`Get-AzLoadBalancer` コマンドを使用して、(前の例の) ロード バランサー オブジェクトを **$slb** 変数に割り当てます。

```azurepowershell-interactive
$slb = Get-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>手順 2:NAT ルールを追加する

既存のロード バランサーに新しい受信 NAT ルールを追加します。 フロントエンド プールにはポート 81、バックエンド プールにはポート 8181 使用します。

```azurepowershell-interactive
$slb | Add-AzLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>手順 3:構成を保存する

`Set-AzureLoadBalancer` コマンドを使用して新しい構成を保存します。

```azurepowershell-interactive
$slb | Set-AzLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>既存のロード バランサーを削除する

`Remove-AzLoadBalancer` コマンドを使用して、**NRP-RG** リソース グループの **NRP-LB** ロード バランサーを削除します。

```azurepowershell-interactive
Remove-AzLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> 削除を確認するプロンプトを表示しない場合は、省略可能な **-Force** スイッチを使用します。

## <a name="next-steps"></a>次のステップ

* [ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)
* [ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)