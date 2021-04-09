---
title: アーキテクチャ:グローバル転送ネットワーク アーキテクチャ
titleSuffix: Azure Virtual WAN
description: クラウド ワークロード間でユビキタスな Any-to-Any 接続を有効にすることで、Azure Virtual WAN でグローバル トランジット ネットワークを実現することについて説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: cherylmc
ms.openlocfilehash: cfb75b6383d8ca449b4bc54b9d21cb16b3a4ad40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99428185"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>グローバル トランジット ネットワーク アーキテクチャと Virtual WAN

現代の企業では、クラウドとオンプレミスに高度に分散されたアプリケーション、データ、ユーザーの間のユビキタスな接続性が必要です。 グローバル トランジット ネットワーク アーキテクチャは、クラウド中心の最新のグローバル エンタープライズ IT フットプリントを統合、接続、制御するために、企業によって採用されています。

グローバル トランジット ネットワーク アーキテクチャは、クラウドでホストされたネットワーク "ハブ" によって、さまざまな種類の "スポーク" に分散されている可能性があるエンドポイント間の推移的な接続が可能になる、従来のハブ アンド スポーク接続モデルが基になっています。

このモデルでは、次のものがスポークになることができます。
* 仮想ネットワーク (VNet)
* 物理的なブランチ サイト
* リモート ユーザー
* インターネット

![ハブ アンド スポーク](./media/virtual-wan-global-transit-network-architecture/figure1.png)

"**図 1:グローバル トランジット ハブアンドスポーク ネットワーク**

図 1 で示すグローバル トランジット ネットワークの論理ビューでは、地理的に分散したユーザー、物理サイト、VNet が、クラウドでホストされたネットワーク ハブを介して相互接続されています。 このアーキテクチャでは、ネットワーク エンドポイント間の論理的な 1 ホップ トランジット接続が可能になります。

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Virtual WAN でのグローバル トランジット ネットワーク

Azure Virtual WAN は Microsoft が管理するクラウド ネットワーク サービスです。 このサービスを構成するすべてのネットワーク コンポーネントは、Microsoft によってホストされて管理されます。 Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。

Azure Virtual WAN を使うと、VNet、ブランチ サイト、SaaS および PaaS アプリケーション、ユーザーのグローバルに分散したクラウド ワークロードのセットの間で、ユビキタスな Any-to-Any 接続を有効にすることにより、グローバル トランジット ネットワーク アーキテクチャを実現できます。

