---
title: "Azure における複数の IP 構成での負荷分散 | Microsoft Docs"
description: "プライマリ IP 構成とセカンダリ IP 構成の間の負荷分散。"
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 4d139e615bea737473d0dfcbed22bf01367a1eb7
ms.lasthandoff: 03/22/2017

---

# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>PowerShell を使用した複数の IP 構成での負荷分散

> [!div class="op_single_selector"]
> * [ポータル](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)

この記事では、1 つのセカンダリ ネットワーク インターフェイス (NIC) に複数の IP アドレスがある Azure Load Balancer の使用方法について説明します。 このシナリオには、Windows を実行する 2 つの VM があり、各 VM にプライマリ NIC とセカンダリ NIC が 1 つずつ存在します。 セカンダリ NIC には、どちらも 2 つの IP 構成が割り当てられています。 それぞれの VM は、contoso.com と fabrikam.com の両方の Web サイトをホストします。 それぞれの Web サイトは、セカンダリ NIC の IP 構成の 1 つにバインドされています。 ここで、Azure Load Balancer を使用して、それぞれの Web サイト用に 2 つのフロントエンド IP アドレスを公開して、トラフィックを Web サイトのそれぞれの IP 構成に分散します。 このシナリオでは、両方のバックエンド プール IP アドレスに加えて、両方のフロントエンドで同じポート番号を使用します。

![LB シナリオの画像](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>複数の IP 構成で負荷分散を行うための手順

この記事で紹介するシナリオを実現するには、次の手順に従います。

1. Azure PowerShell をインストールします。 最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、ご利用のアカウントにサインインする方法については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。
2. 次の設定を使用してリソース グループを作成します。

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    詳細については、[リソース グループの作成](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)に関するページの手順 2. を参照してください。

3. VM を格納する[可用性セットを作成](../virtual-machines/virtual-machines-windows-create-availability-set.md?toc=%2fazure%2fload-balancer%2ftoc.json)します。 このシナリオでは、次のコマンドを使用します。

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. [Windows VM の作成](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)に関する記事の手順 3. ～ 5. に従って、単一の NIC を持つ VM を作成する準備をします。 手順 6.1. を実行し、手順 6.2. の代わりに次のコマンドを実行します。

    ```powershell
    $availset = Get-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzureRmVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    次に、[Windows VM の作成](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)に関する記事の手順 6.3. ～ 6.8. を実行します。

5. 各 VM に 2 番目の IP 構成を追加します。 [仮想マシンに複数の IP アドレスを割り当てる](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add)操作に関する記事の手順に従います。 次の構成設定を使用します。

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    このチュートリアルでは、セカンダリ IP 構成をパブリック IP に関連付ける必要はありません。 コマンドを編集して、パブリック IP の関連付けの部分を削除します。

6. VM2 用に、この記事の手順 4. ～ 6. をもう一度実行します。 このとき、必ず VM 名を VM2 に置き換えてください。 2 つ目の VM には仮想ネットワークを作成する必要がないことに注意してください。 新しいサブネットを作成するかどうかは、ユース ケースに基づいて判断します。

7. 次のように、2 つのパブリック IP アドレスを作成して適切な変数に格納します。

    ```powershell
    $publicIP1 = New-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. 2 つのフロントエンド IP 構成を作成します。

    ```powershell
    $frontendIP1 = New-AzureRmLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. バックエンド アドレス プール、プローブ、および負荷分散規則を作成します。

    ```powershell
    $beaddresspool1 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzureRmLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzureRmLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. これらのリソースを作成したら、ロード バランサーを作成します。

    ```powershell
    $mylb = New-AzureRmLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. 新しく作成したロード バランサーに、2 つ目のバックエンド アドレス プールとフロントエンド IP 構成を追加します。

    ```powershell
    $mylb = Get-AzureRmLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzureRmLoadBalancer

    $mylb | Add-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzureRmLoadBalancer
    
    Add-AzureRmLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzureRmLoadBalancer
    ```

12. 次のコマンドは、NIC を取得し、各セカンダリ NIC の両方の IP 構成をロード バランサーのバックエンド アドレス プールに追加します。

    ```powershell
    $nic1 = Get-AzureRmNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzureRmNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzureRmLoadBalancer

    $nic1 | Set-AzureRmNetworkInterface
    $nic2 | Set-AzureRmNetworkInterface
    ```

13. 最後に、Load Balancer の各フロントエンド IP アドレスを指すように DNS リソース レコードを構成する必要があります。 ドメインを Azure DNS でホストする場合もあります。 Azure DNS を Load Balancer で使用する方法の詳細については、「[Azure DNS を他の Azure サービスで使用する](../dns/dns-for-azure-services.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
- Azure で負荷分散サービスを組み合わせて使う方法について詳しくは、「[Azure で負荷分散サービスを使用する](../traffic-manager/traffic-manager-load-balancing-azure.md)」をご覧ください。
- Azure の各種ログを使って、ロード バランサーの管理やトラブルシューティングを行う方法については、「[Azure Load Balancer のログ分析](../load-balancer/load-balancer-monitor-log.md)」をご覧ください。

