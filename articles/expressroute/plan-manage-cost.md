---
title: Azure ExpressRoute のコストの管理を計画する
description: Azure portal でコスト分析を使用して、Azure ExpressRoute のコストを計画および管理する方法を学習します。
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: de8405477611d62b8a46e8b6b645887cc4d30099
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784243"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>Azure ExpressRoute のコストを計画および管理する

この記事では、ExpressRoute のコストを計画および管理する方法について説明します。 コストを見積もるサービスに対してリソースを追加する前に、まず、ExpressRoute のコストの計画に役立つ Azure 料金計算ツールを使用します。 次に、Azure リソースを追加するときに、推定コストを確認します。 

ExpressRoute リソースの使用を開始したら、Cost Management 機能を使用して、予算の設定とコストの監視を行います。 また、予想コストを確認し、支出の傾向を特定して、対処が必要な領域を特定することもできます。 

ExpressRoute のコストは、Azure の請求にある月額料金の一部でしかないことにご注意ください。 この記事では、ExpressRoute のコストを計画し、管理する方法について説明しますが、サードパーティのサービスを含め、Azure サブスクリプションで使用されるすべての Azure サービスとリソースに対して課金されます。

## <a name="prerequisites"></a>[前提条件]

Cost Management のコスト分析では、ほとんどの種類の Azure アカウントがサポートされますが、すべてではありません。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。 コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 

Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関するページを参照してください。

## <a name="local-vs-standard-vs-premium"></a>Local と Standard と Premium

