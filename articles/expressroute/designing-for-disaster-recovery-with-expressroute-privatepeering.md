---
title: Azure ExpressRoute を使用したディザスター リカバリーの設計 | Microsoft Docs
description: このページでは、Azure ExpressRoute を使用する場合のディザスター リカバリーのアーキテクチャに関する推奨事項を示します。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: cf2b4e385de901254fde3c3d3e807feda98d5b41
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466064"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>ExpressRoute プライベート ピアリングを使用したディザスター リカバリーの設計

ExpressRoute は、Microsoft リソースへのキャリア グレード プライベート ネットワーク接続を提供する高可用性のために設計されています。 つまり、Microsoft ネットワーク内の ExpressRoute パスには単一障害点はありません。 ExpressRoute 回線の可用性を最大化するための設計に関する考慮事項については、「[Designing for high availability with ExpressRoute][HA]」 (ExpressRoute を使用した高可用性のための設計) を参照してください。

しかし、マーフィーのよく知られている格言 -- *何事であれ失敗する可能性のあるものは、いずれ失敗する* -- を考慮し、この記事では、単一の ExpressRoute 回線を使用して対処できるエラーに留まらず、ソリューションに注目しましょう。 つまり、この記事では、geo 冗長 ExpressRoute 回線を使用した、ディザスター リカバリー用の堅牢なバックエンド ネットワーク接続を構築するためのネットワーク アーキテクチャに関する考慮事項を確認していきます。

## <a name="need-for-redundant-connectivity-solution"></a>冗長接続ソリューションの必要性

リージョン サービス全体 (Microsoft、ネットワーク サービス プロバイダー、顧客、またはその他のクラウド サービス プロバイダーのもの) がデグレードする可能性および場合があります。 このようなリージョン全体のサービスへの影響の根本原因には、自然災害が含まれます。 そのため、ビジネス継続性とミッション クリティカルなアプリケーションについては、ディザスター リカバリーを計画することが重要です。   

Azure リージョン、オンプレミス、およびその他の場所のどこでミッション クリティカルなアプリケーションを実行するかに関係なく、フェールオーバー サイトとして別の Azure リージョンを使用することができます。 次の記事では、アプリケーションおよびフロントエンドのアクセスの観点から、ディザスター リカバリーに対処します。

- [エンタープライズ規模のディザスター リカバリー][Enterprise DR]
- [Azure Site Recovery を使用した中小企業でのディザスター リカバリー][SMB DR]

ミッション クリティカルな業務のためにオンプレミス ネットワークと Microsoft 間の ExpressRoute 接続に依存する場合、ディザスター リカバリー計画には geo 冗長ネットワーク接続を含める必要もあります。 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>複数の ExpressRoute 回線の使用に関する課題

複数の接続を使用して同じ一連のネットワークを相互接続する場合、ネットワーク間の並列パスを導入することになります。 並列パスが正しく設計されていない場合、非対称ルーティングになる可能性があります。 パスにステートフル エンティティ (NAT やファイアウォールなど) がある場合、非対称ルーティングによってトラフィック フローがブロックされる可能性があります。  通常、ExpressRoute プライベート ピアリング パス経由では、NAT やファイアウォールなどのステートフル エンティティに遭遇しません。 そのため、ExpressRoute プライベート ピアリング経由の非対称ルーティングで、必ずしもトラフィック フローがブロックされるわけではありません。
 
しかし、geo 冗長並列パス間のトラフィックの負荷を分散する場合、ステートフル エンティティがあるかどうかに関係なく、ネットワーク パフォーマンスは一貫性のないものになります。 この記事では、これらの課題に対処する方法について見ていきましょう。

## <a name="small-to-medium-on-premises-network-considerations"></a>中小企業のオンプレミス ネットワークに関する考慮事項

次の図に示されているネットワークの例を考えてみましょう。 この例では、Contoso のオンプレミスの場所と Azure リージョンの Contoso の VNet 間に、geo 冗長 ExpressRoute 接続が確立されています。 図では、緑色の実線が優先されるパス (ExpressRoute 1 経由) を示し、点線がスタンバイ パス (ExpressRoute 2 経由) を表しています。

[![1]][1]

ディザスター リカバリーのために ExpressRoute 接続を設計する際は、次のことに考慮する必要があります。

- geo 冗長 ExpressRoute 回線を使用する
- ExpressRoute 回線ごとに別のサービス プロバイダー ネットワークを使用する
- [高可用性][HA]のための ExpressRoute 回線をそれぞれ設計する
- 顧客のネットワーク上の異なる場所にある異なる ExpressRoute 回線を終了する

