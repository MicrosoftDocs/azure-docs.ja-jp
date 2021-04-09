---
title: Azure ネットワーク間接続
description: このページでは、Azure ネットワーク機能に基づいたネットワーク間接続とソリューションのアプリケーション シナリオについて説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: duau
ms.openlocfilehash: 018afa1b2a31ebd44925a3fc79cbdc729b2d4695
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202499"
---
# <a name="cross-network-connectivity"></a>ネットワーク間接続

Fabrikam, Inc. は、米国東部に大規模な拠点があり、Azure をデプロイしています。 Fabrikam には、オンプレミスのデプロイと ExpressRoute 経由の Azure デプロイの間にバックエンド接続があります。 同様に、Contoso Ltd. は、米国西部に拠点があり、Azure をデプロイしています。 Contoso には、オンプレミスのデプロイと ExpressRoute 経由の Azure デプロイの間にバックエンド接続があります。  

Fabrikam Inc. は Contoso Ltd. を買収します。この合併の後、Fabrikam はネットワークを相互接続します。 このシナリオを次の図に示します。

![アプリケーションのシナリオ](./media/cross-network-connectivity/premergerscenario.png)

上の図の真ん中の破線の矢印は、必要なネットワーク相互接続を示しています。 具体的には、次の 3 種類の必要なクロス接続があります。1) Fabrikam と Contoso の VNet のクロス接続、2) クロス リージョンのオンプレミスと VNet のクロス接続 (つまり、Contoso の VNet への Fabrikam のオンプレミス ネットワークの接続と、Fabrikam の VNet への Contoso のオンプレミス ネットワークの接続)、および 3) Fabrikam と Contoso のオンプレミス ネットワークのクロス接続。 

次の表は、合併前の Contoso Ltd. の ExpressRoute のプライベート ピアリングのルート テーブルを示しています。

![合併前の Contoso の ExpressRoute ルート テーブル](./media/cross-network-connectivity/contosoexr-rt-premerger.png)

次の表は、合併前の Contoso のサブスクリプションの VM の有効なルートを示しています。 表によれば、VNet 上の VM は、既定のデフォルトのものとは別に、VNet アドレス空間と Contoso のオンプレミス ネットワークを認識しています。

![合併前の Contoso の VM ルート](./media/cross-network-connectivity/contosovm-routes-premerger.png)

次の表は、合併前の Fabrikam Inc. の ExpressRoute のプライベート ピアリングのルート テーブルを示しています。

![合併前の Fabrikam の ExpressRoute ルート テーブル](./media/cross-network-connectivity/fabrikamexr-rt-premerger.png)

次の表は、合併前の Fabrikam のサブスクリプションの VM の有効なルートを示しています。 表によれば、VNet 上の VM は、既定のデフォルトのものとは別に、VNet アドレス空間と Contoso のオンプレミス ネットワークを認識しています。

![合併前の Fabrikam の VM ルート](./media/cross-network-connectivity/fabrikamvm-routes-premerger.png)

この記事では、1 つずつ手順を追って、次の Azure ネットワーク機能を使用して目的のクロス接続を実現する方法について説明します。

* [仮想ネットワーク ピアリング][Virtual network peering] 
* [仮想ネットワークの ExpressRoute 接続][connection]
* [グローバルな展開][Global Reach] 

## <a name="cross-connecting-vnets"></a>VNet のクロス接続

仮想ネットワーク ピアリング (VNet ピアリング) は、2 つの仮想ネットワークを接続しているときに最適で最高のネットワーク パフォーマンスを提供します。 VNet ピアリングは、同じ Azure リージョン内の VNet のピアリング (一般的に VNet ピアリングと呼ばれます) と 2 つの異なる Azure リージョンでの VNet のピアリング (一般的にグローバル VNet ピアリングと呼ばれます) という、2 つ VNet のピアリングを両方ともサポートしています。 

Contoso と Fabrikam の Azure サブスクリプションで VNet 間のグローバル VNet ピアリングを構成しましょう。 2 つの仮想ネットワーク間に仮想ネットワーク ピアリングを作成する方法については、「[仮想ネットワーク ピアリングの作成][Configure VNet peering]」の記事を参照してください。

次の図は、グローバル VNet ピアリングの構成後のネットワーク アーキテクチャを示しています。

![VNet ピアリング後のアーキテクチャ](./media/cross-network-connectivity/vnet-peering.png )

次の表は、Contoso のサブスクリプションの VM に認識されるルートを示しています。 表の最後のエントリに注意してください。 このエントリは、仮想ネットワークのクロス接続の結果です。

![VNet ピアリング後の Contoso の VM ルート](./media/cross-network-connectivity/contosovm-routes-peering.png)

次の表は、Fabrikam のサブスクリプションの VM に認識されるルートを示しています。 表の最後のエントリに注意してください。 このエントリは、仮想ネットワークのクロス接続の結果です。

![VNet ピアリング後の Fabrikam の VM ルート](./media/cross-network-connectivity/fabrikamvm-routes-peering.png)

VNet ピアリングは 2 つの仮想ネットワークを直接リンクさせています (上の 2 つの表で *VNetGlobalPeering* エントリに次ホップがないことを確認できます)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>オンプレミス ネットワークへの VNet のクロス接続

ExpressRoute 回線を複数の仮想ネットワークに接続することができます。 ExpressRoute 回線に接続できる仮想ネットワークの最大数については、「[サブスクリプションとサービスの制限][Subscription limits]」を参照してください。 

