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
ms.date: 06/26/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 07aa6946b9c3994c5afc5c88837f23567b95d8a5
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="about-vpn-gateway-configuration-settings"></a>VPN ゲートウェイの構成設定について

VPN ゲートウェイは、仮想ネットワークとオンプレミスの場所の間でパブリック接続を使って暗号化されたトラフィックを送信する仮想ネットワーク ゲートウェイの一種です。 VPN ゲートウェイを使用して、Azure バックボーン経由で仮想ネットワーク間にトラフィックを送信できます。

VPN Gateway の接続は複数のリソースの構成に依存し、それぞれに構成可能な設定が含まれます。 このセクションでは、Resource Manager デプロイメント モデル に作成される仮想ネットワークの VPN ゲートウェイに関するリソースと設定について説明します。 各接続ソリューションの説明とトポロジ ダイアグラムについては、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」をご覧ください。

## <a name="gwtype"></a>ゲートウェイの種類

各仮想ネットワークに配置できる各種類の仮想ネットワーク ゲートウェイは 1 つに限られています。 仮想ネットワーク ゲートウェイを作成するときは、ゲートウェイの種類が構成に対して適切であることを確認する必要があります。

-GatewayType に使用できる値は次のとおりです。

* Vpn
* ExpressRoute

VPN Gateway では、 `-GatewayType` *Vpn*にする必要があります。

例:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>ゲートウェイの SKU

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-the-gateway-sku"></a>ゲートウェイ SKU の構成

#### <a name="azure-portal"></a>Azure Portal

Azure Portal を使用して Resource Manager の仮想ネットワーク ゲートウェイを作成する場合、ドロップダウンを使用してワーク ゲートウェイ SKU を選択できます。 表示されるオプションは、選択したゲートウェイの種類と VPN の種類に対応します。

#### <a name="powershell"></a>PowerShell

次の PowerShell の例では、`-GatewaySku` が VpnGw1 として指定されています。

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="resize"></a>ゲートウェイ SKU の変更 (サイズ変更)

ゲートウェイ SKU をより強力な SKU にアップグレードする場合、`Resize-AzureRmVirtualNetworkGateway` PowerShell コマンドレットを使用できます。 このコマンドレットを使用してゲートウェイ SKU のサイズをダウングレードすることもできます。

次の PowerShell サンプルでは、ゲートウェイ SKU のサイズを VpnGw2 に変更しています。

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

## <a name="connectiontype"></a>接続の種類

Resource Manager デプロイメント モデルの各構成では、仮想ネットワーク ゲートウェイの接続の種類を指定する必要があります。 `-ConnectionType` に使用できる Resource Manager PowerShell 値は次のとおりです。

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

次の PowerShell の例では、接続の種類 *IPsec*を必要とする S2S 接続を作成しています。

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>VPN の種類

VPN Gateway 構成に対して仮想ネットワーク ゲートウェイを作成する場合は、VPN の種類を指定する必要があります。 選択する VPN の種類は、作成する接続トポロジによって異なります。 たとえば、P2S 接続の場合、VPN の種類は RouteBased である必要があります。 VPN の種類は、使用しているハードウェアによっても異なる場合があります。 S2S 構成では、VPN デバイスが必要です。 一部の VPN デバイスでは、特定の VPN の種類のみがサポートされます。

選択した VPN の種類は、作成するソリューションのすべての接続要件を満たす必要があります。 たとえば、同じ仮想ネットワークに対して、S2S VPN ゲートウェイ接続と P2S VPN ゲートウェイ接続を作成する必要がある場合は、VPN の種類として *RouteBased* を使用します。P2S では、VPN の種類が RouteBased である必要があるためです。 VPN デバイスで RouteBased VPN 接続がサポートされていることも、確認する必要があります。 

一度作成された仮想ネットワーク ゲートウェイの VPN の種類を変更することはできません。 仮想ネットワーク ゲートウェイを削除し、新しいものを作成する必要があります。 次の 2 つの種類の VPN があります。

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

次の PowerShell の例では、 `-VpnType` を *RouteBased*に指定しています。 ゲートウェイを作成するときは、-VpnType が構成に対して適切であることを確認する必要があります。

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>ゲートウェイの要件

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>ゲートウェイ サブネット

