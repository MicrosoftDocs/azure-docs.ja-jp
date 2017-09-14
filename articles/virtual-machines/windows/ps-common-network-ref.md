---
title: "Azure Virtual Networks 用の一般的な PowerShell コマンド | Microsoft Docs"
description: "仮想ネットワークと VM に関連するリソースの作成を開始する際に使用される一般的な PowerShell コマンド"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 6ddb1915c8073a7b64ba03ff6b90e701ad337c3e
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Azure Virtual Networks 用の一般的な PowerShell コマンド

仮想マシンを作成するには、 [仮想ネットワーク](../../virtual-network/virtual-networks-overview.md) を作成するか、VM を追加できる既存の仮想ネットワークについて知る必要があります。 通常、VM を作成するときに、この記事で説明されているリソースの作成を検討する必要があります。

最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、ご利用のアカウントにサインインする方法については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。

この記事内のコマンドを複数実行する場合は、変数が役に立つことがあります。

- $location: ネットワーク リソースの場所。 [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) を使用して、適切な[地理的リージョン](https://azure.microsoft.com/regions/)を検索できます。
- $myResourceGroup: ネットワーク リソースが配置されるリソース グループの名前。

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

| タスク | コマンド |
| ---- | ------- |
| サブネットの構成の作成 |$subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>標準的なネットワークには、[インターネットに接続するロード バランサー](../../load-balancer/load-balancer-internet-overview.md)のサブネットと[内部ロード バランサー](../../load-balancer/load-balancer-internal-overview.md)の別のサブネットがあります。 |
| 仮想ネットワークの作成 |$vnet = [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| 一意のドメイン名のテスト |[Test-AzureRmDnsAvailability](https://docs.microsoft.com/powershell/module/azurerm.network/test-azurermdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>パブリック IP リソースに DNS ドメイン名を指定して、Azure で管理される DNS サーバーの[パブリック IP アドレス](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)に対する domainname.location.cloudapp.azure.com のマッピングを作成できます。 この名前には、文字、数字、ハイフンのみを含めることができます。 最初と最後の文字が英字または数字であり、ドメイン名が Azure の場所内で一意である必要があります。 **True** が返された場合、提案した名前はグローバルに一意です。 |
| パブリック IP アドレスの作成 |$pip = [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>パブリック IP アドレスは、事前にテストしたドメイン名を使用し、ロード バランサーのフロントエンド構成で使用されます。 |
| フロントエンド IP 構成の作成 |$frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>フロントエンド構成には、受信ネットワーク トラフィック用にあらかじめ作成したパブリック IP アドレスが含まれます。 |
| バックエンド アドレス プールの作成 |$beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>ネットワーク インターフェイス経由でアクセスされるロード バランサーのバックエンドの内部アドレスを指定します。 |
| プローブを作成する |$healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>バックエンド アドレス プール内の仮想マシン インスタンスの可用性の確認に使用される正常性プローブが含まれます。 |
| 負荷分散規則の作成 |$lbRule = [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>ロード バランサーのパブリック ポートをバックエンド アドレス プール内のポートに割り当てる規則が含まれます。 |
| 受信 NAT 規則の作成 |$inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>ロード バランサーのパブリック ポートをバックエンド アドレス プール内の特定の仮想マシンのポートにマッピングする規則が含まれます。 |
| ロード バランサーの作成 |$loadBalancer = [New-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| ネットワーク インターフェイスの作成 |$nic1= [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX.X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>パブリック IP アドレスと事前に作成した仮想ネットワークのサブネットを使用して、ネットワーク インターフェイスを作成します。 |

## <a name="get-information-about-network-resources"></a>ネットワーク リソースに関する情報の取得

| タスク | コマンド |
| ---- | ------- |
| 仮想ネットワークの一覧表示 |[Get-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>リソース グループ内のすべての仮想ネットワークが一覧表示されます。 |
| 仮想ネットワークに関する情報の取得 |Get-AzureRmVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| 仮想ネットワーク内のサブネットの一覧表示 |Get-AzureRmVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| サブセットに関する情報の取得 |[Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>指定された仮想ネットワーク内のサブネットに関する情報を取得します。 $Vnet 値は、Get AzureRmVirtualNetwork によって返されるオブジェクトを表します。 |
| IP アドレスの一覧表示 |[Get-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>リソース グループ内の Public IP Addresses を一覧表示します。 |
| ロード バランサーの一覧表示 |[Get-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>リソース グループ内のすべてのロード バランサーが一覧表示されます。 |
| ネットワーク インターフェイスの一覧表示 |[Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>リソース グループ内のすべてのネットワーク インターフェイスが一覧表示されます。 |
| ネットワーク インターフェイスに関する情報の取得 |Get-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>特定のネットワーク インターフェイスに関する情報を取得します。 |
| ネットワーク インターフェイスの IP 構成の取得 |[Get-AzureRmNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>指定したネットワーク インターフェイスの IP 構成に関する情報を取得します。 $nic 値は、Get-AzureRmNetworkInterface によって返されるオブジェクトを表します。 |

## <a name="manage-network-resources"></a>ネットワーク リソースの管理

| タスク | コマンド |
| ---- | ------- |
| 仮想ネットワークへのサブネットの追加 |[Add-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>既存の仮想ネットワークにサブネットを追加します。 $Vnet 値は、Get AzureRmVirtualNetwork によって返されるオブジェクトを表します。 |
| 仮想ネットワークの削除 |[Remove-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>リソース グループから、指定された仮想ネットワークを削除します。 |
| ネットワーク インターフェイスの削除 |[Remove-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermnetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>リソース グループから、指定されたネットワーク インターフェイスを削除します。 |
| ロード バランサーの削除 |[Remove-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>リソース グループから、指定されたロード バランサーを削除します。 |
| パブリック IP アドレスの削除 |[Remove-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>リソース グループから、指定されたパブリック IP アドレスを削除します。 |

## <a name="next-steps"></a>次のステップ
* [VM を作成する](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ときに、作成したネットワーク インターフェイスを使用します。
* [複数のネットワーク インターフェイスを持つ VM を作成する](../../virtual-network/virtual-networks-multiple-nics.md)方法について学習します。


