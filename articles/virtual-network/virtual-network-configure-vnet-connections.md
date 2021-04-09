---
title: 仮想ネットワークまたは VPN 接続の構成および検証
description: さまざまな Azure VPN および仮想ネットワークのデプロイを構成して検証するためのステップ バイ ステップ ガイダンス
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 9a0d06a8f8fa8f68f063404f2b483b817eb0563f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452103"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>仮想ネットワークまたは VPN 接続の構成および検証

このチュートリアルでは、さまざまな Azure VPN と仮想ネットワークのデプロイを構成して検証するステップ バイ ステップ ガイダンスを提供します。 トランジット ルーティング、ネットワーク間接続、Border Gateway Protocol (BGP)、マルチサイト接続、ポイント対サイト接続などのシナリオがあります。

Azure VPN ゲートウェイを使用すると、ほぼすべての種類の接続された仮想ネットワーク トポロジを Azure に柔軟に配置できます。 たとえば、仮想ネットワークを次のように接続できます。

- 複数のリージョン間。
- 仮想ネットワークの種類 (Azure Resource Manager とクラシック) の間。
- Azure 内またはオンプレミスのハイブリッド環境内。
- 異なるサブスクリプション内。 

## <a name="network-to-network-vpn-connection"></a>ネットワーク間 VPN 接続

VPN 経由で仮想ネットワークを別の仮想ネットワークに (ネットワーク間) 接続することは、仮想ネットワークをオンプレミスのサイトの場所に接続することと似ています。 どちらの接続の種類でも、VPN ゲートウェイを使用して、IPsec と IKE を介して安全なトンネルが提供されます。 仮想ネットワークが属しているリージョンやサブスクリプションは異なっていてもかまいません。

