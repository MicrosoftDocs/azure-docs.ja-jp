---
title: Azure Virtual Network ピアリング
titlesuffix: Azure Virtual Network
description: Azure の仮想ネットワーク ピアリングについて説明します。
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: 100bbb6e0ed8e2ea5b35e30e7759a3b11c169b60
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077625"
---
# <a name="virtual-network-peering"></a>仮想ネットワーク ピアリング

仮想ネットワーク ピアリングを使用すると、Azure [仮想ネットワーク](virtual-networks-overview.md)間をシームレスに接続できます。 ピアリングされた仮想ネットワークは、接続において、見かけ上 1 つのネットワークとして機能します。 ピアリングされた仮想ネットワークに存在する仮想マシン間のトラフィックは Microsoft のバックボーン インフラストラクチャを介して、"*プライベート*" IP アドレスのみを使った同一仮想ネットワーク内の仮想マシン間のトラフィックと同じようにルーティングされます。 Azure では、次のことがサポートされています。
* VNet ピアリング - 同じ Azure リージョン内の VNet を接続する
* グローバル VNet ピアリング - 複数の Azure リージョンにわたる VNet を接続する

ローカルまたはグローバルの仮想ネットワーク ピアリングを使うことには、次のような利点があります。

* ピアリングされた仮想ネットワーク間のネットワーク トラフィックはプライベートである。 仮想ネットワーク間のトラフィックは、Microsoft のバックボーン ネットワーク上で保持されます。 仮想ネットワーク間の通信では、パブリック インターネット、ゲートウェイ、暗号化が必要ありません。
* 異なる仮想ネットワーク内のリソース間で、待機時間の短い広帯域幅の接続が可能である。
* 仮想ネットワークがピアリングされると、ある仮想ネットワーク内のリソースは別の仮想ネットワーク内のリソースとの通信できる。
* Azure サブスクリプション間、デプロイ モデル間、および Azure リージョン間でデータを転送できる。
* Azure Resource Manager を使って作成された仮想ネットワークをピアリングできることに加え、Resource Manager を使って作成された仮想ネットワークとクラシック デプロイ モデルを使って作成された仮想ネットワークをピアリングできる。 Azure のデプロイメント モデルについて詳しくは、[Azure のデプロイメント モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。
* ピアリングの作成時またはピアリングの作成後に、いずれの仮想ネットワークのリソースにもダウンタイムは発生しない。

## <a name="connectivity"></a>接続

仮想ネットワークをピアリングすると、一方の仮想ネットワーク内のリソースが、ピアリングされているもう一方の仮想ネットワーク内のリソースに直接接続できるようになります。

同一リージョンでピアリングされた仮想ネットワーク内の仮想マシン間のネットワーク待ち時間は、単一の仮想ネットワーク内での待ち時間と同じです。 ネットワーク スループットは、仮想マシンに許可された帯域幅を基準としています。許可されている帯域幅は、仮想マシンのサイズに比例するものです。 ピアリング内の帯域幅に関してそれ以外の制限は一切ありません。

ピアリングされた仮想ネットワーク内の仮想マシン間のトラフィックは、ゲートウェイやパブリック インターネット経由ではなく、Microsoft のバックボーン インフラストラクチャを通じて直接ルーティングされます。

どちらかの仮想ネットワークにネットワーク セキュリティ グループを適用すれば、もう一方の仮想ネットワークやサブネットへのアクセスを適宜ブロックすることができます。
仮想ネットワーク ピアリングを構成するときに、仮想ネットワーク間のネットワーク セキュリティ グループの規則を開くことも閉じることもできます。 ピアリングされた仮想ネットワーク間で完全な接続を開く場合 (既定のオプション)、特定のサブネットまたは仮想マシンにネットワーク セキュリティ グループを適用して、特定のアクセスをブロック (拒否) することができます。 ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループの概要](security-overview.md)に関する記事を参照してください。

## <a name="service-chaining"></a>サービス チェイニング

