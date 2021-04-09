---
title: オンプレミス ネットワークから Azure Virtual Network への接続:サイト間 VPN:PowerShell
description: PowerShell を使用し、オンプレミスのネットワークから Azure 仮想ネットワークへのパブリック インターネット経由の IPsec サイト間 VPN Gateway 接続を作成します。
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 1488aa6f48c05a8c2dfa2c6162c1bd1df35d4f58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380496"
---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>PowerShell を使用してサイト間 VPN 接続を備えた VNet を作成する

この記事では、PowerShell を使用して、オンプレミス ネットワークから VNet へのサイト間 VPN Gateway 接続を作成する方法について説明します。 この記事の手順は、Resource Manager デプロイ モデルに適用されます。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [Azure Portal](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (クラシック)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

サイト間 VPN Gateway 接続は、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介してオンプレミス ネットワークを Azure 仮想ネットワークに接続するために使用します。 この種類の接続では、外部接続用パブリック IP アドレスが割り当てられていてるオンプレミスの VPN デバイスが必要です。 VPN Gateway の詳細については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。

![クロスプレミスのサイト間 VPN Gateway 接続の図](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>開始する前に

構成を開始する前に、以下の条件を満たしていることを確認します。

* 互換性のある VPN デバイスがあり、デバイスを構成できる人員がいることを確認します。 互換性のある VPN デバイスとデバイスの構成の詳細については、[VPN デバイスの概要](vpn-gateway-about-vpn-devices.md)に関する記事を参照してください。
* VPN デバイスの外部接続用パブリック IPv4 アドレスがあることを確認します。
* オンプレミス ネットワーク構成の IP アドレス範囲を把握していない場合は、詳細な情報を把握している担当者と協力して作業を行ってください。 この構成を作成する場合は、Azure がオンプレミスの場所にルーティングする IP アドレス範囲のプレフィックスを指定する必要があります。 オンプレミス ネットワークのサブネットと接続先の仮想ネットワーク サブネットが重複しないようにしなければなりません。

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>値の例

この記事の例では、次の値を使用します。 この値を使用して、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。

```
#Example values

VnetName                = VNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.1.0.0/16 
SubnetName              = Frontend 
Subnet                  = 10.1.0.0/24 
GatewaySubnet           = 10.1.255.0/27
LocalNetworkGatewayName = Site1
LNG Public IP           = <On-premises VPN device IP address> 
Local Address Prefixes  = 10.101.0.0/24, 10.101.1.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWPIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite1

```

## <a name="1-create-a-virtual-network-and-a-gateway-subnet"></a><a name="VNet"></a>1.仮想ネットワークとゲートウェイ サブネットを作成する

まだ仮想ネットワークがない場合は作成します。 仮想ネットワークを作成する際は、指定したアドレス空間がオンプレミス ネットワーク内に存在するあらゆるアドレス空間と重複していないことを確認します。 

>[!NOTE]
>この VNet をオンプレミスの場所に接続するには、オンプレミスのネットワーク管理者と調整を行って、この仮想ネットワーク専用に使用できる IP アドレスの範囲を見つけ出す必要があります。 VPN 接続の両側に重複するアドレス範囲が存在する場合、トラフィックが期待どおりにルーティングされない可能性があります。 また、この VNet を別の VNet に接続する場合、アドレス空間を他の VNet と重複させることはできません。 したがって、慎重にネットワーク構成を計画してください。
>
>

### <a name="about-the-gateway-subnet"></a>ゲートウェイ サブネットについて

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="create-a-virtual-network-and-a-gateway-subnet"></a><a name="vnet"></a>仮想ネットワークとゲートウェイ サブネットを作成する

次の例では、仮想ネットワークとゲートウェイ サブネットを作成します。 ゲートウェイ サブネットを追加する必要がある仮想ネットワークが既にある場合は、「[作成済みの仮想ネットワークにゲートウェイ サブネットを追加するには](#gatewaysubnet)」を参照してください。

リソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location 'East US'
```

仮想ネットワークを作成します。

1. 変数を設定します。

   ```azurepowershell-interactive
   $subnet1 = New-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27
   $subnet2 = New-AzVirtualNetworkSubnetConfig -Name 'Frontend' -AddressPrefix 10.1.0.0/24
   ```
2. VNet を作成します。

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1 `
   -Location 'East US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet1, $subnet2
   ```

### <a name="to-add-a-gateway-subnet-to-a-virtual-network-you-have-already-created"></a><a name="gatewaysubnet"></a>作成済みの仮想ネットワークにゲートウェイ サブネットを追加するには

既に仮想ネットワークがあるものの、ゲートウェイ サブネットを追加する必要がある場合は、このセクションの手順を使用します。

1. 変数を設定します。

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
   ```
2. ゲートウェイ サブネットを作成します。

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
   ```
3. 構成を設定します。

   ```azurepowershell-interactive
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```

## <a name="2-create-the-local-network-gateway"></a>2.<a name="localnet"></a>ローカル ネットワーク ゲートウェイを作成する

ローカル ネットワーク ゲートウェイ (LNG) は通常、オンプレミスの場所を指します。 これは仮想ネットワーク ゲートウェイと同じではありません。 サイトに Azure が参照できる名前を付け、接続を作成するオンプレミス VPN デバイスの IP アドレスを指定します。 また、VPN ゲートウェイを介して VPN デバイスにルーティングされる IP アドレスのプレフィックスも指定します。 指定するアドレスのプレフィックスは、オンプレミス ネットワークのプレフィックスです。 オンプレミス ネットワークが変わった場合は、プレフィックスを簡単に更新できます。

次の値を使用します。

* *GatewayIPAddress* は、オンプレミス VPN デバイスの IP アドレスです。
* *AddressPrefix* は、オンプレミスのアドレス空間です。

1 つのアドレス プレフィックスを含むローカル ネットワーク ゲートウェイを追加するには

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.101.0.0/24'
  ```

複数のアドレス プレフィックスを含むローカル ネットワーク ゲートウェイを追加するには

  ```azurepowershell-interactive
  New-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
  ```

ローカル ネットワーク ゲートウェイ用の IP アドレスのプレフィックスを変更するには

ローカル ネットワーク ゲートウェイのプレフィックスは変わる場合があります。 IP アドレス プレフィックスを変更する手順は、VPN ゲートウェイ接続を作成したかどうかによって異なります。 「 [ローカル ネットワーク ゲートウェイ用の IP アドレスのプレフィックスを変更するには](#modify) 」セクションを参照してください。

## <a name="3-request-a-public-ip-address"></a><a name="PublicIP"></a>3.パブリック IP アドレスを要求する

VPN ゲートウェイには、パブリック IP アドレスが必要です。 これにはまず IP アドレスのリソースを要求したうえで、仮想ネットワーク ゲートウェイの作成時にそのリソースを参照する必要があります。 IP アドレスは、VPN ゲートウェイの作成時にリソースに対して動的に割り当てられます。 

VPN Gateway では現在、パブリック IP アドレスの "*動的*" 割り当てのみサポートしています。 静的パブリック IP アドレスの割り当てを要求することはできません。 もっとも、VPN ゲートウェイに割り当てられた IP アドレスが後から変わることは基本的にありません。 パブリック IP アドレスが変わるのは、ゲートウェイが削除され、再度作成されたときのみです。 VPN ゲートウェイのサイズ変更、リセット、その他の内部メンテナンス/アップグレードでは、IP アドレスは変わりません。

仮想ネットワークの VPN ゲートウェイに割り当てるパブリック IP アドレスを要求します。

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="4-create-the-gateway-ip-addressing-configuration"></a><a name="GatewayIPConfig"></a>4.ゲートウェイ IP アドレス指定の構成を作成する

ゲートウェイの構成で、使用するサブネット ('GatewaySubnet') とパブリック IP アドレスを定義します。 次の例を使用して、ゲートウェイの構成を作成します。

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="5-create-the-vpn-gateway"></a><a name="CreateGateway"></a>5.VPN ゲートウェイを作成する

仮想ネットワーク VPN ゲートウェイを作成します。

次の値を使用します。

* サイト間構成の *-GatewayType* は *Vpn* です。 ゲートウェイの種類は常に、実装する構成に対応するものとなります。 たとえば、他のゲートウェイ構成では、-GatewayType に ExpressRoute を必要とする場合があります。
* *-VpnType* には、*RouteBased* (ドキュメントによっては動的ゲートウェイと呼ばれます) または *PolicyBased* (ドキュメントによっては静的ゲートウェイと呼ばれます) を指定できます。 VPN ゲートウェイの種類については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。
* 使用するゲートウェイ SKU を選択します。 特定の SKU には構成の制限があります。 詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。 VPN ゲートウェイを作成するときに -GatewaySku に関するエラーが発生した場合は、PowerShell コマンドレットの最新バージョンがインストールされていることを確認してください。

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

このコマンドの実行後、ゲートウェイの構成が完了するまでには、最大で 45 分かかります。

## <a name="6-configure-your-vpn-device"></a><a name="ConfigureVPNDevice"></a>6.VPN デバイスの構成

オンプレミス ネットワークとのサイト間接続には VPN デバイスが必要です。 この手順では、VPN デバイスを構成します。 VPN デバイスを構成する場合は、次の項目が必要です。

- 共有キー。 サイト間 VPN 接続を作成するときにも、これと同じ共有キーを指定します。 ここで紹介している例では、基本的な共有キーを使用しています。 実際には、もっと複雑なキーを生成して使用することをお勧めします。
- 仮想ネットワーク ゲートウェイのパブリック IP アドレス。 パブリック IP アドレスは、Azure Portal、PowerShell、または CLI を使用して確認できます。 PowerShell を使用して仮想ネットワーク ゲートウェイのパブリック IP アドレスを検索するには、次の例を使用します。 この例では、VNet1GWPIP は前の手順で作成したパブリック IP アドレス リソースの名前です。

  ```azurepowershell-interactive
  Get-AzPublicIpAddress -Name VNet1GWPIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="7-create-the-vpn-connection"></a><a name="CreateConnection"></a>7.VPN 接続を作成する

次に、仮想ネットワーク ゲートウェイと VPN デバイスの間にサイト間 VPN 接続を作成します。 サンプルの値は必ず実際の値に変更してください。 共有キーは、VPN デバイスの構成に使用したものと同じ値にする必要があります。 サイト間接続の "-ConnectionType" は **IPsec** です。

1. 変数を設定します。
   ```azurepowershell-interactive
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```

2. 接続を作成します。
   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1 `
   -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

しばらくすると、接続が確立されます。

## <a name="8-verify-the-vpn-connection"></a><a name="toverify"></a>8.VPN 接続の確認

VPN 接続を検証する方法はいくつかあります。

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>仮想マシンに接続するには

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]


## <a name="to-modify-ip-address-prefixes-for-a-local-network-gateway"></a><a name="modify"></a>ローカル ネットワーク ゲートウェイの IP アドレスのプレフィックスを変更するには

オンプレミスの場所にルーティングする IP アドレス プレフィックスが変更された場合には、ローカル ネットワーク ゲートウェイを変更できます。 このような例を使用するときは、実際の環境に合わせて値を変更してください。

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address-for-a-local-network-gateway"></a><a name="modifygwipaddress"></a>ローカル ネットワーク ゲートウェイの IP アドレスを変更するには

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="to-delete-a-gateway-connection"></a><a name="deleteconnection"></a>ゲートウェイ接続を削除するには

接続の名前がわからない場合は、"Get-AzVirtualNetworkGatewayConnection" コマンドレットを使用して調べることができます。

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
-ResourceGroupName TestRG1
```

## <a name="next-steps"></a>次のステップ

*  接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](../index.yml) に関するページを参照してください。
* BGP の詳細については、[BGP の概要](vpn-gateway-bgp-overview.md)に関する記事と [BGP の構成方法](vpn-gateway-bgp-resource-manager-ps.md)に関する記事を参照してください。
* Azure Resource Manager テンプレートを使用してサイト間 VPN 接続を作成する方法については、「[Create a Site-to-Site VPN Connection (サイト間 VPN 接続の作成)](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/)」を参照してください。
* Azure Resource Manager テンプレートを使用して VNet 間 VPN 接続を作成する方法については、「[Deploy HBase geo replication (HBase geo レプリケーションのデプロイ)](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/)」を参照してください。