![IPsec を介したネットワーク間接続](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
複数の仮想ネットワークが同じリージョンに存在する場合、それらを仮想ネットワーク ピアリングを使用して接続することを検討してください。 仮想ネットワーク ピアリングでは、VPN ゲートウェイは使用されません。 スループットが向上し、待機時間が短縮されます。 仮想ネットワーク ピアリング接続を構成するには、 **[Configure and validate VNet Peering]\(VNet ピアリングを構成して検証する\)** を選択します。

仮想ネットワークが Azure Resource Manager デプロイ モデルを介して作成されている場合は、 **[Configure and validate a Resource Manager VNet to a Resource Manager VNet connection]\(Resource Manager VNet 間の接続を構成して検証する\)** を選択して、VPN 接続を構成します。

仮想ネットワークの一方が Azure クラシック デプロイ モデルを介して作成され、もう一方が Resource Manager を介して作成されている場合は、 **[Configure and validate a classic VNet to a Resource Manager VNet connection]\(クラシック VNet と Resource Manager VNet 間の接続を構成して検証する\)** を選択して VPN 接続を構成します。

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>同じリージョン内の 2 つの仮想ネットワークに対する仮想ネットワーク ピアリングの構成

Azure 仮想ネットワーク ピアリングの実装と構成を開始する前に、次の前提条件を満たしていることを確認します。

* ピアリングする仮想ネットワークは同じ Azure リージョンに存在する必要があります。
* ピアリングする仮想ネットワークの IP アドレス空間は、重複していてはなりません。
* 仮想ネットワーク ピアリングは、2 つの仮想ネットワーク間の関係です。 ピアリング間で派生する推移的な関係は存在しません。 たとえば、VNetA が VNetB とピアリングされ、VNetB が VNetC とピアリングされているからといって、VNetA が VNetC とピアリングされていることには "*なりません*"。

これらの前提条件を満たしている場合は、「[チュートリアル: Azure Portal を使用して仮想ネットワーク ピアリングで仮想ネットワークを接続する](./tutorial-connect-virtual-networks-portal.md)」に従って、ピアリングを作成して構成することが出来ます。

ピアリングの構成を確認するには、次の方法を使用します。

1. 必要な[ロールとアクセス許可](virtual-network-manage-peering.md#permissions)があるアカウントを使用して、[Azure portal](https://portal.azure.com/) にサインインします。
2. ポータルの上部にある "**リソースの検索**" というテキストが表示されたボックスに、「**仮想ネットワーク**」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
3. 表示される **[仮想ネットワーク]** ブレードで、ピアリングを作成する仮想ネットワークを選択します。
4. 仮想ネットワークに対して表示されたウィンドウで、 **[設定]** セクションの **[ピアリング]** を選択します。
5. ピアリングを選択して、構成の結果を表示します。

![仮想ネットワーク ピアリング構成を確認するための選択](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Azure PowerShell の場合、コマンド [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) を実行して仮想ネットワーク ピアリングを取得します。 次に例を示します。

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Resource Manager 仮想ネットワークの別の Resource Manager 仮想ネットワークへの接続

ある Resource Manager 仮想ネットワークから別の Resource Manager 仮想ネットワークへの接続を直接構成することができます。 または、IPsec を使用して接続を構成することもできます。

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Resource Manager 仮想ネットワーク間の VPN 接続の構成

IPsec なしで Resource Manager 仮想ネットワーク間の接続を構成するには、[Azure portal を使用したネットワーク間 VPN ゲートウェイ接続の構成](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)に関する記事を参照してください。

IPsec を使用して 2 つの Resource Manager 仮想ネットワーク間の接続を構成するには、各仮想ネットワークで「[Azure portal でサイト間接続を作成する](../vpn-gateway/tutorial-site-to-site-portal.md)」の手順 1 - 5 に従います。

> [!Note]
> これらの手順は、同じサブスクリプションに存在する仮想ネットワークでのみ使用できます。 仮想ネットワークが異なるサブスクリプションにある場合は、PowerShell を使用して接続する必要があります。 [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) に関する記事を参照してください。

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Resource Manager 仮想ネットワーク間の VPN 接続の検証

![クラシック仮想ネットワークから Azure Resource Manager 仮想ネットワークへの接続](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

VPN 接続が正しく構成されていることを確認するには、次の手順に従います。

> [!Note] 
> 次の手順で、仮想ネットワーク コンポーネントの後の数字は、前の図の番号に対応しています。

1. 接続された仮想ネットワークでアドレス空間が重複していないかどうかを確認します。
2. Azure Resource Manager 仮想ネットワーク (1) のアドレス範囲が **接続オブジェクト** インスタンス (4) で正確に定義されていることを確認します。
3. Azure Resource Manager 仮想ネットワーク (6) のアドレス範囲が **接続オブジェクト** インスタンス (3) で正確に定義されていることを確認します。
4. 事前共有キーが接続オブジェクトで一致していることを確認します。
5. Azure Resource Manager 仮想ネットワーク ゲートウェイ VIP (2) が **接続オブジェクト** インスタンス (4) で正確に定義されていることを確認します。
6. Azure Resource Manager 仮想ネットワーク ゲートウェイ VIP (5) が **接続オブジェクト** インスタンス (3) で正確に定義されていることを確認します。

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>クラシック仮想ネットワークの Resource Manager 仮想ネットワークへの接続

別のサブスクリプション、別のリージョンに存在する仮想ネットワーク間で接続を作成することができます。 ゲートウェイの種類をルート ベースとして構成している場合に限り、オンプレミスのネットワークに既に接続されている仮想ネットワークを接続することもできます。

クラシック仮想ネットワークと Resource Manager 仮想ネットワーク間の接続を構成する方法については、「[Azure portal を使って異なるデプロイ モデルの仮想ネットワークを接続する](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)」を参照してください。

![クラシック仮想ネットワークから Azure Resource Manager 仮想ネットワークへの接続を示す図。](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

クラシック仮想ネットワークを Azure Resource Manager 仮想ネットワークに接続するときの構成を確認するには、次の手順に従います。

> [!Note] 
> 次の手順で、仮想ネットワーク コンポーネントの後の数字は、前の図の番号に対応しています。 

1. 接続された仮想ネットワークでアドレス空間が重複していないかどうかを確認します。
2. Azure Resource Manager 仮想ネットワーク (6) のアドレス範囲がクラシック ローカル ネットワーク定義 (3) で正確に定義されていることを確認します。
3. クラシック仮想ネットワーク (1) のアドレス範囲が Azure Resource Manager **接続オブジェクト** インスタンス (4) で正確に定義されていることを確認します。
4. クラシック仮想ネットワーク ゲートウェイ VIP(2) が Azure Resource Manager **接続オブジェクト** インスタンス (4) で正確に定義されていることを確認します。
5. Azure Resource Manager 仮想ネットワーク ゲートウェイ (5) がクラシック **ローカル ネットワーク定義** インスタンス (3) で正確に定義されていることを確認します。
6. 接続されている仮想ネットワークの両方で、事前共有キーが一致していることを確認します。
   - クラシック仮想ネットワーク:**ローカル ネットワーク定義** (3)
   - Azure Resource Manager 仮想ネットワーク:**接続オブジェクト** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>ポイント対サイト VPN 接続を作成する

ポイント対サイト (次の図の *P2S*) 構成では、個々のクライアント コンピューターから仮想ネットワークへのセキュリティで保護された接続を作成することができます。 ポイント対サイト接続は、自宅や会議室など、リモートの場所から仮想ネットワークに接続する場合に便利です。 また、仮想ネットワークに接続するクライアントの数がごくわずかである場合にも便利です。 

ポイント対サイト VPN 接続は、クライアント コンピューターからネイティブ Windows VPN クライアントを介して開始されます。 クライアントの接続には、認証のために証明書が使用されます。

![Point-to-site connection](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

ポイント対サイト接続では、VPN デバイスは必要ありません。 Secure Socket トンネリング プロトコル (SSTP) 経由での VPN 接続が作成されます。 さまざまなデプロイ ツールとデプロイ モデルを使用して、ポイント対サイト接続を仮想ネットワークに接続できます。

* [Azure portal を使用して仮想ネットワークへのポイント対サイト接続を構成する](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Azure portal (クラシック) を使用して仮想ネットワークへのポイント対サイト接続を構成する](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)
* [PowerShell を使用して仮想ネットワークへのポイント対サイト接続を構成する](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="validate-your-point-to-site-connection"></a>ポイント対サイト接続の検証

記事「[トラブルシューティング:Azure ポイント対サイト接続の問題](../vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)」では、ポイント対サイト接続に関する一般的な問題について説明しています。

## <a name="create-a-multisite-vpn-connection"></a>マルチサイトの VPN 接続を作成する

サイト間接続、ポイント対サイト接続、またはネットワーク間接続が既にある仮想ネットワークに、サイト間 (次の図の *S2S*) 接続を追加することができます。 この種類の接続は、一般に "*マルチサイト*" 構成と呼ばれます。 

![マルチサイト接続](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure は現在、2 つのデプロイメント モデルで使用できます。Resource Manager とクラシックです。 これらの 2 つのモデルには、完全に互換性があるわけではありません。 異なるモデルを使用してマルチサイト接続を構成するには、次の記事を参照してください。

* [既存の VPN ゲートウェイ接続を使用してサイト間接続を仮想ネットワークに追加する](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
* [既存の VPN ゲートウェイ接続を使用してサイト間接続を仮想ネットワークに追加する (クラシック)](../vpn-gateway/vpn-gateway-multi-site.md)

> [!Note]
> これらの記事にある手順は、Azure ExpressRoute とサイト間の共存接続の構成には適用されません。 詳細については、[ExpressRoute とサイト間の共存接続](../expressroute/expressroute-howto-coexist-resource-manager.md)に関する記事を参照してください。

## <a name="configure-transit-routing"></a>トランジット ルーティングの構成

トランジット ルーティングは、デイジーチェーン トポロジで複数のネットワークを接続する特定のルーティング シナリオです。 このルーティングにより、チェーンのいずれかの端にある仮想ネットワークのリソースが、中間の仮想ネットワークを介して相互に通信できるようになります。 トランジット ルーティングを使用しない場合、ハブを介してピアリングしたネットワークまたはデバイスは互いに接続できません。

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>ポイント対サイト接続でのトランジット ルーティングの構成

VNetA と VNetB の間にサイト間 VPN 接続を構成したい、というシナリオを考えてみましょう。 また、クライアントが VNetA のゲートウェイに接続するようにポイント対サイト VPN を構成したいとします。 その後、ポイント対サイト クライアントが VNetB に接続し、VNetA を通過するトランジット ルーティングを有効にしたいとします。 

このシナリオは、VNetA と VNetB の間で、BGP がサイト間 VPN で有効になっている場合にサポートされます。 詳細については、「[ポイント対サイト VPN ルーティングについて](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)」を参照してください。

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>ExpressRoute 接続でのトランジット ルーティングの構成

Azure ExpressRoute を利用すれば、接続プロバイダーが提供する専用プライベート接続で、オンプレミスのネットワークを Microsoft Cloud に拡張できます。 ExpressRoute では、Microsoft Azure、Microsoft 365、Dynamics 365 などの Microsoft クラウド サービスへの接続を確立できます。 詳細については、「[ExpressRoute の概要](../expressroute/expressroute-introduction.md)」を参照してください。

![Azure 仮想ネットワークへの ExpressRoute プライベート ピアリング接続](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> VNetA と VNetB が同じ地理的リージョンにある場合は、トランジット ルーティングを構成するのではなく、[両方の仮想ネットワークを ExpressRoute 回線にリンク](../expressroute/expressroute-howto-linkvnet-arm.md)することをお勧めします。 仮想ネットワークが異なる地理的リージョンにある場合で、[ExpressRoute Premium](../expressroute/expressroute-faqs.md#expressroute-premium) をお持ちの場合は、それらを直接回線にリンクすることもできます。 

ExpressRoute とサイト間が共存している場合、トランジット ルーティングはサポートされません。 詳細については、[PowerShell を使用した ExpressRoute およびサイト間の構成](../expressroute/expressroute-howto-coexist-resource-manager.md)に関する記事を参照してください。

ローカル ネットワークを Azure 仮想ネットワークに接続するために ExpressRoute を有効にした場合は、トランジット ルーティングを行う仮想ネットワーク間でピアリングを有効にすることができます。 ローカル ネットワークがリモート仮想ネットワークに接続できるようにするには、[仮想ネットワーク ピアリング](./virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)を構成する必要があります。 

> [!Note]
> 仮想ネットワークピアリングは、同じリージョン内の仮想ネットワークに対してのみ使用できます。

仮想ネットワーク ピアリングにトランジット ルーティングを構成したかどうかを確認するには、次の手順に従います。

1. 必要な[ロールとアクセス許可](virtual-network-manage-peering.md#permissions)があるアカウントを使用して、[Azure portal](https://portal.azure.com/) にサインインします。
2. 前の図に示されているように、[VNetA と VNetB の間にピアリングを作成](./tutorial-connect-virtual-networks-portal.md)します。 
3. 仮想ネットワークに対して表示されたウィンドウで、 **[設定]** セクションの **[ピアリング]** を選択します。
4. 表示するピアリングを選択します。 次に、 **[構成]** をクリックして ExpressRoute 回線に接続されている VNetA ネットワークで **[ゲートウェイ転送を許可する]** が有効になっており、ExpressRoute 回線に接続されていないリモート VNetB ネットワークで **[Use Remote Gateway]\(リモート ゲートウェイを使用する\)** が有効になっていることを確認します。

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>仮想ネットワーク ピアリング接続でのトランジット ルーティングの構成

仮想ネットワークがピアリングされているとき、そのピアリングされた仮想ネットワークのゲートウェイを、オンプレミスのネットワークへのトランジット ポイントとして構成することもできます。 仮想ネットワーク ピアリングでトランジット ルートを構成するには、[ネットワーク間接続](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

> [!Note]
> ゲートウェイ トランジットは、異なるデプロイメント モデルを使って作成された仮想ネットワーク間のピアリング関係ではサポートされません。 ゲートウェイ トランジットが機能するためには、ピアリング関係にある両方の仮想ネットワークが Resource Manager を介して作成されている必要があります。

仮想ネットワーク ピアリングにトランジット ルートを構成したかどうかを確認するには、次の手順に従います。

1. 必要な[ロールとアクセス許可](virtual-network-manage-peering.md#permissions)があるアカウントを使用して、[Azure portal](https://portal.azure.com/) にサインインします。
2. ポータルの上部にある "**リソースの検索**" というテキストが表示されたボックスに、「**仮想ネットワーク**」と入力します。 検索結果に **[仮想ネットワーク]** が表示されたら、それを選択します。
3. 表示される **[仮想ネットワーク]** ブレードで、ピアリング設定を確認する仮想ネットワークを選択します。
4. 選択した仮想ネットワークに対して表示されたウィンドウで、 **[設定]** セクションの **[ピアリング]** を選択します。
5. 表示するピアリングを選択します。 **[構成]** の下で **[ゲートウェイ転送を許可する]** と **[Use remote gateway]\(リモート ゲートウェイを使用する\)** が有効になっていることを確認します。

![仮想ネットワーク ピアリングにトランジット ルートを構成したかどうかを確認するための選択](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>ネットワーク間接続でのトランジット ルーティングの構成

仮想ネットワーク間のトランジット ルーティングを構成するには、Resource Manager デプロイ モデルと PowerShell を使用して、すべての中間ネットワーク間接続で BGP を有効にする必要があります。 手順については、「[PowerShell を使用して Azure VPN Gateway で BGP を構成する方法](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md)」を参照してください。

クラシック デプロイ モデルを介して Azure VPN ゲートウェイ経由でトラフィックを通過させることは可能です。この場合、ネットワーク構成ファイル内の静的に定義されたアドレス空間が使用されます。 BGP はまだ、クラシック デプロイ モデルを介した Azure 仮想ネットワークと VPN ゲートウェイではサポートされていません。 BGP が使用できない場合、手動で通過アドレス空間を定義するとエラーが発生しやすいため、これは推奨していません。

> [!Note]
> クラシックのネットワーク間接続を構成するには、Azure クラシック ポータルを使用するか、クラシック ポータル内でネットワーク構成ファイルを使用します。 Azure Resource Manager デプロイ モデルまたは Azure portal を使用してクラシック仮想ネットワークを作成または変更することはできません。 クラシック仮想ネットワークのトランジット ルーティングに関する詳細については、[Microsoft Developer のブログ](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1)を参照してください。

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>サイト間接続でのトランジット ルーティングの構成

オンプレミス ネットワークとサイト間接続を使用した仮想ネットワークの間にトランジット ルーティングを構成するには、Resource Manager デプロイ モデルと PowerShell を使用して、すべての中間サイト間接続で BGP を有効にする必要があります。 手順については、「[PowerShell を使用して Azure VPN Gateway で BGP を構成する方法](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md)」を参照してください。

クラシック デプロイ モデルを介して Azure VPN ゲートウェイ経由でトラフィックを通過させることは可能です。この場合、ネットワーク構成ファイル内の静的に定義されたアドレス空間が使用されます。 BGP はまだ、クラシック デプロイ モデルを介した Azure 仮想ネットワークと VPN ゲートウェイではサポートされていません。 BGP が使用できない場合、手動で通過アドレス空間を定義するとエラーが発生しやすいため、これは推奨していません。

> [!Note]
> クラシックのサイト間接続を構成するには、Azure クラシック ポータルを使用するか、クラシック ポータル内でネットワーク構成ファイルを使用します。 Azure Resource Manager デプロイ モデルまたは Azure portal を使用してクラシック仮想ネットワークを作成または変更することはできません。 クラシック仮想ネットワークのトランジット ルーティングに関する詳細については、[Microsoft Developer のブログ](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1)を参照してください。

## <a name="configure-bgp-for-a-vpn-gateway"></a>VPN ゲートウェイ用の BGP の構成

BGP は、2 つ以上のネットワーク間でルーティングと到達可能性の情報を交換するためにインターネット上で使用されている標準のルーティング プロトコルです。 Azure 仮想ネットワークのコンテキストで BGP を使用すると、BGP によって Azure VPN ゲートウェイとオンプレミスの VPN デバイス (BGP ピアまたは近隣ノードと呼ばれます) が有効になります。 これらは、関連するゲートウェイまたはルーターの可用性と、プレフィックスが到達できる可能性に関する情報を両方のゲートウェイに伝達する "ルート" をやり取りします。 

また、BGP では、BGP ゲートウェイが特定の BGP ピアから学習したルートを他のすべての BGP ピアに伝達することで、複数のネットワークでトランジット ルーティングを行うこともできます。 詳細については、[BGP と Azure VPN Gateway の概要](../vpn-gateway/vpn-gateway-bgp-overview.md)に関する記事を参照してください。

### <a name="configure-bgp-for-a-vpn-connection"></a>VPN 接続用の BGP の構成

BGP を使用する VPN 接続を構成するには、「[PowerShell を使用して Azure VPN Gateway で BGP を構成する方法](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md)」を参照してください。

自律システム (AS) 番号を作成することによって、仮想ネットワーク ゲートウェイで BGP を有効にします。 基本ゲートウェイでは、BGP はサポートされていません。 ゲートウェイの SKU を確認するには、Azure portal で **[VPN Gateway]** ブレードの **[概要]** セクションに移動します。 SKU が **Basic** の場合は、SKU を **VpnGw1** に変更 ([ゲートウェイのサイズ変更](/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway)に関する記事を参照) する必要があります。 

SKU を確認すると、20 分から 30 分のダウンタイムが発生します。 ゲートウェイが適切な SKU を持つようになったら、[Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway) PowerShell コマンドレットを使用して AS 番号を追加できます。 AS 番号を構成すると、ゲートウェイの BGP ピア IP が自動的に提供されます。

AS 番号と BGP ピア アドレスを `LocalNetworkGateway` に手動で指定する必要があります。 [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.network/new-azurermlocalnetworkgateway) または [Set-AzureRmLocalNetworkGateway](/powershell/module/azurerm.network/set-azurermlocalnetworkgateway) PowerShell コマンドレットを使用して、`ASN` と `-BgpPeeringAddress` 値を設定できます。 一部の AS 番号は Azure 用に予約されており、「[BGP と Azure VPN Gateway について](../vpn-gateway/vpn-gateway-bgp-overview.md#faq)」で説明されているように、それらを使用することはできません。

接続オブジェクトで BGP が有効になっている必要があります。 `-EnableBGP` 値を `$True` に設定するには、[New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection) または [Set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection) を使用します。

### <a name="validate-the-bgp-configuration"></a>BGP 構成の検証

BGP が正しく構成されているかどうかを確認するには、`get-AzureRmVirtualNetworkGateway` と `get-AzureRmLocalNetworkGateway` コマンドレットを実行します。 次に、`BgpSettingsText` 部分で BGP 関連の出力を確認します。 次に例を示します。

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>高可用性のアクティブ/アクティブ VPN 接続を作成する

アクティブ/アクティブ ゲートウェイとアクティブ/スタンバイ ゲートウェイの主な違いは次のとおりです:

* 2 つのパブリック IP アドレスを使用して 2 つのゲートウェイ IP 構成を作成する必要があります。
* **EnableActiveActiveFeature** フラグを設定する必要があります。
* ゲートウェイ SKU は、**VpnGw1**、**VpnGw2**、または **VpnGw3** である必要があります。

クロスプレミスとネットワーク間接続で高可用性を実現するには、複数の VPN ゲートウェイをデプロイし、ネットワークと Azure 間に複数の並列接続を確立する必要があります。 接続オプションとトロポジの概要については、[高可用性のクロスプレミス接続およびネットワーク間接続](../vpn-gateway/vpn-gateway-highlyavailable.md)に関する記事を参照してください。

アクティブ/アクティブのクロスプレミス接続とネットワーク間接続を作成するには、「[Azure VPN ゲートウェイで、アクティブ/アクティブ S2S VPN 接続を構成する](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md)」の手順に従って、アクティブ/アクティブ モードで Azure VPN ゲートウェイを構成します。

> [!Note]  
> * BGP が有効になっているアクティブ/アクティブ モードのローカル ネットワーク ゲートウェイにアドレスを追加する場合、*BGP ピアの /32 アドレスのみを追加* します。 さらにアドレスを追加すると、それらは静的ルートと見なされ、BGP ルートよりも優先されます。
> * Azure に接続するオンプレミス ネットワークには、異なる BGP AS 番号を使用する必要があります。 (これらが同じ場合、オンプレミス VPN デバイスが既に ASN を使用して他の BGP 近隣ノードとピアリングしているのであれば、仮想ネットワーク AS 番号を変更する必要があります。)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>デプロイ後に Azure VPN ゲートウェイの種類を変更する

Azure 仮想ネットワーク ゲートウェイの種類をポリシーベースからルートベース (またはその逆) に変更することはできません。 最初にゲートウェイを削除する必要があります。 その後は、IP アドレスと事前共有キーは保持されません。 その後、目的の種類の新しいゲートウェイを作成できます。 

ゲートウェイを削除して作成するには、次の手順に従います。

1. 元のゲートウェイに関連付けられているすべての接続を削除します。
2. Azure portal、PowerShell、またはクラシック PowerShell を使用してゲートウェイを削除します。 
   * [Azure portal を使用して仮想ネットワーク ゲートウェイを削除する](../vpn-gateway/vpn-gateway-delete-vnet-gateway-portal.md)
   * [PowerShell を使用して仮想ネットワーク ゲートウェイを削除する](../vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell.md)
   * [PowerShell を使用して仮想ネットワーク ゲートウェイを削除する (クラシック)](../vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell.md)
3. 「[VPN ゲートウェイを作成する](../vpn-gateway/tutorial-site-to-site-portal.md#VNetGateway)」の手順に従って、目的の種類の新しいゲートウェイを作成し、VPN の設定を完了します。

> [!Note]
> このプロセスには約 60 分かかります。

## <a name="next-steps"></a>次のステップ

* [Azure VM 間の接続に関する問題のトラブルシューティング](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