ユーザーは、ピアリングされた仮想ネットワーク内の仮想マシンを指し示すユーザー定義ルートを*次ホップ*または仮想ネットワーク ゲートウェイの IP アドレスに構成して、サービス チェイニングを可能にすることができます。 サービス チェイニングを利用すると、一方の仮想ネットワークのトラフィックを、ユーザー定義ルートを介して、ピアリングされた仮想ネットワーク内の仮想アプライアンスまたは仮想ネットワーク ゲートウェイに向けることができます。

ハブ アンド スポーク型のネットワークを展開することができます。この場合、ハブ仮想ネットワークでインフラストラクチャ コンポーネント (ネットワーク仮想アプライアンスや VPN ゲートウェイなど) をホストできます。 すべてのスポーク仮想ネットワークが、ハブ仮想ネットワークとピアリングできるようになります。 トラフィックは、ハブ仮想ネットワークでネットワーク仮想アプライアンスまたは VPN ゲートウェイを経由することができます。 

仮想ネットワーク ピアリングによって、ユーザー定義ルート上の次ホップを、ピアリングされた仮想ネットワーク内の仮想マシンまたは VPN ゲートウェイの IP アドレスにすることができます。 ただし、ExpressRoute ゲートウェイを次ホップの種類として指定するユーザー定義のルートを使用して、仮想ネットワーク間をルーティングすることはできません。 ユーザー定義ルートの詳細については、[ユーザー定義ルートの概要](virtual-networks-udr-overview.md#user-defined)に関する記事をご覧ください。 ハブとスポークのネットワーク トポロジを作成する方法については、[ハブとスポークのネットワーク トポロジ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

## <a name="gateways-and-on-premises-connectivity"></a>ゲートウェイとオンプレミスの接続

すべての仮想ネットワークは、別の仮想ネットワークとピアリングされているかどうかに関係なく独自のゲートウェイを持っており、そのゲートウェイを使用してオンプレミスのネットワークに接続することができます。 ゲートウェイを使用して[仮想ネットワーク間接続](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を構成するときに、それらの仮想ネットワークがピアリングされていてもかまいません。

仮想ネットワーク間の接続に両方の方法が構成されているときは、ピアリング構成を介して (つまり Azure バックボーンを介して) 仮想ネットワーク間のトラフィックが流れます。

仮想ネットワークがピアリングされているとき、そのピアリングされた仮想ネットワークのゲートウェイを、オンプレミスのネットワークへのトランジット ポイントとして構成することもできます。 この場合、リモート ゲートウェイを使用する仮想ネットワークが、その独自のゲートウェイを持つことはできません。 1 つの仮想ネットワークが所有できるゲートウェイは 1 つに限られています。 ゲートウェイは、以下の図に示すように、ローカル ゲートウェイと (ピアリングされた仮想ネットワークの) リモート ゲートウェイのどちらかになります。

![仮想ネットワーク ピアリングのトランジット](./media/virtual-networks-peering-overview/figure04.png)

ゲートウェイのトランジットは、VNet ピアリングとグローバル VNet ピアリングの両方でサポートされています。 異なるデプロイ モデル (Resource Manager とクラシック) を使って作成された仮想ネットワーク間のゲートウェイ転送は、ゲートウェイが仮想ネットワーク (Resource Manager) 内にある場合にのみサポートされます。 転送へのゲートウェイの使用について詳しくは、「[仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成する](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。

1 本の Azure ExpressRoute 接続を共有する仮想ネットワークどうしをピアリングした場合、両者間のトラフィックは、そのピアリング関係を通過することになります (つまり Azure バックボーン ネットワークが使用されます)。 それでも、各仮想ネットワークのローカル ゲートウェイを使用してオンプレミス回線に接続できます。 また、共有ゲートウェイを使用して、オンプレミス接続用のトランジットを構成することもできます。

## <a name="troubleshoot"></a>トラブルシューティング

仮想ネットワークのピアリングを確認するには、仮想ネットワークの任意のサブネットでネットワーク インターフェイスの[有効なルートを確認](diagnose-network-routing-problem.md)します。 仮想ネットワークのピアリングが存在する場合、仮想ネットワーク内のすべてのサブネットは、ピアリングされている各仮想ネットワークのアドレス空間ごとに、*VNet ピアリング*という種類の次ホップとのルートがあります。

Network Watcher の[接続チェック](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を使って、ピアリングされた仮想ネットワーク内の仮想マシンへの接続をトラブルシューティングすることもできます。 接続チェックを実行すると、接続元の仮想マシンのネットワーク インターフェイスから接続先の仮想マシンのネットワーク インターフェイスまでのトラフィックのルーティング方法がわかります。

[仮想ネットワーク ピアリングの問題のトラブルシューティング ツール](https://support.microsoft.com/help/4486956/troubleshooter-for-virtual-network-peering-issues)を試すこともできます。

## <a name="requirements-and-constraints"></a>要件と制約

仮想ネットワークがグローバルにピアリングされている場合のみ、次の制約が適用されます。
- 仮想ネットワーク内のリソースは、グローバルにピアリングされた仮想ネットワークの Basic 内部ロード バランサーのフロントエンド IP アドレスと通信することはできません。 Basic Load Balancer のサポートは、同じリージョン内でのみ存在します。 Standard Load Balancer のサポートは VNet Peering と Global VNet Peering の両方に存在します。 グローバル VNet ピアリングでは機能しない、基本ロード バランサーを使用するサービスについては、[ここ](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)に記載されています。

要件と制約の詳細については、[仮想ネットワーク ピアリングの要件と制約](virtual-network-manage-peering.md#requirements-and-constraints)に関するページを参照してください。 仮想ネットワークで作成できるピアリングの数の制限については、[Azure ネットワーキングの制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関するページを参照してください。 

## <a name="permissions"></a>アクセス許可

仮想ネットワーク ピアリングを作成するために必要なアクセス許可については、[仮想ネットワーク ピアリングのアクセス許可](virtual-network-manage-peering.md#permissions)に関するページを参照してください。

## <a name="pricing"></a>価格

仮想ネットワーク ピアリング接続を利用するイグレス トラフィックとエグレス トラフィックには少額の料金が発生します。 VNet ピアリングとグローバル VNet ピアリングの料金の詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/virtual-network)を参照してください。

ゲートウェイ転送は、1 つの仮想ネットワークがクロスプレミス接続または VNet 間接続用にピアリングされた仮想ネットワーク内の VPN/ExpressRoute ゲートウェイを活用できるようにするピアリング プロパティです。 このシナリオのリモート ゲートウェイを通過するトラフィックに対しては、[VPN ゲートウェイの料金](https://azure.microsoft.com/pricing/details/vpn-gateway/)または ExpressRoute ゲートウェイの料金が発生しますが、[VNet ピアリングの料金](https://azure.microsoft.com/pricing/details/virtual-network)は発生しません。 たとえば、VNetA にオンプレミス接続用の VPN ゲートウェイがあり、VNetB が適切なプロパティを構成して VNetA にピアリングされている場合、VNetB からオンプレミスへのトラフィックに対しては、VPN ゲートウェイまたは ExpressRoute あたりのエグレス料金のみが発生します。 VNet ピアリングの料金は適用されません。 [仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成する](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)方法について学習してください。

## <a name="next-steps"></a>次の手順

* 仮想ネットワーク ピアリングは、同じまたは異なるサブスクリプションに存在する同じまたは異なるデプロイメント モデルを使って作成された仮想ネットワーク間に作成されます。 次のいずれかのシナリオのチュートリアルを完了します。

    |Azure デプロイメント モデル             | サブスクリプション  |
    |---------                          |---------|
    |両方が Resource Manager              |[同じ](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[異なる](create-peering-different-subscriptions.md)|
    |一方が Resource Manager、もう一方がクラシック  |[同じ](create-peering-different-deployment-models.md)|
    |                                   |[異なる](create-peering-different-deployment-models-subscriptions.md)|

* [ハブおよびスポーク ネットワーク トポロジ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)を作成する方法を学習します。
* [仮想ネットワーク ピアリングの設定と変更方法](virtual-network-manage-peering.md)のすべてを学習する
* [VNet ピアリングの FAQ](virtual-networks-faq.md#vnet-peering) のページで、VNet ピアリングとグローバル VNet ピアリングに関する一般的な質問の回答を参照する
