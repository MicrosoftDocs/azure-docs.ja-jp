---
title: Azure Virtual WAN のグローバル トランジット ネットワーク アーキテクチャ | Microsoft Docs
description: Virtual WAN のグローバル トランジット ネットワーク アーキテクチャについて説明します
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 07/23/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 0a5059382c26afd6120dc14a1ab2c7e5d281e7a1
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695262"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>グローバル トランジット ネットワーク アーキテクチャと Virtual WAN

グローバル トランジット ネットワーク アーキテクチャは、クラウド中心の最新のエンタープライズ IT フットプリントを統合、接続、制御するために、企業によって採用されています。 最新のクラウド中心の企業では、ネットワーク トラフィックは HQ にバックホールされる必要はありません。 グローバル トランジット ネットワーク アーキテクチャは、なじみのあるネットワークの概念と、クラウドおよびクラウドベースのアーキテクチャに固有の新しい概念の両方に基づいています。

![アーキテクチャ](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

"**図 1:Virtual WAN でのグローバル トランジット ネットワーク**

現代の企業では、クラウドとオンプレミスに高度に分散されたアプリケーション、データ、ユーザーの間のユビキタスな接続性が必要です。 Azure Virtual WAN では、グローバルに分散した VNet、サイト、アプリケーション、ユーザーのセットの間でユビキタスな Any-to-Any 接続を有効にすることにより、グローバル トランジット ネットワークが実現されます。 Azure Virtual WAN は Microsoft のマネージド サービスです。 このサービスを構成するすべてのネットワーク コンポーネントは、Microsoft によってホストされて管理されます。 Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。

Azure Virtual WAN のアーキテクチャでは、Azure リージョンがハブとして機能し、それにブランチを接続できます。 ブランチが接続されたら、Azure バックボーンを利用して、ブランチから VNet への接続、および必要に応じてブランチ間の接続を確立できます。

仮想 WAN を確立するには、スポーク (ブランチ、VNet、ユーザー) の数が最も多いリージョンに Virtual WAN ハブを 1 つ作成した後、他のリージョン内にあるスポークをハブに接続します。 または、スポークが地理的に分散している場合は、複数のリージョン ハブをインスタンス化して、ハブを相互接続することもできます。 ハブはすべて同じ仮想 WAN の一部ですが、異なるリージョン ポリシーに関連付けることができます。

## <a name="hub"></a>ハブ アンド スポーク トランジット

グローバル トランジット ネットワーク アーキテクチャは、クラウドでホストされたネットワーク "ハブ" によって、さまざまな種類の "スポーク" に分散されている可能性があるエンドポイント間の推移的な接続が可能になる、従来のハブ アンド スポーク接続モデルが基になっています。
  
このモデルでは、次のものがスポークになることができます。

* 仮想ネットワーク (VNet)
* 物理的なブランチ サイト
* リモート ユーザー
* インターネット

![ハブ アンド スポークのグローバル トランジットの図](./media/virtual-wan-global-transit-network-architecture/architecture.png)

"**図 2:ハブ アンド スポーク**

図 2 で示すグローバル ネットワークの論理ビューでは、地理的に分散したユーザー、物理サイト、VNet が、クラウドでホストされたネットワーク ハブを介して相互接続されています。 このアーキテクチャでは、ネットワーク エンドポイント間の論理的な 1 ホップ トランジット接続が可能になります。 ハブにスポークを接続するには、物理ブランチに対する ExpressRoute やサイト間 VPN、VNet に対する VNet 接続、リモート ユーザーに対するポイント対サイト VPN など、さまざまな Azure ネットワーク サービスが使用されます。

## <a name="crossregion"></a>リージョン間の接続

大きな企業では、通常、クラウドのフットプリントは物理的なフットプリントに従います。 ほとんどの企業では、物理的なサイトやユーザーに最も近いリージョンからクラウドにアクセスします。 グローバル ネットワーク アーキテクチャの重要な原則の 1 つは、ネットワーク エンティティとエンドポイントの間のリージョン間接続を有効にすることです。 クラウドのフットプリントは、複数のリージョンにまたがることができます。 つまり、あるリージョンのクラウドに接続されているブランチからのトラフィックは、現在ロードマップに記載されているハブ間接続を使用して、異なるリージョンにある別のブランチまたは VNet に到達できます。

## <a name="any"></a>Any-to-Any 接続

グローバル トランジット ネットワーク アーキテクチャでは、セントラル ネットワーク ハブによって "*Any-to-Any 接続*" を実現できます。 このアーキテクチャにより、構築と管理がより複雑なフル メッシュまたは部分メッシュの接続モデルを使用する必要が、まったくなくなるか減ります。 さらに、メッシュ ネットワークよりハブ アンド スポークの方が、ルーティング制御の構成と保守が簡単です。

グローバル アーキテクチャのコンテキストにおける Any-to-Any 接続により、企業のグローバルに分散したユーザー、ブランチ、データセンター、VNet、アプリケーションは、トランジット ハブ経由で相互に接続できます。 トランジット ハブは、グローバルなトランジット システムとして機能します。

![トラフィックのパス](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

"**図 3:Virtual WAN のトラフィックのパス**

Azure Virtual WAN では、次のグローバル トランジット接続パスがサポートされています。 かっこ内の文字は図 3 に対応します。

* ブランチと VNet (a)  
* ブランチとブランチ (b)
* リモート ユーザーと VNet (c)
* リモート ユーザーとブランチ (d)
* VNet ピアリングを用いた VNet と VNet (e)
* ExpressRoute Global Reach 

### <a name="branchvnet"></a>Branch-to-VNet

ブランチと VNet の間は、Azure Virtual WAN によってサポートされる主要なパスです。 このパスにより、Azure VNet にデプロイされた Azure IAAS エンタープライズ ワークロードにブランチを接続できます。 ブランチは、ExpressRoute またはサイト間 VPN を介して仮想 WAN に接続できます。 トラフィックは、VNet 接続を介して Virtual WAN ハブに接続されている VNet に転送されます。Virtual WAN によってブランチ サイトへのゲートウェイ トランジットが自動的に有効にされるので、[ゲートウェイ トランジット](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)は Virtual WAN の場合は必要ありません。

### <a name="branchbranch"></a>ブランチとブランチ

ブランチは、ExpressRoute 回線またはサイト間 VPN 接続を使用して、Azure Virtual WAN ハブに接続できます。 ブランチに最も近いリージョンにある Virtual WAN ハブに、ブランチを接続できます。

このオプションでは、企業は Azure バックボーンを利用してブランチを接続できます。 ただし、この機能を使用することはできますが、ブランチの接続に Azure Virtual WAN を使う場合とプライベート WAN を使う場合の利点を比較検討する必要があります。

### <a name="usertovnet"></a>リモート ユーザーと VNet

リモート ユーザー クライアントから仮想 WAN へのポイント対サイト接続を使用して、Azure へのセキュリティ保護された直接リモート アクセスを有効にすることができます。 エンタープライズのリモート ユーザーは、クラウドに戻るのに企業 VPN を使う必要がなくなります。

### <a name="usertobranch"></a>リモート ユーザーとブランチ

リモート ユーザーとブランチのパスでは、Azure へのポイント対サイト接続を使っているリモート ユーザーが、クラウドを経由してオンプレミスのワークロードとアプリケーションにアクセスできます。 このパスにより、リモート ユーザーは、Azure とオンプレミスの両方にデプロイされているワークロードに柔軟にアクセスできます。 企業では、Azure Virtual WAN でクラウドベースのセキュリティ保護された集中リモート アクセス サービスを有効にすることができます。

### <a name="vnetvnet"></a>VNet ピアリングを用いた VNet と VNet

複数の VNet に実装されている多層アプリケーションをサポートするために VNet を相互接続するには、VNet ピアリングを使います。 Azure Virtual WAN 経由のVNet 対 VNet のトランジットのシナリオは、現在はサポートされていませんが、Azure のロードマップ上にはあります。 VNet を相互に接続する必要がある場合に推奨されるソリューションは、VNet ピアリングを介して VNet を接続することです。 

### <a name="globalreach"></a>ExpressRoute Global Reach

ExpressRoute は、複数のオンプレミス ネットワークを Microsoft Cloud に接続することができるプライベートで回復性がある方法です。 ExpressRoute Global Reach は、ExpressRoute に対するアドオン機能です。 Global Reach を使用すると、ExpressRoute 回線を相互にリンクして、オンプレミス ネットワーク間にプライベート ネットワークを構築できます。 ExpressRoute を使用して Azure Virtual WAN に接続されたブランチが相互に通信するには、ExpressRoute Global Reach が必要です。

このモデルでは、ExpressRoute を使って Virtual WAN ハブに接続されている各ブランチは、ブランチと VNet の間のパスを使って VNet に接続できます。 ExpressRoute Global Reach の方が Azure WAN より最適なパスを提供するので、ブランチとブランチの間のトラフィックはハブを経由しません。

## <a name="security"></a>セキュリティとポリシーの制御

仮想ネットワーク ハブは相互接続を行うので、通過するすべてのトラフィックを認識する可能性があります。 クラウド ルーティング、ネットワーク ポリシーとセキュリティ、インターネット アクセス制御などの中央ネットワーク機能とサービスをホストする場所になる可能性があります。

## <a name="next-steps"></a>次の手順

Virtual WAN を使って接続を作成します。

* [Virtual WAN を使用するサイト間接続](virtual-wan-site-to-site-portal.md)
* [Virtual WAN を使用する ExpressRoute 接続](virtual-wan-expressroute-portal.md)
