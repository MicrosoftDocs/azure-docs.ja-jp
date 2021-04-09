---
title: ExpressRoute と S2S VPN の共存する接続の構成:Azure PowerShell
description: Resource Manager モデルにおいて、共存できる ExpressRoute とサイト間 VPN 接続を PowerShell を使用して構成します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/06/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 3b6ed39c11e3f90b986ef904ff3f8e9ff3158d0d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574171"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-using-powershell"></a>PowerShell を使用して ExpressRoute およびサイト間の共存接続を構成する
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - クラシック](expressroute-howto-coexist-classic.md)
> 
> 

この記事は、共存する ExpressRoute とサイト間 VPN の接続を構成するのに役立ちます。 サイト間 VPN と ExpressRoute が構成可能な場合、いくつかの利点があります。 ExpressRoute 用にセキュリティで保護されたフェールオーバー パスとしてサイト間 VPN を構成したり、サイト間 VPN を使用して、ExpressRoute 経由で接続されていないサイトに接続したりできます。 この記事では、両方のシナリオを構成する手順について説明します。 この記事は、Resource Manager デプロイ モデルに適用されます。

サイト間 VPN と ExpressRoute が共存する接続を構成することには、いくつかの利点があります。

* ExpressRoute のセキュリティで保護されたフェールオーバー パスとしてサイト間 VPN を構成することができます。 
* また、サイト間 VPN を使用して、ExpressRoute 経由で接続されていないサイトに接続することもできます。 

この記事では、両方のシナリオを構成する手順について説明します。 この記事は、Resource Manager デプロイ モデルに適用されます。また、ここでは PowerShell が使用されます。 これらのシナリオは Azure Portal を使用して構成することもできますが、ドキュメントはまだ使用できません。 最初にどちらのゲートウェイでも構成できます。 通常、新しいゲートウェイやゲートウェイ接続を追加してもダウンタイムは発生しません。

>[!NOTE]
>ExpressRoute 回線上でサイト間 VPN を作成する場合は、[こちらの記事](site-to-site-vpn-over-microsoft-peering.md)をご覧ください。
>

## <a name="limits-and-limitations"></a>制限と制限事項
* **サポートされているのはルート ベースの VPN ゲートウェイのみです。** ルート ベースの [VPN ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpngateways.md)を使用する必要があります。 「[複数のポリシーベース VPN デバイスへの接続](../vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)」で説明されているように、"ポリシーベース トラフィック セレクタ" に VPN 接続が設定されているルートベースの VPN ゲートウェイを使用することもできます。
* **Azure VPN Gateway の AS 番号は 65515 に設定する必要があります。** Azure VPN Gateway は、BGP ルーティング プロトコルをサポートします。 ExpressRoute と Azure VPN を連動させるには、Azure VPN Gateway の自律システム番号を既定値 65515 のままで維持する必要があります。 以前に 65515 以外の AS 番号を選択し、設定を 65515 に変更する場合、設定を適用するには VPN Gateway をリセットする必要があります。
* **ゲートウェイ サブネットは /27 またはそれより短いプレフィックス** (/26、/25 など) でなければなりません。そうでないと、ExpressRoute 仮想ネットワーク ゲートウェイを追加するときに、エラー メッセージが表示されます。
* **デュアルスタック VNet での共存はサポートされていません。** ExpressRoute IPv6 サポートとデュアルスタック ExpressRoute ゲートウェイを使用している場合、VPN Gateway とは共存できません。

## <a name="configuration-designs"></a>構成の設計
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>ExpressRoute のフェールオーバー パスとしてサイト間 VPN を構成する
ExpressRoute のバックアップとしてサイト間 VPN 接続を構成することができます。 この接続は、Azure のプライベート ピアリング パスにリンクされている仮想ネットワークにのみ適用されます。 Azure Microsoft ピアリングを介してアクセスできるサービスの VPN ベースのフェールオーバー ソリューションはありません。 ExpressRoute 回線は常にプライマリ リンクです。 データは、ExpressRoute 回線で障害が発生した場合にのみ、サイト間 VPN パスを通過します。 非対称なルーティングを回避するには、ローカル ネットワーク構成でも、サイト間 VPN よりも ExpressRoute 回線を優先するようにします。 ExpressRoute を受け取るルートの優先度を高く設定すると、ExpressRoute パスを優先することができます。 

