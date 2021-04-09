---
title: 'Azure VPN Gateway: 構成設定'
description: Resource Manager デプロイ モデルで作成された仮想ネットワーク用の VPN Gateway のリソースと設定について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 1aba87b2139fb8a7d395fb3180d2074e47310fa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010814"
---
# <a name="about-vpn-gateway-configuration-settings"></a>VPN ゲートウェイの構成設定について

VPN ゲートウェイは、仮想ネットワークとオンプレミスの場所の間でパブリック接続を使って暗号化されたトラフィックを送信する仮想ネットワーク ゲートウェイの一種です。 VPN ゲートウェイを使用して、Azure バックボーン経由で仮想ネットワーク間にトラフィックを送信できます。

VPN Gateway の接続は複数のリソースの構成に依存し、それぞれに構成可能な設定が含まれます。 このセクションでは、Resource Manager デプロイ モデルに作成される仮想ネットワークの VPN ゲートウェイに関するリソースと設定について説明します。 各接続ソリューションの説明とトポロジ ダイアグラムについては、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」をご覧ください。

この記事の値は、VPN ゲートウェイ (-GatewayType Vpn を使用する仮想ネットワーク ゲートウェイ) に適用されます。 この記事では、すべてのゲートウェイの種類またはゾーン冗長ゲートウェイについては説明されていません。

* -GatewayType 'ExpressRoute' に適用される値については、「[ExpressRoute 用の仮想ネットワーク ゲートウェイについて](../expressroute/expressroute-about-virtual-network-gateways.md)」をご覧ください。

* ゾーン冗長ゲートウェイについては、[ゾーン冗長ゲートウェイについて](about-zone-redundant-vnet-gateways.md)の記事をご覧ください。

* 仮想 WAN については、「[Virtual WAN について](../virtual-wan/virtual-wan-about.md)」をご覧ください。

## <a name="gateway-types"></a><a name="gwtype"></a>ゲートウェイの種類

各仮想ネットワークに配置できる各種類の仮想ネットワーク ゲートウェイは 1 つに限られています。 仮想ネットワーク ゲートウェイを作成するときは、ゲートウェイの種類が構成に対して適切であることを確認する必要があります。

-GatewayType に使用できる値は次のとおりです。

* Vpn
* ExpressRoute

VPN Gateway では、`-GatewayType` *Vpn* にする必要があります。

例:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gateway-skus"></a><a name="gwsku"></a>ゲートウェイの SKU

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>ゲートウェイの SKU を構成する

**Azure Portal**

Azure Portal を使用して Resource Manager の仮想ネットワーク ゲートウェイを作成する場合、ドロップダウンを使用してワーク ゲートウェイ SKU を選択できます。 表示されるオプションは、選択したゲートウェイの種類と VPN の種類に対応します。

**PowerShell**

次の PowerShell の例では、`-GatewaySku` が VpnGw1 として指定されています。 PowerShell を使用してゲートウェイを作成する場合は、まず、IP 構成を作成してから、変数を使用して参照する必要があります。 この例では、構成変数は $gwipconfig となります。

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

**Azure CLI**

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizing-or-changing-a-sku"></a><a name="resizechange"></a>SKU のサイズ変更または変更

VPN ゲートウェイがあり、別のゲートウェイ SKU を使用する場合、使用可能なオプションは、ゲートウェイ SKU のサイズを変更するか、別の SKU に変更することです。 別のゲートウェイ SKU に変更する場合、既存のゲートウェイを完全に削除して、新しいゲートウェイを作成します。 ゲートウェイの作成には、最大で 45 分かかる場合があります。 これと比較して、ゲートウェイ SKU のサイズを変更する場合は、ゲートウェイを削除して再構築する必要がないため、ダウンタイムはあまりありません。 ゲートウェイ SKU を変更する代わりにサイズを変更するオプションが利用できる場合は、そのオプションを利用するようにしてください。 ただし、サイズ変更に関する以下の規則があります。

