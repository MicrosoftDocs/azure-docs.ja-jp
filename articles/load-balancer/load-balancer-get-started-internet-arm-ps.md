---
title: "Azure のインターネットに接続するロード バランサーの作成 - PowerShell | Microsoft Docs"
description: "Resource Manager で PowerShell を使用してインターネットに接続するロード バランサーを作成する方法について説明します"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-resource-manager
ms.assetid: 8257f548-7019-417f-b15f-d004a1eec826
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: 5abd8365ed883831d4c85ebd14de31dbe45d815d
ms.lasthandoff: 01/24/2017

---

# <a name="get-started"></a>Resource Manager で PowerShell を使用したインターネットに接続するロード バランサーの作成

> [!div class="op_single_selector"]
> * [ポータル](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [テンプレート](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、リソース マネージャーのデプロイ モデルについて説明します。 [従来のデプロイ モデルを使用してインターネットに接続するロード バランサーを作成する方法](load-balancer-get-started-internet-classic-cli.md)についても説明します。

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Azure PowerShell を使用したソリューションのデプロイ

以下の手順では、PowerShell で Azure Resource Manager を使用して、インターネットに接続するロード バランサーを作成する方法について説明します。 Azure Resource Manager では、それぞれのリソースは個別に作成されて構成された後、ロード バランサーを作成するためにまとめられます。

ロード バランサーをデプロイするには、次のオブジェクトを作成して構成する必要があります:

* フロント エンド IP 構成: 受信ネットワーク トラフィックのパブリック IP (PIP) アドレスが含まれます。
* バック エンド アドレス プール: ロード バランサーからネットワーク トラフィックを受信する、仮想マシンのネットワーク インターフェイス (NIC) が含まれます。
* 負荷分散規則: ロード バランサーのパブリック ポートをバック エンド アドレス プール内のポートにマッピングする規則が含まれます。
* 受信 NAT 規則: ロード バランサーのパブリック ポートをバック エンド アドレス プール内の特定の仮想マシンのポートにマッピングする規則が含まれます。
* プローブ: バック エンド アドレス プール内の仮想マシン インスタンスの可用性を確認するために使用する正常性プローブが含まれます。

詳細については、「 [Azure Resource Manager によるロード バランサーのサポート](load-balancer-arm.md)」を参照してください。

## <a name="set-up-powershell-to-use-resource-manager"></a>Resource Manager を使用するための PowerShell をセットアップ

PowerShell 用 Azure Resource Manager (ARM) モジュールが最新の製品版であることを確認する:

1. Azure にサインインします。

    ```powershell
    Login-AzureRmAccount
    ```

    メッセージが表示されたら、資格情報を入力します。

2. アカウントのサブスクリプションを確認します。

    ```powershell
    Get-AzureRmSubscription
    ```

3. 使用する Azure サブスクリプションを選択します。

    ```powershell
    Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'
    ```

4. リソース グループを作成します。 (既存のリソース グループを使用する場合は、この手順をスキップしてください。)

    ```powershell
    New-AzureRmResourceGroup -Name NRP-RG -location "West US"
    ```

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Virtual Network と、フロント エンド IP プールのパブリック IP アドレスの作成

1. サブネットと仮想ネットワークを作成します。

    ```powershell
    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
    ```

2. フロントエンド IP プールで使用される **PublicIP** という名前の Azure パブリック IP アドレス リソースを作成します。DNS 名は **loadbalancernrp.westus.cloudapp.azure.com** です。 次のコマンドでは、静的な割り当てタイプが使用されます。

    ```powershell
    $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -DomainNameLabel loadbalancernrp
    ```

   > [!IMPORTANT]
   > ロード バランサーはその FQDN のプレフィックスとしてパブリック IP のドメイン ラベルを使用します。 これは、ロード バランサー FQDN としてクラウド サービスを使用するクラシック デプロイ モデルからの変更点です。
   > この例では、FQDN は **loadbalancernrp.westus.cloudapp.azure.com**になります。

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>フロントエンド IP プールとバックエンド アドレス プールの作成

1. **PublicIp** リソースを使用する **LB-Frontend** という名前のフロントエンド IP プールを作成します。

    ```powershell
    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP
    ```

2. **LB-backend**という名前のバックエンド アドレス プールを作成します。

    ```powershell
    $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend
    ```

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>NAT 規則、ロード バランサー規則、プローブ、およびロード バランサーの作成

次の例では、以下の項目が作成されます:

* ポート 3441 のすべての受信トラフィックをポート 3389 に転送する NAT 規則
* ポート 3442 のすべての受信トラフィックをポート 3389 に転送する NAT 規則
* **HealthProbe.aspx**
* バックエンド プールのアドレスでポート 80 ～ 80 に入ってくるすべてのトラフィックを分散するロード バランサー規則
* これらのオブジェクトをすべて使用するロード バランサー

次の手順を使用します:

1. NAT 規則を作成します。

    ```powershell
    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389
    ```

2. 正常性プローブを作成します。 プローブは次の&2; とおりの方法で構成できます。

    HTTP プローブ

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

    TCP プローブ

    ```powershell
    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
    ```

3. ロード バランサー規則を作成します。

    ```powershell
    $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

4. ロード バランサーを作成するには、以前に作成したオブジェクトを使用します。

    ```powershell
    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

## <a name="create-nics"></a>NIC の作成

ネットワーク インターフェイスを作成し (または既存のネットワーク インターフェイスを変更し)、それを NAT 規則、ロード バランサー規則、プローブに関連付ける:

1. NIC の作成先となる仮想ネットワークと仮想ネットワークのサブネットを取得します。

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
    ```

2. **lb-nic1-be**という名前の NIC を作成し、それを最初の NAT 規則に関連付け、それから最初 (で唯一) のバックエンド アドレス プールに関連付けます。

    ```powershell
    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
    ```

3. **lb-nic2-be**という名前の NIC を作成し、それを&2; 番目の NAT 規則に関連付け、それから最初 (で唯一) のバックエンド アドレス プールに関連付けます。

    ```powershell
    $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
    ```

4. NIC を確認します。

        $backendnic1

    予想される出力:

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
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
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. `Add-AzureRmVMNetworkInterface` コマンドレットを使用し、NIC をさまざまな VM に割り当てます。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

仮想マシンの作成と NIC の割り当て方法については、「[Powershell を使用した Windows VM の作成](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)」をご覧ください。

## <a name="add-the-network-interface-to-the-load-balancer"></a>ロード バランサーにネットワーク インターフェイスを追加する

1. Azure からロード バランサーを取得します。

    変数にロード バランサーのリソースを読み込みます (まだ実行していない場合)。 変数は **$lb** と呼ばれます。先ほど作成したロード バランサーのリソースと同じ名前を使用します。

    ```powershell
    $lb= get-azurermloadbalancer -name NRP-LB -resourcegroupname NRP-RG
    ```

2. 変数にバックエンド構成を読み込みます。

    ```powershell
    $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
    ```

3. 変数に作成済みのネットワーク インターフェイスを読み込みます。 変数名は **$nic**です。 ネットワーク インターフェイス名は、前述の例と同じです。

    ```powershell
    $nic =get-azurermnetworkinterface -name lb-nic1-be -resourcegroupname NRP-RG
    ```

4. ネットワーク インターフェイス上のバックエンド構成を変更します。

    ```powershell
    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
    ```

5. ネットワーク インターフェイス オブジェクトを保存します。

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```

    ネットワーク インターフェイスがロード バランサーのバックエンド プールに追加されると、そのロード バランサー リソースの負荷分散規則に基づいてネットワーク トラフィックの受信を開始します。

## <a name="update-an-existing-load-balancer"></a>既存のロード バランサーの更新

1. 前述の例のロード バランサーを使用し、ロード バランサーのオブジェクトを `Get-AzureLoadBalancer` を使って変数 **$slb** に割り当てます。

    ```powershell
    $slb = get-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
    ```

2. 次の例では、フロント エンド プールでポート 81、バック エンド プールでポート 8181 を使用し、既存のロード バランサーに新しい受信 NAT 規則を追加します。

    ```powershell
    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP
    ```

3. `Set-AzureLoadBalancer`を使用して、新しい構成を保存します。

    ```powershell
    $slb | Set-AzureRmLoadBalancer
    ```

## <a name="remove-a-load-balancer"></a>ロード バランサーの削除

`Remove-AzureLoadBalancer` コマンドを使用して、**NRP-RG** というリソース グループから、以前に作成された**NRP-LB** という名前のロード バランサーを削除します。

```powershell
Remove-AzureRmLoadBalancer -Name NRP-LB -ResourceGroupName NRP-RG
```

> [!NOTE]
> オプションのスイッチ **-Force** を使用することで、削除のためのプロンプトを回避できます。

## <a name="next-steps"></a>次のステップ

[内部ロード バランサーの構成の開始](load-balancer-get-started-ilb-arm-ps.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

