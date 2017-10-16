---
title: "Azure の仮想ネットワーク ピアリング | Microsoft Docs"
description: "Azure の仮想ネットワーク ピアリングについて説明します。"
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: narayan;anavin
ms.openlocfilehash: 082cd8a6cf50f76c89fe5995047396c734f83034
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network-peering"></a>仮想ネットワーク ピアリング

[Azure の仮想ネットワーク (VNet)](virtual-networks-overview.md) は Azure 内の独自のプライベート ネットワーク空間であり、VNet を使って Azure リソース同士を安全に接続することができます。

仮想ネットワーク ピアリングにより、仮想ネットワーク間をシームレスに接続できます。 ピアリングされた仮想ネットワークは、接続において、見かけ上 1 つのネットワークとして機能します。 ピアリングされた仮想ネットワーク内の仮想マシンは、互いに直接通信することができます。
ピアリングされた仮想ネットワークに存在する仮想マシン間のトラフィックは Microsoft のバックボーン インフラストラクチャを介して、*プライベート* IP アドレスのみを使った同一仮想ネットワーク内の仮想マシン間のトラフィックと同じようにルーティングされます。

>[!IMPORTANT]
> 異なる Azure リージョン間での仮想ネットワーク ピアリングも可能です。 現在、この機能はプレビュー段階にあります。 [プレビュー版を利用するにはサブスクリプションを登録](virtual-network-create-peering.md)してください。 同一リージョン内の仮想ネットワーク ピアリングの機能は一般公開されています。
>

仮想ネットワーク ピアリングを使う利点をいくつか挙げます。

