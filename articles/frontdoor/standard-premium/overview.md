---
title: Azure Front Door Standard/Premium のドキュメント | Microsoft Docs
description: この記事では、Azure Front Door Standard/Premium の概要を示します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 574340825567dcd512a5da1b311c57fe12954e34
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030547"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium (プレビュー) とは

> [!IMPORTANT]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [Azure Front Door ドキュメント](../front-door-overview.md)を参照してください。

Azure Front Door Standard/Premium は、Microsoft グローバル境界ネットワークを使用し、インテリジェントな脅威保護と統合する、高速かつ安全で信頼性の高い最新クラウド CDN です。 これにより、Azure Front Door、Azure Content Delivery Network (CDN) Standard、Azure Web Application Firewall (WAF) の機能が、セキュリティで保護された 1 つのクラウド CDN プラットフォームに統合されます。

Azure Front Door Standard/Premium を使用することで、グローバルなコンシューマーおよびエンタープライズ アプリケーションをセキュリティで保護された高パフォーマンスのパーソナル アプリケーションに変換し、ユーザーに近いネットワーク境界で世界中のユーザーにコンテンツを届けることができます。 また、迅速なフェールオーバーによるグローバルな HTTP 負荷分散の利点を活かしながら、ウォームアップなしでアプリケーションをスケールアウトすることもできます。

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Azure Front Door Standard/Premium のアーキテクチャ" lightbox="../media/overview/front-door-overview-expanded.png":::

Azure Front Door Standard/Premium は、レイヤー 7 (HTTP/HTTPS 層) で動作し、スプリット TCP と Microsoft のグローバル ネットワークでエニーキャスト プロトコルを使用してグローバル接続を向上させます。 Azure Front Door では、ルール セットを使用するカスタマイズされたルーティング方法に基づいて、最も高速で最も可用性が高い配信元にクライアント要求を確実にルーティングできます。 アプリケーションの配信元は、Azure の内部または外部でホストされている、インターネットに公開された任意のサービスです。 Azure Front Door Standard/Premium には、各種のアプリケーション ニーズと自動フェールオーバー シナリオに対応する、さまざまなトラフィック ルーティング方法と配信元の正常性監視オプションが用意されています。 Traffic Manager と同様に、Front Door には、Azure リージョン全体の障害を含め、障害に対する回復性があります。

また、Azure Front Door は、統合された Web Application Firewall 保護、ボット保護、組み込みのレイヤー 3 およびレイヤー 4 分散型サービス拒否 (DDoS) 保護を使用して、エッジでもアプリを保護します。 プライベート リンク サービスを使用して、プライベート バックエンドもセキュリティで保護します。 Azure Front Door は、世界的な規模で Microsoft のクラス最高のセキュリティを提供します。  

>[!NOTE]
> Azure では、ユーザーのシナリオのためにフル マネージドの負荷分散ソリューションのスイートが提供されます。
>
> * DNS ベースのグローバルなルーティングを検討中であり、トランスポート層セキュリティ (TLS) プロトコル終端 ("SSL オフロード") の要件や、HTTP/HTTPS 要求ごとまたはアプリケーション層の処理の要件が **ない** 場合は、[Traffic Manager](../../traffic-manager/traffic-manager-overview.md) を検討してください。
> * アプリケーション層でリージョン内のサーバー間の負荷分散が必要な場合は、[Application Gateway](../../application-gateway/overview.md) を検討してください。
> * ネットワーク層の負荷分散を行う場合は、[Load Balancer](../../load-balancer/load-balancer-overview.md) を検討してください。
>
> 実際のエンド ツー エンドのシナリオでは、必要に応じてこれらのソリューションを組み合わせると役に立つことがあります。
> Azure の負荷分散オプションの比較については、「[Azure の負荷分散オプションの概要](/azure/architecture/guide/technology-choices/load-balancing-overview)」を参照してください。

> [!IMPORTANT]
> Azure Front Door Standard/Premium は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="why-use-azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium (プレビュー) を使用する理由

Azure Front Door Standard/Premium では単一の統合プラットフォームを提供します。これを使用すると、設定不要の組み込みセキュリティ統合と、シンプルで予測可能な価格モデルを通じて、動的および静的な高速化に対応できます。 また、Front Door では、アプリのグローバルなルーティングを定義、管理、監視することができます。

Azure Front Door Standard/Premium (プレビュー) に含まれる主な機能:

- **[スプリット TCP](../front-door-routing-architecture.md#splittcp)** ベースのエニーキャスト プロトコルを使用した、アプリケーション パフォーマンスの高速化。

- インテリジェントな **[正常性プローブ](concept-health-probes.md)** による監視と、 **[配信元](concept-origin.md)** の間での負荷分散。

- 柔軟なドメイン検証により独自の **[カスタム ドメイン](how-to-add-custom-domain.md)** を定義。

- **[Web Application Firewall (WAF)](../../web-application-firewall/afds/afds-overview.md)** が統合されたアプリケーション セキュリティ。

- SSL オフロードと、統合された **[証明書管理](how-to-configure-https-custom-domain.md)** 。

- **[Private Link](concept-private-link.md)** を使用して配信元をセキュリティで保護。  

- **[ルール セット](concept-rule-set.md)** を使用した、カスタマイズ可能なトラフィック ルーティングと最適化。

- Front Door とセキュリティ パターンの両方に対応したオールインワン ダッシュボードを備えた **[組み込みレポート](how-to-reports.md)** 。

- Azure Monitoring と統合される **[リアルタイムの監視](how-to-monitor-metrics.md)** およびアラート。

- Front Door の各要求および失敗した正常性プローブに関する **[ログ記録](how-to-logs.md)** 。

- エンド ツー エンドの IPv6 接続と HTTP/2 プロトコルのネイティブ サポート。

## <a name="pricing"></a>価格

Azure Front Door Standard/Premium には、Standard と Premium の 2 つの SKU があります。 [レベルの比較](tier-comparison.md)に関する記事をご覧ください。 料金情報については、[Front Door の価格](https://azure.microsoft.com/pricing/details/frontdoor/)に関するページを参照してください。 

## <a name="whats-new"></a>新機能

RSS フィードを購読し、[Azure の更新情報](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door)ページで、最新の Azure Front Door 機能の更新を確認します。

## <a name="next-steps"></a>次のステップ

* [フロント ドアの作成](create-front-door-portal.md)方法について学習します。
