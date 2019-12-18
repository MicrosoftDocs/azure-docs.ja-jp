---
title: S2S VPN および VNet-to-VNet 接続用の IPsec/IKE ポリシー
titleSuffix: Azure VPN Gateway
description: Azure Resource Manager と PowerShell を使用して、Azure VPN Gateway で S2S または VNet-to-VNet 接続の IPsec/IKE ポリシーを構成します。
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: b0dabf0ee3370abab3d0f9d6f1bf26dd622862cf
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151786"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>S2S VPN または VNet-to-VNet 接続の IPsec/IKE ポリシーを構成する

この記事では、Resource Manager デプロイ モデルと PowerShell を使用して、サイト間の VPN または VNet 間の接続の IPsec/IKE ポリシーを作成する手順について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>Azure VPN ゲートウェイの IPsec/IKE ポリシーのパラメーターについて
IPsec/IKE 標準プロトコルは、幅広い暗号アルゴリズムをさまざまな組み合わせでサポートしています。 このプロトコルがクロスプレミスと VNet-to-VNet 接続でコンプライアンスまたはセキュリティ要件を満たすためにどのように役立つかについては、「[About cryptographic requirements and Azure VPN gateways](vpn-gateway-about-compliance-crypto.md)」(暗号化要件と Azure VPN Gateway について) を参照してください。

この記事では、IPsec/IKE ポリシーを作成して構成し、新規または既存の接続に適用する手順を示します。