* 仮想ネットワーク ピアリングを介したトラフィックは完全にプライベートである。 Microsoft のバックボーン ネットワークを通るので、パブリックのインターネットやゲートウェイを経由することはありません。
* 異なる仮想ネットワーク内のリソース間で、待機時間の短い広帯域幅の接続が可能である。
* ピアリングされた仮想ネットワーク間で互いのリソースを利用できる。
* 仮想ネットワーク ピアリングは、Azure サブスクリプション間、デプロイメント モデル間、および Azure リージョン間 (プレビュー) でデータを転送するのに役立つ。
* Azure Resource Manager を使って作成された仮想ネットワークをピアリングできることに加え、Resource Manager を使って作成された仮想ネットワークとクラシック デプロイメント モデルを使って作成された仮想ネットワークをピアリングできる。 Azure の 2 つのデプロイメント モデルの違いの詳細については、[Azure のデプロイメント モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

## <a name="requirements-constraints"></a>要件と制約

* 同一リージョン内の仮想ネットワーク ピアリングの機能は一般公開されています。 異なるリージョン間での仮想ネットワーク ピアリングは現在プレビュー段階にあり、米国中西部、カナダ中部、および米国西部 2 で提供されています。 [プレビュー版を利用するにはサブスクリプションを登録](virtual-network-create-peering.md)してください。
    > [!WARNING]
    > このシナリオで作成された仮想ネットワーク ピアリングでは、一般公開リリースのシナリオと同じレベルの可用性と信頼性が得られないことがあります。 また、一部の機能が制限されている場合があります。一部の Azure リージョンではご利用いただけない場合もあります。 この機能の可用性とステータスに関する最新の通知については、[Azure Virtual Network の更新情報](https://azure.microsoft.com/updates/?product=virtual-network)に関するページをご覧ください。

* ピアリングする仮想ネットワークの IP アドレス空間は、重複していてはなりません。
* 仮想ネットワークが別の仮想ネットワークとピアリングされた後で、仮想ネットワークに対してアドレス空間の追加または削除を実行することはできません。
* 仮想ネットワーク ピアリングは、2 つの仮想ネットワーク間の関係です。 ピアリング間で派生する推移的な関係は存在しません。 たとえば、virtualNetworkA が virtualNetworkB とピアリングし、virtualNetworkB が virtualNetworkC とピアリングしている場合、virtualNetworkA とvirtualNetworkC にはピアリング関係は "*ありません*"。
* 2 つの異なるサブスクリプションに存在する仮想ネットワークをピアリングできます。そのためには、両方のサブスクリプションの特権ユーザー (具体的なアクセス許可については、[こちら](create-peering-different-deployment-models-subscriptions.md#permissions)を参照) がピアリングを承認しており、サブスクリプションが同じ Azure Active Directory テナントに関連付けられていることが必要となります。 別々の Active Directory テナントに関連付けられているサブスクリプション内の仮想ネットワークは、[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) を使って接続することができます。
* ピアリングは、Resource Manager デプロイメント モデルを使って作成された仮想ネットワーク同士か、Resource Manager デプロイメント モデルを使って作成された仮想ネットワークとクラシック デプロイメント モデルを使って作成された仮想ネットワークの間で行うことができます。 ただし、クラシック デプロイメント モデルを使って作成された仮想ネットワークを互いにピアリングすることはできません。 クラシック デプロイメント モデルを使って作成された仮想ネットワークは、[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) を使用して接続できます。
* ピアリングされた仮想ネットワークに存在する仮想マシン間の通信には帯域幅上の特別な制限はありませんが、仮想マシンのサイズに基づく帯域幅の上限は依然として適用されます。 さまざまなサイズの仮想マシンの最大ネットワーク帯域幅の詳細については、[Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の VM サイズに関する記事を参照してください。

     ![基本的な仮想ネットワーク ピアリング](./media/virtual-networks-peering-overview/figure03.png)

## <a name="connectivity"></a>接続

仮想ネットワークをピアリングすると、一方の仮想ネットワーク内のリソースが、ピアリングされているもう一方の仮想ネットワーク内のリソースに直接接続できるようになります。

同一リージョンでピアリングされた仮想ネットワーク内の仮想マシン間のネットワーク待機時間は、単一の仮想ネットワーク内での待機時間と同じです。 ネットワーク スループットは、仮想マシンに許可された帯域幅を基準としています。許可されている帯域幅は、仮想マシンのサイズに比例するものです。 ピアリング内の帯域幅に関してそれ以外の制限は一切ありません。

ピアリングされた仮想ネットワーク内の仮想マシン間のトラフィックは、ゲートウェイやパブリック インターネット経由ではなく、Microsoft のバックボーン インフラストラクチャを通じて直接ルーティングされます。

仮想ネットワーク内の仮想マシンは、同一リージョンでピアリングされた仮想ネットワーク内の内部ロードバランサーにアクセスすることができます。 プレビュー段階のグローバルにピアリングされた仮想ネットワークでは、内部ロード バランサーのサポートはありません。 グローバルな仮想ネットワーク ピアリングの一般公開リリースでは、内部ロード バランサーがサポートされる予定です。

どちらかの仮想ネットワークにネットワーク セキュリティ グループを適用すれば、もう一方の仮想ネットワークやサブネットへのアクセスを適宜ブロックすることができます。
仮想ネットワーク ピアリングを構成するときに、仮想ネットワーク間のネットワーク セキュリティ グループの規則を開くことも閉じることもできます。 ピアリングされた仮想ネットワーク間で完全な接続を開く場合 (既定のオプション)、特定のサブネットまたは仮想マシンにネットワーク セキュリティ グループを適用して、特定のアクセスをブロック (拒否) することができます。 ネットワーク セキュリティ グループの詳細については、[ネットワーク セキュリティ グループの概要](virtual-networks-nsg.md)に関する記事をご覧ください。

## <a name="service-chaining"></a>サービス チェイニング

ユーザーは、ピアリングされた仮想ネットワーク内の仮想マシンを指し示すユーザー定義ルートを "次ホップ" の IP アドレスとして構成して、サービス チェイニングを可能にすることができます。 サービス チェイニングを利用すると、一方の仮想ネットワークのトラフィックを、ユーザー定義ルートを介して、ピアリングされた仮想ネットワーク内の仮想アプライアンスに向けることができます。

また、ハブ アンド スポーク型の環境を効果的に構築することもできます。この場合、ハブでインフラストラクチャ コンポーネント (ネットワーク仮想アプライアンスなど) をホストできます。 すべてのスポーク仮想ネットワークが、ハブ仮想ネットワークとピアリングできるようになります。 トラフィックは、ハブ仮想ネットワークで実行されているネットワーク仮想アプライアンスを経由することができます。 つまり、仮想ネットワーク ピアリングによって、ユーザー定義ルート上の次ホップ IP アドレスを、ピアリングされた仮想ネットワーク内の仮想マシンの IP アドレスにすることができます。 ユーザー定義ルートの詳細については、[ユーザー定義ルートの概要](virtual-networks-udr-overview.md)に関する記事をご覧ください。 [ハブおよびスポーク ネットワーク トポロジ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)を作成する方法の詳細もご覧ください。

## <a name="gateways-and-on-premises-connectivity"></a>ゲートウェイとオンプレミスの接続

すべての仮想ネットワークは、別の仮想ネットワークとピアリングされているかどうかに関係なく独自のゲートウェイを持っており、そのゲートウェイを使用してオンプレミスのネットワークに接続することができます。 ゲートウェイを使用して[仮想ネットワーク間接続](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)を構成するときに、それらの仮想ネットワークがピアリングされていてもかまいません。

仮想ネットワーク間の接続に両方の方法が構成されているときは、ピアリング構成を介して (つまり Azure バックボーンを介して) 仮想ネットワーク間のトラフィックが流れます。

同一リージョンで仮想ネットワークがピアリングされているとき、そのピアリングされた仮想ネットワークのゲートウェイを、オンプレミスのネットワークへのトランジット ポイントとして構成することもできます。 この場合、リモート ゲートウェイを使用する仮想ネットワークが、その独自のゲートウェイを持つことはできません。 1 つの仮想ネットワークが所有できるゲートウェイは 1 つに限られています。 ゲートウェイは、以下の図に示すように、ローカル ゲートウェイと (ピアリングされた仮想ネットワークの) リモート ゲートウェイのどちらかになります。

![仮想ネットワーク ピアリングのトランジット](./media/virtual-networks-peering-overview/figure04.png)

ゲートウェイ トランジットは、異なるデプロイメント モデルを使って作成された仮想ネットワーク間または異なるリージョンの仮想ネットワーク間のピアリング関係ではサポートされません。 ゲートウェイ トランジットが機能するためには、ピアリング関係にある両方の仮想ネットワークが Resource Manager を介して作成されており、同一リージョン内にある必要があります。 現在、グローバルにピアリングされた仮想ネットワークでは、ゲートウェイ トランジットをサポートしていません。

1 本の Azure ExpressRoute 接続を共有する仮想ネットワークどうしをピアリングした場合、両者間のトラフィックは、そのピアリング関係を通過することになります (つまり Azure バックボーン ネットワークが使用されます)。 それでも、各仮想ネットワークのローカル ゲートウェイを使用してオンプレミス回線に接続できます。 また、共有ゲートウェイを使用して、オンプレミス接続用のトランジットを構成することもできます。

## <a name="permissions"></a>アクセス許可

仮想ネットワーク ピアリングは、特権の下で許可される操作であり、 VirtualNetworks 名前空間に独立した機能として存在します。 ユーザーに特定の権限を付与することによってピアリングを承認することができます。 仮想ネットワークに対する読み取り/書き込みアクセス権を持つユーザーには、この権限が自動的に継承されます。

仮想ネットワークに対するピアリング操作は、管理者権限を持つユーザーまたはピアリング機能の特権を与えられたユーザーが開始できます。 必要な最小レベルのアクセス許可は、ネットワーク共同作成者です。 対応するピアリング要求がもう一方の側に存在し、その他各種の要件が満たされた場合にピアリングは確立されます。

たとえば、myvirtual networkA と myvirtual networkB という名前の仮想ネットワークをピアリングする場合、各仮想ネットワークに対する次の最小限のロールまたはアクセス許可をアカウントに割り当てる必要があります。

|Virtual Network|デプロイメント モデル|役割|アクセス許可|
|---|---|---|---|
|myvirtual networkA|リソース マネージャー|[ネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |クラシック|[従来のネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|該当なし|
|myvirtual networkB|リソース マネージャー|[ネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||クラシック|[従来のネットワークの共同作業者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

## <a name="monitor"></a>監視

Resource Manager を使用して作成された 2 つの仮想ネットワークをピアリングするときは、ピアリングする仮想ネットワークごとにピアリングを構成する必要があります。
ピアリング接続の状態を監視することができます。 ピアリングの状態は次のいずれかになります。

* **開始済み:** 1 つ目の仮想ネットワークから 2 つ目の仮想ネットワークへのピアリングを作成すると、ピアリングの状態が "開始済み" になります。

* **接続済み:** 2 つ目の仮想ネットワークから 1 つ目の仮想ネットワークへのピアリングを作成すると、ピアリングの状態が "接続済み" になります。 1 つ目の仮想ネットワークのピアリングの状態を確認すると、状態が "開始済み" から "接続済み" に変わっていることがわかります。 両方の仮想ネットワーク ピアリングの状態が "接続済み" になるまで、ピアリングは正常に確立されません。

* **切断済み**: 接続が確立された後にピアリング リンクのいずれかが削除された場合、ピアリングの状態は "切断済み" になります。

## <a name="troubleshoot"></a>トラブルシューティング

ピアリング接続のトラフィック フローをトラブルシューティングするには、[有効なルートを確認](virtual-network-routes-troubleshoot-portal.md)します。

Network Watcher の[接続チェック](../network-watcher/network-watcher-connectivity-portal.md)を使って、ピアリングされた仮想ネットワーク内の仮想マシンへの接続をトラブルシューティングすることもできます。 接続チェックを行うと、接続元 VM のネットワーク インターフェイスから接続先 VM のネットワーク インターフェイスへのルーティングの状態を確認することができます。

## <a name="limits"></a>制限

1 つの仮想ネットワークで許容されるピアリングの数には制限があります。 既定のピアリングの数は 50 です。 ピアリングの数は増やすことができます。 詳しくは、[Azure ネットワークの制限](../azure-subscription-service-limits.md#networking-limits)に関するセクションをご覧ください。

## <a name="pricing"></a>価格

仮想ネットワーク ピアリング接続を利用するイグレス トラフィックとエグレス トラフィックには少額の料金が発生します。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/virtual-network)を参照してください。

## <a name="next-steps"></a>次のステップ

* 仮想ネットワークのピアリング チュートリアルを完了します。 仮想ネットワーク ピアリングは、同じまたは異なるサブスクリプションに存在する同じまたは異なるデプロイメント モデルを使って作成された仮想ネットワーク間に作成されます。 次のいずれかのシナリオのチュートリアルを完了します。

    |Azure デプロイメント モデル  | [サブスクリプション]  |
    |---------|---------|
    |両方が Resource Manager |[同じ](virtual-network-create-peering.md)|
    | |[異なる](create-peering-different-subscriptions.md)|
    |一方が Resource Manager、もう一方がクラシック     |[同じ](create-peering-different-deployment-models.md)|
    | |[異なる](create-peering-different-deployment-models-subscriptions.md)|

* [ハブおよびスポーク ネットワーク トポロジ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)を作成する方法を学習します
* すべての[仮想ネットワーク ピアリング設定とその変更方法](virtual-network-manage-peering.md)を学習します
