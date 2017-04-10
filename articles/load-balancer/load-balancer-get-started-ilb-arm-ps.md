---
title: "Azure の内部ロード バランサーの作成 - PowerShell | Microsoft Docs"
description: "リソース マネージャーで PowerShell を使用して、内部ロード バランサーを作成する方法について説明します"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: 7bd31ab8f52ec5e81f6966000554be46eaa59396
ms.lasthandoff: 01/24/2017

---

# <a name="create-an-internal-load-balancer-using-powershell"></a>Create an internal load balancer using PowerShell (PowerShell を使用して内部ロード バランサーを作成する)

> [!div class="op_single_selector"]
> * [[アクセス ポリシー]](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [テンプレート](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../azure-resource-manager/resource-manager-deployment-model.md)&2; 種類のデプロイメント モデルがあります。  この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、[クラシック デプロイメント モデル](load-balancer-get-started-ilb-classic-ps.md)ではなくこのモデルをお勧めします。

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

次の手順では、Azure Resource Manager で PowerShell を使用して内部ロード バランサーを作成する方法について説明します。 Azure Resource Manager では、内部ロード バランサーを作成するための項目は、個別に構成されてからロード バランサーを作成するために組み合わされます。

ロード バランサーをデプロイするには、次のオブジェクトを作成して構成する必要があります。

* フロントエンド IP 構成 - 受信ネットワーク トラフィックのプライベート IP アドレスを構成します。
* バックエンド アドレス プール: フロントエンド IP プールからの負荷分散されたトラフィックを受信するネットワーク インターフェイスを構成します。
* 負荷分散規則 - ロード バランサーのソースおよびローカル ポート構成。
* プローブ - 仮想マシン インスタンスの正常性状態のプローブを構成します。
* 受信 NAT 規則 - 仮想マシン インスタンスのいずれかに直接アクセスするためのポート規則を構成します。

Azure リソース マネージャーでのロード バランサー コンポーネントの詳細については、「 [Azure リソース マネージャーによるロード バランサーのサポート](load-balancer-arm.md)」をご覧ください。

次の手順では、2 つの仮想マシンの間でロード バランサーを構成する方法について説明します。

## <a name="setup-powershell-to-use-resource-manager"></a>リソース マネージャーを使用するように PowerShell をセットアップする

PowerShell 用 Azure モジュールが最新の製品版であり、Azure サブスクリプションにアクセスできるように PowerShell が正しく設定されていることを確認します。

### <a name="step-1"></a>手順 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>手順 2

アカウントのサブスクリプションを確認します。

```powershell
Get-AzureRmSubscription
```

資格情報を使用して認証を行うよう求められます。

### <a name="step-3"></a>手順 3.

使用する Azure サブスクリプションを選択します。

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="create-resource-group-for-load-balancer"></a>ロード バランサーのリソース グループの作成

新しいリソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。 ロード バランサーを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

上記の例では、「NRP RG」という名前のリソース グループと「West US」という名前の場所を作成しました。

## <a name="create-virtual-network-and-a-private-ip-address-for-front-end-ip-pool"></a>Virtual Network とフロント エンド IP プールのパブリック IP アドレスを作成する

仮想ネットワークのサブネットを作成し、変数 $backendSubnet に割り当てます。

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

仮想ネットワークを作成します。

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

仮想ネットワークを作成し、サブネット lb-subnet-be を仮想ネットワーク NRPVNet に追加して、変数 $vnet に割り当てます。

## <a name="create-front-end-ip-pool-and-backend-address-pool"></a>フロント エンド IP プールとバックエンド アドレス プールの作成

受信ロード バランサー ネットワーク トラフィック用のフロント エンド IP プールと、負荷が分散されたトラフィックを受け取るためのバックエンド アドレス プールを設定します。

### <a name="step-1"></a>手順 1

受信ネットワーク トラフィックのエンドポイントとなる、サブネット 10.0.2.0/24 のプライベート IP アドレス 10.0.2.5 を使用して、フロントエンド IP プールを作成します。

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2"></a>手順 2.

フロント エンド IP プールから受信トラフィックを受け取るために使用するバック エンド アドレス プールを設定します。

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-lb-rules-nat-rules-probe-and-load-balancer"></a>LB ルール、NAT ルール、プローブ、およびロード バランサーの作成

フロント エンド IP プールとバックエンド アドレス プールを作成した後、ロード バランサーのリソースに属するルールを作成する必要があります。

### <a name="step-1"></a>手順 1

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

上記の例では、次の項目が作成されます。

* ポート 3441 へのすべての受信トラフィックがポート 3389 に向かう NAT ルール。
* ポート 3442 へのすべての受信トラフィックがポート 3389 に向かう 2 番目の NAT ルール。
* パブリック ポート 80 上のすべての受信トラフィックをバック エンド アドレス プールのローカル ポート 80 に負荷分散するロード バランサー ルール。
* パス "HealthProbe.aspx" の正常性状態を確認するプローブ ルール。

### <a name="step-2"></a>手順 2

すべてのオブジェクト (NAT ルール、ロード バランサー ルール、プローブ構成) を一緒に追加してロード バランサーを作成します。

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-network-interfaces"></a>ネットワーク インターフェイスの作成

内部ロード バランサーの作成後、負荷分散された受信ネットワーク トラフィック、NAT 規則、およびプローブを受信するネットワーク インターフェイスを定義する必要があります。 この場合、ネットワーク インターフェイスは個別に構成され、後で仮想マシンに割り当てることができます。

### <a name="step-1"></a>手順 1

リソースの仮想ネットワークとサブネットを取得し、ネットワーク インターフェイスを作成します。

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

この手順では、ロード バランサーのバックエンド プールに属し、ネットワーク インターフェイスの RDP に対する最初の NAT 規則を関連付けるネットワーク インターフェイスを作成します。

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2"></a>手順 2.

LB-Nic2-BE という名前の&2; 番目のネットワーク インターフェイスを作成します。

このステップでは、同じロード バランサーのバックエンド プールに割り当て、RDP 用に作成された&2; 番目の NAT 規則を関連付けて、2 番目のネットワーク インターフェイスを作成します。

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

最終的に、結果として次の情報が表示されます。

    $backendnic1

予想される出力:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3"></a>手順 3.

コマンド Add-AzureRmVMNetworkInterface を使用して、NIC を仮想マシンに割り当てます。

仮想マシンを作成して NIC に割り当てる詳しい手順については、[PowerShell を使用した Azure VM の作成](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)に関するドキュメントを参照してください。

## <a name="add-the-network-interface"></a>ネットワーク インターフェイスの追加

既に仮想マシンを作成済みの場合は、次の手順でネットワーク インターフェイスを追加することができます。

### <a name="step-1"></a>手順 1

変数にロード バランサーのリソースを読み込みます (まだ実行していない場合)。 変数名は $lb で、上記で作成したロード バランサーのリソースと同じ名前を使用します。

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2"></a>手順 2.

変数にバックエンド構成を読み込みます。

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb
```

### <a name="step-3"></a>手順 3.

変数に作成済みのネットワーク インターフェイスを読み込みます。 使用する変数名は $nic です。 使用するネットワーク インターフェイス名は、上記の例と同じです。

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4"></a>手順 4.

ネットワーク インターフェイス上のバックエンド構成を変更します。

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5"></a>手順 5.

ネットワーク インターフェイス オブジェクトを保存します。

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

ネットワーク インターフェイスがロード バランサーのバックエンド プールに追加されると、そのロード バランサー リソースの負荷分散規則に基づいてネットワーク トラフィックの受信を開始します。

## <a name="update-an-existing-load-balancer"></a>既存のロード バランサーの更新

### <a name="step-1"></a>手順 1
上の例のロード バランサーを使用し、ロード バランサーのオブジェクトを変数 $slb using Get-AzureRmLoadBalancer に割り当てます。

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2"></a>手順 2.

次の例では、フロント エンドでポート 81、バック エンド プールでポート 8181 を使用し、既存のロード バランサーに新しい受信 NAT 規則を追加します。

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3"></a>手順 3.

Set-AzureLoadBalancer を使用して、新しい構成を保存します。

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-a-load-balancer"></a>ロード バランサーの削除

コマンド Remove-AzureRmLoadBalancer を使用して、リソース グループ "NRP-RG" から、前に作成された "NRP-LB" という名前のロード バランサーを削除します。

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> オプションのスイッチ -Force を使用することで、削除のためのプロンプトを回避できます。

## <a name="next-steps"></a>次のステップ

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

