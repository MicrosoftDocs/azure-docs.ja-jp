---
title: "共存する ExpressRoute とサイト間 VPN の接続の構成: Resource Manager: Azure | Microsoft Docs"
description: "接続この記事では、リソース マネージャーのデプロイ モデルにおいて、共存できる ExpressRoute 接続とサイト間 VPN 接続を構成する手順について説明します。"
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c7717b14-3da3-4a6d-b78e-a5020766bc2c
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 17f179d9bd614b5ea05bef8c4ca4173eecc6b9be
ms.lasthandoff: 03/24/2017


---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections"></a>ExpressRoute 接続とサイト間接続の共存の構成
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - クラシック](expressroute-howto-coexist-classic.md)
> 
> 

サイト間 VPN と ExpressRoute が構成可能な場合、いくつかの利点があります。 ExpressRoute 用にセキュリティで保護されたフェールオーバー パスとしてサイト間 VPN を構成したり、サイト間 VPN を使用して、ExpressRoute 経由で接続されていないサイトに接続したりできます。 この記事では、両方のシナリオを構成する手順について説明します。 この記事は、リソース マネージャーのデプロイ モデルに適用されます。 この構成は、Azure ポータルで使用できません。

**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> ExpressRoute 回線を事前に構成してから、以下の手順に従ってください。 以下の手順に従う前に、必ず、[ExpressRoute 回線の作成](expressroute-howto-circuit-arm.md)と[ルーティングの構成](expressroute-howto-routing-arm.md)に関するガイドに従ってください。
> 
> 

## <a name="limits-and-limitations"></a>制限と制限事項
* **トランジット ルーティングはサポートされていません。** サイト間 VPN 経由で接続されたローカル ネットワークと ExpressRoute 経由で接続されたローカル ネットワーク間で (Azure 経由で) ルーティングすることはできません。
* **Basic SKU ゲートウェイはサポートされていません。** [ExpressRoute ゲートウェイ](expressroute-about-virtual-network-gateways.md)と [VPN ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpngateways.md)のどちらについても、Basic SKU 以外のゲートウェイを使用する必要があります。
* **サポートされているのはルート ベースの VPN ゲートウェイのみです。** [ルート ベースの VPN ゲートウェイを使用する必要があります](../vpn-gateway/vpn-gateway-about-vpngateways.md)。
* **VPN ゲートウェイでは静的ルートを構成する必要があります。** ローカル ネットワークが ExpressRoute とサイト間 VPN の両方に接続されている場合は、ローカル ネットワーク内で静的ルートを構成して、パブリック インターネットへのサイト間 VPN 接続をルーティングする必要があります。
* **ExpressRoute ゲートウェイは先に構成する必要があります。** サイト間 VPN ゲートウェイを追加する前に、まず ExpressRoute ゲートウェイを作成する必要があります。

## <a name="configuration-designs"></a>構成の設計
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>ExpressRoute のフェールオーバー パスとしてサイト間 VPN を構成する
ExpressRoute のバックアップとしてサイト間 VPN 接続を構成することができます。 これは、Azure のプライベート ピアリング パスにリンクされている仮想ネットワークにのみ適用されます。 Azure パブリックおよび Microsoft ピアリングを通じてアクセス可能なサービスの VPN ベースのフェールオーバー ソリューションはありません。 ExpressRoute 回線は常にプライマリ リンクです。 データは、ExpressRoute 回線で障害が発生した場合にのみ、サイト間 VPN パスを通過します。

> [!NOTE]
> 両方のルートが同じである場合は、ExpressRoute 回線がサイト間 VPN よりも優先されますが、Azure では最長プレフィックスの一致を使用してパケットの宛先へのルートを選択します。
> 
> 

