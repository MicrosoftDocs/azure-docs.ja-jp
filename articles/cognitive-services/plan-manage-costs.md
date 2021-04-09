---
title: Azure Cognitive Services のコストを管理する計画
description: Azure portal でコスト分析を使用して、Azure Cognitive Services のコストを計画および管理する方法を学習します。
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: db99fa5caff27a24aa04e4780b25ade3f7c25496
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699931"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Azure Cognitive Services のコストを計画および管理する

この記事では、Azure Cognitive Services のコストを計画および管理する方法について説明します。 コストを見積もるサービスに対してリソースを追加する前に、まず、Cognitive Services のコストの計画に役立つ Azure 料金計算ツールを使用します。 次に、Azure リソースを追加するときに、推定コストを確認します。 Cognitive Services リソース (Computer Vision、LUIS、Text Analytics、Translator など) の使用を開始したら、Cost Management 機能を使用し、予算を設定し、コストを監視します。 また、予想コストを確認し、支出の傾向を特定して、対処が必要な領域を特定することもできます。Cognitive Services のコストは、Azure の請求に記載された月額料金の一部でしかありません。 この記事では、Cognitive Services のコストを計画し、管理する方法について説明しますが、サードパーティのサービスを含め、Azure サブスクリプションで使用されるすべての Azure サービスとリソースに対して課金されます。

## <a name="prerequisites"></a>[前提条件]

Cost Management のコスト分析では、ほとんどの種類の Azure アカウントがサポートされますが、すべてではありません。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。 コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関するページを参照してください。

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>Cognitive Services 使用前のコスト見積もり

Cognitive Services を追加する前に、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してコストを見積もります。

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Cognitive Services の Azure 料金計算ツール" border="true":::

ワークスペースに新しいリソースを追加したら、この計算ツールに戻り、同じリソースをここに追加して、コストの見積もりを更新します。

詳細については、[Azure Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)に関するページを参照してください。

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>Cognitive Services の詳細な課金モデルを理解する

Cognitive Services は、新しいリソースをデプロイするときに[コストが発生する](https://azure.microsoft.com/pricing/details/cognitive-services/) Azure インフラストラクチャ上で実行されます。 追加のインフラストラクチャでコストが発生する可能性があることを理解しておくことが重要です。 デプロイされたリソースに変更を加える場合は、このコストを管理する必要があります。 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>Cognitive Services で通常発生するコスト

通常、Azure リソースのデプロイ後、ご利用の価格レベルと、エンドポイントに対して行った API 呼び出しによってコストが決定されます。 ご利用のサービスにコミットメント レベルがある場合、ご利用のレベルで割り当てられている呼び出し数を超えたとき、超過分が請求されることがあります。

次のサービスを使用すると、追加コストが発生する場合があります。

#### <a name="qna-maker"></a>QnA Maker

QnA Maker 用のリソースを作成すると、他の Azure サービスのリソースも作成される場合があります。 具体的な内容を次に示します。

- [Azure App Service (ランタイム用)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure Cognitive Search (データ用)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>リソースの削除後に発生する可能性があるコスト

#### <a name="qna-maker"></a>QnA Maker

QnA Maker リソースの削除後、次のリソースが引き続き存在する可能性があります。 これらを削除するまで、これらのコストは発生し続けます。

- [Azure App Service (ランタイム用)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure Cognitive Search (データ用)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-azure-prepayment-credit-with-cognitive-services"></a>Cognitive Services で Azure 前払いクレジットを使用する

Cognitive Services の料金は、Azure 前払い (旧称: 年額コミットメント) のクレジットを使用して支払うことができます。 ただし、Azure 前払いのクレジットを使用して、サードパーティの製品やサービス (Azure Marketplace からのものを含む) の料金を支払うことはできません。

## <a name="monitor-costs"></a>コストを監視する

<!-- Note to Azure service writer: Modify the following as needed for your service. Replace example screenshots with ones taken for your service. If you need assistance capturing screenshots, ask banders for help. -->

Cognitive Services で Azure リソースを使用と、コストが発生します。 Azure リソース使用のユニット コストは、期間 (秒、分、時間、日数) やユニット使用量 (バイト、メガバイトなど) によって異なります。 Cognitive Services を使い始めるとすぐにコストが発生し、[コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)でコストを確認できます。

コスト分析を使用すると、さまざまな期間について、Cognitive Services のコストをグラフや表で表示できます。 たとえば、日単位、現在の月、以前の月、年単位などがあります。 予算や予想コストを基準としてコストを表示することもできます。 時間経過を示す、より長い期間のビューに切り替えると、支出の傾向を特定するのに役立ちます。 超過出費が発生した可能性のある時期を確認できます。 予算を作成したら、それを超えた場所も簡単に確認できます。

コスト分析で Cognitive Services のコストを表示するには:

1. Azure portal にサインインします。
2. Azure portal でスコープを開き、メニューから **[コスト分析]** を選択します。 たとえば、 **[サブスクリプション]** に移動し、一覧からサブスクリプションを選択して、メニューから **[コスト分析]** を選択します。 コスト分析で別のスコープに切り替えるには、 **[スコープ]** を選択します。
3. 既定では、最初のドーナツ グラフにサービスのコストが表示されます。 グラフで [Cognitive Services] というラベルが付いた領域を選択します。

最初にコスト分析を開いたときに、実際の月額コストが表示されます。 月単位のすべての使用コストを示す例を次に示します。

:::image type="content" source="./media/cost-management/all-costs.png" alt-text="サブスクリプションの累積コストを示す例":::

- Cognitive Services などの 1 つのサービスのコストに絞り込むには、 **[フィルターの追加]** を選択してから、 **[サービス名]** を選択します。 次に、 **[Cognitive Services]** を選択します。

Cognitive Services のみのコストを表示する例を次に示します。

:::image type="content" source="./media/cost-management/cognitive-services-costs.png" alt-text="Cognitive Services の累積コストを示す例":::

前の例では、サービスの現在のコストが表示されます。 Azure リージョン (場所) 別およびリソース グループ別の Cognitive Services のコストも表示されます。 ここでは、コストを自分で調べることができます。

## <a name="create-budgets"></a>予算を作成する

[予算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成して、コストを管理し、異常な支出や浪費のリスクについて、関係者に自動的に通知する[アラート](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 

監視の粒度をさらに細かく示す必要がある場合は、Azure の特定のリソースまたはサービスに対するフィルターを使用して予算を作成できます。 フィルターを使用すると、追加のコストがかかる新しいリソースが誤って作成されないようにすることができます。 予算を作成するときのフィルター オプションの詳細については、[グループとフィルターのオプション](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関する記事を参照してください。

## <a name="export-cost-data"></a>コスト データのエクスポート

また、ストレージ アカウントに[コスト データをエクスポート](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)することもできます。 これは、自分や他のユーザーがコストに関する追加のデータ分析を行う必要がある場合に便利です。 たとえば、財務チームは、Excel や Power BI を使用してデータを分析できます。 日単位、週単位、または月単位のスケジュールでコストをエクスポートし、カスタムの日付範囲を設定することができます。 コスト データのエクスポートは、推奨されるコスト データセット取得方法です。

## <a name="next-steps"></a>次のステップ

- [Azure Cost Management を使用してクラウドへの投資を最適化する](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)方法について説明します。
- [コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を使用してコストを管理する方法について詳細に説明します。
- [予期しないコストを回避](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)する方法について説明します。
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) のガイド付き学習コースを受講します。