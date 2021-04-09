---
title: Azure Virtual WAN とセキュリティ保護付きハブを使用した中国との相互接続
description: Azure Virtual WAN とセキュリティ保護付きハブを使用して中国との相互接続を行う方法について説明します。
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: sukishen
ms.openlocfilehash: 169477ffdf6fd593f1b6f01469c7303f8bd3a488
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96531839"
---
# <a name="interconnect-with-china-using-azure-virtual-wan-and-secure-hub"></a>Azure Virtual WAN とセキュリティ保護付きハブを使用した中国との相互接続

一般的な自動車、製造、物流の産業、または大使館などのその他の機関を調査した場合、中国との相互接続を向上させる方法に関する問題がよくあります。 多くの場合、これらの向上は、Microsoft 365 や Azure グローバル サービスのようなクラウド サービスの使用、または中国国内のブランチとお客様のバックボーンとの相互接続に関連しています。

こうした事例のほとんどで、お客様は、長い待ち時間、低帯域幅、不安定な接続、中国国外 (ヨーロッパや米国など) への高額な接続コストに苦労しています。

この苦労の原因は、インターネットの中国部分を保護し、中国へのトラフィックのフィルタリングを行う "Great Firewall of China (万里のファイアウォール)" です。 香港特別行政区やマカオといった特別行政区を除き、中華人民共和国から中国国外へのトラフィックはほぼすべて Great Firewall を通過します。 香港特別行政区やマカオを通過するトラフィックは、完全に Great Firewall で処理されるのではなく、Great Firewall のサブセットによって処理されます。

:::image type="content" source="./media/interconnect-china/provider.png" alt-text="図は、プロバイダーの相互接続を示しています。":::

Virtual WAN を使用すれば、お客様は、中国のサイバーセキュリティ法に違反することなく、Microsoft Cloud Services へのより高性能で安定した接続や自社ネットワークへの接続を確立することができます。

## <a name="requirements-and-workflow"></a><a name="requirements"></a>要件とワークフロー

中国のサイバーセキュリティ法を順守した状態を保つには、一連の特定条件を満たす必要があります。

まず、中国の ICP (インターネット コンテンツ プロバイダー) ライセンスを所有するネットワークおよび ISP と連携する必要があります。 多くの場合、次のいずれかのプロバイダーになります。

* China Telecom Global Ltd.
* China Mobile Ltd.
* China Unicom Ltd.
* PCCW Global Ltd.
* Hong Kong Telecom Ltd.

プロバイダーとお客様のニーズに応じて、次のいずれかのネットワーク接続サービスを購入して、中国国内のブランチを相互接続する必要があります。

* MPLS/IPVPN ネットワーク 
* SDWAN (Software Defined WAN)
* DIA (Dedicated Internet Access)

次に、Microsoft グローバル ネットワークとその Edge ネットワークへのブレークアウトを、北京や上海ではなく、香港特別行政区で提供することについてそのプロバイダーと合意する必要があります。 この場合、香港特別行政区というのが非常に重要になります。これは、中国への物理的な接続と場所のためです。

多くのお客様は、地図を見て、中国により近く見えるシンガポールを相互接続に使用することを考えますが、これは適切ではありません。 ネットワーク ファイバー マップをたどると、ほぼすべてのネットワーク接続が北京、上海、香港特別行政区を通過します。 このことから、中国への相互接続の場所としては、香港特別行政区の方がより適切な選択となります。

プロバイダーによっては、サービス内容が異なる場合があります。 次の表は、この記事を執筆した時点の情報に基づいて、プロバイダーと提供されるサービスの例を示したものです。

| サービス | プロバイダーの例 |
| --- | --- |
| MPLS/IPVPN ネットワーク |PCCW、China Telecom Global |
|SDWAN| PCCW、China Telecom Global|
| DIA (Dedicated Internet Access) | PCCW、Hong Kong Telecom、China Mobil|

Microsoft グローバル バックボーンに接続するために次の 2 つのソリューションのどちらを使用するかについてプロバイダーと合意することができます。

