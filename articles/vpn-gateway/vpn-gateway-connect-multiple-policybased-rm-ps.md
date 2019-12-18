---
title: 'Azure VPN Gateway: オンプレミスのポリシー ベースの複数の VPN デバイスにゲートウェイを接続する'
description: Azure Resource Manager と PowerShell を使用して、複数のポリシー ベース VPN デバイスに Azure ルート ベース VPN ゲートウェイを構成します。
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: yushwang
ms.openlocfilehash: f307e37debe47d2d352b1a375259fe7a0d6dd3cd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146704"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>PowerShell を使って複数のオンプレミス ポリシー ベース VPN デバイスに VPN ゲートウェイを接続する

この記事は、S2S VPN 接続のカスタム IPsec/IKE ポリシーを利用して、複数のオンプレミス ポリシー ベース VPN デバイスに接続するように Azure ルート ベース VPN ゲートウェイを構成するのに役立ちます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>ポリシー ベースおよびルート ベースの VPN ゲートウェイについて

ポリシー ベース VPN デバイス*と*ルート ベース VPN デバイスでは、IPsec トラフィック セレクターを設定する方法が異なります。

* **ポリシー ベース** VPN デバイスは、両方のネットワークからプレフィクスの組み合わせを使用して、トラフィックがどのように IPsec トンネルを介して暗号化/復号化されるかを定義します。 これは通常、パケット フィルタリングを実行するファイアウォール デバイスで構築されます。 IPsec トンネルの暗号化と復号化は、パケット フィルタリングと処理エンジンに追加されます。
* **ルート ベース** VPN デバイスは、あらゆる (ワイルドカード) のトラフィック セレクターを使用して、ルーティング/転送テーブルを異なる IPsec トンネルへのトラフィックに転送します。 これは通常、それぞれの IPsec トンネルがネットワーク インターフェイスまたは VTI (仮想トンネル インターフェイス) としてモデル化されるルーターのプラットフォームで構築されます。

次の図では、2 つのモデルが強調表示されています。

