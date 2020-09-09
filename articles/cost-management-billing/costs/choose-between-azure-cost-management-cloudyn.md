---
title: Azure Cost Management か Cloudyn かの選択
description: この記事では、実際のコスト管理の要件に Azure Cost Management と Cloudyn のどちらが適しているかについてわかりやすく説明しています。
author: bandersmsft
ms.author: banders
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a46e02bfa9590b5ef34affa45f3c468fba6999c2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684968"
---
# <a name="choose-between-azure-cost-management-and-cloudyn"></a>Azure Cost Management か Cloudyn かの選択

Cloudyn は、2020 年末までに非推奨となる予定です。 既にある Cloudyn の機能は、可能な限り直接 Azure portal に統合されています。 現在、CSP 以外のお客様が新たにオンボードすることはできません。 既存の製品に対するサポートは、完全に非推奨化されるまで継続されます。

Microsoft では Cloudyn を買収し、そのコスト管理機能を Cloudyn ポータルから Azure にネイティブに移行しています。 新しい機能を使用するには、Azure portal にサインインし、Azure サービスの一覧の [[コスト管理と請求]](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) に移動します。 Cloudyn に比べて、ネイティブ エクスペリエンスではパフォーマンスが向上し、データ待機時間が約 8 時間低下します。

Enterprise Agreement、従量課金制、および MSDN のオファー カテゴリ向けの重要な機能の Azure Cost Management への移行が完了しています。 CSP サブスクリプションの Azure Cost Management への移行が進行中です。

まだ移行されていないオファー カテゴリをご利用のお客様は、引き続き Cloudyn ポータルを使用する必要があります。 それ以外のお客様は、Azure Cost Management を使用できます。

## <a name="recommended-services-by-offer"></a>プランごとに推奨されるサービス

| Microsoft Azure プラン | 推奨されるコスト管理サービス |
| --- | --- |
| Azure Enterprise Agreement | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Microsoft 顧客契約 | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| パートナーによってサポートされる Microsoft 顧客契約 | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |


## <a name="available-cost-management-features"></a>使用可能な Cost Management 機能

次の機能の一部は Cloudyn で使用できますが、それらのすべてを Azure Cost Management で使用できるようになりました。

- API
- Azure のコスト最適化の推奨情報。以下はその一例です。
    - Azure インスタンスの適切なサイズとシャットダウンに関する推奨情報
    - Azure 予約の推奨事項
- Budgets
- コスト分析
- Azure ストレージ アカウントへのデータのエクスポート
- 待ち時間の短縮
- Power BI テンプレート アプリ
- リソースのタグのサポート
- AWS 向けのクラウド間コスト分析サポート

## <a name="next-steps"></a>次のステップ
- [Azure Cost Management の詳細については、こちらを参照してください](../cost-management-billing-overview.md)。