Fabrikam の ExpressRoute 回線を Contoso サブスクリプションの VNet に接続し、同様に Contoso の ExpressRoute 回線を Fabrikam サブスクリプションの VNet に接続して、仮想ネットワークとオンプレミス ネットワーク間のクロス接続を有効にしてみましょう。 仮想ネットワークを別のサブスクリプションの ExpressRoute 回線に接続するには、承認を作成して使用する必要があります。  記事「[ExpressRoute 回線に仮想ネットワークを接続する][Connect-ER-VNet]」を参照してください。

次の図は、仮想ネットワークへの ExpressRoute クロス接続を構成した後のネットワーク アーキテクチャを示しています。

![ExpressRoutes のクロス接続後のアーキテクチャ](./media/cross-network-connectivity/exr-x-connect.png)

次の表は、ExpressRoute 経由でオンプレミス ネットワークに仮想ネットワークをクロス接続した後の、Contoso Ltd. の ExpressRoute のプライベート ピアリングのルート テーブルを示しています。 ルート テーブルに両方の仮想ネットワークに属しているルートがあることを確認できます。

![ExR と VNet のクロス接続後の Contoso の ExpressRoute ルート テーブル](./media/cross-network-connectivity/contosoexr-rt-xconnect.png)

次の表は、ExpressRoute 経由でオンプレミス ネットワークに仮想ネットワークをクロス接続した後の、Fabrikam Inc. の ExpressRoute のプライベート ピアリングのルート テーブルを示しています。 ルート テーブルに両方の仮想ネットワークに属しているルートがあることを確認できます。

![ExR と VNet のクロス接続後の Fabrikam の ExpressRoute ルート テーブル](./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png)

次の表は、Contoso のサブスクリプションの VM に認識されるルートを示しています。 表の *[仮想ネットワーク ゲートウェイ]* のエントリに注意してください。 VM には、両方のオンプレミス ネットワークのルートが表示されます。

![ExR と VNet のクロス接続後の Contoso の VM ルート](./media/cross-network-connectivity/contosovm-routes-xconnect.png)

次の表は、Fabrikam のサブスクリプションの VM に認識されるルートを示しています。 表の *[仮想ネットワーク ゲートウェイ]* のエントリに注意してください。 VM には、両方のオンプレミス ネットワークのルートが表示されます。

![ExR と VNet のクロス接続後の Fabrikam の VM ルート](./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png)

>[!NOTE]
>Fabrikam と Contoso、またはそのいずれかのサブスクリプションで、それぞれのハブ VNet へのスポーク Vnet を含めることもできます (ハブとスポークの設計は、この記事のアーキテクチャの図には示されていません)。 ExpressRoute へのハブ VNet ゲートウェイ間のクロス接続では、東部と西部のハブとスポークの間の通信も可能になります。
>

## <a name="cross-connecting-on-premises-networks"></a>オンプレミス ネットワークのクロス接続

ExpressRoute Global Reach は、異なる ExpressRoute 回線に接続されているオンプレミス ネットワーク間の接続を実現します。 Contoso と Fabrikam の ExpressRoute 回線の間の Global Reach を構成してみましょう。 ExpressRoute 回線は別のサブスクリプションにあるため、承認を作成して使用する必要があります。 詳細な手順については、「[ExpressRoute Global Reach を構成する][Configure Global Reach]」を参照してください。

次の図は、Global Reach の構成後のネットワーク アーキテクチャを示しています。

![Global Reach の構成後のアーキテクチャ](./media/cross-network-connectivity/globalreach.png)

次の表は、Global Reach の構成後の Contoso Ltd. の ExpressRoute のプライベート ピアリングのルート テーブルを示しています。 ルート テーブルに両方のオンプレミス ネットワークに属しているルートがあることを確認できます。 

![Global Reach 後の Contoso の ExpressRoute ルート テーブル](./media/cross-network-connectivity/contosoexr-rt-gr.png)

次の表は、Global Reach の構成後の Fabrikam Inc. の ExpressRoute のプライベート ピアリングのルート テーブルを示しています。 ルート テーブルに両方のオンプレミス ネットワークに属しているルートがあることを確認できます。

![Global Reach 後の Fabrikam の ExpressRoute ルート テーブル]( ./media/cross-network-connectivity/fabrikamexr-rt-gr.png )

## <a name="next-steps"></a>次のステップ

VNet と VNet ピアリングについてさらにご質問がある場合、「[仮想ネットワークに関する FAQ][VNet-FAQ]」をご覧ください。 ExpressRoute と仮想ネットワーク接続についてさらにご質問がある場合は、「[ExpressRoute の FAQ][ER-FAQ]」を参照してください。

Global Reach は国や地域ごとに国や地域でロールアウトされています。 Global Reach が必要な国や地域で使用可能かどうかを確認するには、「[ExpressRoute Global Reach][Global Reach]」を参照してください。

<!--Link References-->
[Virtual network peering]: ../virtual-network/virtual-network-peering-overview.md
[connection]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[Global Reach]: ./expressroute-global-reach.md
[Configure VNet peering]: ../virtual-network/create-peering-different-subscriptions.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[Subscription limits]: ../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits
[Connect-ER-VNet]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[ER-FAQ]: ./expressroute-faqs.md
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq