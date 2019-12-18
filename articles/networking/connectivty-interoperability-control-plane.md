---
title: 'Azure バックエンド接続機能の相互運用性: コントロール プレーンの分析 | Microsoft Docs'
description: この記事では、Azure における ExpressRoute、サイト間 VPN、仮想ネットワーク ピアリングの間の相互運用性を分析する際に使用できるテスト セットアップのコントロール プレーン分析について説明します。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4921e4c4fc0da95250a0171c66d6a69093b10687
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873847"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-control-plane-analysis"></a>Azure バックエンド接続機能の相互運用性: コントロール プレーンの分析

この記事では、[テストセットアップ][Setup] のコントロールプレーン分析について説明します。 [テスト セットアップの構成][Configuration] とテスト セットアップの[データ プレーン分析][Data-Analysis] についてもご覧ください。

基本的に､コントロール プレーン分析では､1 つのトポロジ内の各ネットワーク間で交換されるルートを調べます。 コントロール プレーン分析は､ネットワークからのトポロジの見え方の違いを理解するのに役立ちます。

## <a name="hub-and-spoke-vnet-perspective"></a>ハブ アンド スポーク VNet の視点

次の図は、ハブ仮想ネットワーク (VNet) とスポーク VNet の視点からネットワークを表しています (青色で強調表示)。 この図はまた、各ネットワークの自律システム番号 (ASN) と、各ネットワーク間で交換されるルートを示しています。 

![1][1]

VNet の Azure ExpressRoute ゲートウェイの ASN が Microsoft エンタープライズ エッジ ルーター (MSEE) の ASN と異なっています。 ExpressRoute ゲートウェイではプライベート ASN (値は **65515**) が使用されているのに対し､MSEE ではパブリック ASN (値は **12076**) がグローバルに使用されています。 ExpressRoute ピアリングを構成するときは､MSEE がピアとなるため、ピア ASN として **12076** を使用します。 Azure 側では､MSEE は ExpressRoute ゲートウェイとの間で eBGP ピアリングを確立します。 各 ExpressRoute ピアリング用に MSEE が確立するデュアル eBGP ピアリングは､コントロール プレーン レベルではトランスペアレントです。 このため､ExpressRoute のルート テーブルを見ると､VNet の プレフィックスにその VNet の ExpressRoute ゲートウェイの ASN が確認できます。 

次の図は ExpressRoute のルート テーブルの例を示しています。 

![5][5]

Azure 内では、ASN はピアリングの視点からのみ重要です。 既定では、ExpressRoute ゲートウェイも、Azure VPN Gateway の VPN ゲートウェイも、ASN は **65515** です。

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>ExpressRoute 1 を経由するオンプレミスの場所 1 と リモート VNet の視点

オンプレミスの場所 1 とリモート VNet はどちらも、ExpressRoute 1 を介してハブ VNet に接続されています。 次の図に示すように、両者は、このトポロジに対して同じ視点を共有しています。