既定では、すべての ExpressRoute パスでルートを同じようにアドバタイズする場合、Azure で等コスト マルチパス (ECMP) ルーティングを使用して、すべての ExpressRoute パス間のオンプレミスでバインドされたトラフィックの負荷が分散されます。

しかし、geo 冗長 ExpressRoute 回線では、ネットワーク パスが異なる別のネットワークのパフォーマンスを考慮する必要があります (特にネットワーク待機時間について)。 通常の操作時により一貫したネットワークパフォーマンスを得るには、最小限の待機時間を提供する ExpressRoute 回線を優先する必要がある場合があります。

以下のいずれかの手法を使用して、別の回線より ExpressRoute 回線を優先するように Azure に影響を与えることができます。

- 他の ExpressRoute 回線と比較し、優先される ExpressRoute 回線経由でより具体的なルートをアドバタイズする
- 優先される ExpressRoute 回線に仮想ネットワークをリンクする接続に対して、より高い接続の重みを構成する
- AS パスがより長い (AS パスを前に付加) 優先順位の低い ExpressRoute 回線経由でルートをアドバタイズする

### <a name="more-specific-route"></a>より具体的なルート

次の図は、より具体的なルートのアドバタイズを使用して、ExpressRoute パスの選択に影響を与える様子を示したものです。 図の例では、Contoso のオンプレミスの /24 IP 範囲が、優先されるパス (ExpressRoute 1) 経由では 2 つの /25 アドレス範囲として、スタンバイ パス (ExpressRoute 2) 経由では /24 としてアドバタイズされています。

[![2]][2]

/24 と比べ、/25 はより具体的であるため、Azure では通常の状態で ExpressRoute 1 経由で 10.1.11.0/24 宛てのトラフィックを送信します。 ExpressRoute 1 の両方の接続がダウンした場合、VNet では ExpressRoute 2 経由のみの 10.1.11.0/24 ルートのアドバタイズが認識されるため、このエラー状態ではスタンバイ回線が使用されます。

### <a name="connection-weight"></a>接続の重み

次のスクリーンショットは、Azure portal 経由の ExpressRoute 接続の重みの構成を示しています。

[![3]][3]

次の図は、接続の重みを使用して ExpressRoute パスの選択に影響を与える様子を示したものです。 既定の接続の重みは 0 です。 以下の例では、ExpressRoute 1 の接続の重みが 100 として構成されています。 VNet で複数の ExpressRoute 回線経由でアドバタイズされたルート プレフィックスが受信された場合、その VNet では重みが最も高い接続が優先されます。

[![4]][4]

ExpressRoute 1 の両方の接続がダウンした場合、VNet では ExpressRoute 2 経由のみの 10.1.11.0/24 ルートのアドバタイズが認識されるため、このエラー状態ではスタンバイ回線が使用されます。

### <a name="as-path-prepend"></a>AS パスを前に付加

次の図は、AS パスを前に付加を使用して ExpressRoute パスの選択に影響を与える様子を示したものです。 図では、ExpressRoute 1 経由でのルートのアドバタイズが eBGP の既定の動作を示しています。 ExpressRoute 2 経由でのルートのアドバタイズでは、ルートの AS パスにさらにオンプレミス ネットワークの ASN が付加されます。 同じルートが複数の ExpressRoute 回線経由で受信されている場合、eBGP ルートの選択プロセスごとに、VNet では AS パスが最も短いルートが優先されます。 

[![5]][5]

ExpressRoute 1 の両方の接続がダウンした場合、VNet では ExpressRoute 2 経由のみの 10.1.11.0/24 ルートのアドバタイズが認識されます。 その結果、より長い AS パスの関連性が失われます。 そのため、スタンバイ回線がこのエラー状態で使用されます。

いずれかの手法を使用して、他のものより Azure ExpressRoute のものを優先するように Azure に影響を与える場合は、オンプレミス ネットワークでも、非対称フローを回避するために Azure でバインドされたトラフィックに対して同じ ExpressRoute パスを優先することを確認する必要もあります。 通常、他のものより ExpressRoute 回線を優先するようにオンプレミス ネットワークに影響を与える場合は、ローカルの基本設定値が使用されます。 ローカルの基本設定は内部の BGP (iBGP) メトリックです。 ローカルの基本設定値が最も高い BGP ルートが優先されます。

> [!IMPORTANT]
> スタンバイとして特定の ExpressRoute 回線を使用する場合は、それらを積極的に管理し、定期的にフェールオーバー操作をテストする必要があります。 
> 

## <a name="large-distributed-enterprise-network"></a>大規模な分散エンタープライズ ネットワーク