### <a name="policy-based-vpn-example"></a>ポリシー ベース VPN 例
![ポリシー ベース](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>ルート ベース VPN の例
![ルート ベース](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>ポリシー ベース VPN の Azure のサポート
現在、Azure では、ルート ベース VPN ゲートウェイとポリシー ベース VPN ゲートウェイの両方の VPN ゲートウェイ モードをサポートしています。 これらは、異なる内部プラットフォームに基づいて構築され、結果として異なる仕様になります。

|                          | **PolicyBased VPN ゲートウェイ** | **RouteBased VPN ゲートウェイ**       |**RouteBased VPN ゲートウェイ**                          |
| ---                      | ---                         | ---                              |---                                                 |
| **Azure ゲートウェイ SKU**    | Basic                       | Basic                            | Standard、HighPerformance、VpnGw1、VpnGw2、VpnGw3  |
| **IKE バージョン**          | IKEv1                       | IKEv2                            | IKEv1 および IKEv2                                    |
| **最大S2S 接続** | **1**                       | 10                               |標準: 10<br> その他の SKU:30                     |
|                          |                             |                                  |                                                    |

カスタムの IPsec/IKE ポリシーでは、Azure ルート ベース VPN ゲートウェイを構成し、オプション "**PolicyBasedTrafficSelectors**" を指定してプレフィクス ベースのトラフィック セレクターを使用し、オンプレミス ポリシー ベース VPN デバイスに接続できるようになりました。 この機能では、Azure 仮想ネットワークおよび VPN ゲートウェイから複数のオンプレミス ポリシー ベース VPN/ファイアウォール デバイスに接続し、現在の Azure ポリシー ベース VPN ゲートウェイから単一の接続制限をなくすことができます。

> [!IMPORTANT]
> 1. この接続を有効にするには、オンプレミス ポリシー ベース VPN デバイスが **IKEv2** をサポートし、Azure ルートベース VPN ゲートウェイに接続する必要があります。 VPN デバイスの仕様を確認してください。
> 2. このメカニズムを使用してポリシー ベース VPN デバイスを介して接続するオンプレミス ネットワークは、Azure 仮想ネットワークにのみ接続できます。**同じ Azure VPN ゲートウェイを介して別のオンプレミス ネットワークまたは仮想ネットワークには移行できません**。
> 3. 構成オプションは、カスタムの IPsec/IKE 接続ポリシーの一部です。 ポリシー ベースのトラフィック セレクター オプションを有効にした場合は、完全なポリシー (IPsec/IKE 暗号化と整合性アルゴリズム、キーの強度、および SA の有効期間) を指定する必要があります。

以下の図は、Azure VPN ゲートウェイを介した移行ルーティングがポリシー ベース オプションで動作しない理由を示しています。

![ポリシー ベースの移行](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

図に示すように、Azure VPN ゲートウェイには、仮想ネットワークから各オンプレミス ネットワーク プレフィックスへのトラフィック セレクターはありますが、クロス接続のプレフィックスはありません。 たとえば、オンプレミス サイト 2、サイト 3、およびサイト 4 は、それぞれが VNet1 と通信できますが、Azure VPN ゲートウェイ経由で相互に接続することはできません。 図では、この構成の下にある Azure VPN ゲートウェイでは使用できない交差接続トラフィック セレクターを示しています。

## <a name="configurepolicybased"></a>接続でのポリシー ベース トラフィック セレクターの構成

この記事の手順では、「[S2S VPN または VNet-to-VNet 接続の IPsec/IKE ポリシーを構成する](vpn-gateway-ipsecikepolicy-rm-powershell.md)」に示されている同じ例に従って、S2S VPN 接続を確立します。 これを次の図に示します。

![s2s ポリシー](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

この接続を有効にするワークフローは次のとおりです。
1. クロスプレミス接続用に仮想ネットワーク、VPN ゲートウェイ、およびローカル ネットワーク ゲートウェイを作成する
2. IPsec/IKE ポリシーの作成
3. S2S または VNet 対 VNet 接続を作成するときにはこのポリシーを適用し、接続で**ポリシー ベース トラフィック セレクターを有効にする**
4. 接続が既に作成されている場合、ポリシーを既存の接続に適用するか、更新する

## <a name="before-you-begin"></a>開始する前に

Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>接続でのポリシー ベース トラフィック セレクターの有効化

このセクションの「[IPsec/IKE ポリシーの構成の記事のパート 3](vpn-gateway-ipsecikepolicy-rm-powershell.md)」を完了したことを確認してください。 以下の例では、同じパラメーターと手順を使用します。

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>手順1 - 仮想ネットワーク、VPN ゲートウェイ、およびローカル ネットワーク ゲートウェイを作成する

#### <a name="1-connect-to-your-subscription-and-declare-your-variables"></a>1.サブスクリプションに接続して変数を宣言する

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

変数を宣言します。 この演習では、次の変数を使用します。

```azurepowershell-interactive
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2.仮想ネットワーク、VPN ゲートウェイ、およびローカル ネットワーク ゲートウェイを作成する

リソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

次の例を使用して、3 つのサブネットを持つ仮想ネットワーク TestVNet1 と VPN ゲートウェイを作成します。 値を代入する場合は、ゲートウェイ サブネットの名前を必ず GatewaySubnet にすることが重要です。 別の名前にすると、ゲートウェイの作成は失敗します。

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>手順 2 - IPsec/IKE ポリシーを使用して S2S VPN 接続を作成する

#### <a name="1-create-an-ipsecike-policy"></a>1.IPsec/IKE ポリシーの作成

> [!IMPORTANT]
> 接続で "UsePolicyBasedTrafficSelectors" オプションを有効にするために、IPsec/IKE ポリシーを作成する必要があります。

次の例では、以下のアルゴリズムとパラメーターを使用して IPsec/IKE ポリシーを作成します。
* IKEv2:AES256、SHA384、DHGroup24
* IPsec:AES256、SHA256、PFS なし、SA の有効期間 14,400 秒および 102,400,000 KB

```azurepowershell-interactive
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2.ポリシー ベースのトラフィックのセレクターと IPsec/IKE ポリシーを使用して S2S VPN 接続を作成する
S2S VPN 接続を作成し、前の手順で作成した IPsec/IKE ポリシーを適用します。 パラメーター "-UsePolicyBasedTrafficSelectors $True" を追加して、接続でポリシー ベース トラフィック セレクターを有効にします。

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

手順が完了したら、S2S VPN 接続は、定義された IPsec/IKE ポリシーを使用し、接続でポリシー ベース トラフィック セレクターを有効にします。 同じ手順を繰り返し、同じ Azure VPN ゲートウェイから追加のオンプレミス ポリシー ベース VPN デバイスへの接続を追加できます。

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>接続でのポリシー ベース トラフィック セレクターの更新
最後のセクションでは、既存の S2S VPN 接続のポリシー ベース トラフィック セレクター オプションを更新する方法を示します。

### <a name="1-get-the-connection"></a>1.接続を取得する
接続リソースを取得します。

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2.ポリシー ベース トラフィック セレクター オプションを確認する
次の行は、ポリシー ベース トラフィック セレクターが接続に使用されているかどうかを示しています。

```azurepowershell-interactive
$connection6.UsePolicyBasedTrafficSelectors
```

この行が "**True**" を返す場合、ポリシー ベース トラフィック セレクターが接続で構成されています。それ以外の場合は "**False**" が返されます。

### <a name="3-enabledisable-the-policy-based-traffic-selectors-on-a-connection"></a>3.接続でのポリシー ベース トラフィック セレクターを有効/無効にする
接続リソースを取得したら、このオプションを有効または無効にできます。

#### <a name="to-enable-usepolicybasedtrafficselectors"></a>UsePolicyBasedTrafficSelectors を有効にするには
次の例では、ポリシー ベース トラフィック セレクター オプションを有効にしますが、IPsec/IKE ポリシーは変更されません。

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

#### <a name="to-disable-usepolicybasedtrafficselectors"></a>UsePolicyBasedTrafficSelectors を無効にするには
次の例では、ポリシー ベース トラフィック セレクター オプションを無効にしますが、IPsec/IKE ポリシーは変更されません。

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

## <a name="next-steps"></a>次の手順
接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 手順については、 [仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) に関するページを参照してください。

カスタムの IPsec/IKE ポリシーの詳細については、[S2S VPN または VNet 対 VNet 接続用のIPsec/IKE ポリシーを構成する](vpn-gateway-ipsecikepolicy-rm-powershell.md)に関するページも参照してください。