* 香港特別行政区で終了する Microsoft Azure ExpressRoute を取得する。 これは、MPLS/IPVPN を使用する場合になります。 現在、香港特別行政区への ExpressRoute の唯一の ICP ライセンス プロバイダーは China Telecom Global です。 ただし、Megaport や InterCloud などのクラウド エクスチェンジ プロバイダーを利用している場合は、その他のプロバイダーを利用することもできます。 詳細については、「[ExpressRoute 接続プロバイダー](../expressroute/expressroute-locations-providers.md#partners)」を参照してください。

* 次のインターネット エクスチェンジ ポイントのいずれかで DIA (Dedicated Internet Access) を直接使用する、またはプライベート ネットワーク相互接続を使用する。

次の一覧に、香港特別行政区で利用可能なインターネット エクスチェンジを示します。

* AMS-IX Hong Kong
* BBIX Hong Kong
* Equinix Hong Kong
* HKIX

この接続を使用する場合、Microsoft Services の次の BGP ホップは、Microsoft 自律システム番号 (AS#) 8075 である必要があります。 使用する場所または SDWAN ソリューションが 1 つの場合は、それを接続用に選択します。

中国と香港特別行政区の相互接続に関する現在の変更により、これらのネットワーク プロバイダーのほとんどは中国と香港特別行政区の間に MPLS ブリッジを構築しています。

中国内ではサイト間 VPN 接続が許可されており、そのほとんどが安定していることがわかります。 これは、世界のその他の地域におけるブランチ間のサイト間接続にも当てはまります。 プロバイダーは、両側に VPN/SDWAN アグリゲーションを作成し、それらの間を MPLS 経由でブリッジするようになりました。

:::image type="content" source="./media/interconnect-china/china-mpls-bridge.png" alt-text="図は、中国の MPLS ブリッジを示しています。":::

いずれの場合も、中国への 2 つ目かつ通常のインターネット ブレークアウトを用意しておくことをお勧めします。 これは、トラフィックを、Microsoft 365 や Azure などのクラウド サービスへのエンタープライズ トラフィックと、法律で規制されたインターネット トラフィックとに分割するためです。

中国国内の準拠しているネットワーク アーキテクチャは、次の例のようになります。

:::image type="content" source="./media/interconnect-china/multi-branch.png" alt-text="図は、複数のブランチを示しています。":::

この例では、香港特別行政区に Microsoft グローバル ネットワークとの相互接続を設けることで、中国国外のブランチやデータセンターへのサービスや相互接続を使用するために、[Azure Virtual WAN グローバル トランジット アーキテクチャ](virtual-wan-global-transit-network-architecture.md)や追加のサービス (Azure Virtual WAN のセキュリティ保護付きハブなど) の利用を開始できるようになりました。

## <a name="hub-to-hub-communication"></a><a name="hub-to-hub"></a>ハブ間の通信

このセクションでは、Virtual WAN のハブ間通信を使用して相互接続を行います。 このシナリオでは、新しい Virtual WAN ハブ リソースを作成し、香港特別行政区、他の任意のリージョン、Azure リソースが既にあるリージョン、または接続したい場所にある Virtual WAN ハブに接続します。

サンプル アーキテクチャは次の例のようになります。

:::image type="content" source="./media/interconnect-china/sample.png" alt-text="図は、サンプル WAN を示しています。":::

この例で、中国のブランチは、VPN または MPLS 接続を使用して、中国の Azure Cloud および相互に接続しています。 グローバル サービスに接続する必要があるブランチは、MPLS、または香港特別行政区に直接接続するインターネットベースのサービスを使用します。 香港特別行政区でも他のリージョンでも ExpressRoute を使用する場合は、[ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) を構成して両方の ExpressRoute 回線を相互接続する必要があります。

ExpressRoute Global Reach は一部のリージョンでは使用できません。 たとえば、ブラジルやインドと相互接続する必要がある場合は、[クラウド エクスチェンジ プロバイダー](../expressroute/expressroute-locations.md#connectivity-through-exchange-providers)を利用してルーティング サービスを提供する必要があります。

次の図は、このシナリオの両方の例を示しています。

:::image type="content" source="./media/interconnect-china/global.png" alt-text="図は Global Reach を示しています。":::

## <a name="secure-internet-breakout-for-microsoft-365"></a><a name="secure"></a>Microsoft 365 のセキュリティ保護されたインターネット ブレークアウト

もう 1 つの考慮事項は、中国と Virtual WAN の確立されたバックボーン コンポーネントとの間のエントリ ポイント、およびお客様のバックボーンのネットワーク セキュリティとログ記録です。 多くの場合、Microsoft Edge ネットワークに直接接続し、それによって、Microsoft 365 サービスに使用される Azure Front Door サーバーに接続するには、香港特別行政区でインターネットにブレークアウトする必要があります。

Virtual WAN のどちらのシナリオでも、[Azure Virtual WAN セキュリティ保護付きハブ](../firewall-manager/secured-virtual-hub.md)を利用します。 Azure Firewall Manager を使用することで、通常の Virtual WAN ハブをセキュリティ保護付きハブに変更し、そのハブ内で Azure Firewall をデプロイして管理することができます。

次の図は、このシナリオの例を示しています。

:::image type="content" source="./media/interconnect-china/internet.png" alt-text="図は、Web および Microsoft サービス トラフィックのインターネット ブレークアウトを示しています。":::

## <a name="architecture-and-traffic-flows"></a><a name="traffic"></a>アーキテクチャとトラフィック フロー

香港特別行政区への接続に関する選択に応じて、全体的なアーキテクチャは若干変化する可能性があります。 このセクションでは、VPN または SDWAN と ExpressRoute の異なる組み合わせで、3 つの使用可能なアーキテクチャを示します。

これらのすべてのオプションは、Azure Virtual WAN のセキュリティ保護付きハブを使用して、香港特別行政区での Microsoft 365 直接接続を実現します。 これらのアーキテクチャは、[Microsoft 365 Multi-Geo](/microsoft-365/enterprise/microsoft-365-multi-geo) に関するコンプライアンス要件もサポートしており、そのトラフィックは、次の Microsoft 365 Front Door の場所の近くに保持されます。 その結果、中国国外での Microsoft 365 の使用も改善されています。

Azure Virtual WAN をインターネット接続と共に使用すると、すべての接続が [Microsoft Azure Peering Services (MAPS)](../peering-service/about.md) などの追加サービスの恩恵を受けることができます。 MAPS は、サードパーティのインターネット サービス プロバイダーから Microsoft グローバル ネットワークに送られるトラフィックを最適化するために構築されました。

### <a name="option-1-sdwan-or-vpn"></a><a name="option-1"></a>オプション 1: SDWAN または VPN

このセクションでは、香港特別行政区およびその他のブランチに SDWAN または VPN を使用する設計について説明します。 このオプションは、Virtual WAN バックボーンの両方のサイトで純粋なインターネット接続を使用しているときの使用とトラフィック フローを示します。 この事例では、香港特別行政区への接続は、DIA (Dedicated Internet Access)、または ICP プロバイダーの SDWAN ソリューションを使用して行われています。 他のブランチでは、純粋なインターネットや SDWAN ソリューションも使用されています。

:::image type="content" source="./media/interconnect-china/china-traffic.png" alt-text="図は、中国から香港特別行政区へのトラフィックを示しています。":::

このアーキテクチャでは、すべてのサイトは、VPN と Azure Virtual WAN を使用して Microsoft グローバル ネットワークに接続されています。 サイトと香港特別行政区の間のトラフィックは、Microsoft ネットワーク経由で送信され、ラスト マイルのみ通常のインターネット接続を使用しています。

### <a name="option-2-expressroute-and-sdwan-or-vpn"></a><a name="option-2"></a>オプション 2: ExpressRoute と SDWAN または VPN

このセクションでは、香港特別行政区では ExpressRoute、他のブランチでは VPN/SDWAN を使用する設計について説明します。 このオプションは、香港特別行政区で終了する ExpressRoute と SDWAN または VPN 経由で接続されている他のブランチの使用を示しています。 現在、香港特別行政区の ExpressRoute は、[Express Route パートナー](../expressroute/expressroute-locations-providers.md#global-commercial-azure)の一覧表にある、短いプロバイダー一覧に限定されています。

:::image type="content" source="./media/interconnect-china/expressroute.png" alt-text="図は、中国から香港特別行政区へのトラフィックの ExpressRoute を示しています。":::

さらに、中国からの ExpressRoute を韓国や日本などで終了するオプションもあります。 しかし、コンプライアンス、規制、待ち時間を考慮すると、現時点では、香港特別行政区が最良の選択肢です。

### <a name="option-3-expressroute-only"></a><a name="option-3"></a>オプション 3:ExpressRoute のみ

このセクションでは、ExpressRoute が香港特別行政区およびその他のブランチに使用されている設計について説明します。 このオプションでは、両端で ExpressRoute を使用する相互接続を示します。 ここでは、他とは異なるトラフィック フローがあります。 Microsoft 365 のトラフィックは、Azure Virtual WAN のセキュリティ保護付きハブにフローし、そこから Microsoft Edge ネットワークとインターネットへとフローします。

相互接続されたブランチへと送られる、またはそこから中国国内の場所へと送られるトラフィックは、そのアーキテクチャ内で異なるアプローチに従います。 現在、Virtual WAN では、ExpressRoute 間の転送はサポートされていません。 トラフィックは、Virtual WAN ハブを通過せずに、ExpressRoute Global Reach またはサードパーティの相互接続を利用します。 ある Microsoft Enterprise Edge (MSEE) から別の Microsoft Enterprise Edge (MSEE) に直接フローします。

:::image type="content" source="./media/interconnect-china/expressroute-virtual.png" alt-text="図は、ExpressRoute Global Reach を示しています。":::

現在、ExpressRoute Global Reach はすべての国/地域で利用できるわけではありませんが、Azure Virtual WAN を使用してソリューションを構成することができます。

たとえば、Microsoft ピアリングを使用して ExpressRoute を構成し、そのピアリングを介して VPN トンネルを Azure Virtual WAN に 接続することができます。 これで、Global Reach とサードパーティ プロバイダーおよびサービス (Megaport Cloud など) を使用せずに、VPN と ExpressRoute 間の転送を再び有効にしました。

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。

* [グローバル トランジット ネットワーク アーキテクチャと Azure Virtual WAN](virtual-wan-global-transit-network-architecture.md)

* [Virtual WAN のハブを作成する](virtual-wan-site-to-site-portal.md)

* [Virtual WAN のセキュリティ保護付きハブを構成する](../firewall-manager/secure-cloud-network.md)

* [Azure Peering Service プレレビューの概要](../peering-service/about.md)