* [パート 1: IPsec/IKE ポリシーを作成して設定するためのワークフロー](#workflow)
* [パート 2: サポートされる暗号アルゴリズムとキーの強度](#params)
* [パート 3: IPsec/IKE ポリシーを使用して新しい S2S VPN 接続を作成する](#crossprem)
* [パート 4: IPsec/IKE ポリシーを使用して新しい VNet-to-VNet 接続を作成する](#vnet2vnet)
* [パート 5 - 管理 (作成、追加、削除) 接続の IPSEC/IKE ポリシー](#managepolicy)

> [!IMPORTANT]
> 1. IPsec/IKE ポリシーは、次のゲートウェイ SKU でのみ機能する点に注意してください。
>    * ***VpnGw1、VpnGw2、VpnGw3*** (ルート ベース)
>    * ***Standard*** および ***HighPerformance*** (ルート ベース)
> 2. ある特定の接続に対して指定できるポリシーの組み合わせは ***1 つ***だけです。
> 3. IKE (メイン モード) と IPsec (クイック モード) の両方について、すべてのアルゴリズムとパラメーターを指定する必要があります。 ポリシーを部分的に指定することはできません。
> 4. オンプレミスの VPN デバイスでポリシーがサポートされることを、VPN デバイス ベンダーの仕様で確認してください。 ポリシーに対応していない場合、S2S または VNet-to-VNet 接続を確立することはできません。

## <a name ="workflow"></a>パート 1 - IPsec/IKE ポリシーを作成して設定するためのワークフロー
このセクションでは、S2S VPN または VNet-to-VNet 接続の IPsec/IKE ポリシーの作成と更新を行うためのワークローについて説明します。
1. 仮想ネットワークと VPN ゲートウェイを作成する
2. クロスプレミス接続用のローカル ネットワーク ゲートウェイ、または VNet-to-VNet 接続用の別の仮想ネットワーク ゲートウェイを作成する
3. 選択したアルゴリズムとパラメーターを使用して IPsec/IKE ポリシーを作成する
4. IPsec/IKE ポリシーを使用して接続 (IPsec または VNet2VNet) を作成する
5. 既存の接続の IPsec/IKE ポリシーを追加/更新/削除する

この記事では、次の図に示す IPsec/IKE ポリシーを設定して構成します。

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>パート 2 - サポートされる暗号アルゴリズムとキーの強度

以下の表は、サポートされている暗号アルゴリズムと、ユーザーが構成できるキーの強度を一覧にしたものです。

| **IPsec/IKEv2**  | **オプション**    |
| ---  | --- 
| IKEv2 暗号化 | AES256、AES192、AES128、DES3、DES  
| IKEv2 整合性  | SHA384、SHA256、SHA1、MD5  |
| DH グループ         | DHGroup24、ECP384、ECP256、DHGroup14、DHGroup2048、DHGroup2、DHGroup1、なし |
| IPsec 暗号化 | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、なし    |
| IPsec 整合性  | GCMASE256、GCMAES192、GCMAES128、SHA256、SHA1、MD5 |
| PFS グループ        | PFS24、ECP384、ECP256、PFS2048、PFS2、PFS1、なし 
| QM SA の有効期間   | (**オプション**: 指定されていない場合、既定値が使用されます)<br>秒 (整数: **最小 300**/既定値 27,000 秒)<br>キロバイト数 (整数: **最小 1024**/既定値 102,400,000 キロバイト)   |
| トラフィック セレクター | UsePolicyBasedTrafficSelectors** ($True/$False； **Optional**、指定されていない場合、既定値は $False)    |
|  |  |

> [!IMPORTANT]
> 1. **ご使用のオンプレミス VPN デバイスの構成は、Azure IPsec/IKE ポリシーで指定した次のアルゴリズムおよびパラメーターと一致している (または含んでいる) 必要があります。**
>    * IKE 暗号化アルゴリズム (メイン モード/フェーズ 1)
>    * IKE 整合性アルゴリズム (メイン モード/フェーズ 1)
>    * DH グループ (メイン モード/フェーズ 1)
>    * IPsec 暗号化アルゴリズム (クイック モード/フェーズ 2)
>    * IPsec 整合性アルゴリズム (クイック モード/フェーズ 2)
>    * PFS グループ (クイック モード/フェーズ 2)
>    * トラフィック セレクター (UsePolicyBasedTrafficSelectors が使用される場合)
>    * SA の有効期間は、ローカルの指定のみとなります。一致している必要はありません。
>
> 2. **IPsec 暗号化アルゴリズム用として GCMAES を使用する場合は、IPsec 整合性に、同じ GCMAES アルゴリズムとキーの長さを選択する必要があります。たとえば、両方に GCMAES128 を使用するなどです。**
> 3. 上記の表では:
>    * IKEv2 はメイン モードまたはフェーズ 1 に対応しています
>    * IPsec はクイック モードまたはフェーズ 2 に対応しています
>    * DH グループは、メイン モードまたはフェーズ 1 で使用される Diffie-Hellman グループを指定します
>    * PFS グループは、クイック モードまたはフェーズ 2 で使用される Diffie-Hellman グループを指定します
> 4. Azure VPN ゲートウェイでは、IKEv2 メイン モード SA の有効期間が 28,800 秒に固定されます
> 5. 接続の "UsePolicyBasedTrafficSelectors" を $True に設定すると、Azure VPN ゲートウェイは、オンプレミスのポリシー ベースの VPN ファイアウォールに接続するように構成されます。 PolicyBasedTrafficSelectors を有効にする場合は、オンプレミス ネットワーク (ローカル ネットワーク ゲートウェイ) プレフィックスと Azure Virtual Network プレフィックスのすべての組み合わせに対応するトラフィック セレクターが VPN デバイスに定義されている必要があります (any-to-any は不可)。 たとえば、オンプレミス ネットワークのプレフィックスが 10.1.0.0/16 と 10.2.0.0/16 で、仮想ネットワークのプレフィックスが 192.168.0.0/16 と 172.16.0.0/16 である場合、次のトラフィック セレクターを指定する必要があります。
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

ポリシー ベースのトラフィック セレクターの詳細については、[複数のオンプレミスのポリシー ベース VPN デバイスへの接続](vpn-gateway-connect-multiple-policybased-rm-ps.md)に関する記事を参照してください。

以下の表は、カスタム ポリシーでサポートされている、対応する Diffie-Hellman グループを示したものです。

| **Diffie-Hellman グループ**  | **DHGroup**              | **PFSGroup** | **キーの長さ** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768 ビット MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024 ビット MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048 ビット MODP  |
| 19                        | ECP256                   | ECP256       | 256 ビット ECP    |
| 20                        | ECP384                   | ECP384       | 384 ビット ECP    |
| 24                        | DHGroup24                | PFS24        | 2048 ビット MODP  |

詳細については、[RFC3526](https://tools.ietf.org/html/rfc3526) と [RFC5114](https://tools.ietf.org/html/rfc5114) を参照してください。

## <a name ="crossprem"></a>パート 3 - IPsec/IKE ポリシーを使用して新しい S2S VPN 接続を作成する

このセクションでは、IPsec/IKE ポリシーを使用して S2S VPN 接続を作成する手順について説明します。 以下の手順によって、次の図に示す接続が作成されます。

![s2s ポリシー](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

S2S VPN 接続を作成するための詳細な手順については、[S2S VPN 接続の作成](vpn-gateway-create-site-to-site-rm-powershell.md)に関する記事をご覧ください。

### <a name="before"></a>開始する前に

* Azure サブスクリプションを持っていることを確認します。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
* Azure Resource Manager PowerShell コマンドレットをインストールします。 PowerShell コマンドレットのインストールの詳細については、「[Azure PowerShell の概要](/powershell/azure/overview)」を参照してください。

### <a name="createvnet1"></a>手順 1 - 仮想ネットワーク、VPN ゲートウェイ、およびローカル ネットワーク ゲートウェイを作成する

#### <a name="1-declare-your-variables"></a>1.変数を宣言する

この練習では、最初に変数を宣言します。 運用環境の場合、実際の値を使用します。

```powershell
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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2.サブスクリプションに接続して新しいリソース グループを作成する

リソース マネージャー コマンドレットを使用するように PowerShell モードを切り替えてください。 詳細については、「 [リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

PowerShell コンソールを開き、アカウントに接続します。 接続するには、次のサンプルを参照してください。

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3.仮想ネットワーク、VPN ゲートウェイ、およびローカル ネットワーク ゲートウェイを作成する

次の例は、3 つのサブネットを持つ仮想ネットワーク TestVNet1 と VPN ゲートウェイを作成します。 値を代入するときは、ゲートウェイの名前を必ず GatewaySubnet にすることが重要です。 別の名前にすると、ゲートウェイの作成は失敗します。

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="s2sconnection"></a>手順 2 - IPsec/IKE ポリシーを使用して S2S VPN 接続を作成する

#### <a name="1-create-an-ipsecike-policy"></a>1.IPsec/IKE ポリシーの作成

次のサンプル スクリプトは、次のアルゴリズムとパラメーターを使用して IPsec/IKE ポリシーを作成します。

* IKEv2:AES256、SHA384、DHGroup24
* IPsec:AES256、SHA256、PFS なし、SA の有効期間 14,400 秒および 102,400,000 KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

IPsec に GCMAES を使用する場合、IPsec 暗号化と整合性の両方で同じ GCMAES アルゴリズムとキーの長さを使用する必要があります。 上記の例では、GCMAES256 を使用した場合、該当するパラメーターは "-IpsecEncryption GCMAES256 -IpsecIntegrity GCMAES256" になります。

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2.IPsec/IKE ポリシーを使用して S2S VPN 接続を作成する

S2S VPN 接続を作成し、上記で作成した IPsec/IKE ポリシーを適用します。

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

必要に応じて、接続作成コマンドレットに "-UsePolicyBasedTrafficSelectors $True" を追加して、Azure VPN ゲートウェイがオンプレミスのポリシー ベースの VPN デバイスに接続できるようにすることができます (前述の説明を参照してください)。

> [!IMPORTANT]
> 接続に IPsec/IKE ポリシーを指定すると、Azure VPN ゲートウェイは、その特定の接続に対して指定された暗号アルゴリズムとキーの強度を使用する IPsec/IKE 提案のみを送受信します。 接続するオンプレミスの VPN デバイスが、ポリシーの完全な組み合わせを使用するか受け入れることを確認する必要があります。それ以外の場合、S2S VPN トンネルは確立されません。


## <a name ="vnet2vnet"></a>パート 4 - IPsec/IKE ポリシーを使用して新しい VNet-to-VNet 接続を作成する

IPsec/IKE ポリシーを使用した VNet-to-VNet 接続の作成手順は、S2S VPN 接続の作成手順に似ています。 以下のサンプル スクリプトは、次の図に示す接続を作成します。

![v2v ポリシー](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

VNet 間の接続を作成するための詳細な手順については、[VNet-to-VNet 接続の作成](vpn-gateway-vnet-vnet-rm-ps.md)に関する記事を参照してください。 TestVNet1 と VPN Gateway を作成して構成するには、[パート 3](#crossprem) の手順を完了している必要があります。

### <a name="createvnet2"></a>手順 1 - 2 つ目の仮想ネットワークと VPN ゲートウェイを作成する

#### <a name="1-declare-your-variables"></a>1.変数を宣言する

値は実際の構成で使用する値に置換します。

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2.新しいリソース グループに 2 つ目の仮想ネットワークと VPN ゲートウェイを作成する

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>手順 2 - IPsec/IKE ポリシーを使用して VNet 間の接続を作成する

S2S VPN 接続と同じように、IPsec/IKE ポリシーを作成し、新しい接続にポリシーを適用します。

#### <a name="1-create-an-ipsecike-policy"></a>1.IPsec/IKE ポリシーの作成

次のサンプル スクリプトは、次のアルゴリズムとパラメーターを使用して別の IPsec/IKE ポリシーを作成します。
* IKEv2:AES128、SHA1、DHGroup14
* IPsec:GCMAES128、GCMAES128、PFS14、SA の有効期間 14,400 秒および 102,400,000 KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2.IPsec/IKE ポリシーを使用して、VNet-to-VNet 接続を作成する

VNet 間の接続を作成し、作成済みの IPsec/IKE ポリシーを適用します。 この例では、両方のゲートウェイが同じサブスクリプションにあります。 このため、両方の接続を、同じ PowerShell セッションで同じ IPsec/IKE ポリシーを使用して、作成して構成することができます。

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> 接続に IPsec/IKE ポリシーを指定すると、Azure VPN ゲートウェイは、その特定の接続に対して指定された暗号アルゴリズムとキーの強度を使用する IPsec/IKE 提案のみを送受信します。 両方の接続の IPsec ポリシーが同じであることを確認してください。それ以外の場合、VNet 間の接続は確立されません。

これらの手順を完了すると、数分後に接続が確立され、この記事で最初に示した次のネットワーク トポロジが設定されます。

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>パート 5 - 接続の IPsec/IKE ポリシーを更新する

最後のセクションでは、既存の S2S または VNet 間接続の IPsec/IKE ポリシーを管理する方法を示します。 ここでは、接続に対して次の操作を行います。

1. 接続の IPsec/IKE ポリシーを表示する
2. 接続に IPsec/IKE ポリシーを追加する、またはポリシーを更新する
3. 接続から IPsec/IKE ポリシーを削除する

S2S 接続と VNet-to-VNet 接続の両方に同じ手順が適用されます。

> [!IMPORTANT]
> IPsec/IKE ポリシーは、"*Standard*" および "*HighPerformance*" のルート ベースの VPN ゲートウェイでのみサポートされています。 Basic ゲートウェイ SKU またはポリシー ベースの VPN ゲートウェイでは機能しません。

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1.接続の IPsec/IKE ポリシーを表示する

次の例は、接続に構成されている IPsec/IKE ポリシーを取得する方法を示しています。 これらのスクリプトは、ここまでの手順の続きでもあります。

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

最後のコマンドによって、接続に構成されている現在の IPsec/IKE ポリシーが表示されます (ポリシーが存在する場合)。 以下は、接続の出力サンプルです。

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

IPsec/IKE ポリシーが構成されていない場合、コマンド (PS> $connection6.policy) は何も返しません。 これは、接続に IPsec/IKE が構成されていないという意味ではなく、カスタム IPsec/IKE ポリシーがないことを意味します。 実際の接続では、オンプレミスの VPN デバイスと Azure VPN ゲートウェイの間でネゴシエートされる既定のポリシーが使用されます。

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2.IPsec/IKE ポリシーを接続に追加するか、ポリシーを更新する

接続に新しいポリシーを追加する手順と既存のポリシーを更新する手順は同じです。つまり、新しいポリシーを作成した後、新しいポリシーを接続に適用します。

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

"UsePolicyBasedTrafficSelectors" を有効にしてオンプレミスのポリシー ベースの VPN デバイスに接続するには、"-UsePolicyBaseTrafficSelectors" パラメーターをコマンドレットに追加するか、$False を設定してこのオプションを無効にします。

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

接続をもう一度取得して、ポリシーが更新されていることを確認できます。

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

最後の行によって、次の例のような出力が表示されます。

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3.接続から IPsec/IKE ポリシーを削除する

接続からカスタム ポリシーが削除されると、Azure VPN ゲートウェイは [IPsec/IKE 提案の既定のリスト](vpn-gateway-about-vpn-devices.md)に戻り、オンプレミスの VPN デバイスとの再ネゴシエーションを再び実行します。

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

同じスクリプトを使用して、接続からポリシーが削除されたかどうかを確認できます。

## <a name="next-steps"></a>次の手順

ポリシー ベースのトラフィック セレクターの詳細については、[複数のオンプレミスのポリシー ベースの VPN デバイスへの接続](vpn-gateway-connect-multiple-policybased-rm-ps.md)に関する記事を参照してください。

接続が完成したら、仮想ネットワークに仮想マシンを追加することができます。 手順については、 [仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) に関するページを参照してください。
