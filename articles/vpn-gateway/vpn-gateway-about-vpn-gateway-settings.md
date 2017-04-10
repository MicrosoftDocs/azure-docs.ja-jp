---
title: "クロスプレミス Azure 接続用の VPN Gateway の設定 | Microsoft Docs"
description: "Azure 仮想ネットワーク ゲートウェイ用の VPN Gateway の設定について説明します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: ae665bc5-0089-45d0-a0d5-bc0ab4e79899
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b902d2e79633959a6f76ddd45b1193177b0e8465
ms.openlocfilehash: 1ac5a78c8d9419e4c641bf66f8dac7aa8cbcd179
ms.lasthandoff: 02/14/2017


---
# <a name="about-vpn-gateway-configuration-settings"></a>VPN ゲートウェイの構成設定について
VPN ゲートウェイは、仮想ネットワークとオンプレミスの場所の間でパブリック接続を使って暗号化されたトラフィックを送信する仮想ネットワーク ゲートウェイの一種です。 VPN ゲートウェイを使用して、Azure バックボーン経由で仮想ネットワーク間にトラフィックを送信できます。

VPN Gateway の接続は複数のリソースの構成に依存し、それぞれに構成可能な設定が含まれます。 このセクションでは、Resource Manager デプロイメント モデル に作成される仮想ネットワークの VPN ゲートウェイに関するリソースと設定について説明します。 各接続ソリューションの説明とトポロジ ダイアグラムについては、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」をご覧ください。  

## <a name="a-namegwtypeagateway-types"></a><a name="gwtype"></a>ゲートウェイの種類
各仮想ネットワークに配置できる各種類の仮想ネットワーク ゲートウェイは&1; つに限られています。 仮想ネットワーク ゲートウェイを作成するときは、ゲートウェイの種類が構成に対して適切であることを確認する必要があります。

-GatewayType に使用できる値は次のとおりです。 

* Vpn
* ExpressRoute

VPN Gateway では、 `-GatewayType` *Vpn*にする必要があります。  

例:

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
    -VpnType RouteBased