![Azure Virtual WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

"**図 2:グローバル トランジット ネットワークと Virtual WAN**

Azure Virtual WAN アーキテクチャでは、Virtual WAN のハブは Azure リージョンにおいてプロビジョニングされ、それにブランチ、VNet、リモート ユーザーを接続できます。 物理ブランチ サイトは、Premium または Standard の ExpressRoute またはサイト間 VPN によってハブに接続されます。VNet は、VNet 接続によってハブに接続されます。リモート ユーザーは、ユーザー VPN (ポイント対サイト VPN) を使ってハブに直接接続できます。 また、Virtual WAN を使うと、あるリージョン内の VNet を別のリージョンの Virtual WAN ハブに接続できるリージョン間 VNet 接続もサポートされます。

Virtual WAN を確立するには、スポーク (ブランチ、VNet、ユーザー) の数が最も多いリージョンに Virtual WAN ハブを 1 つ作成した後、他のリージョン内にあるスポークをハブに接続します。 これは、エンタープライズ フットプリントの大部分が 1 つのリージョン内にあってリモート スポークの数が少ない場合に適したオプションです。  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>ハブ間接続

エンタープライズ クラウドのフットプリントは複数のクラウド リージョンにまたがることができ、物理サイトやユーザーに最も近いリージョンからクラウドにアクセスするのに最適です (待ち時間対応)。 グローバル トランジット ネットワーク アーキテクチャの重要な原則の 1 つは、すべてのクラウドとオンプレミスのネットワーク エンドポイントの間のリージョン間接続を有効にすることです。 つまり、あるリージョンのクラウドに接続されているブランチからのトラフィックは、[Azure のグローバル ネットワーク](https://azure.microsoft.com/global-infrastructure/global-network/)によって有効になるハブ間接続を使用して、異なるリージョンにある別のブランチまたは VNet に到達できます。

![リージョン間](./media/virtual-wan-global-transit-network-architecture/figure3.png)

"**図 3:Virtual WAN のリージョン間接続**

1 つの Virtual WAN で複数のハブを有効にすると、ハブはハブ間リンクによって自動的に相互接続されるため、複数のリージョンに分散されたブランチと VNet の間のグローバル接続が可能になります。 

さらに、すべて同じ Virtual WAN の一部である複数のハブを、異なるリージョン アクセス ポリシーおよびセキュリティ ポリシーに関連付けることができます。 詳細については、後の「[セキュリティとポリシーの制御](#security)」を参照してください。

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Any-to-Any 接続

グローバル トランジット ネットワーク アーキテクチャを使うと、Virtual WAN ハブ経由で Any-to-Any 接続を実現できます。 このアーキテクチャにより、構築と管理がより複雑な、スポーク間のフル メッシュ接続または部分メッシュ接続を使用する必要が、まったくなくなるか減ります。 さらに、メッシュ ネットワークよりハブ アンド スポークの方が、ルーティング制御の構成と保守が簡単です。

グローバル アーキテクチャのコンテキストにおける Any-to-Any 接続により、企業のグローバルに分散したユーザー、ブランチ、データセンター、VNet、アプリケーションは、"トランジット" ハブ経由で相互に接続できます。 Azure Virtual WAN は、グローバルなトランジット システムとして機能します。

![Any-to-Any](./media/virtual-wan-global-transit-network-architecture/figure4.png)

"**図 4:Virtual WAN のトラフィックのパス**

Azure Virtual WAN では、次のグローバル トランジット接続パスがサポートされています。 かっこ内の文字は図 4 に対応します。

* ブランチと VNet (a)
* ブランチとブランチ (b)
  * ExpressRoute Global Reach と Virtual WAN
* リモート ユーザーと VNet (c)
* リモート ユーザーとブランチ (d)
* VNet 対 VNet (e)
* ブランチ対ハブとハブ対ブランチ (f)
* ブランチ対ハブとハブ対 VNet (g)
* VNet 対ハブとハブ対 VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>ブランチ対 VNet (a) およびブランチ対 VNet クロスリージョン (g)

ブランチと VNet の間は、Azure Virtual WAN によってサポートされる主要なパスです。 このパスにより、Azure VNet にデプロイされた Azure IAAS エンタープライズ ワークロードにブランチを接続できます。 ブランチは、ExpressRoute またはサイト間 VPN を介して仮想 WAN に接続できます。 トラフィックは、VNet 接続を介して Virtual WAN ハブに接続されている VNet に転送されます。 Virtual WAN によってブランチ サイトへのゲートウェイ トランジットが自動的に有効になるので、明示的な[ゲートウェイ トランジット](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)は Virtual WAN には必要ありません。 SD-WAN CPE を Virtual WAN に接続する方法については、「[Virtual WAN パートナー](virtual-wan-configure-automation-providers.md)」を参照してください。

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach と Virtual WAN

ExpressRoute は、複数のオンプレミス ネットワークを Microsoft Cloud に接続することができるプライベートで回復性がある方法です。 Virtual WAN を使うと、ExpressRoute 回線接続がサポートされます。 ExpressRoute を使って Virtual WAN にブランチ サイトを接続するには、1) Premium または Standard 回線、2) Global Reach が有効な場所に配置された回線が必要です。

ExpressRoute Global Reach は、ExpressRoute に対するアドオン機能です。 Global Reach を使用すると、ExpressRoute 回線を相互にリンクして、オンプレミス ネットワーク間にプライベート ネットワークを構築できます。 ExpressRoute を使用して Azure Virtual WAN に接続されたブランチが相互に通信するには、ExpressRoute Global Reach が必要です。

このモデルでは、ExpressRoute を使って Virtual WAN ハブに接続されている各ブランチは、ブランチと VNet の間のパスを使って VNet に接続できます。 ExpressRoute Global Reach の方が Azure WAN より最適なパスを提供するので、ブランチとブランチの間のトラフィックはハブを経由しません。

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>ブランチ対ブランチ (b) およびブランチ対ブランチ クロスリージョン (f)

ブランチは、ExpressRoute 回線またはサイト間 VPN 接続を使用して、Azure Virtual WAN ハブに接続できます。 ブランチに最も近いリージョンにある Virtual WAN ハブに、ブランチを接続できます。

このオプションでは、企業は Azure バックボーンを利用してブランチを接続できます。 ただし、この機能を使用することはできますが、ブランチの接続に Azure Virtual WAN を使う場合とプライベート WAN を使う場合の利点を比較検討する必要があります。  

> [!NOTE]
> Virtual WAN 間でのブランチ間接続の無効化を構成すると、ブランチ間接続を無効にできます。 この構成によって、VPN (S2S と P2S) と Express Route 接続サイトの間のルート伝達がブロックされます。 この構成は、ブランチと VNet 間および VNet と VNet 間のルート伝達と接続には影響しません。 Azure portal を使用してこの設定を構成するには、次の手順を実行します。[Virtual WAN Configuration] (Virtual WAN 構成) メニューの [Setting:Branch-to-Branch - Disabled] (設定: ブランチとブランチ - 無効) を選択します。 

### <a name="remote-user-to-vnet-c"></a>リモート ユーザーと VNet (c)

リモート ユーザー クライアントから Virtual WAN へのポイント対サイト接続を使用して、Azure へのセキュリティ保護された直接リモート アクセスを有効にすることができます。 エンタープライズのリモート ユーザーは、クラウドに戻るのに企業 VPN を使う必要がなくなります。

### <a name="remote-user-to-branch-d"></a>リモート ユーザーとブランチ (d)

リモート ユーザーとブランチのパスでは、Azure へのポイント対サイト接続を使っているリモート ユーザーが、クラウドを経由してオンプレミスのワークロードとアプリケーションにアクセスできます。 このパスにより、リモート ユーザーは、Azure とオンプレミスの両方にデプロイされているワークロードに柔軟にアクセスできます。 企業では、Azure Virtual WAN でクラウドベースのセキュリティ保護された集中リモート アクセス サービスを有効にすることができます。

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet 対 VNet トランジット (e) および VNet 対 VNet クロスリージョン (h)

VNet 対 VNet トランジットを使うと、VNet を相互に接続し、複数の VNet に実装されている多層アプリケーションを相互接続できます。 必要に応じて、VNet ピアリングを使用して VNet を相互に接続でき、これは VWAN ハブ経由の転送が不要なシナリオに適している場合があります。


## <a name="force-tunneling-and-default-route-in-azure-virtual-wan"></a><a name="DefaultRoute"></a>Azure Virtual WAN での強制トンネリングと既定のルート

強制トンネリングを有効にするには、Virtual WAN の VPN、ExpressRoute、または Virtual Network 接続で既定のルートの有効化を構成します。

仮想ハブは、仮想ネットワーク、サイト間 VPN、または ExpressRoute 接続に対し、学習した既定のルートを伝達します (既定の有効化フラグが、その接続で "有効" になっている場合)。 

このフラグは、ユーザーが仮想ネットワーク接続、VPN 接続、または ExpressRoute 接続を編集するときに表示されます。 サイトまたは ExpressRoute 回線がハブに接続されると、このフラグは既定で無効になります。 VNet を仮想ハブに接続するための仮想ネットワーク接続が追加されたときは、既定で有効になります。 既定のルートの起点は Virtual WAN ハブではありません。Virtual WAN ハブにファイアウォールをデプロイした結果としてそのハブが既定のルートを既に学習している場合、または接続されている別のサイトで強制トンネリングが有効な場合に、既定のルートが伝達されます。

## <a name="security-and-policy-control"></a><a name="security"></a>セキュリティとポリシーの制御

Azure Virtual WAN ハブにより、ハイブリッド ネットワーク上のすべてのネットワーク エンドポイントが相互接続され、すべてのトランジット ネットワーク トラフィックが認識される可能性があります。 VWAN ハブ内に Azure Firewall を展開し、クラウドベースのセキュリティ、アクセス、ポリシー制御を有効にすることにより、Virtual WAN ハブをセキュリティ保護付き仮想ハブに変換できます。 Virtual WAN ハブ内の Azure Firewall のオーケストレーションは、Azure Firewall Manager で実行できます。

[Azure Firewall Manager](../firewall-manager/index.yml) を使うと、セキュリティを管理し、グローバル トランジット ネットワーク用にスケーリングする機能が提供されます。 Azure Firewall Manager には、Azure Firewall と共に、ルーティングの一元管理、グローバル ポリシーの管理、サードパーティによる高度なインターネット セキュリティ サービスの機能が用意されています。

![Azure Firewall によるセキュリティ保護付き仮想ハブ](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**図 5:Azure Firewall によるセキュリティ保護付き仮想ハブ**

Virtual WAN に対する Azure Firewall では、次のグローバルなセキュリティ保護付きトランジット接続パスがサポートされています。 かっこ内の文字は図 5 に対応します。

* VNet 対 VNet セキュリティ保護付きトランジット (e)
* VNet 対インターネットまたはサードパーティ セキュリティ サービス (i)
* ブランチ対インターネットまたはサードパーティ セキュリティ サービス (i)

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet 対 VNet セキュリティ保護付きトランジット (e)

VNet 対 VNet セキュリティ保護付きトランジットを使うと、VNet を Virtual WAN ハブ内の Azure Firewall を介して相互に接続できます。

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet 対インターネットまたはサードパーティ セキュリティ サービス (i)

VNet 対インターネットを使うと、VNet を Virtual WAN ハブ内の Azure Firewall を介してインターネットに接続できます。 サポートされているサードパーティのセキュリティ サービスを介したインターネットへのトラフィックは、Azure Firewall を経由しません。 Azure Firewall Manager を使用して、サポートされているサードパーティのセキュリティ サービスを介して Vnet 対インターネットのパスを構成できます。  

### <a name="branch-to-internet-or-third-party-security-service-j"></a>ブランチ対インターネットまたはサードパーティ セキュリティ サービス (i)
ブランチ対インターネットを使用すると、ブランチはVirtual WAN ハブ内の Azure Firewall を介してインターネットに接続できます。 サポートされているサードパーティのセキュリティ サービスを介したインターネットへのトラフィックは、Azure Firewall を経由しません。 Azure Firewall Manager を使用して、サポートされているサードパーティのセキュリティ サービスを介してブランチ対インターネットのパスを構成できます。 

### <a name="branch-to-branch-secured-transit-cross-region-f"></a>リージョン間のブランチ対ブランチのセキュリティ保護付きトランジット (f)

ブランチは、ExpressRoute 回線またはサイト間 VPN 接続を使用して、Azure Firewall によるセキュリティ保護付き仮想ハブに接続できます。 ブランチに最も近いリージョンにある Virtual WAN ハブに、ブランチを接続できます。

このオプションでは、企業は Azure バックボーンを利用してブランチを接続できます。 ただし、この機能を使用することはできますが、ブランチの接続に Azure Virtual WAN を使う場合とプライベート WAN を使う場合の利点を比較検討する必要があります。  

> [!NOTE]
> ファイアウォール経由のトラフィックのハブ間の処理は現在サポートされていません。 ハブ間のトラフィックは、セキュリティ保護付き仮想ハブ内の適切なブランチにルーティングされます。ただし、トラフィックは各ハブの Azure Firewall をバイパスします。

### <a name="branch-to-vnet-secured-transit-g"></a>ブランチ対 VNet セキュリティ保護付きトランジット (g)

ブランチ対 VNet セキュリティ保護付きトランジットを使用すると、ブランチは、仮想 WAN ハブと同じリージョン内の仮想ネットワーク、および別のリージョンの別の仮想 WAN ハブに接続されている別の仮想ネットワークと通信できます。

> [!NOTE]
> ハブ間のファイアウォールは現在サポートされていません。 ハブ間のトラフィックは、各ハブ内の Azure Firewall をバイパスして直接移動します。  同じリージョン内の仮想ネットワークへの接続経由のトラフィックは、セキュリティ保護付きハブの Azure Firewall によって処理されます。


### <a name="how-do-i-enable-default-route-00000-in-a-secured-virtual-hub"></a>セキュリティ保護付き仮想ハブで既定のルート (0.0.0.0/0) を有効にする方法

Virtual WAN ハブ (セキュリティ保護付き仮想ハブ) に展開された Azure Firewall は、すべてのブランチ (VPN または ExpressRoute によって接続)、スポーク Vnet およびユーザー (P2S VPN 経由で接続) に対して、インターネットまたは信頼されたセキュリティ プロバイダーへの既定のルーターとして構成できます。 この構成は Azure Firewall Manager を使用して行う必要があります。  Azure Firewall 経由のブランチ (ユーザーを含む) からのすべてのトラフィック、および Vnet 対インターネットを構成する方法については、「ハブへのトラフィックのルーティング」を参照してください。 

これは次の 2 つの手順で構成されます。

1. セキュリティ保護付き仮想ハブのルート設定メニューを使用して、インターネット トラフィック ルーティングを構成します。 ファイアウォール経由でインターネットにトラフィックを送信できる Vnet とブランチを構成します。

2. ハブまたは信頼されたセキュリティ プロバイダーの Azure FW 経由でインターネット (0.0.0.0/0) にトラフィックをルーティングできる接続 (Vnet とブランチ) を構成します。 この手順により、既定のルートが、この接続を介して Virtual WAN ハブに接続されている、選択したブランチと Vnet に確実に伝達されます。 

### <a name="force-tunneling-traffic-to-on-premises-firewall-in-a-secured-virtual-hub"></a>セキュリティ保護付き仮想ハブでのオンプレミスのファイアウォールへのトラフィックの強制トンネリング

ブランチ (VPN または ER サイト) のいずれかから仮想ハブによって (BGP 経由で) 学習された既定のルートが既にある場合、この既定のルートは Azure Firewall Manager の設定から学習された既定のルートによってオーバーライドされます。 この場合、Vnet とブランチからハブに入ってきてインターネットに送信されるすべてのトラフィックは、Azure Firewall または信頼されたセキュリティ プロバイダーにルーティングされます。

> [!NOTE]
> 現在、Vnet、ブランチ、またはユーザーから送信されたインターネットへのトラフィックに対して、オンプレミスのファイアウォールまたは Azure Firewall (および信頼されたセキュリティ プロバイダー) を選択するオプションはありません。 Azure Firewall Manager の設定から学習した既定のルートが、いずれかのブランチから学習した既定のルートより常に優先されます。


## <a name="next-steps"></a>次のステップ

Virtual WAN を使って接続を作成し、VWAN ハブに Azure Firewall を展開します。

* [Virtual WAN を使用するサイト間接続](virtual-wan-site-to-site-portal.md)
* [Virtual WAN を使用する ExpressRoute 接続](virtual-wan-expressroute-portal.md)
* [VWAN に Azure FW を展開するための Azure Firewall Manager](../firewall-manager/index.yml)
