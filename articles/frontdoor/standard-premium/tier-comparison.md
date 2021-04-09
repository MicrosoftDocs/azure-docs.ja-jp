---
title: Azure Front Door Standard/Premium SKU の比較
description: この記事では、Azure Front Door Standard と Premium SKU の概要と、これらの機能面の違いについて説明します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 1753f2bb649e73d7a5fe6c1cc32361a418ea7f63
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181807"
---
# <a name="overview-of-azure-front-door-standardpremium-sku-preview"></a>Azure Front Door Standard/Premium SKU の概要 (プレビュー)

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

Azure Front Door には、[Azure Front Door](../front-door-overview.md)、Azure Front Door Standard (プレビュー)、Azure Front Door Premium (プレビュー) の 3 つの SKU があります。 Azure Front Door Standard/Premium SKU は、Azure Front Door、Microsoft の Azure CDN Standard、Azure WAF の機能を、インテリジェントな脅威保護を用いて、セキュリティで保護された 1 つのクラウド CDN プラットフォームに統合したものです。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳細については、「[**Microsoft Azure プレビューの追加使用条件**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

* **Azure Front Door Standard SKU** は、以下を提供します。

    * コンテンツ配信の最適化
    * 静的および動的なコンテンツ アクセラレーションの両方を提供
    * グローバル負荷分散
    * SSL オフロード
    * ドメインと証明書の管理
    * 強化されたトラフィック分析 
    * 基本的なセキュリティ機能

* **Azure Front Door Premium SKU** には Standard SKU のすべての機能に加えて、以下が備わっています。

    * WAF 全体の広範なセキュリティ機能
    * ボット保護
    * Private Link のサポート
    * Microsoft の脅威インテリジェンスとセキュリティ分析との統合。 

![各 Front Door SKU の比較を示す図。](../media/tier-comparison/tier-comparison.png)

## <a name="feature-comparison"></a>機能の比較

| 機能 |      Standard      |  Premium |
|----------|:-------------:|------:|
| カスタム ドメイン | はい | はい |
| SSL オフロード | はい | はい |
| キャッシュ |  はい  | はい |
| 圧縮 | はい | はい   |
| グローバル負荷分散 | はい  | はい |
| 第 7 層のルーティング | はい | はい |
| URL 書き換え | はい | はい |
| ルール エンジン | はい | はい |
| プライベート オリジン (Private Link) | いいえ | はい |
| WAF | カスタム ルールのみ | はい |
| ボット保護 | いいえ | はい |
| 強化されたメトリックと診断 | はい | はい |
| トラフィック レポート | はい | はい |
| セキュリティ レポート | いいえ | はい | 

## <a name="next-steps"></a>次のステップ

[フロント ドアの作成](create-front-door-portal.md)方法について学習します
