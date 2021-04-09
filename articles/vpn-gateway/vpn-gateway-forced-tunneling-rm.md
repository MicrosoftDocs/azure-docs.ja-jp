---
title: サイト間接続用の強制トンネリングを構成する
description: すべてのインターネットへのトラフィックをオンプレミスの場所に (強制的に) リダイレクトする方法。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cherylmc
ms.openlocfilehash: afd1c1d5312a9fbf39b401b0cbb4b9997f27407a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869040"
---
# <a name="configure-forced-tunneling"></a>強制トンネリングについて

強制トンネリングを使用すると、検査および監査のために、サイト間の VPN トンネルを介して、インターネットへのすべてのトラフィックをオンプレミスの場所に戻すようにリダイレクトする (つまり、"強制する") ことができます。 これは、ほとんどの企業 IT ポリシーの重要なセキュリティ要件です。 強制トンネリングを構成しない場合、Azure の VM からインターネットへのトラフィックは、トラフィックを検査または監査できるオプションを使用せずに、常に Azure ネットワーク インフラストラクチャからインターネットへ直接トラバースします。 認証されていないインターネット アクセスは、情報の漏えいまたは他の種類のセキュリティ侵害を招く可能性があります。

強制トンネリングは、Azure PowerShell を使用して構成できます。 Azure portal を使用して構成することはできません。 この記事は、Resource Manager デプロイ モデルを使用して作成された仮想ネットワークの強制トンネリングを構成する際に役立ちます。 クラシック デプロイ モデル向けに強制トンネリングを構成する場合は、[強制トンネリング - クラシック](vpn-gateway-about-forced-tunneling.md)に関する記事を参照してください。

## <a name="about-forced-tunneling"></a>強制トンネリングについて

以下の図には、強制トンネリングのしくみを示しています。

:::image type="content" source="./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png" alt-text="図は、強制トンネリングを示しています。":::

この例では、フロントエンド サブネットは強制トンネリングされていません。 フロントエンドのサブネット内のワークロードは、直接、インターネットから顧客の要求を承認し応答し続けることができます。 Mid-tier および Backend のサブネットは、トンネリングを強制されます。 これら 2 つのサブネットからインターネットへのアウトバウンド接続は、サイト間 (S2S) VPN トンネルの 1 つを介して、オンプレミス サイトに (強制) リダイレクトされます。

これにより、必要な多層サービス アーキテクチャが継続的に使用可能になっている間は、Azure 内の仮想マシンやクラウド サービスからのインターネット アクセスを制限および検査することができます。 仮想ネットワーク内にインターネットに接続されたワークロードがない場合、仮想ネットワーク全体に強制トンネリングを適用することもできます。

## <a name="requirements-and-considerations"></a>要件と考慮事項

Azure では、強制トンネリングは仮想ネットワークのカスタム ユーザー定義ルートを使用して構成されます。 オンプレミス サイトへのトラフィックのリダイレクトは、Azure VPN Gateway への既定のルートとして表されます。 ユーザー定義のルーティングおよび仮想ネットワークの詳細については、[カスタム ユーザー定義のルート](../virtual-network/virtual-networks-udr-overview.md#user-defined)に関する記事を参照してください。

* 各仮想ネットワーク サブネットには、システム ルーティング テーブルが組み込まれています。 システム ルーティング テーブルには、次の 3 つのグループがあります。
  
  * **ローカル VNet ルート:** 直接、同じ仮想ネットワーク内の宛先 VM へ。
  * **オンプレミス ルート:** Azure VPN ゲートウェイへ。
  * **既定のルート:** 直接、インターネットへ。 前の 2 つのルートが網羅していないプライベート IP アドレスへ送信されるパケットは削除されます。
* この手順ではユーザー定義ルート (UDR) を使用して、既定のルートを追加するルーティング テーブルを作成し、そのルーティング テーブルを VNet サブネットに関連付け、それらのサブネットでの強制トンネリングを有効にします。
* 強制トンネリングは、ルートベースの VPN ゲートウェイを持つ VNet に関連付ける必要があります。 仮想ネットワークに接続されたクロスプレミス ローカル サイト間で「既定のサイト」を設定する必要があります。 また、オンプレミス VPN デバイスを、トラフィック セレクターとして 0.0.0.0/0 を使用して構成する必要があります。 
* ExpressRoute の強制トンネリングは、このメカニズムを使用して構成されていませんが、代わりに ExpressRoute BGP ピアリング セッションを介して既定のルートを通知することで有効化されます。 詳細については、「[ExpressRoute のドキュメント](https://azure.microsoft.com/documentation/services/expressroute/)」を参照してください。
* VPN Gateway と ExpressRoute Gateway の両方を同じ VNet にデプロイしている場合、ExpressRoute Gateway では構成済みの "既定のサイト" が VNet にアドバタイズされるため、ユーザー定義ルート (UDR) が不要になります。

## <a name="configuration-overview"></a>構成の概要

次の手順は、リソース グループと VNet の作成に役立ちます。 その後、VPN Gateway を作成し、強制トンネリングを構成します。 この手順では、仮想ネットワークである "MultiTier-VNet" には "Frontend"、"Midtier"、"Backend" という 3 つのサブネットがあり、"DefaultSiteHQ" と 3 つの Branch の計 4 つのクロスプレミス接続があります。

以下の手順で "DefaultSiteHQ" を強制トンネリングの既定のサイト接続として設定し、強制トンネリングが使用されるように "Midtier" と "Backend" サブネットを構成します。

## <a name="before-you-begin"></a><a name="before"></a>開始する前に

Azure Resource Manager PowerShell コマンドレットの最新版をインストールしてください。 PowerShell コマンドレットのインストールの詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/) 」を参照してください。

> [!IMPORTANT]
> PowerShell コマンドレットの最新バージョンのインストールが必要です。 最新バージョンをインストールしていない場合、一部のコマンドレットの実行時に検証エラーが発生することがあります。
>
>

### <a name="to-sign-in"></a>サインインするには

[!INCLUDE [Sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>強制トンネリングについて

> [!NOTE]
> "The output object type of this cmdlet will be modified in a future release"\(このコマンドレットの出力オブジェクトの種類は将来のリリースで変更されます\) という警告メッセージが表示されることがあります。 これは想定される動作であり、これらの警告は無視してもかまいません。
>
>


1. リソース グループを作成します。

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. 仮想ネットワークを作成し、サブネットを指定します。

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. ローカル ネットワーク ゲートウェイを作成します。

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. ルート テーブルおよびルート規則を作成します。

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. ルート テーブルを Midtier および Backend サブネットに関連付けます。

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. 仮想ネットワーク ゲートウェイを作成します。 ゲートウェイを作成して構成するため、この手順の完了には 45 分以上かかる場合があります。 GatewaySKU の値に関する ValidateSet エラーが発生した場合は、[PowerShell コマンドレットの最新バージョン](#before)がインストールされていることを確認してください。 PowerShell コマンドレットの最新バージョンには、最新の Gateway SKU の新しい有効値が含まれています。

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. 仮想ネットワーク ゲートウェイに既定のサイトを割り当てます。 **-GatewayDefaultSite** は、強制ルーティング構成を動作させるコマンドレット パラメーターです。適切な設定になるように、慎重に構成してください。 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. サイト間 VPN 接続を確立します。

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```