>[!NOTE]
> ExpressRoute Microsoft ピアリングを有効にしている場合、ExpressRoute 接続で Azure VPN Gateway のパブリック IP アドレスを受け取ることができます。 バックアップとしてサイト間 VPN 接続を設定するには、VPN 接続がインターネットにルーティングされるように、オンプレミス ネットワークを構成する必要があります。
>

> [!NOTE]
> 両方のルートが同じである場合は、ExpressRoute 回線がサイト間 VPN よりも優先されますが、Azure では最長プレフィックスの一致を使用してパケットの宛先へのルートを選択します。
> 
> 

![ExpressRoute のバックアップとしてサイト間 VPN 接続が示されている図。](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>ExpressRoute 経由で接続されていないサイトに接続するようにサイト間 VPN を構成する
サイト間 VPN 経由で Azure に直接接続するサイトと、ExpressRoute 経由で接続するサイトがあるネットワークを構成することができます。 

![共存](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> 仮想ネットワークをトランジット ルーターとして構成することはできません。
> 
> 

## <a name="selecting-the-steps-to-use"></a>使用する手順の選択
2 とおりの手順があり、そのいずれかを選択できます。 接続先にする既存の仮想ネットワークがある場合と、新しい仮想ネットワークを作成する場合とでは、選択できる構成手順が異なります。

* VNet がないので作成する必要がある。
  
    仮想ネットワークがまだない場合、この手順で Resource Manager デプロイ モデルを使用して新しい仮想ネットワークを作成し、新しい ExpressRoute 接続とサイト間 VPN 接続を作成します。 仮想ネットワークを構成する際は、「[新しい仮想ネットワークおよび共存する接続を作成するには](#new)」の手順に従ってください。
* Resource Manager デプロイ モデル VNet が既にある。
  
    既存のサイト間 VPN 接続または ExpressRoute 接続を使用して、仮想ネットワークを既に配置している場合があります。 このシナリオでは、ゲートウェイのサブネット マスクが /28 以下 (/28、/29 など) の場合は、既存のゲートウェイを削除する必要があります。 「[既存の VNet で共存する接続を構成するには](#add)」では、ゲートウェイを削除し、新しい ExpressRoute 接続とサイト間 VPN 接続を作成する手順について説明しています。
  
    ゲートウェイを削除して再作成すると、クロスプレミス接続のダウンタイムが発生します。 ただし、移行するように構成されている場合でも、VM やサービスは、ゲートウェイの構成中にロード バランサーを経由して通信できます。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]


## <a name="to-create-a-new-virtual-network-and-coexisting-connections"></a><a name="new"></a>新しい仮想ネットワークおよび共存する接続を作成するには
この手順では、VNet を作成し、共存するサイト間接続と ExpressRoute 接続を作成します。 この構成に使用するコマンドレットは、使い慣れたコマンドレットとは少し異なる場合があります。 必ず、これらの手順で指定されているコマンドレットを使用してください。

1. サインインして、使用するサブスクリプションを選択します。

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. 変数を設定します。

   ```azurepowershell-interactive
   $location = "Central US"
   $resgrp = New-AzResourceGroup -Name "ErVpnCoex" -Location $location
   $VNetASN = 65515
   ```
3. ゲートウェイ サブネットを含む仮想ネットワークを作成します。 仮想ネットワークの作成の詳細については、「[Create a virtual network (仮想ネットワークの作成)](../virtual-network/manage-virtual-network.md#create-a-virtual-network)」を参照してください。 サブネットの作成の詳細については、[サブネットの作成](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)に関するページを参照してください。
   
   > [!IMPORTANT]
   > ゲートウェイ サブネットは /27 またはこれより短いプレフィックス (/26 や /25 など) にする必要があります。
   > 
   > 
   
    新しい VNet を作成します。

   ```azurepowershell-interactive
   $vnet = New-AzVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
   ```
   
    サブネットを追加します。

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    VNet 構成を保存します。

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. <a name="vpngw"></a>次に、サイト間 VPN ゲートウェイを作成します。 VPN ゲートウェイの構成の詳細については、[サイト間接続を使用した VNet の構成](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)に関するページを参照してください。 GatewaySku は *VpnGw1*、*VpnGw2*、*VpnGw3*、*Standard*、*HighPerformance* の各 VPN ゲートウェイでのみサポートされています。 ExpressRoute と VPN Gateway が共存する構成は、Basic SKU ではサポートされていません。 VpnType には、*RouteBased* を指定する必要があります。

   ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
   ```
   
    Azure VPN ゲートウェイは、BGP ルーティング プロトコルをサポートします。 次のコマンドに -Asn スイッチを追加することで、その仮想ネットワークの ASN (AS 番号) を指定できます。 パラメーターの指定がない場合、AS番号は既定で 65515 になります。

   ```azurepowershell-interactive
   $azureVpn = New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
   ```
   
    Azure で VPN ゲートウェイに使用される BGP ピアリング IP と AS 番号は、$azureVpn.BgpSettings.BgpPeeringAddress と $azureVpn.BgpSettings.Asn で確認できます。 詳細については、Azure VPN ゲートウェイの [BGP の構成](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md)に関するページをご覧ください。
5. ローカル サイト VPN ゲートウェイのエンティティを作成します。 このコマンドは、オンプレミスの VPN ゲートウェイを構成しません。 代わりに、パブリック IP やオンプレミスのアドレス空間などのローカル ゲートウェイ設定を指定できるため、Azure VPN ゲートウェイがこれに接続できます。
   
    ローカル VPN デバイスでサポートされるのが静的ルーティングのみの場合、静的ルートは次のように構成できます。

   ```azurepowershell-interactive
   $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   ```
   
    ローカル VPN デバイスで BGP がサポートされており、動的ルーティングを有効にする場合、ローカル VPN デバイスで使用される BGP ピアリング IP と AS 番号を把握しておく必要があります。

   ```azurepowershell-interactive
   $localVPNPublicIP = "<Public IP>"
   $localBGPPeeringIP = "<Private IP for the BGP session>"
   $localBGPASN = "<ASN>"
   $localAddressPrefix = $localBGPPeeringIP + "/32"
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
   ```
6. ローカルの VPN デバイスを構成して、新しい Azure VPN ゲートウェイに接続します。 VPN デバイス構成の詳細については、「 [VPN デバイスの構成](../vpn-gateway/vpn-gateway-about-vpn-devices.md)」を参照してください。

7. Azure のサイト間 VPN ゲートウェイをローカル ゲートウェイにリンクします。

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   New-AzVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
   ```
 

8. 既存の ExpressRoute 回線に接続する場合は、手順 8 と 9 をスキップし、手順 10 にジャンプします。 ExpressRoute 回線を構成します。 ExpressRoute 回線の構成の詳細については、[ExpressRoute 回線の作成](expressroute-howto-circuit-arm.md)に関するページを参照してください。


9. ExpressRoute 回線上で Azure プライベート ピアリングを構成します。 ExpressRoute 回線上で Azure プライベート ピアリングを構成する方法の詳細については、[ピアリングの構成](expressroute-howto-routing-arm.md)に関するページを参照してください。

10. <a name="gw"></a>ExpressRoute ゲートウェイを作成します。 ExpressRoute ゲートウェイの構成の詳細については、「 [ExpressRoute gateway configuration (ExpressRoute ゲートウェイの構成)](expressroute-howto-add-gateway-resource-manager.md)」を参照してください。 GatewaySKU には、*Standard*、*HighPerformance*、または *UltraPerformance* を指定します。

    ```azurepowershell-interactive
    $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
    $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
    $gw = New-AzVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
    ```
11. ExpressRoute ゲートウェイを ExpressRoute 回線にリンクします。 この手順が完了すると、オンプレミスのネットワークと Azure 間の接続が ExpressRoute 経由で確立されます。 リンク操作の詳細については、「 [Link VNets to ExpressRoute (ExpressRoute への Vnet のリンク)](expressroute-howto-linkvnet-arm.md)」を参照してください。

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
    New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
    ```

## <a name="to-configure-coexisting-connections-for-an-already-existing-vnet"></a><a name="add"></a>既存の VNet で共存する接続を構成するには
仮想ネットワーク ゲートウェイが 1 つしかない仮想ネットワークを所有していて (サイト間 VPN ゲートウェイなど)、なおかつ種類が異なる別のゲートウェイ (ExpressRoute ゲートウェイなど) を追加する場合は、ゲートウェイ サブネットのサイズを確認してください。 ゲートウェイ サブネットが /27 以上である場合は、以降の手順をスキップして、前のセクションの手順に従って、サイト間 VPN ゲートウェイまたは ExpressRoute ゲートウェイを追加することができます。 ゲートウェイ サブネットが /28 または /29 の場合、まず仮想ネットワーク ゲートウェイを削除してから、ゲートウェイ サブネットのサイズを増やす必要があります。 このセクションの手順では、その方法を説明します。

この構成に使用するコマンドレットは、使い慣れたコマンドレットとは少し異なる場合があります。 必ず、これらの手順で指定されているコマンドレットを使用してください。

1. 既存の ExpressRoute またはサイト間 VPN ゲートウェイを削除します。

   ```azurepowershell-interactive 
   Remove-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
   ```
2. ゲートウェイ サブネットを削除します。

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
   ```
3. /27 以上のゲートウェイ サブネットを追加します。
   
   > [!NOTE]
   > 仮想ネットワーク内の IP アドレスが不足していてゲートウェイ サブネットのサイズを増やせない場合は、IP アドレス空間を追加する必要があります。
   > 
   > 

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    VNet 構成を保存します。

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. この時点では、仮想ネットワークにゲートウェイがありません。 新しいゲートウェイを作成して接続を設定するには、次の例を使用します。

   変数を設定します。

    ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   ```

   ゲートウェイを作成します。

   ```azurepowershell-interactive
   $gw = New-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup> -Location <yourlocation> -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
   ```

   接続を作成します。

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
   New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
   ```

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>VPN ゲートウェイにポイント対サイト構成を追加するには

共存設定で VPN ゲートウェイにポイント対サイト構成を追加するには、次の手順に従うことができます。 VPN ルート証明書をアップロードするには、PowerShell をコンピューターにローカルにインストールするか、Azure portal を使用する必要があります。

1. VPN クライアント アドレス プールを追加します。

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
   ```
2. VPN ゲートウェイ用に、Azure に VPN [ルート証明書](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#Certificates)をアップロードします。 この例では、次の PowerShell コマンドレットを実行しているローカル コンピューターにルート証明書が保存されていて、PowerShell をローカルで実行していることを前提としています。 Azure portal を使用して証明書をアップロードすることもできます。

   ```powershell
   $p2sCertFullName = "RootErVpnCoexP2S.cer" 
   $p2sCertMatchName = "RootErVpnCoexP2S" 
   $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
   if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
   $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) 
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
   ```
ポイント対サイト VPN の詳細については、 [ポイント対サイト接続の構成](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)に関するページを参照してください。

## <a name="to-enable-transit-routing-between-expressroute-and-azure-vpn"></a>ExpressRoute と Azure VPN の間でトランジット ルーティングを有効にするには
ExpressRoute に接続されているローカル ネットワークのいずれかと、サイト間 VPN 接続に接続されている別のローカル ネットワークの間で接続を有効にする場合、[Azure Route Server](../route-server/expressroute-vpn-support.md) を設定する必要があります。


## <a name="next-steps"></a>次のステップ
ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
