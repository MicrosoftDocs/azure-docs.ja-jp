---
title: Azure Virtual Network ピアリング
titlesuffix: Azure Virtual Network
description: Azure Virtual Network でネットワークを接続できるようにする方法などの Azure の仮想ネットワーク ピアリングの詳細について説明します。
services: virtual-network
documentationcenter: na
author: altambaw
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: kumud
ms.openlocfilehash: 6ba0caa2546fdb9d4845e2c7adf87a06d906dd12
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87265162"
---
# <a name="virtual-network-peering"></a>仮想ネットワーク ピアリング

仮想ネットワーク ピアリングを使用すると、Azure [仮想ネットワーク](virtual-networks-overview.md)のネットワーク間をシームレスに接続できます。 仮想ネットワークは、接続において、見かけ上 1 つのネットワークとして機能します。 仮想マシン間のトラフィックは、Microsoft のバックボーン インフラストラクチャを使用します。 同じネットワーク内の仮想マシン間のトラフィックと同様に、トラフィックは Microsoft の*プライベート* ネットワークのみを介してルーティングされます。

Azure では、次の種類のピアリングがサポートされています。

* 仮想ネットワーク ピアリング:同じ Azure リージョン内の仮想ネットワークを接続します。
* グローバル仮想ネットワーク ピアリング:Azure リージョン間で仮想ネットワークを接続します。

ローカルまたはグローバルの仮想ネットワーク ピアリングを使うことには、次のような利点があります。