![2][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>サイト間 VPN を経由するオンプレミスの場所 1 とブランチ VNet の視点

オンプレミスの場所 1 とブランチ VNet はどちらも、サイト間 VPN 接続を介して、ハブ VNet の VPN ゲートウェイに接続されています。 次の図に示すように、両者は、このトポロジに対して同じ視点を共有しています。

![3][3]

## <a name="on-premises-location-2-perspective"></a>オンプレミスの場所 2 の視点

オンプレミスの場所 2 は、ExpressRoute 2 のプライベート ピアリング経由でハブ VNet に接続されています。 

![4][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute とサイト間 VPN 接続の併用

###  <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute 上のサイト間 VPN

ExpressRoute Microsoft ピアリングを使用してサイト間 VPN を構成することにより、オンプレミス ネットワークと Azure VNet の間でプライベートにデータを交換することができます。 この構成を使用すれば、機密性、信頼性、整合性が確保されたデータ交換を実現できます。 また、このデータ交換には、アンチリプレイ対策も講じられています。 ExpressRoute Microsoft ピアリングを使用してトンネル モードでサイト間 IPsec VPN を構成する方法の詳細については、[ExpressRoute Microsoft ピアリングでのサイト間 VPN][S2S-Over-ExR] に関するページを参照してください。 

Microsoft ピアリングを使用するサイト間 VPN 構成に伴う主な制限はスループットです。 IPsec トンネル上では、VPN ゲートウェイの容量によりスループットが制限されます。 VPN ゲートウェイのスループットは、ExpressRoute のスループットよりも低くなります。 このシナリオでは、高いセキュリティが要求されるトラフィックには IPsec トンネルを使用し、それ以外のすべてのトラフィックにはプライベート ピアリングを使用すると、ExpressRoute の帯域幅使用率の最適化に役立ちます。

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute の安全なフェールオーバー パスとしてのサイト間 VPN

ExpressRoute は、高可用性を確保する冗長回線ペアの役割を果たします。 異なる Azure リージョンで geo 冗長 ExpressRoute 接続を構成することができます。 また、このテスト セットアップで紹介したとおり、Azure リージョン内でサイト間 VPN を使用して、ExpressRoute 接続のフェールオーバー パスを作成することができます。 ExpressRoute とサイト間 VPN の両方に同じプレフィックスがアドバタイズされた場合、Azure では ExpressRoute が優先されます。 ExpressRoute とサイト間 VPN の間で非対称なルーティングを回避するために、オンプレミス ネットワークも、ExpressRoute 接続をサイト間 VPN 接続よりも優先して使用するように構成する必要があります。

ExpressRoute とサイト間 VPN が共存する接続を構成する方法の詳細については、[ExpressRoute とサイト間の共存][ExR-S2S-CoEx]に関するページを参照してください。

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>バックエンド接続をスポーク VNet とブランチの場所に拡張する

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>VNet ピアリングを使用したスポーク VNet 接続

ハブ アンド スポークの VNet アーキテクチャは、広く利用されています。 ハブは Azure 内の VNet であり、スポーク VNet とオンプレミス ネットワークの間の接続の中央拠点として機能します。 スポークはハブとピアリングする VNet であり、ワークロードの分離に利用できます。 オンプレミスのデータセンターとハブとの間のトラフィックは、ExpressRoute または VPN 接続を経由して送信されます。 このアーキテクチャの詳細については、「[Azure にハブスポーク ネットワーク トポロジを実装する][Hub-n-Spoke]」を参照してください。

リージョン内の VNet ピアリングでは、スポーク VNet はハブ VNet ゲートウェイ (VPN ゲートウェイと ExpressRoute ゲートウェイの両方) を使用して、リモート ネットワークと通信を行うことができます。

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>サイト間 VPN を使用したブランチ VNet 接続

異なるリージョンにあるブランチ VNet と、オンプレミス ネットワークを、ハブ VNet を介して相互に通信させたい場合があります。 この構成に対する Azure のネイティブ ソリューションは、VPN を使用したサイト間 VPN 接続です。 その他に、ネットワーク仮想アプライアンス (NVA) をハブでのルーティングに使用する方法もあります。

詳細については、「[VPN ゲートウェイとは][VPN]」および[高可用性 NVA のデプロイ][Deploy-NVA]に関するページを参照してください。

## <a name="next-steps"></a>次の手順

テスト セットアップの[データ プレーン分析][Data-Analysis]と Azure ネットワーク監視機能のビューを確認します。

「[ExpressRoute の FAQ][ExR-FAQ]」を参照して以下を行います。
-   ExpressRoute ゲートウェイに接続できる ExpressRoute 回線の数について学習する。
-   ExpressRoute 回線に接続できる ExpressRoute ゲートウェイの数について学習する。
-   ExpressRoute のその他のスケールの制限について学習する。


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "トポロジのハブとスポーク VNet の視点"
[2]: ./media/backend-interoperability/Loc1ExRView.png "トポロジの ExpressRoute 1 を経由する場所 1 とリモート VNeT の視点"
[3]: ./media/backend-interoperability/Loc1VPNView.png "トポロジのサイト間 VPN を経由する場所 1 とブランチ VNet からの視点"
[4]: ./media/backend-interoperability/Loc2View.png "トポロジの場所 2 からの視点"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png " ExpressRoute 1 のルート テーブル"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