大規模な分散エンタープライズ ネットワークがある場合は、複数の ExpressRoute 回線が存在する可能性があります。 このセクションでは、追加のスタンバイ回線を必要とすることなく、アクティブ/アクティブ ExpressRoute 回線を使用してディザスター リカバリーを設計する方法を見てみましょう。 

以下の図に示されている例を考えてみましょう。 例では、Contoso に、2 つの異なるピアリングの場所にある ExpressRoute 回線経由で、2 つの異なる Azure リージョンにある 2 つの Contoso IaaS デプロイに接続されているオンプレミスの場所が 2 つあります。 

[![6]][6]

ディザスター リカバリーをどのように設計するかが、クロス リージョンからクロス ロケーション (region1/region2 から location2/location1) のトラフィックがどのようにルーティングされるかに影響します。 異なる方法でクロス リージョンからクロス ロケーションのトラフィックをルーティングする、2 つの異なるディザスター アーキテクチャについて考えてみましょう。

### <a name="scenario-1"></a>シナリオ 1

最初のシナリオでは、Azure リージョンとオンプレミス ネットワーク間のすべてのトラフィックが定常状態でローカル ExpressRoute 回線経由で流れるように、ディザスター リカバリーを設計しましょう。 ローカル ExpressRoute 回線で障害が発生した場合、Azure とオンプレミス ネットワーク間のすべてのトラフィック フローでリモート ExpressRoute 回線が使用されます。

シナリオ 1 は次の図に示されています。 図の緑色の線は、VNet1 とオンプレミス ネットワーク間のトラフィック フローのパスを示しています。 青色の線は、VNet2 とオンプレミス ネットワーク間のトラフィック フローのパスを示しています。 実線は定常状態での必要なパスを示しており、破線は、定常状態のトラフィック フローを渡す対応する ExpressRoute 回線のエラー時のトラフィック パスを示しています。 

[![7]][7]

オンプレミス ネットワークでバインドされたトラフィックのローカル ピアリング場所の ExpressRoute への接続を優先するように、VNet に影響を与える接続の重みを使用するシナリオを設計することができます。 ソリューションを完了するには、対称的な逆方向のトラフィック フローを確保する必要があります。 ExpressRoute 回線を優先するために、(オンプレミス側で ExpressRoute 回線が終了される) BGP ルーター間の iBGP セッションでローカルの基本設定を使用することができます。 ソリューションは次の図に示されています。 

[![8]][8]

### <a name="scenario-2"></a>シナリオ 2

シナリオ 2 は次の図に示されています。 図の緑色の線は、VNet1 とオンプレミス ネットワーク間のトラフィック フローのパスを示しています。 青色の線は、VNet2 とオンプレミス ネットワーク間のトラフィック フローのパスを示しています。 定常状態 (図の実線) では、VNet とオンプレミスの場所の間のすべてのトラフィックは、ほとんどの場合、Microsoft バックボーン経由で流れ、ExpressRoute のエラー状態 (図の破線) でのみ、オンプレミスの場所間の相互接続を介して流れます。

[![9]][9]

ソリューションは次の図に示されています。 図に示されているように、より具体的なルート (オプション 1) または AS パスを前に付加 (オプション 2) を使用して VNet パスの選択に影響を与える、シナリオを設計することができます。 Azure でバインドされたトラフィックのオンプレミス ネットワーク ルートの選択に影響を与えるには、オンプレミスの場所間の相互接続を優先順位の低いものとして構成する必要があります。 相互接続リンクを望ましいものとしてどのように構成するかは、オンプレミス ネットワーク内で使用されるルーティング プロトコルによって異なります。 iBGP ではローカルの基本設定、IGP (OSPF または IS-IS) ではメトリックを使用できます。

[![10]][10]


## <a name="next-steps"></a>次の手順

この記事では、ExpressRoute 回線のプライベート ピアリング接続のディザスター リカバリーを設計する方法について説明しました。 次の記事では、アプリケーションおよびフロントエンドのアクセスの観点から、ディザスター リカバリーに対処します。

- [エンタープライズ規模のディザスター リカバリー][Enterprise DR]
- [Azure Site Recovery を使用した中小企業でのディザスター リカバリー][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "中小規模のオンプレミス ネットワークに関する考慮事項"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "より具体的なルートを使用してパスの選択に影響を与える"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "Azure portal を使用して接続の重みを構成する"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "接続の重みを使用してパスの選択に影響を与える"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "AS パスを前に付加を使用してパスの選択に影響を与える"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "大規模な分散オンプレミス ネットワークに関する考慮事項"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "シナリオ 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "アクティブ/アクティブの ExpressRoute 回線のソリューション 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "シナリオ 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "アクティブ/アクティブの ExpressRoute 回線のソリューション 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





