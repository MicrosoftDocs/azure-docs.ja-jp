---
title: Azure Traffic Manager | Microsoft Docs
description: この記事では、Azure Traffic Manager の概要を示します。 実際のアプリケーションのユーザー トラフィックを負荷分散するための選択肢として適切かどうかを見極めましょう。
services: traffic-manager
author: KumudD
manager: twooley
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: kumud
ms.openlocfilehash: a8504cea3af6eb7633cf6738a73f757767d83138
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534319"
---
# <a name="what-is-traffic-manager"></a>Traffic Manager について
Azure Traffic Manager は、世界中の Azure リージョン間でサービスへのトラフィックを最適に配分しつつ、高可用性と応答性を実現する DNS ベースのトラフィック ロード バランサーです。

Traffic Manager は、DNS を使用し、トラフィック ルーティング方法とエンドポイントの正常性に基づいて最適なサービス エンドポイントにクライアント要求を誘導します。 エンドポイントは、Azure の内部または外部でホストされている、インターネットに公開されたサービスです。 Traffic Manager には、さまざまなアプリケーション ニーズと自動フェールオーバー モデルに対応する、さまざまな[トラフィック ルーティング方法](traffic-manager-routing-methods.md)と[エンドポイント監視オプション](traffic-manager-monitoring.md)が用意されています。 Traffic Manager は Azure リージョン全体の障害などの障害に対応します。

>[!NOTE]
> Azure では、ユーザーのシナリオのためにフル マネージドの負荷分散ソリューションのスイートが提供されます。 トランスポート層セキュリティ (TLS) プロトコル終端 ("SSL オフロード") または HTTP/HTTPS 要求によるアプリケーション レイヤーの処理が必要な場合は、「[Application Gateway](../application-gateway/application-gateway-introduction.md)」をご覧ください。 リージョン内での負荷分散が必要な場合は、[Load Balancer](../load-balancer/load-balancer-overview.md) に関するページを参照してください。 実際のエンド ツー エンドのシナリオでは、必要に応じてこれらのソリューションを組み合わせると役に立つことがあります。

Traffic Managerには、次の機能が用意されています。

## <a name="increase-application-availability"></a>アプリケーションの可用性の向上

Traffic Manager では、エンドポイントを監視し、エンドポイントがダウンした場合の自動フェールオーバー機能を提供することにより、重要なアプリケーションの高可用性を実現します。
    
## <a name="improve-application-performance"></a>アプリケーション パフォーマンスの向上

Azure では、クラウド サービスや Web サイトを世界各地に配置されたデータセンターで実行できます。 Traffic Manager は、クライアントのネットワーク待機時間が最も短いエンドポイントにトラフィックを転送することで、アプリケーションの応答性を高めます。

## <a name="perform-service-maintenance-without-downtime"></a>ダウンタイムなしでのサービス メンテナンスの実行

ダウンタイムを発生させることなく、アプリケーションで定期的なメンテナンスを行うことができます。 メンテナンス中は、Traffic Manager で別のエンドポイントにトラフィックを誘導することができます。

## <a name="combine-hybrid-applications"></a>ハイブリッド アプリケーションの結合

Traffic Manager は、Azure 以外の外部エンドポイントをサポートしているため、"[クラウドへのバースト](https://azure.microsoft.com/overview/what-is-cloud-bursting/)"、"クラウドへの移行"、"クラウドへのフェールオーバー" など、クラウドとオンプレミスのハイブリッド デプロイで使用することができます。

## <a name="distribute-traffic-for-complex-deployments"></a>複雑なデプロイのトラフィック分散

[入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md)を使用することにより、複数のトラフィック ルーティング方法を組み合わせて、柔軟で洗練されたトラフィック ルーティング スキームを作成し、より大規模で複雑なデプロイのニーズに対応するようスケーリングできます。

## <a name="pricing"></a>価格

価格情報については、「[Traffic Manager の価格](https://azure.microsoft.com/pricing/details/traffic-manager/)」をご覧ください。


## <a name="next-steps"></a>次の手順

- [Traffic Manager プロファイルの作成](traffic-manager-create-profile.md)方法を確認します。
- [Traffic Manager のしくみ](traffic-manager-how-it-works.md)を確認します。
- Traffic Manager について[よく寄せられる質問](traffic-manager-FAQs.md)をご覧ください。