![共存](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>ExpressRoute 経由で接続されていないサイトに接続するようにサイト間 VPN を構成する
サイト間 VPN 経由で Azure に直接接続するサイトと、ExpressRoute 経由で接続するサイトがあるネットワークを構成することができます。 

![共存](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> トランジット ルーターとして仮想ネットワークを構成することはできません。
> 
> 

## <a name="selecting-the-steps-to-use"></a>使用する手順の選択
共存できる接続を構成するために、選択できる 2 とおりの手順があります。 接続先にする既存の仮想ネットワークがある場合と、新しい仮想ネットワークを作成する場合とでは、選択できる構成手順が異なります。

* VNet がないので作成する必要がある。
  
    仮想ネットワークがまだない場合、この手順で、リソース マネージャーのデプロイ モデルを使用して新しい仮想ネットワークを作成し、新しい ExpressRoute 接続とサイト間 VPN 接続を作成する方法を説明します。 構成するには、この記事の「 [新しい仮想ネットワークおよび共存する接続を作成するには](#new)」の手順に従います。
* リソース マネージャーのデプロイ モデル VNet が既にある。
  
    既存のサイト間 VPN 接続または ExpressRoute 接続を使用して、仮想ネットワークを既に配置している場合があります。 「 [既存の VNet の共存する接続を構成するには](#add) 」では、ゲートウェイを削除し、新しい ExpressRoute 接続とサイト間 VPN 接続を作成する手順について説明します。 新しい接続を作成する場合は、非常に特殊な順序で完了する必要がありますので注意してください。 他の記事の手順を使用してゲートウェイと接続を作成しないでください。
  
    この手順では、共存できる接続を作成するために、ゲートウェイを削除する必要があるので、新しいゲートウェイを構成します。 これは、ゲートウェイを削除して接続を再作成する間、クロスプレミス接続でダウンタイムが発生しますが、VM やサービスを新しい仮想ネットワークに移行する必要がないことを意味します。 移行するように構成されている場合でも、VM やサービスは、ゲートウェイの構成中にロード バランサーを経由して通信できます。

## <a name="new"></a>新しい仮想ネットワークおよび共存する接続を作成するには
この手順では、VNet を作成し、共存するサイト間接続と ExpressRoute 接続を作成します。

1. Azure PowerShell コマンドレットの最新版をインストールする必要があります。 PowerShell コマンドレットのインストールの詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」を参照してください。 この構成に使用するコマンドレットは、使い慣れたコマンドレットとは少し異なる場合があることにご注意ください。 必ず、これらの手順で指定されているコマンドレットを使用してください。
2. アカウントにログインし、環境を設定します。
   
        login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
        $location = "Central US"
        $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location
3. ゲートウェイ サブネットを含む仮想ネットワークを作成します。 仮想ネットワーク構成の詳細については、「 [Azure Virtual Network configuration (Azure Virtual Network の構成)](../virtual-network/virtual-networks-create-vnet-arm-ps.md)」を参照してください。
   
   > [!IMPORTANT]
   > ゲートウェイ サブネットは /27 またはこれより短いプレフィックス (/26 や /25 など) にする必要があります。
   > 
   > 
   
    新しい VNet を作成します。
   
        $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 
   
    サブネットを追加します。
   
        Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   
    VNet 構成を保存します。
   
        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
4. <a name="gw"></a>ExpressRoute ゲートウェイを作成します。 ExpressRoute ゲートウェイの構成の詳細については、「 [ExpressRoute gateway configuration (ExpressRoute ゲートウェイの構成)](expressroute-howto-add-gateway-resource-manager.md)」を参照してください。 GatewaySKU には、*Standard*、*HighPerformance*、または *UltraPerformance* を指定します。
   
        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 
5. ExpressRoute ゲートウェイを ExpressRoute 回線にリンクします。 この手順が完了すると、オンプレミスのネットワークと Azure 間の接続が ExpressRoute 経由で確立されます。 リンク操作の詳細については、「 [Link VNets to ExpressRoute (ExpressRoute への Vnet のリンク)](expressroute-howto-linkvnet-arm.md)」を参照してください。
   
        $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
        New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
6. <a name="vpngw"></a>次に、サイト間 VPN ゲートウェイを作成します。 VPN ゲートウェイの構成の詳細については、[サイト間接続を使用した VNet の構成](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)に関するページを参照してください。 GatewaySKU には、*Standard*、*HighPerformance*、または *UltraPerformance* を指定します。 VpnType には、 *RouteBased*を指定する必要があります。
   
        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"
   
    Azure VPN ゲートウェイでは BGP がサポートされます。 次のコマンドで -EnableBgp を指定できます。
   
        $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard" -EnableBgp $true
   
    Azure で VPN ゲートウェイに使用される BGP ピアリング IP と AS 番号は、$azureVpn.BgpSettings.BgpPeeringAddress と $azureVpn.BgpSettings.Asn で確認できます。 詳細については、Azure VPN ゲートウェイの [BGP の構成](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md)に関するページをご覧ください。
7. ローカル サイト VPN ゲートウェイのエンティティを作成します。 このコマンドは、オンプレミスの VPN ゲートウェイを構成しません。 代わりに、パブリック IP やオンプレミスのアドレス空間などのローカル ゲートウェイ設定を指定できるため、Azure VPN ゲートウェイがこれに接続できます。
   
    ローカル VPN デバイスでサポートされるのが静的ルーティングのみの場合、静的ルートは次のように構成できます。
   
        $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   
    ローカル VPN デバイスで BGP がサポートされており、動的ルーティングを有効にする場合、ローカル VPN デバイスで使用される BGP ピアリング IP と AS 番号を把握しておく必要があります。
   
        $localVPNPublicIP = "<Public IP>"
        $localBGPPeeringIP = "<Private IP for the BGP session>"
        $localBGPASN = "<ASN>"
        $localAddressPrefix = $localBGPPeeringIP + "/32"
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
8. ローカルの VPN デバイスを構成して、新しい Azure VPN ゲートウェイに接続します。 VPN デバイス構成の詳細については、「 [VPN デバイスの構成](../vpn-gateway/vpn-gateway-about-vpn-devices.md)」を参照してください。
9. Azure のサイト間 VPN ゲートウェイをローカル ゲートウェイにリンクします。
   
        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>

## <a name="add"></a>既存の VNet の共存する接続を構成するには
既存の仮想ネットワークがある場合は、ゲートウェイ サブネットのサイズを確認します。 ゲートウェイ サブネットが /28 または /29 の場合、まず仮想ネットワーク ゲートウェイを削除してから、ゲートウェイ サブネットのサイズを増やしてください。 このセクションの手順で、その方法を説明します。

ゲートウェイ サブネットが /27 以上で、仮想ネットワークが ExpressRoute 経由で接続されている場合、以降の手順をスキップして、前のセクションの [手順 6、サイト間 VPN ゲートウェイの作成手順](#vpngw) に進みます。 

> [!NOTE]
> この既存のゲートウェイを削除すると、この構成で作業している間、ローカル環境から仮想ネットワークに接続できなくなります。 
> 
> 

1. Azure PowerShell コマンドレットの最新版をインストールする必要があります。 PowerShell コマンドレットのインストールの詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」を参照してください。 この構成に使用するコマンドレットは、使い慣れたコマンドレットとは少し異なる場合があることにご注意ください。 必ず、これらの手順で指定されているコマンドレットを使用してください。 
2. 既存の ExpressRoute またはサイト間 VPN ゲートウェイを削除します。 
   
        Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
3. ゲートウェイ サブネットを削除します。
   
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
        Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
4. /27 以上のゲートウェイ サブネットを追加します。
   
   > [!NOTE]
   > 仮想ネットワーク内の IP アドレスが不足していてゲートウェイ サブネットのサイズを増やせない場合は、IP アドレス空間を追加する必要があります。
   > 
   > 
   
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   
    VNet 構成を保存します。
   
        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
5. この時点では、VNet にゲートウェイがありません。 新しいゲートウェイを作成し、接続を完了するには、前述の [手順 4、ExpressRoute ゲートウェイの作成手順](#gw)に進みます。

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>VPN ゲートウェイにポイント対サイト構成を追加するには
共存設定で VPN ゲートウェイにポイント対サイト構成を追加するのには、次の手順に従います。

1. VPN クライアント アドレス プールを追加します。 
   
        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
2. VPN ゲートウェイ用に、Azure に VPN ルート証明書をアップロードします。 この例では、次の PowerShell コマンドレットを実行しているローカル コンピューターにルート証明書が保存されていることを前提としています。 
   
        $p2sCertFullName = "RootErVpnCoexP2S.cer"
        $p2sCertMatchName = "RootErVpnCoexP2S"
        $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
        if ($p2sCertToUpload.count -eq 1){
            write-host "cert found"
        } else {
            write-host "cert not found"
            exit
        } 
        $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

ポイント対サイト VPN の詳細については、 [ポイント対サイト接続の構成](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。


