---
title: リージョン間ロード バランサー (プレビュー)
titleSuffix: Azure Load Balancer
description: Azure Load Balancer のリージョン間ロード バランサー階層の概要。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: b0e51d11797bc5767f19b25a92a2d29a66ea1bb2
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176724"
---
# <a name="cross-region-load-balancer-preview"></a>リージョン間ロード バランサー (プレビュー)

Azure Load Balancer では、ロード バランサーのフロントエンドに到着した受信トラフィックを、バックエンド プールのインスタンスに分配できます。

Azure Standard Load Balancer では、リージョン間の負荷分散がサポートされ、次のような geo 冗長 HA シナリオを実現できます。

* 複数のリージョンから発信された受信トラフィック。
* 次の最適なリージョンのデプロイへの[即時グローバル フェールオーバー](#regional-redundancy)。
* [超低遅延](#ultra-low-latency)を使用した、最も近い Azure リージョンへのリージョン間の負荷分散。
* 1 つのエンドポイントの背後で[スケールアップまたはスケールダウン](#ability-to-scale-updown-behind-a-single-endpoint)する機能。
* [[静的 IP]](#static-ip)
* [クライアント IP の保持](#client-ip-preservation)
* 学習曲線のない、[既存のロード バランサーに基づく](#build-cross-region-solution-on-existing-azure-load-balancer)ソリューション

> [!IMPORTANT]
> リージョン間ロード バランサーは、現在プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

リージョン間の負荷分散では、リージョンの標準的なロード バランサーと同等のハイ パフォーマンスと低待機時間の利点を得られます。 

リージョン間ロード バランサーのフロントエンド IP 構成は静的であり、[ほとんどの Azure リージョン](#participating-regions)にわたって公開されます。

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="リージョン間ロード バランサーの図。" border="true":::

> [!NOTE]
> リージョン間ロード バランサー上の負荷分散規則のバックエンド ポートは、リージョンの標準ロード バランサー上の負荷分散規則またはインバウンド NAT 規則のフロントエンド ポートと一致している必要があります。 

### <a name="regional-redundancy"></a>リージョン冗長

既存のロード バランサーにグローバル フロントエンド パブリック IP アドレスを追加して、リージョン冗長を構成します。 

1 つのリージョンで障害が発生した場合、トラフィックは次の最も近い正常なリージョンのロード バランサーにルーティングされます。  

リージョン間ロード バランサーの正常性プローブによって、20 秒ごとに可用性に関する情報が収集されます。 1 つのリージョンのロード バランサーの可用性が 0 に落ちると、リージョン間ロード バランサーによってエラーが検出されます。 その後、そのリージョン ロード バランサーはローテーションから外されます。 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="グローバル リージョンのトラフィック ビューの図。" border="true":::

### <a name="ultra-low-latency"></a>超低遅延

geo 近接の負荷分散アルゴリズムは、ユーザーの地理的な場所と、お客様のリージョン デプロイに基づいています。 

クライアントから開始されたトラフィックは、最も近い参加リージョンに到達し、Microsoft のグローバル ネットワーク バックボーンを経由して、最も近いリージョン デプロイに到達します。 

たとえば、リージョン間ロード バランサーと、次の Azure リージョンの標準ロード バランサーがあるとします。

* 米国西部
* 北ヨーロッパ

フローがシアトルから開始された場合、トラフィックは米国西部に入ります。 このリージョンは、シアトルから最も近い参加リージョンです。 トラフィックは、最も近いリージョンのロード バランサー (米国西部) にルーティングされます。

Azure のリージョン間ロード バランサーでは、ルーティングの決定に geo 近接負荷分散アルゴリズムが使用されます。 

リージョン ロード バランサーの構成済み負荷分散モードは、geo 近接に複数のリージョン ロード バランサーが使用されている場合に、最終的なルーティングを決定するために使用されます。

詳細については、「[Azure Load Balancer の分散モードを構成する](./load-balancer-distribution-mode.md)」を参照してください。


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>1 つのエンドポイントの背後でスケールアップまたはスケールダウンする機能

リージョン間ロード バランサーのグローバル エンドポイントを顧客に公開すると、中断せずにグローバル エンドポイントの背後にあるリージョン デプロイを追加または削除できるようになります。 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>静的 IP
リージョン間ロード バランサーには静的パブリック IP が付属しています。これにより、IP アドレスを同じままにすることができます。 静的 IP の詳細については、[こちら](../virtual-network/public-ip-addresses.md#allocation-method)を参照してください

### <a name="client-ip-preservation"></a>クライアント IP の保持
リージョン間ロード バランサーは、レイヤー 4 のパススルー ネットワーク ロード バランサーです。 このパススルーによって、パケットの元の IP が保持されます。  元の IP は、仮想マシン上で実行するコードで使用できます。 この保持機能により、IP アドレスに固有のロジックを適用できます。

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>既存の Azure Load Balancer でリージョン間ソリューションを構築する
リージョン間ロード バランサーのバックエンド プールには、1 つ以上のリージョン ロード バランサーが含まれています。 

既存のロード バランサーのデプロイをリージョン間ロード バランサーに追加して、高可用性のリージョン間デプロイを実現します。

**ホーム リージョン** は、リージョン間ロード バランサーがデプロイされる場所です。 このリージョンは、トラフィックのルーティング方法には影響しません。 ホーム リージョンがダウンしても、トラフィック フローは影響を受けません。

### <a name="home-regions"></a>ホーム リージョン
* 米国東部 2
* 米国西部
* 西ヨーロッパ
* 東南アジア
* 米国中部
* 北ヨーロッパ
* 東アジア

> [!NOTE]
> リージョン間ロード バランサーをデプロイできるのは、上記の 8 つのリージョンのいずれかに限られます。

**参加リージョン** は、ロード バランサーのグローバル パブリック IP を使用できる場所です。 

ユーザーが開始したトラフィックは、Microsoft のコア ネットワークを経由して、最も近い参加リージョンに到達します。 

リージョン間ロード バランサーによって、トラフィックが適切なリージョン ロード バランサーにルーティングされます。

### <a name="participating-regions"></a>参加リージョン
* 米国東部 
* 西ヨーロッパ 
* 米国中部 
* 米国東部 2 
* 米国西部 
* 北ヨーロッパ 
* 米国中南部 
* 米国西部 2 
* 英国南部 
* 東南アジア 
* 米国中北部 
* 東日本 
* 東アジア 
* 米国中西部 
* オーストラリア南東部 
* オーストラリア東部 
* インド中部 

## <a name="limitations"></a>制限事項

* リージョン間のフロントエンド IP 構成はパブリックのみです。 現在、内部フロントエンドはサポートされていません。

* リージョン間ロード バランサーのバックエンド プールにプライベートまたは内部ロード バランサーを追加することはできません 

* リージョン間 IPv6 フロントエンド IP 構成はサポートされていません。 

* 現在、正常性プローブを構成することはできません。 既定の正常性プローブによって、リージョン ロード バランサーに関する可用性情報が 20 秒ごとに自動的に収集されます。 

* Azure Kubernetes Service (AKS) との統合は現在使用できません。 AKS クラスターがバックエンドでデプロイされている Standard ロード バランサーを使用してリージョン間ロード バランサーをデプロイした場合、接続が切断される場合があります。

## <a name="pricing-and-sla"></a>料金と SLA
リージョン間ロード バランサーでは、標準のロード バランサーの [SLA](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) が共有されます。

 
## <a name="next-steps"></a>次の手順

- 「[チュートリアル:Azure portal を使用してリージョン間ロード バランサーを作成する](tutorial-cross-region-portal.md)」を参照して、リージョン間ロードバランサーを作成する。
- 「[パブリック標準ロード バランサーを作成する](quickstart-load-balancer-standard-public-portal.md)」を参照して、標準リージョン ロード バランサーを作成する。
- [Azure Load Balancer](load-balancer-overview.md) についてさらに詳しく学習する。
