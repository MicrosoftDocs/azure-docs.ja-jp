---
title: 'ExpressRoute とサイト間 VPN の接続を構成する - 共存: クラシック:Azure | Microsoft Docs'
description: この記事では、クラシック デプロイ モデルにおいて、共存できる ExpressRoute 接続とサイト間 VPN 接続を構成する手順について説明します。
documentationcenter: na
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 70e7c689acac094890545ac1e65374e9377a0be0
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084211"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-classic"></a>ExpressRoute 接続とサイト間接続の共存の構成 (クラシック)
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - クラシック](expressroute-howto-coexist-classic.md)
> 
> 

この記事は、共存する ExpressRoute とサイト間 VPN の接続を構成するのに役立ちます。 サイト間 VPN と ExpressRoute が構成可能な場合、いくつかの利点があります。 ExpressRoute 用にセキュリティで保護されたフェールオーバー パスとしてサイト間 VPN を構成したり、サイト間 VPN を使用して、ExpressRoute 経由で接続されていないサイトに接続したりできます。 この記事では、両方のシナリオを構成する手順について説明します。 この記事は、クラシック デプロイ モデルに適用されます。 この構成は、ポータルで使用できません。

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Azure のデプロイ モデルについて**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> ExpressRoute 回線を事前に構成してから、以下の手順に従ってください。 以下の手順に従う前に、必ず、[ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md)と[ルーティングの構成](expressroute-howto-routing-classic.md)に関するガイドに従ってください。
> 
> 

## <a name="limits-and-limitations"></a>制限と制限事項
* **トランジット ルーティングはサポートされていません。** サイト間 VPN 経由で接続されたローカル ネットワークと ExpressRoute 経由で接続されたローカル ネットワーク間で (Azure 経由で) ルーティングすることはできません。
* **ポイント対サイトはサポートされていません。** ExpressRoute に接続されているのと同じ VNet に対しては、ポイント対サイト VPN 接続を有効にできません。 ポイント対サイト VPN と ExpressRoute を同じ VNet に共存させることはできません。
* **サイト間 VPN ゲートウェイでは強制トンネリングを有効にできません。** できることは、すべてのインターネットへのトラフィックを ExpressRoute 経由でオンプレミスのネットワークに "強制的に" 戻すことのみです。
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

