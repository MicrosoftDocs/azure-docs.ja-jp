---
title: Azure Virtual Networks 用の一般的な PowerShell コマンド
description: 仮想ネットワークと VM に関連するリソースの作成を開始する際に使用される一般的な PowerShell コマンド
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: b4d6b20e63c42616aad0f8776fae159a0f2aa455
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87088378"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Azure Virtual Networks 用の一般的な PowerShell コマンド

仮想マシンを作成するには、 [仮想ネットワーク](../../virtual-network/virtual-networks-overview.md) を作成するか、VM を追加できる既存の仮想ネットワークについて知る必要があります。 通常、VM を作成するときに、この記事で説明されているリソースの作成を検討する必要があります。

最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、ご利用のアカウントにサインインする方法については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azure/)」を参照してください。

この記事内のコマンドを複数実行する場合は、変数が役に立つことがあります。

- $location: ネットワーク リソースの場所。 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) を使用して、適切な[地理的リージョン](https://azure.microsoft.com/regions/)を検索できます。
- $myResourceGroup: ネットワーク リソースが配置されるリソース グループの名前。

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

| タスク | command |
| ---- | ------- |
| サブネットの構成の作成 |$subnet1 = [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>標準的なネットワークには、[インターネットに接続するロード バランサー](../../load-balancer/load-balancer-overview.md)のサブネットと[内部ロード バランサー](../../load-balancer/load-balancer-overview.md)の別のサブネットがあります。 |
| 仮想ネットワークの作成 |$vnet = [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| 一意のドメイン名のテスト |[Test-AzDnsAvailability](/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>パブリック IP リソースに DNS ドメイン名を指定して、Azure で管理される DNS サーバーの[パブリック IP アドレス](../../virtual-network/public-ip-addresses.md)に対する domainname.location.cloudapp.azure.com のマッピングを作成できます。 この名前には、文字、数字、ハイフンのみを含めることができます。 最初と最後の文字が英字または数字であり、ドメイン名が Azure の場所内で一意である必要があります。 **True** が返された場合、提案した名前はグローバルに一意です。 |
| パブリック IP アドレスの作成 |$pip = [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>パブリック IP アドレスは、事前にテストしたドメイン名を使用し、ロード バランサーのフロントエンド構成で使用されます。 |
| フロントエンド IP 構成の作成 |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>フロントエンド構成には、受信ネットワーク トラフィック用にあらかじめ作成したパブリック IP アドレスが含まれます。 |
| バックエンド アドレス プールの作成 |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>ネットワーク インターフェイス経由でアクセスされるロード バランサーのバックエンドの内部アドレスを指定します。 |
| プローブを作成する |$healthProbe = [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>バックエンド アドレス プール内の仮想マシン インスタンスの可用性の確認に使用される正常性プローブが含まれます。 |
| 負荷分散規則の作成 |$lbRule = [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>ロード バランサーのパブリック ポートをバックエンド アドレス プール内のポートに割り当てる規則が含まれます。 |
| 受信 NAT 規則の作成 |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>ロード バランサーのパブリック ポートをバックエンド アドレス プール内の特定の仮想マシンのポートにマッピングする規則が含まれます。 |
| ロード バランサーの作成 |$loadBalancer = [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| ネットワーク インターフェイスの作成 |$nic1= [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX.X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>パブリック IP アドレスと事前に作成した仮想ネットワークのサブネットを使用して、ネットワーク インターフェイスを作成します。 |

## <a name="get-information-about-network-resources"></a>ネットワーク リソースに関する情報の取得

| タスク | command |
| ---- | ------- |
| 仮想ネットワークの一覧表示 |[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>リソース グループ内のすべての仮想ネットワークが一覧表示されます。 |
| 仮想ネットワークに関する情報の取得 |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| 仮想ネットワーク内のサブネットの一覧表示 |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| サブセットに関する情報の取得 |[Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>指定された仮想ネットワーク内のサブネットに関する情報を取得します。 $vnet 値は、Get-AzVirtualNetwork によって返されるオブジェクトを表します。 |
| IP アドレスの一覧表示 |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>リソース グループ内の Public IP Addresses を一覧表示します。 |
| ロード バランサーの一覧表示 |[Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>リソース グループ内のすべてのロード バランサーが一覧表示されます。 |
| ネットワーク インターフェイスの一覧表示 |[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>リソース グループ内のすべてのネットワーク インターフェイスが一覧表示されます。 |
| ネットワーク インターフェイスに関する情報の取得 |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>特定のネットワーク インターフェイスに関する情報を取得します。 |
| ネットワーク インターフェイスの IP 構成の取得 |[Get-AzNetworkInterfaceIPConfig](/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>指定したネットワーク インターフェイスの IP 構成に関する情報を取得します。 $nic 値は、Get-AzNetworkInterface によって返されるオブジェクトを表します。 |

## <a name="manage-network-resources"></a>ネットワーク リソースの管理

| タスク | command |
| ---- | ------- |
| 仮想ネットワークへのサブネットの追加 |[Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>既存の仮想ネットワークにサブネットを追加します。 $vnet 値は、Get-AzVirtualNetwork によって返されるオブジェクトを表します。 |
| 仮想ネットワークの削除 |[Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>リソース グループから、指定された仮想ネットワークを削除します。 |
| ネットワーク インターフェイスの削除 |[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>リソース グループから、指定されたネットワーク インターフェイスを削除します。 |
| ロード バランサーの削除 |[Remove-AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>リソース グループから、指定されたロード バランサーを削除します。 |
| パブリック IP アドレスの削除 |[Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>リソース グループから、指定されたパブリック IP アドレスを削除します。 |

## <a name="next-steps"></a>次の手順
[VM を作成する](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json)ときに、作成したネットワーク インターフェイスを使用します。