1. Basic SKU を除き、同じ世代 (Generation1 または Generation2) 内で VPN ゲートウェイ SKU を別の VPN ゲートウェイ SKU にサイズ変更することができます。 たとえば、Generation1 の VpnGw1 は Generation1 の VpnGw2 にサイズ変更できますが、Generation2 の VpnGw2 にはサイズ変更できません。
2. 古いゲートウェイ SKU では、Basic、Standard、HighPerformance SKU の間でサイズ変更できます。
3. Basic/Standard/HighPerformance SKU から VpnGw SKU にサイズ変更することは **できません**。 代わりに新しい SKU に[変更](#change)する必要があります。

#### <a name="to-resize-a-gateway"></a><a name="resizegwsku"></a>ゲートウェイのサイズを変更する

**Azure Portal**

[!INCLUDE [Resize a SKU - portal](../../includes/vpn-gateway-resize-gw-portal-include.md)]

**PowerShell**

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="to-change-from-an-old-legacy-sku-to-a-new-sku"></a><a name="change"></a>古い (レガシ) SKU から新しい SKU に変更する

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connection-types"></a><a name="connectiontype"></a>接続の種類

Resource Manager デプロイ モデルの各構成では、仮想ネットワーク ゲートウェイの接続の種類を指定する必要があります。 `-ConnectionType` に使用できる Resource Manager PowerShell 値は次のとおりです。

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

次の PowerShell の例では、接続の種類 *IPsec* を必要とする S2S 接続を作成しています。

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpn-types"></a><a name="vpntype"></a>VPN の種類

VPN Gateway 構成に対して仮想ネットワーク ゲートウェイを作成する場合は、VPN の種類を指定する必要があります。 選択する VPN の種類は、作成する接続トポロジによって異なります。 たとえば、P2S 接続の場合、VPN の種類は RouteBased である必要があります。 VPN の種類は、使用しているハードウェアによっても異なる場合があります。 S2S 構成では、VPN デバイスが必要です。 一部の VPN デバイスでは、特定の VPN の種類のみがサポートされます。

選択した VPN の種類は、作成するソリューションのすべての接続要件を満たす必要があります。 たとえば、同じ仮想ネットワークに対して、S2S VPN ゲートウェイ接続と P2S VPN ゲートウェイ接続を作成する必要がある場合は、VPN の種類として *RouteBased* を使用します。P2S では、VPN の種類が RouteBased である必要があるためです。 VPN デバイスで RouteBased VPN 接続がサポートされていることも、確認する必要があります。 

一度作成された仮想ネットワーク ゲートウェイの VPN の種類を変更することはできません。 仮想ネットワーク ゲートウェイを削除し、新しいものを作成する必要があります。 次の 2 つの種類の VPN があります。

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

次の PowerShell の例では、 `-VpnType` を *RouteBased* に指定しています。 ゲートウェイを作成するときは、-VpnType が構成に対して適切であることを確認する必要があります。

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="gateway-requirements"></a><a name="requirements"></a>ゲートウェイの要件

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gateway-subnet"></a><a name="gwsub"></a>ゲートウェイ サブネット

VPN ゲートウェイを作成する前に、ゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サブネットには、仮想ネットワーク ゲートウェイの VM とサービスが使用する IP アドレスが含まれます。 仮想ネットワーク ゲートウェイを作成すると、ゲートウェイ VM はゲートウェイ サブネットにデプロイされ、必要な VPN ゲートウェイ設定で構成されます。 ゲートウェイ サブネットには、追加の VM などをデプロイしないでください。 ゲートウェイ サブネットを正常に動作させるには、"GatewaySubnet" という名前を付ける必要があります。 ゲートウェイ サブネットに "GatewaySubnet" という名前を付けることで、これが仮想ネットワーク ゲートウェイの VM とサービスをデプロイするサブネットであることを Azure が認識できます。

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

ゲートウェイ サブネットを作成するときに、サブネットに含まれる IP アドレスの数を指定します。 ゲートウェイ サブネット内の IP アドレスは、ゲートウェイ VM とゲートウェイ サービスに割り当てられます。 一部の構成では、他の構成よりも多くの IP アドレスを割り当てる必要があります。 

ゲートウェイ サブネットのサイズを計画する際は、作成する構成に関するドキュメントを参照してください。 たとえば、ExpressRoute/VPN Gateway が共存する構成には、他のほとんどの構成より大規模なゲートウェイ サブネットが必要です。 また、ゲートウェイ サブネットには、将来の構成の追加に対応できる十分な数の IP アドレスが含まれるようにしてください。 /29 のような小さいゲートウェイ サブネットを作成できますが、使用できるアドレス空間がある場合は、/27 以上 (/27、/26 など) のゲートウェイ サブネットを作成することをお勧めします。 このゲートウェイ サブネットは、ほとんどの構成に対応します。

次の Resource Manager PowerShell の例では、GatewaySubnet という名前のゲートウェイ サブネットを示しています。 CIDR 表記で /27 を指定しています。これで既存のほとんどの構成で IP アドレスに十分対応できます。

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="local-network-gateways"></a><a name="lng"></a>ローカル ネットワーク ゲートウェイ

ローカル ネットワーク ゲートウェイは、仮想ネットワーク ゲートウェイとは異なります。 VPN ゲートウェイの構成を作成する場合、通常、ローカル ネットワーク ゲートウェイはオンプレミス ネットワークとそれに対応する VPN デバイスを表します。 クラシック デプロイ モデルでは、ローカル ネットワーク ゲートウェイはローカル サイトとして参照されます。

ローカル ネットワーク ゲートウェイに名前とオンプレミス VPN デバイスのパブリック IP アドレスまたは完全修飾ドメイン名 (FQDN) を指定し、オンプレミスの場所に存在するアドレスのプレフィックスを指定します。 Azure は、ネットワーク トラフィックの宛先アドレスのプレフィックスを参照して、ローカル ネットワーク ゲートウェイに指定された構成を確認し、それに応じてパケットをルーティングします。 VPN デバイスで Border Gateway Protocol (BGP) を使用する場合、VPN デバイスの BGP ピア IP アドレスとオンプレミス ネットワークの自律システム番号 (ASN) を指定します。 また、VPN ゲートウェイ接続を使用している VNet 間の構成に対して、ローカル ネットワーク ゲートウェイを指定します。

次の PowerShell の例では、新しいローカル ネットワーク ゲートウェイを作成します。

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

場合によっては、ローカル ネットワーク ゲートウェイ設定を変更する必要があります。 たとえば、アドレス範囲を追加または変更する場合や、VPN デバイスの IP アドレスが変更された場合などです。 「[PowerShell を使用したローカル ネットワーク ゲートウェイの設定の変更](vpn-gateway-modify-local-network-gateway.md)」を参照してください。

## <a name="rest-apis-powershell-cmdlets-and-cli"></a><a name="resources"></a>REST API、PowerShell コマンドレット、および CLI

VPN Gateway 構成に対して REST API、PowerShell コマンドレット、または Azure CLI を使用する場合のテクニカル リソースおよび具体的な構文の要件については、次のページを参照してください。

| **クラシック** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/az.network/#networking) |[PowerShell](/powershell/module/az.network#vpn) |
| [REST API](/previous-versions/azure/reference/jj154113(v=azure.100)) |[REST API](/rest/api/network/virtualnetworkgateways) |
| サポートされていません | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>次のステップ

使用可能な接続構成の詳細については、「[VPN Gateway の構成設定について](vpn-gateway-about-vpngateways.md)」を参照してください。