## <a name="a-namegwskuagateway-skus"></a><a name="gwsku"></a>ゲートウェイの SKU
[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configuring-the-gateway-sku"></a>ゲートウェイの SKU を構成する
####<a name="specifying-the-gateway-sku-in-the-azure-portal"></a>Azure Portal でゲートウェイの SKU を指定する

Azure Portal を使用して Resource Manager の仮想ネットワーク ゲートウェイを作成する場合、ドロップダウンを使用してワーク ゲートウェイ SKU を選択できます。 表示されるオプションは、選択したゲートウェイの種類と VPN の種類に対応します。

たとえば、ゲートウェイの種類で "VPN" を選択し、VPN の種類で "Policy-based" を選択した場合、PolicyBased の VPN では、"Basic" SKU のみが使用可能なため、"Basic" SKU のみが表示されます。 "Route-based" を選択する場合は、Basic、Standard、および HighPerformance SKU から選択できます。 

####<a name="specifying-the-gateway-sku-using-powershell"></a>PowerShell を使用してゲートウェイの SKU を指定する

次の PowerShell の例では、 `-GatewaySku` が *Standard*として指定されています。

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
    -GatewayType Vpn -VpnType RouteBased

####<a name="changing-a-gateway-sku"></a>ゲートウェイの SKU を変更する

ゲートウェイ SKU をより強力な SKU にアップグレードする場合 (Basic/Standard から HighPerformance)、`Resize-AzureRmVirtualNetworkGateway` PowerShell コマンドレットを使用できます。 このコマンドレットを使用してゲートウェイ SKU のサイズをダウングレードすることもできます。

次の PowerShell サンプルでは、ゲートウェイ SKU のサイズを HighPerformance に変更しています。

    $gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

### <a name="estimated-aggregate-throughput-by-gateway-sku-and-type"></a>ゲートウェイの SKU および種類別の予測される合計スループット
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="a-nameconnectiontypeaconnection-types"></a><a name="connectiontype"></a>接続の種類
Resource Manager デプロイメント モデルの各構成では、仮想ネットワーク ゲートウェイの接続の種類を指定する必要があります。 `-ConnectionType` に使用できる Resource Manager PowerShell 値は次のとおりです。

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

次の PowerShell の例では、接続の種類 *IPsec*を必要とする S2S 接続を作成しています。

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="a-namevpntypeavpn-types"></a><a name="vpntype"></a>VPN の種類
VPN Gateway 構成に対して仮想ネットワーク ゲートウェイを作成する場合は、VPN の種類を指定する必要があります。 選択する VPN の種類は、作成する接続トポロジによって異なります。 たとえば、P2S 接続の場合、VPN の種類は RouteBased である必要があります。 VPN の種類は、使用しているハードウェアにも左右されます。 S2S 構成では、VPN デバイスが必要です。 一部の VPN デバイスでは、特定の VPN の種類のみがサポートされます。

選択した VPN の種類は、作成するソリューションのすべての接続要件を満たす必要があります。 たとえば、同じ仮想ネットワークに対して、S2S VPN ゲートウェイ接続と P2S VPN ゲートウェイ接続を作成する必要がある場合は、VPN の種類として *RouteBased* を使用します。P2S では、VPN の種類が RouteBased である必要があるためです。 VPN デバイスで RouteBased VPN 接続がサポートされていることも、確認する必要があります。 

一度作成された仮想ネットワーク ゲートウェイの VPN の種類を変更することはできません。 仮想ネットワーク ゲートウェイを削除し、新しいものを作成する必要があります。 次の&2; つの種類の VPN があります。

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

次の PowerShell の例では、 `-VpnType` を *RouteBased*に指定しています。 ゲートウェイを作成するときは、-VpnType が構成に対して適切であることを確認する必要があります。 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig `
    -GatewayType Vpn -VpnType RouteBased

## <a name="a-namerequirementsagateway-requirements"></a><a name="requirements"></a>ゲートウェイの要件
[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="a-namegwsubagateway-subnet"></a><a name="gwsub"></a>ゲートウェイ サブネット
VNet の仮想ネットワーク ゲートウェイを構成するために、ゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サブネットには、仮想ネットワーク ゲートウェイ サービスが使用する IP アドレスが含まれます。 ゲートウェイ サブネットを正常に動作させるには、 *GatewaySubnet* という名前を付ける必要があります。 この名前により、Azure はこのサブネットをゲートウェイに使用することを認識できます。

ゲートウェイ サブネットを作成する際、サブネットに含まれる IP アドレスの数を指定します。 ゲートウェイ サブネット内の IP アドレスは、ゲートウェイ サービスに割り当てられます。 一部の構成では、他の構成よりも多くの IP アドレスをゲートウェイ サービスに割り当てる必要があります。 ゲートウェイ サブネットには、将来の拡大や新しい接続構成の追加に対応できる十分な IP アドレスが含まれるようにしてください。 そのため、/29 のような小さいゲートウェイ サブネットを作成できますが、/28 以上 (/28、/27、/26 など) のゲートウェイ サブネットを作成することをお勧めします。 作成する構成の要件を調べて、ゲートウェイ サブネットがその要件を満たしていることを確認してください。

次の Resource Manager PowerShell の例では、GatewaySubnet という名前のゲートウェイ サブネットを示しています。 CIDR 表記で /27 を指定しています。これで既存のほとんどの構成で IP アドレスに十分対応できます。

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="a-namelngalocal-network-gateways"></a><a name="lng"></a>ローカル ネットワーク ゲートウェイ
VPN Gateway 構成を作成する場合、ほとんどのローカル ネットワーク ゲートウェイはオンプレミスの場所になります。 クラシック デプロイ モデルでは、ローカル ネットワーク ゲートウェイはローカル サイトとして参照されます。 

ローカル ネットワーク ゲートウェイに名前とオンプレミス VPN デバイスのパブリック IP アドレスを指定し、オンプレミスの場所に存在するアドレスのプレフィックスを指定します。 Azure は、ネットワーク トラフィックの宛先アドレスのプレフィックスを参照して、ローカル ネットワーク ゲートウェイに指定された構成を確認し、それに応じてパケットをルーティングします。 また、VPN ゲートウェイ接続を使用している VNet 間の構成に対して、ローカル ネットワーク ゲートウェイを指定します。

次の PowerShell の例では、新しいローカル ネットワーク ゲートウェイを作成します。

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

場合によっては、ローカル ネットワーク ゲートウェイ設定を変更する必要があります。 たとえば、アドレス範囲を追加または変更する場合や、VPN デバイスの IP アドレスが変更された場合などです。 クラシック VNet の場合、[ローカル ネットワーク] ページのクラシック ポータルでこれらの設定を変更できます。 Resource Manager の場合、「 [PowerShell を使用したローカル ネットワーク ゲートウェイの設定の変更](vpn-gateway-modify-local-network-gateway.md)」を参照してください。

## <a name="a-nameresourcesarest-apis-and-powershell-cmdlets"></a><a name="resources"></a>REST API および PowerShell コマンドレット
VPN Gateway 構成に対して REST API および PowerShell コマンドレットを使用する場合のテクニカル リソースおよび特定構文の要件については、次のページを参照してください。

| **クラシック** | **Resource Manager** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [REST API](https://msdn.microsoft.com/library/jj154113.aspx) |[REST API](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>次のステップ
使用可能な接続構成の詳細については、「 [VPN Gateway について](vpn-gateway-about-vpngateways.md) 」を参照してください。 