![共存](media/expressroute-howto-coexist-classic/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>ExpressRoute 経由で接続されていないサイトに接続するようにサイト間 VPN を構成する
サイト間 VPN 経由で Azure に直接接続するサイトと、ExpressRoute 経由で接続するサイトがあるネットワークを構成することができます。 

![共存](media/expressroute-howto-coexist-classic/scenario2.jpg)

> [!NOTE]
> トランジット ルーターとして仮想ネットワークを構成することはできません。
> 
> 

## <a name="selecting-the-steps-to-use"></a>使用する手順の選択
共存できる接続を構成するために、選択できる 2 とおりの手順があります。 接続先にする既存の仮想ネットワークがある場合と、新しい仮想ネットワークを作成する場合とでは、選択できる構成手順が異なります。

* VNet がないので作成する必要がある。
  
    仮想ネットワークがまだない場合、この手順で、クラシック デプロイ モデルを使用して新しい仮想ネットワークを作成し、新しい ExpressRoute 接続とサイト間 VPN 接続を作成する方法を説明します。 構成するには、この記事の「 [新しい仮想ネットワークおよび共存する接続を作成するには](#new)」の手順に従います。
* クラシック デプロイ モデル VNet が既にある。
  
    既存のサイト間 VPN 接続または ExpressRoute 接続を使用して、仮想ネットワークを既に配置している場合があります。 記事のセクション「[既存の VNet の共存する接続を構成するには](#add)」では、ゲートウェイを削除し、新しい ExpressRoute 接続とサイト間 VPN 接続を作成する手順について説明します。 新しい接続を作成する場合は、非常に特殊な順序で完了する必要がありますので注意してください。 他の記事の手順を使用してゲートウェイと接続を作成しないでください。
  
    この手順では、共存できる接続を作成するために、ゲートウェイを削除する必要があるので、新しいゲートウェイを構成します。 これは、ゲートウェイを削除して接続を再作成する間、クロスプレミス接続でダウンタイムが発生しますが、VM やサービスを新しい仮想ネットワークに移行する必要がないことを意味します。 移行するように構成されている場合でも、VM やサービスは、ゲートウェイの構成中にロード バランサーを経由して通信できます。

## <a name="new"></a>新しい仮想ネットワークおよび共存する接続を作成するには
この手順では、VNet を作成し、共存するサイト間接続と ExpressRoute 接続を作成します。

1. Azure PowerShell コマンドレットの最新版をインストールする必要があります。 PowerShell コマンドレットのインストールの詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview) 」を参照してください。 この構成に使用するコマンドレットは、使い慣れたコマンドレットとは少し異なる場合があることにご注意ください。 必ず、これらの手順で指定されているコマンドレットを使用してください。 
2. 仮想ネットワークのスキーマを作成します。 構成スキーマの詳細については、「 [Azure Virtual Network の構成スキーマ](https://msdn.microsoft.com/library/azure/jj157100.aspx)」を参照してください。
   
    スキーマを作成する場合は、次の値を使用していることを確認します。
   
   * 仮想ネットワークのゲートウェイ サブネットは /27 またはこれより短いプレフィックス (/26 や /25 など) にする必要があります。
   * ゲートウェイ接続の種類は "Dedicated" です。
     
             <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
               <AddressSpace>
                 <AddressPrefix>10.17.159.192/26</AddressPrefix>
               </AddressSpace>
               <Subnets>
                 <Subnet name="Subnet-1">
                   <AddressPrefix>10.17.159.192/27</AddressPrefix>
                 </Subnet>
                 <Subnet name="GatewaySubnet">
                   <AddressPrefix>10.17.159.224/27</AddressPrefix>
                 </Subnet>
               </Subnets>
               <Gateway>
                 <ConnectionsToLocalNetwork>
                   <LocalNetworkSiteRef name="MyLocalNetwork">
                     <Connection type="Dedicated" />
                   </LocalNetworkSiteRef>
                 </ConnectionsToLocalNetwork>
               </Gateway>
             </VirtualNetworkSite>
3. xml スキーマ ファイルを作成して構成したら、ファイルをアップロードします。 これにより、仮想ネットワークが作成されます。
   
    次のコマンドレットを使用してファイルをアップロードし、値を自身の値に置き換えます。
   
        Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'
4. <a name="gw"></a>ExpressRoute ゲートウェイを作成します。 GatewaySKU を *Standard*、*HighPerformance*、または *UltraPerformance* に指定し、GatewayType を *DynamicRouting* に指定します。
   
    次のサンプルを使用して、自身の値に置き換えます。
   
        New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance
5. ExpressRoute ゲートウェイを ExpressRoute 回線にリンクします。 この手順が完了すると、オンプレミスのネットワークと Azure 間の接続が ExpressRoute 経由で確立されます。
   
        New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET
6. <a name="vpngw"></a>次に、サイト間 VPN ゲートウェイを作成します。 GatewaySKU を *Standard*、*HighPerformance*、または *UltraPerformance* にし、GatewayType を *DynamicRouting* にする必要があります。
   
        New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance
   
    ゲートウェイ ID とパブリック IP を含む仮想ネットワーク ゲートウェイ設定を取得するには、`Get-AzureVirtualNetworkGateway` コマンドレットを使用します。
   
        Get-AzureVirtualNetworkGateway
   
        GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
        GatewayName          : S2SVPN
        LastEventData        :
        GatewayType          : DynamicRouting
        LastEventTimeStamp   : 5/29/2015 4:41:41 PM
        LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
        LastEventID          : 23002
        State                : Provisioned
        VIPAddress           : 104.43.x.y
        DefaultSite          :
        GatewaySKU           : HighPerformance
        Location             :
        VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
        SubnetId             :
        EnableBgp            : False
        OperationDescription : Get-AzureVirtualNetworkGateway
        OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
        OperationStatus      : Succeeded
7. ローカル サイト VPN ゲートウェイのエンティティを作成します。 このコマンドは、オンプレミスの VPN ゲートウェイを構成しません。 代わりに、パブリック IP やオンプレミスのアドレス空間などのローカル ゲートウェイ設定を指定できるため、Azure VPN ゲートウェイがこれに接続できます。
   
   > [!IMPORTANT]
   > サイト間 VPN のローカル サイトは、netcfg で定義されていません。 代わりに、このコマンドレットを使用してローカル サイトのパラメーターを指定する必要があります。 ポータルまたは netcfg ファイルを使用して、これを定義することはできません。
   > 
   > 
   
    次のサンプルを使用して、自身の値に置き換えます。
   
        New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>
   
   > [!NOTE]
   > ローカル ネットワークに複数のルートがある場合は、それらすべてを配列として渡すことができます。  $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")  
   > 
   > 

    ゲートウェイ ID とパブリック IP を含む仮想ネットワーク ゲートウェイ設定を取得するには、`Get-AzureVirtualNetworkGateway` コマンドレットを使用します。 次の例を参照してください。

        Get-AzureLocalNetworkGateway

        GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
        GatewayName          : MyLocalNetwork
        IpAddress            : 23.39.x.y
        AddressSpace         : {10.1.2.0/24}
        OperationDescription : Get-AzureLocalNetworkGateway
        OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
        OperationStatus      : Succeeded


1. ローカルの VPN デバイスを構成して新しいゲートウェイに接続します。 VPN デバイスを構成するときに、手順 6 で取得した情報を使用します。 VPN デバイス構成の詳細については、「 [VPN デバイスの構成](../vpn-gateway/vpn-gateway-about-vpn-devices.md)」を参照してください。
2. Azure のサイト間 VPN ゲートウェイをローカル ゲートウェイにリンクします。
   
    この例では、connectedEntityId がローカル ゲートウェイ ID です。これは、`Get-AzureLocalNetworkGateway` を実行すると見つけることができます。 `Get-AzureVirtualNetworkGateway` コマンドレットを使用すると、virtualNetworkGatewayId を見つけることができます。 この手順の後に、ローカル ネットワークと Azure 間の接続がサイト間 VPN 接続経由で確立されます。

        New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>

## <a name="add"></a>既存の VNet で共存する接続を構成するには
既存の仮想ネットワークがある場合は、ゲートウェイ サブネットのサイズを確認します。 ゲートウェイ サブネットが /28 または /29 の場合、まず仮想ネットワーク ゲートウェイを削除してから、ゲートウェイ サブネットのサイズを増やしてください。 このセクションの手順で、その方法を説明します。

ゲートウェイ サブネットが /27 以上で、仮想ネットワークが ExpressRoute 経由で接続されている場合、以降の手順をスキップして、前のセクションの [手順 6、サイト間 VPN ゲートウェイの作成手順](#vpngw) に進みます。

> [!NOTE]
> この既存のゲートウェイを削除すると、この構成で作業している間、ローカル環境から仮想ネットワークに接続できなくなります。
> 
> 

1. Azure リソース マネージャー PowerShell コマンドレットの最新版をインストールする必要があります。 PowerShell コマンドレットのインストールの詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview) 」を参照してください。 この構成に使用するコマンドレットは、使い慣れたコマンドレットとは少し異なる場合があることにご注意ください。 必ず、これらの手順で指定されているコマンドレットを使用してください。 
2. 既存の ExpressRoute またはサイト間 VPN ゲートウェイを削除します。 次のコマンドレットを使用して、自身の値に置き換えます。
   
        Remove-AzureVNetGateway –VnetName MyAzureVNET
3. 仮想ネットワークのスキーマをエクスポートします。 次の PowerShell コマンドレットを使用して、自身の値に置き換えます。
   
        Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”
4. ゲートウェイ サブネットが /27 またはこれより短いプレフィックス (/26 や /25 など) になるように、ネットワーク構成ファイルのスキーマを編集します。 次の例を参照してください。 
   
   > [!NOTE]
   > 仮想ネットワーク内の IP アドレスが不足していてゲートウェイ サブネットのサイズを増やせない場合は、IP アドレス空間を追加する必要があります。 構成スキーマの詳細については、「 [Azure Virtual Network の構成スキーマ](https://msdn.microsoft.com/library/azure/jj157100.aspx)」を参照してください。
   > 
   > 
   
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>
5. 以前のゲートウェイがサイト間 VPN であった場合は、接続の種類を **Dedicated**に変更する必要もあります。
   
                 <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
6. この時点では、VNet にゲートウェイがありません。 新しいゲートウェイを作成し、接続を完了するには、前述の [手順 4、ExpressRoute ゲートウェイの作成手順](#gw)に進みます。

## <a name="next-steps"></a>次の手順
ExpressRoute の詳細については、「 [ExpressRoute の FAQ](expressroute-faqs.md)

