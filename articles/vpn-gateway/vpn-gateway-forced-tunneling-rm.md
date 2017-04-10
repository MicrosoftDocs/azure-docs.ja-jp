---
title: "Azure のサイト間接続用に強制トンネリングを構成する: Resource Manager | Microsoft Docs"
description: "すべてのインターネットへのトラフィックをオンプレミスの場所に &quot;強制的に&quot; リダイレクトする方法。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cbe58db8-b598-4c9f-ac88-62c865eb8721
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 5183fc3b4e7ec3fe6060a6a9551656332300995f
ms.openlocfilehash: 5bf02651c6a3fdd1369676e225cb859dab6fa5d8


---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Azure Resource Manager デプロイ モデルを使用した強制トンネリングの構成
> [!div class="op_single_selector"]
> * [PowerShell - クラシック](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell - Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

強制トンネリングを使用すると、検査および監査のために、サイト間の VPN トンネルを介して、インターネットへのすべてのトラフィックをオンプレミスの場所に戻すようにリダイレクトする (つまり、"強制する") ことができます。 これは、ほとんどの企業 IT ポリシーの重要なセキュリティ要件です。

強制トンネリングを使用しない場合、Azure の VM からインターネットへのトラフィックは、トラフィックを検査または監査できるオプションを使用せずに、常に Azure ネットワーク インフラストラクチャからインターネットへ直接トラバースします。 認証されていないインターネット アクセスは、情報の漏えいまたは他の種類のセキュリティ侵害を招く可能性があります。

この記事では、Resource Manager デプロイ モデルを使用して作成された仮想ネットワークの強制トンネリングを構成する手順について説明します。

**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**強制トンネリングに使用されるデプロイ モデルとツール**

強制トンネリング接続は、クラシック デプロイ モデルと Resource Manager デプロイ モデルの両方に対して構成できます。 詳細については後の表を参照してください。 この表は、この構成について新しい記事、新しいデプロイメント モデル、追加のツールが利用できるようになったら更新されるものです。 記事が利用できるようになったら、表から直接リンクできるようにします。

[!INCLUDE [vpn-gateway-table-forced-tunneling](../../includes/vpn-gateway-table-forcedtunnel-include.md)]

## <a name="about-forced-tunneling"></a>強制トンネリングについて
以下の図には、強制トンネリングのしくみを示しています。 

![強制トンネリング](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

上記の例では、フロントエンドのサブネットは、トンネリングを強制されません。 フロントエンドのサブネット内のワークロードは、直接、インターネットから顧客の要求を承認し応答し続けることができます。 Mid-tier およびMid-tier のサブネットは、トンネリングを強制されます。 これら&2; つのサブネットからのインターネットへのオウトバウンド接続は、S2S VPN トンネルの&1; つを介して、オンプレミス サイトに ”強制” リダイレクトされます。

これにより、必要な多層サービス アーキテクチャが継続的に使用可能になっている間は、Azure 内の仮想マシンやクラウド サービスからのインターネット アクセスを制限および検査することができます。 また、仮想ネットワーク内に、インターネットに接続されたワークロードがない場合、仮想ネットワーク全体に強制トンネリングを適用することもできます。

## <a name="requirements-and-considerations"></a>要件と考慮事項
Azure では、強制トンネリングは仮想ネットワークのユーザー定義ルートを使用して構成されます。 オンプレミス サイトへのトラフィックのリダイレクトは、Azure VPN Gateway への既定のルートとして表されます。 ユーザー定義のルートおよび仮想ネットワークの詳細については、「 [ユーザー定義のルートと IP 転送](../virtual-network/virtual-networks-udr-overview.md)」を参照してください。

* 各仮想ネットワーク サブネットには、システム ルーティング テーブルが組み込まれています。 システム ルーティング テーブルには、次の&3; つのグループがあります。
  
  * **ローカル VNet ルーティング:** 直接、同じ仮想ネットワーク内の宛先 VM へ
  * **オンプレミス ルート:** Azure VPN ゲートウェイへ
  * **既定のルート:** 直接、インターネットへ。 前の&2; つのルートが網羅していないプライベート IP アドレスへ送信されるパケットは削除されます。
* この手順では、ユーザー定義ルート (UDR) を使用することにより、既定のルートを追加するルーティング テーブルを作成し、そのルーティング テーブルを、ご使用の VNet サブネットに関連付け、それらのサブネットでの強制トンネリングを有効にします。
* 強制トンネリングは、ルートベースの VPN ゲートウェイを持つ VNet に関連付ける必要があります。 仮想ネットワークに接続されたクロスプレミス ローカル サイト間で「既定のサイト」を設定する必要があります。
* ExpressRoute の強制トンネリングは、このメカニズムを使用して構成されていませんが、代わりに ExpressRoute BGP ピアリング セッションを介して既定のルートを通知することで有効化されます。 詳細については、 [ExpressRoute Documentation](https://azure.microsoft.com/documentation/services/expressroute/) を参照してください。

## <a name="configuration-overview"></a>構成の概要
次の手順は、リソース グループと VNet の作成に役立ちます。 その後、VPN Gateway を作成し、強制トンネリングを構成します。 この手順では、仮想ネットワークである "MultiTier-VNet" には、*Frontend*、*Midtier*、*Backend* の 3 つのサブネットがあり、*DefaultSiteHQ* と 3 つの *Branch* の計 4 つのクロス プレミス接続があります。

以下の手順で *DefaultSiteHQ* を強制トンネリングの既定のサイト接続として設定し、強制トンネリングが使用されるように Midtier と Backend を構成します。

## <a name="before-you-begin"></a>開始する前に
構成を開始する前に、以下がそろっていることを確認します。

* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
* Azure Resource Manager PowerShell コマンドレット (1.0 以降) の最新版をインストールする必要があります。 PowerShell コマンドレットのインストールの詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」を参照してください。

## <a name="configure-forced-tunneling"></a>強制トンネリングについて
1. PowerShell コンソールで、Azure アカウントにログインします。 このコマンドレットは、Azure アカウントのログイン資格情報をユーザーに求めます。 ログイン後にアカウント設定がダウンロードされるため、Azure PowerShell で使用できるようになります。
   
        Login-AzureRmAccount 
2. Azure サブスクリプションの一覧を取得します。
   
        Get-AzureRmSubscription
3. 使用するサブスクリプションを指定します。 
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
4. リソース グループを作成します。
   
        New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"
5. 仮想ネットワークを作成し、サブネットを指定します。 
   
        $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
        $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
        $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
        $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
        $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
6. ローカル ネットワーク ゲートウェイを作成します。
   
        $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
        $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
        $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
        $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
7. ルート テーブルおよびルート規則を作成します。
   
        New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
        $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
        Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
        Set-AzureRmRouteTable -RouteTable $rt
8. ルート テーブルを Midtier および Backend サブネットに関連付けます。
   
        $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
        Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
9. 既定のサイトでゲートウェイを作成します。 ゲートウェイを作成して構成するため、この手順の完了には 45 分以上かかる場合があります。<br> `-GatewayDefaultSite` は、強制ルーティング構成を動作させるコマンドレット パラメーターです。適切な設定になるように、慎重に構成してください。 このパラメーターは、PowerShell 1.0 以降で利用できます。
   
        $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
        $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
        New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false
10. サイト間 VPN 接続を確立します。
    
         $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
         $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
         $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
         $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
         $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
         New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
         New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
         New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
         New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
         Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"




<!--HONumber=Jan17_HO4-->