* 異なる仮想ネットワーク内のリソース間で、待機時間の短い広帯域幅の接続が可能である。
* ある仮想ネットワーク内のリソースは別の仮想ネットワーク内のリソースとの通信できる。
* Azure サブスクリプション、Azure Active Directory テナント、デプロイ モデル、Azure リージョン間で仮想ネットワーク間でデータを転送する機能。
* Azure Resource Manager によって作成された仮想ネットワークをピアリングする機能。
* Resource Manager を使用して作成された仮想ネットワークを、クラシック デプロイ モデルで作成されたものにピアリングする機能。 Azure のデプロイメント モデルについて詳しくは、[Azure のデプロイメント モデルの概要](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。
* ピアリングの作成時またはピアリングの作成後に、いずれの仮想ネットワークのリソースにもダウンタイムは発生しない。

ピアリングされた仮想ネットワーク間のネットワーク トラフィックはプライベートである。 仮想ネットワーク間のトラフィックは、Microsoft のバックボーン ネットワーク上で保持されます。 仮想ネットワーク間の通信では、パブリック インターネット、ゲートウェイ、暗号化が必要ありません。

## <a name="connectivity"></a>接続

ピアリングされた仮想ネットワークでは、一方の仮想ネットワーク内のリソースが、ピアリングされているもう一方の仮想ネットワーク内のリソースに直接接続できるようになります。

同一リージョンでピアリングされた仮想ネットワーク内の仮想マシン間のネットワーク待ち時間は、単一の仮想ネットワーク内での待ち時間と同じです。 ネットワーク スループットは、仮想マシンに許可された帯域幅を基準としています。許可されている帯域幅は、仮想マシンのサイズに比例するものです。 ピアリング内の帯域幅に関してそれ以外の制限は一切ありません。

ピアリングされた仮想ネットワーク内の仮想マシン間のトラフィックは、ゲートウェイやパブリック インターネット経由ではなく、Microsoft のバックボーン インフラストラクチャを通じて直接ルーティングされます。

どちらかの仮想ネットワークにネットワーク セキュリティ グループを適用して、もう一方の仮想ネットワークやサブネットへのアクセスを適宜ブロックすることができます。
仮想ネットワーク ピアリングを構成するときに、仮想ネットワーク間のネットワーク セキュリティ グループの規則を開く、または閉じます。 ピアリングされた仮想ネットワーク間で完全な接続を開く場合、ネットワーク セキュリティ グループを適用して、特定のアクセスをブロック (拒否) することができます。 既定のオプションは、完全な接続です。 ネットワーク セキュリティ グループの詳細については、[セキュリティ グループ](security-overview.md)に関するページをご覧ください。

## <a name="service-chaining"></a>サービス チェイニング

サービス チェイニングを利用すると、一方の仮想ネットワークのトラフィックを、ユーザー定義ルートを介して、ピアリングされたネットワーク内の仮想アプライアンスまたはゲートウェイに向けることができます。

サービス チェイニングを可能にするには、ピアリングされた仮想ネットワーク内の仮想マシンを指し示すユーザー定義ルートを*次ホップ*の IP アドレスとして構成します。 ユーザー定義のルートを仮想ネットワーク ゲートウェイをポイントして、サービス チェーンを有効にすることもできます。

ハブ アンド スポーク型のネットワークを展開することができます。この場合、ハブ仮想ネットワークでインフラストラクチャ コンポーネント (ネットワーク仮想アプライアンスや VPN ゲートウェイなど) をホストします。 すべてのスポーク仮想ネットワークが、ハブ仮想ネットワークとピアリングできるようになります。 トラフィックは、ハブ仮想ネットワークでネットワーク仮想アプライアンスまたは VPN ゲートウェイを経由します。

仮想ネットワーク ピアリングによって、ユーザー定義ルート上の次ホップを、ピアリングされた仮想ネットワーク内の仮想マシンまたは VPN ゲートウェイの IP アドレスにすることができます。 Azure ExpressRoute ゲートウェイを次ホップの種類として指定するユーザー定義のルートを使用して、仮想ネットワーク間をルーティングすることはできません。 ユーザー定義ルートの詳細については、[ユーザー定義ルートの概要](virtual-networks-udr-overview.md#user-defined)に関する記事をご覧ください。 ハブとスポークのネットワーク トポロジを作成する方法については、「[Azure のハブスポーク ネットワーク トポロジ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。

## <a name="gateways-and-on-premises-connectivity"></a>ゲートウェイとオンプレミスの接続

各仮想ネットワークには、ピアリングされた仮想ネットワークも含め、独自のゲートウェイを持つことができます。 仮想ネットワークは、独自のゲートウェイを使用して、オンプレミス ネットワークに接続できます。 ピアリングされた仮想ネットワークであっても、ゲートウェイを使用して[仮想ネットワーク間接続](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を構成できます。

仮想ネットワーク間の接続に両方の方法を構成すると、ピアリング構成を介して仮想ネットワーク間のトラフィックが流れます。 トラフィックは、Azure のバックボーンを使用します。

そのピアリングされた仮想ネットワークのゲートウェイを、オンプレミスのネットワークへのトランジット ポイントとして構成することもできます。 この場合、リモート ゲートウェイを使用する仮想ネットワークが、その独自のゲートウェイを持つことはできません。 1 つの仮想ネットワークが所有できるゲートウェイは 1 つに限られています。 ゲートウェイは、以下の図に示すように、ローカル ゲートウェイとピアリングされた仮想ネットワークのリモート ゲートウェイのどちらかになります。

![仮想ネットワーク ピアリングのトランジット](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

仮想ネットワーク ピアリングとグローバル仮想ネットワーク ピアリングの両方で、ゲートウェイ転送がサポートされます。

異なるデプロイメント モデルを使用して作成された仮想ネットワーク間のゲートウェイ転送がサポートされています。 ゲートウェイは、Resource Manager モデルの仮想ネットワーク内にある必要があります。 転送へのゲートウェイの使用について詳しくは、「[仮想ネットワーク ピアリングの VPN ゲートウェイ転送を構成する](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。

1 つの Azure ExpressRoute 接続を共有する仮想ネットワークをピアリングする場合、間のトラフィックは、ピアリング関係を通過します。 トラフィックは、Azure のバックボーン ネットワークを使用します。 それでも、各仮想ネットワークのローカル ゲートウェイを使用してオンプレミス回線に接続できます。 また、共有ゲートウェイを使用して、オンプレミス接続用のトランジットを構成することもできます。

## <a name="troubleshoot"></a>トラブルシューティング

仮想ネットワークがピアリングされていることを確認するには、有効なルートを確認します。 仮想ネットワーク内の任意のサブネット内のネットワーク インターフェイスのルートを確認します。 仮想ネットワークのピアリングが存在する場合、仮想ネットワーク内のすべてのサブネットは、ピアリングされている各仮想ネットワークのアドレス空間ごとに、*VNet ピアリング*という種類の次ホップとのルートがあります。 詳しくは、「[仮想マシンのルーティングに関する問題を診断する](diagnose-network-routing-problem.md)」をご覧ください。

Azure Network Watcher を使って、ピアリングされた仮想ネットワーク内の仮想マシンへの接続をトラブルシューティングすることもできます。 接続チェックを実行すると、接続元の仮想マシンのネットワーク インターフェイスから接続先の仮想マシンのネットワーク インターフェイスまでのトラフィックのルーティング方法がわかります。 詳細については、「[Azure Portal を使用した Azure Network Watcher との接続のトラブルシューティング](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine)」を参照してください。

また、「[仮想ネットワーク ピアリングの問題をトラブルシューティングする](virtual-network-troubleshoot-peering-issues.md)」もお試しください。

## <a name="constraints-for-peered-virtual-networks"></a>ピアリングした仮想ネットワークの制約<a name="requirements-and-constraints"></a>

仮想ネットワークがグローバルにピアリングされている場合のみ、次の制約が適用されます。

* 仮想ネットワーク内のリソースは、グローバルにピアリングされた仮想ネットワークの Basic 内部ロード バランサー (ILB) のフロントエンド IP アドレスと通信することはできません。
* Basic ロード バランサーを使用する一部のサービスは、グローバルな仮想ネットワーク ピアリングでは動作しません。 詳細については、「[グローバル VNet ピアリングとロード バランサーに関連する制約は何ですか?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)」を参照してください。

詳細については、「[要件と制約](virtual-network-manage-peering.md#requirements-and-constraints)」を参照してください。 サポートされているピアリングの数の詳細については、「[ネットワークの制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)」を参照してください。

## <a name="permissions"></a>アクセス許可

仮想ネットワーク ピアリングを作成するために必要なアクセス許可については、[アクセス許可](virtual-network-manage-peering.md#permissions)に関するページを参照してください。

## <a name="pricing"></a>価格

仮想ネットワーク ピアリング接続を利用するイングレスとエグレス トラフィックには少額の料金が発生します。 詳細については、「[Virtual Network の価格](https://azure.microsoft.com/pricing/details/virtual-network)」を参照してください。

ゲートウェイ転送は、1 つの仮想ネットワークがピアリングされた仮想ネットワーク内の VPN/ExpressRoute ゲートウェイを活用できるようにするピアリング プロパティです。 ゲートウェイの転送は、クロス プレミスとネットワーク間接続の両方で機能します。 ピアリングされた仮想ネットワークでのゲートウェイ (イングレスまたはエグレス) へのトラフィックには、スポーク VNet (または非ゲートウェイ VNet) で仮想ネットワークのピアリングの料金が発生します。 VPN gateway の料金については、「[VPN Gateway の価格](https://azure.microsoft.com/pricing/details/vpn-gateway/)」、ExpressRoute ゲートウェイの料金については、ExpressRoute ゲートウェイの料金についてのページを参照してください。

>[!NOTE]
> このドキュメントの以前のバージョンでは、仮想ネットワークのピアリングの料金は、スポーク VNet (または非ゲートウェイ VNet) のゲートウェイ転送には適用されないことが規定されていました。 価格のページごとの価格が正確に反映されるようになりました。

## <a name="next-steps"></a>次のステップ

* 2 つの仮想ネットワーク間のピアリングを作成できます。 ネットワークは、同じサブスクリプション、同じサブスクリプション内の異なるデプロイメント モデル、または異なるサブスクリプションに属することができます。 次のいずれかのシナリオのチュートリアルを完了します。

    |Azure デプロイメント モデル             | サブスクリプション  |
    |---------                          |---------|
    |両方が Resource Manager              |[同じ](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[異なる](create-peering-different-subscriptions.md)|
    |一方が Resource Manager、もう一方がクラシック  |[同じ](create-peering-different-deployment-models.md)|
    |                                   |[異なる](create-peering-different-deployment-models-subscriptions.md)|

* ハブとスポークのネットワーク トポロジを作成する方法については、「[Azure のハブスポーク ネットワーク トポロジ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。
* すべての仮想ネットワーク ピアリング設定の詳細については、「[仮想ネットワーク ピアリングの作成、変更、削除](virtual-network-manage-peering.md)」を参照してください。
* 一般的な仮想ネットワーク ピアリングとグローバル仮想ネットワーク ピアリングの質問に対する回答については、「[VNet ピアリング](virtual-networks-faq.md#vnet-peering)」を参照してください。
