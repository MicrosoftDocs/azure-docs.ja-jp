---
title: Azure Availability Zones にゾーン冗長仮想ネットワーク ゲートウェイを作成する | Microsoft Docs
description: Availability Zones に VPN Gateway と ExpressRoute ゲートウェイをデプロイする
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: cherylmc
ms.openlocfilehash: f2b459ccfd7e3f513b9b6526864321ce247ae7aa
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004518"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Azure Availability Zones にゾーン冗長仮想ネットワーク ゲートウェイを作成する

Azure Availability Zones に、VPN ゲートウェイと ExpressRoute ゲートウェイをデプロイできます。 これにより、仮想ネットワーク ゲートウェイに回復性、スケーラビリティ、高可用性が提供されます。 Azure Availability Zones にゲートウェイをデプロイすると、オンプレミス ネットワークの Azure への接続をゾーン レベルの障害から保護しながら、ゲートウェイを 1 つのリージョン内に物理的かつ論理的に分離できます。 詳細については、[ゾーン冗長仮想ネットワーク ゲートウェイ](about-zone-redundant-vnet-gateways.md)に関するページと [Azure Availability Zones](../availability-zones/az-overview.md)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

お使いのコンピューターにローカルにインストールされている PowerShell か、Azure Cloud Shell のいずれかを使用できます。 PowerShell をローカルにインストールして使用する場合、この機能には最新バージョンの PowerShell モジュールが必要です。

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>PowerShell をローカルで使用するには

Cloud Shell を使用するのではなくコンピューターで PowerShell をローカルに使用する場合は、PowerShell モジュール 1.0.0 以降をインストールする必要があります。 インストールされている PowerShell のバージョンを確認するには、次のコマンドを使用します。

```azurepowershell
Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
```

アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="variables"></a>1.変数を宣言する

この例の手順で使用する値は以下のとおりです。 さらに、一部の例では、手順で宣言された変数を使用します。 実際の環境でこれらの手順を使用するときは、必ずこれらの値を実際の値で置き換えてください。 場所を指定するときは、指定するリージョンがサポートされていることを確認してください。 詳細については、[FAQ](#faq) をご覧ください。

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>2.仮想ネットワークの作成

リソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

仮想ネットワークを作成します。

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>3.Add the gateway subnet

ゲートウェイ サブネットには、仮想ネットワーク ゲートウェイ サービスが使用する予約済み IP アドレスが含まれます。 次の例を使用して、ゲートウェイ サブネットを追加し設定します。

ゲートウェイ サブネットを追加します。

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

仮想ネットワークのゲートウェイ サブネット構成を設定します。

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="publicip"></a>4.パブリック IP アドレスの要求
 
この手順では、作成するゲートウェイに当てはまる説明を選択してください。 ゲートウェイをデプロイするゾーンの選択は、パブリック IP アドレスに対して指定されたゾーンに依存します。

### <a name="ipzoneredundant"></a>ゾーン冗長ゲートウェイの場合

**Standard** PublicIpaddress SKU を使用してパブリック IP アドレスを要求し、ゾーンは指定しません。 この場合、作成される Standard パブリック IP アドレスはゾーン冗長パブリック IP となります。   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>ゾーン ゲートウェイの場合

**Standard** PublicIpaddress SKU を使用してパブリック IP アドレスを要求し、 ゾーン (1、2、または 3) を指定します。 すべてのゲートウェイ インスタンスがこのゾーンにデプロイされます。

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>リージョン ゲートウェイの場合

**Basic** PublicIpaddress SKU を使用してパブリック IP アドレスを要求します。 この場合、ゲートウェイはリージョン ゲートウェイとしてデプロイされ、ゲートウェイにはゾーン冗長性が組み込まれません。 ゲートウェイ インスタンスはすべてのゾーンにそれぞれ作成されます。

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5.IP 構成を作成する

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6.ゲートウェイを作成する

仮想ネットワーク ゲートウェイを作成します。

### <a name="for-expressroute"></a>ExpressRoute の場合

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>VPN Gateway の場合

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="faq"></a>FAQ

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>これらの新しい SKU をデプロイすると何が変わりますか。

お客様の観点からは、ゾーン冗長性を備えたゲートウェイをデプロイできるようになります。 つまり、すべてのゲートウェイ インスタンスが複数の Azure Availability Zones にわたってデプロイされ、それぞれの Availability Zone が別々の障害ドメインおよび更新ドメインとなります。 これにより、ゲートウェイの信頼性、可用性、およびゾーン障害に対する回復性が向上します。

### <a name="can-i-use-the-azure-portal"></a>Azure Portal を使用することはできますか。

はい、Azure portal を使用して新しい SKU をデプロイできます。 ただし、これらの新しい SKU が表示されるのは、Azure Availability Zones がある Azure リージョンのみとなります。

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>新しい SKU を使用する場合、どのようなリージョンを利用できますか。

利用可能なリージョンの最新の一覧については、[Availability Zones](../availability-zones/az-overview.md#services-support-by-region) に関するページを参照してください。

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>既存の仮想ネットワーク ゲートウェイをゾーン冗長ゲートウェイまたはゾーン ゲートウェイに変更、移行、アップグレードすることはできますか。

既存の仮想ネットワーク ゲートウェイからゾーン冗長ゲートウェイまたはゾーン ゲートウェイへの移行は、現時点ではサポートされていません。 しかし、既存のゲートウェイを削除して、ゾーン冗長ゲートウェイまたはゾーン ゲートウェイを再作成することは可能です。

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>VPN ゲートウェイと Express Route ゲートウェイの両方を同じ仮想ネットワークにデプロイできますか。

同じ仮想ネットワーク内での VPN Gateway と Express Route ゲートウェイの共存がサポートされています。 ただし、ゲートウェイ サブネット用に、/27 IP アドレス範囲を予約する必要があります。