ExpressRoute には、次の 3 種類の回線 SKU があります。[*Local*](./expressroute-faqs.md#expressroute-local)、*Standard*、[*Premium*](./expressroute-faqs.md#expressroute-premium)。 ExpressRoute の使用量に対して課金される方法は、この 3 つの SKU の種類によって異なります。 Local SKU では、無制限データ プランで自動的に課金されます。 Standard および Premium SKU では、従量制または無制限データ プランを選択できます。 Global Reach アドオンを使用する場合を除き、すべてのイングレス データの料金は無料です。 コストと予算を最適化するために、実際のワークロードに最適な SKU の種類とデータ プランを理解しておくことが重要です。

## <a name="estimate-costs"></a>コストの見積もり

ExpressRoute 回線を作成する前に、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してコストを見積もります。 

1. 左側で、 **[ネットワーク]** を選択し、 **[Azure ExpressRoute]** を選択して開始します。 

1. ピアリングの場所に応じて適切な *[ゾーン]* を選択します。 ドロップダウンから適切な *[ゾーン]* を選択するには、[ExpressRoute 接続プロバイダー](./expressroute-locations-providers.md#partners)を参照してください。 

1. 次に、見積もりを行う *[SKU]* 、 *[回線速度]* 、 *[データ プラン]* を選択します。 

1. *[追加の送信データ転送]* に、1 か月間に使用する可能性がある送信データの量を GB 単位で入力します。 

1. 最後に、"*Global Reach アドオン*" を見積に追加できます。

次のスクリーンショットでは、計算ツールを使用したコストの見積もりを示します。

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 料金計算ツールでの ExpressRoute のコスト見積り":::

詳細については、「[Azure ExpressRoute の価格](https://azure.microsoft.com/pricing/details/expressroute/)」をご覧ください。

### <a name="expressroute-gateway-estimated-cost"></a>ExpressRoute ゲートウェイの推定コスト

ExpressRoute ゲートウェイを使用して仮想ネットワークを ExpressRoute 回線にリンクしている場合は、次の手順に従って、ゲートウェイの使用コストを見積もることができます。

1. 左側で、 **[ネットワーク]** を選択し、 **[VPN Gateway]** を選択して開始します。 

1. ゲートウェイの *[リージョン]* を選択し、」*種類*」 を **[ExpressRoute ゲートウェイ]** に変更します。

1. ドロップダウンから *[ゲートウェイの種類]* を選択します。

1. *[ゲートウェイ時間]* を入力します。 (720 時間 = 30 日)

## <a name="understand-the-full-billing-model-for-expressroute"></a>ExpressRoute の詳細な課金モデルを理解する

ExpressRoute は、新しいリソースをデプロイするときに ExpressRoute と共にコストが発生する Azure インフラストラクチャ上で実行されます。 追加のインフラストラクチャでコストが発生する可能性があることを理解しておくことが重要です。 デプロイされたリソースに変更を加える場合は、このコストを管理する必要があります。 

### <a name="costs-that-typically-accrue-with-expressroute"></a>ExpressRoute で通常発生するコスト

ExpressRoute 回線を作成するとき、ExpressRoute ゲートウェイを作成して、仮想ネットワークを回線にリンクすることができます。 ゲートウェイは、1 時間あたりの料金と、ExpressRoute 回線のコストを加算して課金されます。 さまざまなゲートウェイ SKU の料金を見るには [ExpressRoute の価格](https://azure.microsoft.com/en-us/pricing/details/expressroute) を参照し、[ExpressRoute ゲートウェイ] を選択してください。
 
### <a name="costs-might-accrue-after-resource-deletion"></a>リソースの削除後にコストが発生する可能性がある

ExpressRoute 回線を削除した後に ExpressRoute ゲートウェイがある場合、削除するまで料金は課金されます。

### <a name="using-azure-prepayment-credit"></a>Azure 前払いクレジットを使用する

Azure ExpressRoute の料金は、Azure 前払い (旧称: 年額コミットメント) のクレジットを使用して支払うことができます。 ただし、Azure 前払いのクレジットを使用して、サードパーティの製品やサービス (Azure Marketplace からのものを含む) の料金を支払うことはできません。

## <a name="monitor-costs"></a>コストを監視する

ExpressRoute で Azure リソースを使用する場合、コストが発生します。 Azure リソース使用のユニット コストは、期間 (秒、分、時間、日数) やユニット使用量 (バイト、メガバイトなど) によって異なります。ExpressRoute の使用が開始されるとすぐにコストが発生し、[コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)でコストを確認できます。

コスト分析を使用すると、さまざまな期間について、ExpressRoute 回線コストをグラフや表で表示できます。 たとえば、日単位、現在の月、以前の月、年単位などがあります。 予算や予想コストを基準としてコストを表示することもできます。 時間経過を示す、より長い期間のビューに切り替えると、支出の傾向を特定するのに役立ちます。 超過出費が発生した可能性のある時期を確認できます。 予算を作成したら、それを超えた場所も簡単に確認できます。

ExpressRoute のコストをコスト分析で表示するには、次の操作を行います。

1. Azure portal にサインインします。

1. **[サブスクリプション]** に移動し、一覧からサブスクリプションを選択して、メニューから **[コスト分析]** を選択します。 コスト分析で別のスコープに切り替えるには、 **[スコープ]** を選択します。 既定では、最初のドーナツ グラフにサービスのコストが表示されます。

    最初にコスト分析を開いたときに、実際の月額コストが表示されます。 月単位のすべての使用コストを示す例を次に示します。

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="サブスクリプションの累積コストを示す例":::
    

1.  Expressroute など、1 つのサービスのコストを絞り込むには、 **[フィルターの追加]** を選択してから、 **[サービス名]** を選択します。 次に、 **[ExpressRoute]** を選択します。

    ExpressRoute のみのコストを表示する例を次に示します。

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="ExpressRoute の累積コストを示す例":::

前の例では、サービスの現在のコストが表示されます。 また、Azure リージョン (場所) 別およびリソース グループ別の ExpressRoute のコストも表示されます。 ここでは、コストを自分で調べることができます。

## <a name="create-budgets-and-alerts"></a>予算とアラートを作成する

[予算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成して、コストを管理し、異常な支出や浪費のリスクについて、関係者に自動的に通知する[アラート](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 

監視の粒度をさらに細かく示す必要がある場合は、Azure の特定のリソースまたはサービスに対するフィルターを使用して予算を作成できます。 フィルターを使用すると、追加のコストがかかる新しいリソースが誤って作成されないようにすることができます。 予算を作成するときのフィルター オプションの詳細については、[グループとフィルターのオプション](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関する記事を参照してください。

## <a name="export-cost-data"></a>コスト データのエクスポート

また、ストレージ アカウントに[コスト データをエクスポート](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)することもできます。 これは、自分や他のユーザーがコストに関する追加のデータ分析を行う必要がある場合に便利です。 たとえば、財務チームは、Excel や Power BI を使用してデータを分析できます。 日単位、週単位、または月単位のスケジュールでコストをエクスポートし、カスタムの日付範囲を設定することができます。 コスト データのエクスポートは、推奨されるコスト データセット取得方法です。

## <a name="next-steps"></a>次のステップ

- Azure ExpressRoute での価格のしくみについて詳しく説明します。 [Azure ExpressRoute の価格の概要](https://azure.microsoft.com/en-us/pricing/details/expressroute/)に関する記事を参照してください。
- [Azure Cost Management を使用してクラウドへの投資を最適化する](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)方法について説明します。
- [コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を使用してコストを管理する方法について詳細に説明します。
- [予期しないコストを回避](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)する方法について説明します。
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) のガイド付き学習コースを受講します。