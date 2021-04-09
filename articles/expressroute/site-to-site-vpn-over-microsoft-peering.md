---
title: 'Azure ExpressRoute: Microsoft ピアリング経由の S2S VPN を構成する'
description: サイト間 VPN ゲートウェイを使用した ExpressRoute Microsoft ピアリング回線経由の Azure への IPsec/IKE 接続を構成します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 366f27a0e2a22e9aa10dda20e105bf644255bdd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89393141"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>ExpressRoute Microsoft ピアリング経由のサイト間 VPN を構成する

この記事は、ExpressRoute プライベート接続経由の、オンプレミス ネットワークと Azure 仮想ネットワーク (VNet) の間のセキュリティで保護された暗号化接続を構成するときに役立ちます。 Microsoft ピアリングを使って、選択したオンプレミス ネットワークと Azure VNet の間に、サイト間 IPsec/IKE VPN トンネルを確立できます。 ExpressRoute 経由の安全なトンネルを構成することで、機密性、アンチリプレイ、信頼性、および整合性が確保されたデータ交換が可能です。

>[!NOTE]
>Microsoft ピアリング経由のサイト間 VPN を設定すると、VPN ゲートウェイおよび VPN エグレスに対して料金が発生します。 詳細については、「[VPN Gateway の価格](https://azure.microsoft.com/pricing/details/vpn-gateway)」を参照してください。
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a><a name="architecture"></a>アーキテクチャ


  ![接続の概要](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


高可用性と冗長性を実現するために、ExpressRoute 回線の 2 つの MSEE-PE ペア経由のトンネルを複数構成し、トンネル間で負荷分散できます。

  ![高可用性のオプション](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

Microsoft ピアリング経由の VPN トンネルを終了するには、VPN ゲートウェイを使用するか、Azure Marketplace を通じて利用できる適切なネットワーク仮想アプライアンス (NVA) を使用します。 ルートの交換は、暗号化されたトンネルを介して静的または動的に行うことができます。その際、そのルート交換が、基になる Microsoft ピアリングに公開されることはありません。 この記事の例では、BGP (Microsoft ピアリングの作成に使用される BGP セッションとは異なります) を使用して、暗号化されたトンネル経由でプレフィックスが動的に交換されます。

>[!IMPORTANT]
>オンプレミス側では、通常、Microsoft ピアリングは DMZ で終了し、プライベート ピアリングはコア ネットワーク ゾーンで終了します。 2 つのゾーンは、ファイアウォールを使用して分離されます。 ExpressRoute 経由のセキュリティで保護されたトンネリングを有効化するためだけに Microsoft ピアリングを構成する場合は、Microsoft ピアリング経由で公開されている目的のパブリック IP アドレスのみをフィルター処理してください。
>
>

## <a name="workflow"></a><a name="workflow"></a>ワークフロー

1. ExpressRoute 回線用に Microsoft ピアリングを構成します。
2. 選択した Azure リージョン パブリック プレフィックスを、Microsoft ピアリング経由でオンプレミス ネットワークに公開します。
3. VPN ゲートウェイを構成し、IPsec トンネルを確立します。
4. オンプレミスの VPN デバイスを構成します。
5. サイト間 IPsec/IKE 接続を作成します。
6. (省略可能) オンプレミスの VPN デバイスでファイアウォール/フィルター処理を構成します。
7. ExpressRoute 回線経由の IPsec 通信をテストして検証します。

## <a name="1-configure-microsoft-peering"></a><a name="peering"></a>1.Microsoft ピアリングを構成する

ExpressRoute 経由のサイト間 VPN 接続を構成するには、ExpressRoute Microsoft ピアリングを活用する必要があります。

* 新しい ExpressRoute 回線を構成するには、[ExpressRoute の前提条件](expressroute-prerequisites.md)に関する記事を確認し、[ExpressRoute 回線を作成および変更](expressroute-howto-circuit-arm.md)します。

* ExpressRoute 回線が既にあり、Microsoft ピアリングが構成されていない場合は、[ExpressRoute 回路のピアリングの作成と変更](expressroute-howto-routing-arm.md#msft)に関する記事を使用して、Microsoft ピアリングを構成します。

構成した回線と Microsoft ピアリングは、Azure Portal の **[概要]** ページで簡単に確認できます。

![回線](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="2-configure-route-filters"></a><a name="routefilter"></a>2.ルート フィルターを構成する

ExpressRoute 回線の Microsoft ピアリング経由で利用するサービスがルート フィルターによって識別されます。 これは、実質的には全 BGP コミュニティ値から成る許可リストです。 

![ルート フィルター](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

この例は、"*Azure 米国西部 2*" リージョンのみでのデプロイです。 ルート フィルター規則が追加され、米国西部 2 リージョン プレフィックスの公開のみが許可されます。その BGP コミュニティ値は *12076:51026* です。 **[ルールの管理]** を選択して、許可するリージョン プレフィックスを指定します。

ルート フィルター内で、ルート フィルターを適用する ExpressRoute 回線を選択する必要もあります。 ExpressRoute 回線を選択するには、 **[回線の追加]** を選択します。 前の図では、ルート フィルターは、例の ExpressRoute 回線に関連付けられています。

### <a name="21-configure-the-route-filter"></a><a name="configfilter"></a>2.1 ルート フィルターを構成する

ルート フィルターを構成します。 手順については、[Microsoft ピアリングのルート フィルターの構成](how-to-routefilter-portal.md)に関するページをご覧ください。

### <a name="22-verify-bgp-routes"></a><a name="verifybgp"></a>2.2 BGP ルートを確認する

ExpressRoute 回線経由の Microsoft ピアリングを適切に作成し、ルート フィルターを回線に関連付けたら、MSEE とピアリングしている PE デバイスで MSEE から受信した BGP ルートを確認できます。 検証コマンドは、PE デバイスのオペレーティング システムによって異なります。

#### <a name="cisco-examples"></a>Cisco の例

この例では、Cisco IOS-XE コマンドを使用します。 例では、仮想ルーティングおよび転送 (VRF) インスタンスが、ピアリング トラフィックの分離に使用されます。

```
show ip bgp vpnv4 vrf 10 summary
```

次の部分的な出力は、ASN 12076 (MSEE) でネイバー \*.243.229.34 から 68 プレフィックスを受信したことを示しています。

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

ネイバーから受信したプレフィックスの一覧を表示するには、次の例を使用します。

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

正しいプレフィックス セットを受信していることを確認するために、クロス検証を実行できます。 次の Azure PowerShell コマンドは、サービスおよび Azure リージョンごとに Microsoft ピアリング経由で公開されたプレフィックスの一覧を出力します。

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="3-configure-the-vpn-gateway-and-ipsec-tunnels"></a><a name="vpngateway"></a>3.VPN ゲートウェイと IPsec トンネルを構成する

このセクションでは、Azure VPN ゲートウェイとオンプレミス VPN デバイスの間で、IPsec VPN トンネルを作成します。 この例では、Cisco Cloud Service Router (CSR1000) VPN デバイスを使用します。

次の図は、オンプレミスの VPN デバイス 1 と、Azure VPN ゲートウェイ インスタンスのペアの間に確立された IPsec VPN トンネルを示しています。 オンプレミスの VPN デバイス 2 と Azure VPN ゲートウェイ インスタンスのペアの間に確立された 2 つの IPsec VPN トンネルは図に示されていません。また、構成の詳細も表示されていません。 ただし、追加の VPN トンネルがあると高可用性が向上します。

  ![VPN トンネル](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

IPsec トンネル ペア経由で、eBGP セッションが確立され、プライベート ネットワーク ルートが交換されます。 次の図は、IPsec トンネル ペア経由で確立された eBGP セッションを示しています。

  ![トンネル ペア経由の eBGP セッション](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

次の図は、サンプル ネットワークの抽象化された概要です。

  ![サンプル ネットワーク](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Azure Resource Manager テンプレートの例について

例では、VPN ゲートウェイと IPsec トンネルの終了が、Azure Resource Manager テンプレートを使用して構成されます。 Resource Manager テンプレートを初めて使用する場合、または Resource Manager テンプレートの基本を理解する必要がある場合は、「[Azure Resource Manager テンプレートの構造と構文の詳細](../azure-resource-manager/templates/template-syntax.md)」を参照してください。 このセクションのテンプレートでは、未開発の Azure 環境 (VNet) が作成されます。 ただし、既存の VNet がある場合は、それをテンプレートで参照できます。 VPN ゲートウェイ IPsec/IKE サイト間の構成に慣れていない場合は、[サイト間接続の作成](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)に関するページをご覧ください。

>[!NOTE]
>この構成を作成するために Azure Resource Manager テンプレートを使用する必要はありません。 この構成は、Azure Portal または PowerShell を使用して作成できます。
>
>

### <a name="31-declare-the-variables"></a><a name="variables3"></a>3.1 変数を宣言する

この例では、変数の宣言は、サンプル ネットワークに対応します。 変数を宣言するときは、環境に合わせてこのセクションを変更します。

* **localAddressPrefix** 変数は、IPsec トンネルを終了するオンプレミスの IP アドレスの配列です。
* VPN スループットは、**gatewaySku** によって決まります。 gatewaySku と vpnType の詳細については、[VPN ゲートウェイの構成設定](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku)に関するページをご覧ください。 価格については、「[VPN Gateway の価格](https://azure.microsoft.com/pricing/details/vpn-gateway)」を参照してください。
* **vpnType** を **RouteBased** に設定します。

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premises configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="32-create-virtual-network-vnet"></a><a name="vnet"></a>3.2 仮想ネットワーク (VNet) を作成する

既存の VNet を VPN トンネルに関連付ける場合、この手順はスキップできます。

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="33-assign-public-ip-addresses-to-vpn-gateway-instances"></a><a name="ip"></a>3.3 パブリック IP アドレスを VPN ゲートウェイ インスタンスに割り当てる
 
VPN ゲートウェイの各インスタンスにパブリック IP アドレスを割り当てます。

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="34-specify-the-on-premises-vpn-tunnel-termination-local-network-gateway"></a><a name="termination"></a>3.4 オンプレミスの VPN トンネルの終了を指定する (ローカル ネットワーク ゲートウェイ)

オンプレミスの VPN デバイスは、**ローカル ネットワーク ゲートウェイ** と呼ばれます。 次の JSON スニペットは、リモート BGP ピアの詳細も指定します。

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="35-create-the-vpn-gateway"></a><a name="creategw"></a>3.5 VPN ゲートウェイを作成する

テンプレートのこのセクションは、VPN ゲートウェイを、アクティブ/アクティブ構成に必要な設定で構成します。 以下の要件に注意してください。

* **"RouteBased"** VpnType で VPN ゲートウェイを作成します。 VPN ゲートウェイとオンプレミス VPN の間の BGP ルーティングを有効にする場合、この設定は必須です。
* VPN ゲートウェイの 2 つのインスタンスと、指定したオンプレミス デバイスの間の VPN トンネルをアクティブ/アクティブ モードで確立するために、Resource Manager テンプレートで **"activeActive"** パラメーターは **true** に設定されています。 高可用性 VPN ゲートウェイの詳細については、[高可用性 VPN ゲートウェイ接続](../vpn-gateway/vpn-gateway-highlyavailable.md)に関するページをご覧ください。
* VPN トンネル間の eBGP セッションを構成するには、両側に 2 つの異なる ASN を指定する必要があります。 プライベート ASN 番号を指定することをお勧めします。 詳細については、[BGP と Azure VPN ゲートウェイの概要](../vpn-gateway/vpn-gateway-bgp-overview.md)に関するページをご覧ください。

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="36-establish-the-ipsec-tunnels"></a><a name="ipsectunnel"></a>3.6 IPsec トンネルを確立する

スクリプトの最後のセクションは、Azure VPN ゲートウェイとオンプレミス VPN デバイスの間に IPsec トンネルを作成します。

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="4-configure-the-on-premises-vpn-device"></a><a name="device"></a>4.オンプレミスの VPN デバイスを構成する

Azure VPN ゲートウェイは、さまざまなベンダーの多くの VPN デバイスと互換性があります。 構成情報と、VPN ゲートウェイとの連携が検証されているデバイスについては、[VPN デバイス情報](../vpn-gateway/vpn-gateway-about-vpn-devices.md)に関するページをご覧ください。

VPN デバイスを構成する場合は、次の項目が必要です。

* 共有キー。 これは、サイト間 VPN 接続を作成するときに指定するのと同じ共有キーです。 例では基本的な共有キーを使用します。 実際には、もっと複雑なキーを生成して使用することをお勧めします。
* VPN ゲートウェイのパブリック IP アドレス。 パブリック IP アドレスは、Azure Portal、PowerShell、または CLI を使用して確認できます。 Azure Portal を使用して VPN ゲートウェイのパブリック IP アドレスを調べるには、[仮想ネットワーク ゲートウェイ] に移動し、該当するゲートウェイの名前をクリックします。

通常、eBGP ピアは直接接続されます (多くの場合、WAN 接続経由)。 ただし、ExpressRoute Microsoft ピアリングを介した IPsec VPN トンネル経由 eBGP を構成する場合は、eBGP ピア間に複数のルーティング ドメインがあります。 直接接続されていない 2 つのピア間に eBGP ネイバー関係を確立するには、**ebgp-multihop** コマンドを使用します。 ebgp-multihop コマンドに続く整数は、BGP パケットの TTL 値を指定します。 **maximum-paths eibgp 2** コマンドは、2 つの BGP パス間におけるトラフィックの負荷分散を有効にします。

### <a name="cisco-csr1000-example"></a><a name="cisco1"></a>Cisco CSR1000 の例

次の例は、オンプレミスの VPN デバイスとしての Hyper-V 仮想マシンにおける Cisco CSR1000 の構成を示しています。

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="5-configure-vpn-device-filtering-and-firewalls-optional"></a><a name="firewalls"></a>5.VPN デバイスのフィルター処理とファイアウォールを構成する (省略可能)

要件に従って、ファイアウォールとフィルター処理を構成します。

## <a name="6-test-and-validate-the-ipsec-tunnel"></a><a name="testipsec"></a>6.IPsec トンネルをテストして検証する

IPsec トンネルの状態を Azure VPN ゲートウェイで確認するには、次の Powershell コマンドを使用します。

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

出力例:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Azure VPN ゲートウェイ インスタンスのトンネルの状態を個別に確認するには、次の例を使用します。

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

出力例:

```azurepowershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

オンプレミスの VPN デバイスでトンネルの状態を確認することもできます。

Cisco CSR1000 の例:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

出力例:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

仮想トンネル インターフェイス (VTI) の回線プロトコルは、IKE フェーズ 2 が完了するまで "起動" に変わりません。 次のコマンドは、セキュリティ アソシエーションを確認します。

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verify-end-to-end-connectivity-between-the-inside-network-on-premises-and-the-azure-vnet"></a><a name="verifye2e"></a>オンプレミス ネットワーク内と Azure VNet の間のエンド ツー エンド接続を確認する

IPsec トンネルが起動中で、静的ルートが正しく設定されている場合は、リモート BGP ピアの IP アドレスに ping を実行できます。

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verify-the-bgp-sessions-over-ipsec"></a><a name="verifybgp"></a>IPsec 経由の BGP セッションを確認する

Azure VPN ゲートウェイで、BGP ピアの状態を確認します。

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

出力例:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

オンプレミスの VPN コンセントレーターから eBGP 経由で受信したネットワーク プレフィックスの一覧を確認するには、"Origin" 属性によってフィルター処理できます。

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

出力例では、ASN 65010 は、オンプレミス VPN の BGP 自律システム番号です。

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

公開されたルートの一覧を表示するには、次のコマンドを使用します。

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

出力例:

```azurepowershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

オンプレミスの Cisco CSR1000 の例:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

オンプレミスの Cisco CSR1000 から Azure VPN ゲートウェイに公開されたネットワークの一覧を表示するには、次のコマンドを使用します。

```
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>次のステップ

* [ExpressRoute に使用する Network Performance Monitor の構成](how-to-npm.md)

* [既存の VPN ゲートウェイ接続を使用してサイト間接続を VNet に追加する](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