VPN ゲートウェイを作成する前に、ゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サブネットには、仮想ネットワーク ゲートウェイの VM とサービスが使用する IP アドレスが含まれます。 仮想ネットワーク ゲートウェイを作成すると、ゲートウェイ VM はゲートウェイ サブネットにデプロイされ、必要な VPN ゲートウェイ設定で構成されます。 ゲートウェイ サブネットには、追加の VM などをデプロイしないでください。 ゲートウェイ サブネットを正常に動作させるには、"GatewaySubnet" という名前を付ける必要があります。 ゲートウェイ サブネットに "GatewaySubnet" という名前を付けることで、これが仮想ネットワーク ゲートウェイの VM とサービスをデプロイするサブネットであることを Azure が認識できます。

ゲートウェイ サブネットを作成するときに、サブネットに含まれる IP アドレスの数を指定します。 ゲートウェイ サブネット内の IP アドレスは、ゲートウェイ VM とゲートウェイ サービスに割り当てられます。 一部の構成では、他の構成よりも多くの IP アドレスを割り当てる必要があります。 作成する構成の指示を調べて、作成するゲートウェイ サブネットがその要件を満たしていることを確認してください。 また、ゲートウェイ サブネットには、将来の構成の追加に対応できる十分な数の IP アドレスが含まれるようにしてください。 /29 のような小さいゲートウェイ サブネットを作成できますが、/28 以上 (/28、/27、/26 など) のゲートウェイ サブネットを作成することをお勧めします。 そうすれば、将来的に機能を追加する場合に、ゲートウェイを破棄し、ゲートウェイ サブネットを削除してから再作成して、より多くの IP アドレスを割り当てられるようにする必要がなくなります。

次の Resource Manager PowerShell の例では、GatewaySubnet という名前のゲートウェイ サブネットを示しています。 CIDR 表記で /27 を指定しています。これで既存のほとんどの構成で IP アドレスに十分対応できます。

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>ローカル ネットワーク ゲートウェイ

VPN Gateway 構成を作成する場合、ほとんどのローカル ネットワーク ゲートウェイはオンプレミスの場所になります。 クラシック デプロイ モデルでは、ローカル ネットワーク ゲートウェイはローカル サイトとして参照されます。 

ローカル ネットワーク ゲートウェイに名前とオンプレミス VPN デバイスのパブリック IP アドレスを指定し、オンプレミスの場所に存在するアドレスのプレフィックスを指定します。 Azure は、ネットワーク トラフィックの宛先アドレスのプレフィックスを参照して、ローカル ネットワーク ゲートウェイに指定された構成を確認し、それに応じてパケットをルーティングします。 また、VPN ゲートウェイ接続を使用している VNet 間の構成に対して、ローカル ネットワーク ゲートウェイを指定します。

次の PowerShell の例では、新しいローカル ネットワーク ゲートウェイを作成します。

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

場合によっては、ローカル ネットワーク ゲートウェイ設定を変更する必要があります。 たとえば、アドレス範囲を追加または変更する場合や、VPN デバイスの IP アドレスが変更された場合などです。 クラシック VNet の場合、[ローカル ネットワーク] ページのクラシック ポータルでこれらの設定を変更できます。 Resource Manager の場合、「 [PowerShell を使用したローカル ネットワーク ゲートウェイの設定の変更](vpn-gateway-modify-local-network-gateway.md)」を参照してください。

## <a name="resources"></a>REST API および PowerShell コマンドレット

VPN Gateway 構成に対して REST API、PowerShell コマンドレット、または Azure CLI を使用する場合のテクニカル リソースおよび具体的な構文の要件については、次のページを参照してください。

| **クラシック** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/azure#networking) |[PowerShell](/powershell/module/azurerm.network#vpn) |
| [REST API](https://msdn.microsoft.com/library/jj154113) |[REST API](/rest/api/network/virtualnetworkgateways) |
| サポートされていません | [Azure CLI](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>次のステップ

使用可能な接続構成の詳細については、「[VPN Gateway の構成設定について](vpn-gateway-about-vpngateways.md)」を参照してください。
