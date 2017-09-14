---
title: "オンプレミスのネットワークを Azure 仮想ネットワークに接続する: サイト間 VPN: PowerShell | Microsoft Docs"
description: "パブリック インターネットを経由したオンプレミスのネットワークから Azure 仮想ネットワークへの IPsec 接続を作成する手順。 これらの手順は、PowerShell を使用してクロスプレミスのサイト間 VPN Gateway 接続を作成する際に役立ちます。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 27f4a8fb9a83b98e99df635bf4c80f6048ce348c
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>PowerShell を使用してサイト間 VPN 接続を備えた VNet を作成する

この記事では、PowerShell を使用して、オンプレミス ネットワークから VNet へのサイト間 VPN Gateway 接続を作成する方法について説明します。 この記事の手順は、Resource Manager デプロイメント モデルに適用されます。 また、この構成の作成には、次のリストから別のオプションを選択して、別のデプロイ ツールまたはデプロイ モデルを使用することもできます。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (クラシック)](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [クラシック ポータル (クラシック)](vpn-gateway-site-to-site-create.md)
> 
>


サイト間 VPN Gateway 接続は、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介してオンプレミス ネットワークを Azure 仮想ネットワークに接続するために使用します。 この種類の接続では、外部接続用パブリック IP アドレスが割り当てられていてるオンプレミスの VPN デバイスが必要です。 VPN Gateway の詳細については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。

![クロスプレミスのサイト間 VPN Gateway 接続の図](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-diagram.png)

## <a name="before"></a>開始する前に

構成を開始する前に、以下の条件を満たしていることを確認します。

* 互換性のある VPN デバイスがあり、デバイスを構成できる人員がいることを確認します。 互換性のある VPN デバイスとデバイスの構成の詳細については、[VPN デバイスの概要](vpn-gateway-about-vpn-devices.md)に関する記事を参照してください。
* VPN デバイスの外部接続用パブリック IPv4 アドレスがあることを確認します。 この IP アドレスを NAT の内側に割り当てることはできません。
* オンプレミス ネットワーク構成の IP アドレス範囲を把握していない場合は、詳細な情報を把握している担当者と協力して作業を行ってください。 この構成を作成する場合は、Azure がオンプレミスの場所にルーティングする IP アドレス範囲のプレフィックスを指定する必要があります。 オンプレミス ネットワークのサブネットと接続先の仮想ネットワーク サブネットが重複しないようにしなければなりません。
* Azure Resource Manager PowerShell コマンドレットの最新版をインストールしてください。 PowerShell コマンドレットは頻繁に更新されるため、最新の機能を利用するには、通常、PowerShell コマンドレットを更新する必要があります。 PowerShell コマンドレットを更新しないと、指定した値で失敗することがあります。 PowerShell コマンドレットのダウンロードとインストールの詳細については、[Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)に関するページを参照してください。

### <a name="example"></a>値の例

この記事の例では、次の値を使用します。 この値を使用して、テスト環境を作成できます。また、この値を参考にしながら、この記事の例を確認していくこともできます。

```
#Example values

VnetName                = TestVNet1
ResourceGroup           = TestRG1
Location                = East US 
AddressSpace            = 10.11.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.11.1.0/28 
GatewaySubnet           = 10.11.0.0/27
LocalNetworkGatewayName = Site2
LNG Public IP           = <VPN device IP address> 
Local Address Prefixes  = 10.0.0.0/24, 20.0.0.0/24
Gateway Name            = VNet1GW
PublicIP                = VNet1GWIP
Gateway IP Config       = gwipconfig1 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2

```


## <a name="Login"></a>1.サブスクリプションへの接続

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="VNet"></a>2.仮想ネットワークとゲートウェイ サブネットを作成する

まだ仮想ネットワークがない場合は作成します。 仮想ネットワークを作成する際は、指定したアドレス空間がオンプレミス ネットワーク内に存在するあらゆるアドレス空間と重複していないことを確認します。

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [No NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="vnet"></a>仮想ネットワークとゲートウェイ サブネットを作成するには

次の例では、仮想ネットワークとゲートウェイ サブネットを作成します。 ゲートウェイ サブネットを追加する必要がある仮想ネットワークが既にある場合は、「[作成済みの仮想ネットワークにゲートウェイ サブネットを追加するには](#gatewaysubnet)」を参照してください。

リソース グループを作成します。

```powershell
New-AzureRmResourceGroup -Name TestRG1 -Location 'East US'
```

仮想ネットワークを作成します。

1. 変数を設定します。

  ```powershell
  $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.11.0.0/27
  $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix 10.11.1.0/28
  ```
2. VNet を作成します。

  ```powershell
  New-AzureRmVirtualNetwork -Name TestVNet1 -ResourceGroupName TestRG1 `
  -Location 'East US' -AddressPrefix 10.11.0.0/16 -Subnet $subnet1, $subnet2
  ```

### <a name="gatewaysubnet"></a>作成済みの仮想ネットワークにゲートウェイ サブネットを追加するには

1. 変数を設定します。

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG1 -Name TestVet1
  ```
2. ゲートウェイ サブネットを作成します。

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.11.0.0/27 -VirtualNetwork $vnet
  ```
3. 構成を設定します。

  ```powershell
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

## 3.<a name="localnet"></a>ローカル ネットワーク ゲートウェイを作成する

ローカル ネットワーク ゲートウェイは通常、オンプレミスの場所を指します。 サイトに Azure が参照できる名前を付け、接続を作成するオンプレミス VPN デバイスの IP アドレスを指定します。 また、VPN ゲートウェイを介して VPN デバイスにルーティングされる IP アドレスのプレフィックスも指定します。 指定するアドレスのプレフィックスは、オンプレミス ネットワークのプレフィックスです。 オンプレミス ネットワークが変わった場合は、プレフィックスを簡単に更新できます。

次の値を使用します。

* *GatewayIPAddress* は、オンプレミス VPN デバイスの IP アドレスです。 VPN デバイスを NAT の内側に配置することはできません。
* *AddressPrefix* は、オンプレミスのアドレス空間です。

1 つのアドレス プレフィックスを含むローカル ネットワーク ゲートウェイを追加するには

  ```powershell
  New-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.0.0.0/24'
  ```

複数のアドレス プレフィックスを含むローカル ネットワーク ゲートウェイを追加するには

  ```powershell
  New-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1 `
  -Location 'East US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')
  ```

ローカル ネットワーク ゲートウェイ用の IP アドレスのプレフィックスを変更するには<br>
ローカル ネットワーク ゲートウェイのプレフィックスは変わる場合があります。 IP アドレス プレフィックスを変更する手順は、VPN ゲートウェイ接続を作成したかどうかによって異なります。 「 [ローカル ネットワーク ゲートウェイ用の IP アドレスのプレフィックスを変更するには](#modify) 」セクションを参照してください。

## <a name="PublicIP"></a>4.パブリック IP アドレスを要求する

VPN ゲートウェイには、パブリック IP アドレスが必要です。 これにはまず IP アドレスのリソースを要求したうえで、仮想ネットワーク ゲートウェイの作成時にそのリソースを参照する必要があります。 IP アドレスは、VPN ゲートウェイの作成時にリソースに対して動的に割り当てられます。 VPN Gateway では現在、パブリック IP アドレスの "*動的*" 割り当てのみサポートしています。 静的パブリック IP アドレスの割り当てを要求することはできません。 もっとも、VPN ゲートウェイに割り当てられた IP アドレスが後から変わることは基本的にありません。 パブリック IP アドレスが変わるのは、ゲートウェイが削除され、再度作成されたときのみです。 VPN ゲートウェイのサイズ変更、リセット、その他の内部メンテナンス/アップグレードでは、IP アドレスは変わりません。

仮想ネットワークの VPN ゲートウェイに割り当てるパブリック IP アドレスを要求します。

```powershell
$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>5.ゲートウェイ IP アドレス指定の構成を作成する

ゲートウェイの構成で、使用するサブネットとパブリック IP アドレスを定義します。 次の例を使用して、ゲートウェイの構成を作成します。

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name TestVNet1 -ResourceGroupName TestRG1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>6.VPN ゲートウェイを作成する

仮想ネットワーク VPN ゲートウェイを作成します。 VPN ゲートウェイの作成が完了するまでに、45 分以上かかることがあります。

次の値を使用します。

* サイト間構成の *-GatewayType* は *Vpn* です。 ゲートウェイの種類は常に、実装する構成に対応するものとなります。 たとえば、他のゲートウェイ構成では、-GatewayType に ExpressRoute を必要とする場合があります。
* *-VpnType* には、*RouteBased* (ドキュメントによっては動的ゲートウェイと呼ばれます) または *PolicyBased* (ドキュメントによっては静的ゲートウェイと呼ばれます) を指定できます。 VPN ゲートウェイの種類については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。
* 使用するゲートウェイ SKU を選択します。 特定の SKU には構成の制限があります。 詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。 VPN ゲートウェイを作成するときに -GatewaySku に関するエラーが発生した場合は、PowerShell コマンドレットの最新バージョンがインストールされていることを確認してください。

```powershell
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="ConfigureVPNDevice"></a>7.VPN デバイスの構成

オンプレミス ネットワークとのサイト間接続には VPN デバイスが必要です。 この手順では、VPN デバイスを構成します。 VPN デバイスを構成する際に、次の情報が必要になります。

- 共有キー。 サイト間 VPN 接続を作成するときにも、これと同じ共有キーを指定します。 ここで紹介している例では、基本的な共有キーを使用しています。 実際には、もっと複雑なキーを生成して使用することをお勧めします。
- 仮想ネットワーク ゲートウェイのパブリック IP アドレス。 パブリック IP アドレスは、Azure Portal、PowerShell、または CLI を使用して確認できます。 PowerShell を使用して仮想ネットワーク ゲートウェイのパブリック IP アドレスを検索するには、次の例を使用します。

  ```powershell
  Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG1
  ```

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="CreateConnection"></a>8.VPN 接続を作成する

次に、仮想ネットワーク ゲートウェイと VPN デバイスの間にサイト間 VPN 接続を作成します。 サンプルの値は必ず実際の値に変更してください。 共有キーは、VPN デバイスの構成に使用したものと同じ値にする必要があります。 サイト間接続の "-ConnectionType" は *IPsec* です。

1. 変数を設定します。
  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
  $local = Get-AzureRmLocalNetworkGateway -Name Site2 -ResourceGroupName TestRG1
  ```

2. 接続を作成します。
  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite2 -ResourceGroupName TestRG1 `
  -Location 'East US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

しばらくすると、接続が確立されます。

## <a name="toverify"></a>9.VPN 接続の確認

VPN 接続を検証する方法はいくつかあります。

[!INCLUDE [Verify connection](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="connectVM"></a>仮想マシンに接続するには

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]


## <a name="modify"></a>ローカル ネットワーク ゲートウェイの IP アドレス プレフィックスの変更

オンプレミスの場所にルーティングする IP アドレス プレフィックスが変更された場合には、ローカル ネットワーク ゲートウェイを変更できます。 2 種類の手順があります。 どちらの手順を選択するかは、既にゲートウェイ接続を作成しているかどうかによって異なります。

[!INCLUDE [Modify prefixes](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>ローカル ネットワーク ゲートウェイのゲートウェイ IP アドレスの変更

[!INCLUDE [Modify gateway IP address](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>次のステップ

*  接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 詳細については、[Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) に関するページを参照してください。
* BGP の詳細については、[BGP の概要](vpn-gateway-bgp-overview.md)に関する記事と [BGP の構成方法](vpn-gateway-bgp-resource-manager-ps.md)に関する記事を参照してください。

