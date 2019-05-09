---
title: Azure での Cloudyn の概要 | Microsoft Docs
description: Cloudyn は、Azure や他のクラウド リソースを使用する際に役立つマルチクラウド コスト管理ソリューションです。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 08a1980a8855fc119a5066e058e9bb4d2df6646e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57998877"
---
# <a name="what-is-the-cloudyn-service"></a>Cloudyn サービスとは

Microsoft の子会社である Cloudyn によって、Azure リソースおよび AWS や Google などの他のクラウド プロバイダーにおけるクラウドの使用状況と支出を追跡できます。 わかりやすいダッシュボードのレポートは、コストの割り当てとショーバック/チャージバックに役立ちます。 Cloudyn を使うと、使用率が低いリソースを識別して管理、調整することにより、クラウドの支出を最適化できます。

入門ビデオを視聴するには、「[Azure Cloudyn の概要](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo)」を参照してください。

Azure Cost Management は Cloudyn と同様の機能を提供します。 Azure Cost Management は、Azure のネイティブのコスト管理ソリューションです。 このソリューションにより、コストを分析し、予算を作成して管理し、データをエクスポートし、最適化のための推奨事項を検討して対処することで、費用を節約できます。 詳細については、「[Azure Cost Management](overview-cost-mgt.md)」を参照してください。

## <a name="cloudyn-features-moving-to-azure-cost-management"></a>Azure Cost Management に移行される Cloudyn の機能

Microsoft では Cloudyn を買収し、そのコスト管理機能を Cloudyn ポータルから Azure にネイティブに移行しています。 新しい機能を使用するには、Azure portal にサインインし、Azure サービスの一覧の [[コスト管理と請求]](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) に移動します。 Cloudyn に比べて、ネイティブ エクスペリエンスではパフォーマンスが向上し、データ待機時間が約 8 時間低下します。

Enterprise Agreement、従量課金制、および MSDN のオファー カテゴリ向けの重要な機能の Azure Cost Management への移行が完了しています。 CSP サブスクリプションの Azure Cost Management への移行が進行中です。

まだ移行されていないオファー カテゴリをご利用のお客様は、引き続き Cloudyn ポータルを使用する必要があります。 それ以外のお客様は、Azure Cost Management を使用できます。

| Microsoft Azure のオファーと機能 | 推奨されるコスト管理サービス |
| --- | --- |
| Azure Enterprise Agreement | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |
| AWS 向けのクラウド間コスト分析サポート (プレビュー段階) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| AWS の推奨事項 | [Cloudyn](https://azure.cloudyn.com) |

次の機能の一部は Cloudyn で使用できますが、それらのすべてを Azure Cost Management で使用できるようになりました。

- API
- Azure コンピューティングの推奨事項
- Azure 予約の推奨事項
- 予算
- コスト分析
- Azure ストレージ アカウントへのデータのエクスポート
- 待ち時間の短縮
- Power BI コンテンツ パックとコネクタ
- リソースのタグのサポート

## <a name="monitor-usage-and-spending"></a>使用状況と支出の監視

クラウドでは一定期間のリソース使用量に対して料金を払うので、クラウド インフラストラクチャでは使用状況と支出を監視することが非常に重要です。 使用量が契約のしきい値を超えると、予想外の超過コストがすぐに発生する可能性があります。 いくつかの重要な要因により、特別な監視が困難な場合があります。 第一に、平均使用量に基づくコストの予測では、請求期間中は使用量が一定であるものと想定します。 第二に、コストが予算に近づくか予算を超えるときは、支出調整のために事前に通知を受け取ることが重要です。 そして、クラウド サービス プロバイダーによっては、コスト予測としきい値の対比レポートまたは期間ごとの比較レポートが提供されないことがあります。

レポートは、支出を監視し、クラウド使用量、コスト、傾向を分析して追跡するのに役立ちます。 時間経過レポートを使うと、通常の傾向とは異なる異常を検出できます。 クラウドのデプロイにおける非効率性は、最適化レポートを見るとわかります。 非効率性はコスト分析レポートでもわかります。

## <a name="manage-costs"></a>コストを管理する

履歴データは、一定期間の使用状況とコストを分析して傾向を明らかにし、コストを管理するのに役立ちます。 傾向は、今後の支出を予測するのに使われます。 Cloudyn には、便利な予想コスト レポートもあります。

コスト割り当ては、タグ付けポリシーに基づいてコストを分析することにより、コストを管理します。 カスタム アカウント、リソース、エンティティのタグを使って、コストの割り当てを調整できます。 Category Manager は、タグを整理して、ガバナンスの強化に役立ちます。 また、ショーバック/チャージバックのコスト割り当てを使って、リソースの使用状況と関連するコストを示すことで、消費行動に影響を与えたり、テナントの顧客に課金したりします。

アクセス制御は、ユーザーとチームが必要なコスト管理データだけにアクセスできるようにすることで、コストの管理を支援します。 アクセス権の割り当てには、エンティティ構造、ユーザー管理、受信者一覧付きのスケジュールされたレポートを使います。

アラートを使うと、通常とは異なる支出や支出超過が発生したときに自動的に通知を受け取ることで、コストを管理できます。 また、異常な支出や浪費のリスクについて、他の関係者に自動的に通知することもできます。 さまざまなレポートは、予算とコストしきい値に基づくアラートをサポートします。 ただし、CSP パートナー アカウントまたはサブスクリプションのアラートは、現在サポートされていません。

## <a name="improve-efficiency"></a>効率性を改善する

Cloudyn を利用すると、VM の最適な使用状況を判断し、アイドル状態の VM を識別したり、アイドル状態の VM やアタッチされていないディスクを削除したりできます。 サイズ最適化レポートや非効率性レポートの情報を使って、ダウンサイジングまたはアイドル状態の VM の削除に関する計画を作成できます。 ただし、CSP パートナー アカウントまたはサブスクリプションの最適化レポートは、現在サポートされていません。

AWS 予約済みインスタンスをプロビジョニングした場合、最適化レポートで予約済みインスタンスの使用率を向上できます。最適化レポートでは、購入の推奨事項を表示し、未使用の予約を変更し、プロビジョニングを計画することができます。


## <a name="next-steps"></a>次の手順

Cloudyn について理解できたので、次はクラウド環境を登録して、データの探索を始めてみましょう。

- [個々の Azure サブスクリプションとコスト データの表示を登録する](quick-register-azure-sub.md)
