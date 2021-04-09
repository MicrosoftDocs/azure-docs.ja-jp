---
title: Azure Traffic Manager | Microsoft Docs
description: この記事では、Azure Traffic Manager の概要を示します。 実際のアプリケーションのユーザー トラフィックを負荷分散するための選択肢として適切かどうかを見極めましょう。
services: traffic-manager
author: duongau
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2021
ms.author: duau
ms.openlocfilehash: 8c8897218b153c8584c89abab98934268ccd555d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182164"
---
# <a name="what-is-traffic-manager"></a>Traffic Manager について
Azure Traffic Manager は、DNS ベースのトラフィック ロード バランサーです。 このサービスを使用すると、パブリックに公開されているアプリケーションへのトラフィックを世界各国の Azure リージョン全体に分散することができます。 また、Traffic Manager によって、パブリック エンドポイントには高可用性と高い応答速度が確保されます。

Traffic Manager では、DNS を使用して、トラフィック ルーティング方法に基づいて適切なサービス エンドポイントにクライアント要求が誘導されます。 さらに、Traffic Manager では、各エンドポイントの稼働状況も監視されます。 Azure の内部または外部でホストされている、インターネットに公開された任意のサービスをエンドポイントとすることができます。 Traffic Manager には、さまざまなアプリケーション ニーズと自動フェールオーバー モデルに対応する、さまざまな[トラフィック ルーティング方法](traffic-manager-routing-methods.md)と[エンドポイント監視オプション](traffic-manager-monitoring.md)が用意されています。 Traffic Manager は Azure リージョン全体の障害などの障害に対応します。

>[!NOTE]
> Azure では、ユーザーのシナリオのためにフル マネージドの負荷分散ソリューションのスイートが提供されます。 
> * アプリケーション レイヤーでリージョン内のサーバー間の負荷分散が必要な場合は、[Application Gateway](../application-gateway/overview.md) に関するページを参照してください。
> * Web トラフィックのグローバル ルーティングを最適化し、迅速なグローバル フェールオーバーによってトップレベルのエンドユーザー パフォーマンスと信頼性を最適化する必要がある場合は、[Front Door](../frontdoor/front-door-overview.md) に関するページを参照してください。
> * ネットワーク層の負荷分散を行う場合は、[Load Balancer](../load-balancer/load-balancer-overview.md) を検討してください。 
> 
> 実際のエンド ツー エンドのシナリオでは、必要に応じてこれらのソリューションを組み合わせると役に立つことがあります。
> Azure の負荷分散オプションの比較については、「[Azure の負荷分散オプションの概要](/azure/architecture/guide/technology-choices/load-balancing-overview)」を参照してください。

Traffic Manager には、以下の機能が用意されています。

## <a name="increase-application-availability"></a>アプリケーションの可用性の向上

Traffic Manager では、エンドポイントを監視し、エンドポイントがダウンした場合の自動フェールオーバー機能を提供することにより、重要なアプリケーションの高可用性を実現します。
    
## <a name="improve-application-performance"></a>アプリケーション パフォーマンスの向上

Azure では、クラウド サービスや Web サイトを世界各地に配置されたデータセンターで実行できます。 Traffic Manager は、待ち時間が最も短いエンドポイントにトラフィックを誘導することで Web サイトの応答性を高めることができます。

## <a name="service-maintenance-without-downtime"></a>ダウンタイムなしでのサービス メンテナンス

ダウンタイムを発生させることなく、アプリケーションで定期的なメンテナンスを実行することができます。 メンテナンス中は、Traffic Manager で別のエンドポイントにトラフィックを誘導することができます。

## <a name="combine-hybrid-applications"></a>ハイブリッド アプリケーションの結合

Traffic Manager は、Azure 以外の外部エンドポイントをサポートしているため、"[クラウドへのバースト](https://azure.microsoft.com/overview/what-is-cloud-bursting/)"、"クラウドへの移行"、"クラウドへのフェールオーバー" など、クラウドとオンプレミスのハイブリッド デプロイで使用することができます。

## <a name="distribute-traffic-for-complex-deployments"></a>複雑なデプロイのトラフィック分散

[入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md)を使用することにより、複数のトラフィック ルーティング方法を組み合わせて、柔軟で洗練されたトラフィック ルーティング スキームを作成し、より大規模で複雑なデプロイのニーズに対応するようスケーリングできます。

## <a name="pricing"></a>価格

価格情報については、「[Traffic Manager の価格](https://azure.microsoft.com/pricing/details/traffic-manager/)」をご覧ください。


## <a name="next-steps"></a>次のステップ

- [Traffic Manager プロファイルの作成](./quickstart-create-traffic-manager-profile.md)方法を確認します。
- [Traffic Manager のしくみ](traffic-manager-how-it-works.md)を確認します。
- Traffic Manager について[よく寄せられる質問](traffic-manager-FAQs.md)をご覧ください。