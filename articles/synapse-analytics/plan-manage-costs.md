---
title: Azure Synapse Analytics のコストの管理を計画する
description: Azure portal でコスト分析を使用して、Azure Synapse Analytics のコストを計画および管理する方法について説明します。
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: 06586b5bf20619f57b2ad1c3d5de84dd61952261
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561247"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Azure Synapse Analytics のコストを計画して管理する

この記事では、Azure Synapse Analytics のコストを計画して管理する方法について説明します。 コストを見積もるサービスに対してリソースを追加する前に、まず、Azure Synapse のコストの計画に役立つ Azure 料金計算ツールを使用します。 次に、Azure Synapse リソースを追加したときの推定コストを確認します。

Azure Synapse リソースの使用を開始した後、コスト管理機能を使用して、予算の設定とコストの監視を行います。 また、予想コストを確認し、支出の傾向を特定して、対処が必要な領域を特定することもできます。Azure Synapse のコストは、Azure で課金される月額料金の一部でしかありません。 この記事では、Azure Synapse のコストを計画して管理する方法について説明しますが、サードパーティのサービスを含め、課金は、Azure サブスクリプションで使用されるすべての Azure サービスとリソースに対して行われます。

## <a name="prerequisites"></a>[前提条件]

Cost Management のコスト分析では、ほとんどの種類の Azure アカウントがサポートされますが、すべてではありません。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。 コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関するページを参照してください。

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Azure Synapse Analytics を使用する前にコストを見積もる

Azure Synapse Analytics を追加する前に、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してコストを見積もります。

Azure Synapse には、次のコストの見積もりで確認できるように、料金が異なるさまざまなリソースがあります。 

![Azure 料金計算ツールの推定コストを示す例](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Azure Synapse Analytics の詳細な課金モデルを理解する

Azure Synapse は、新しいリソースをデプロイすると Azure Synapse と併せてコストが発生する Azure インフラストラクチャ上で実行されます。 追加のインフラストラクチャでコストが発生する可能性があることを理解しておくことが重要です。 デプロイされたリソースに変更を加える場合は、このコストを管理する必要があります。 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Azure Synapse Analytics で通常発生するコスト

Azure Synapse のリソースを作成すると、他の Azure サービスのリソースも作成されます。 具体的な内容を次に示します。

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>リソースの削除後にコストが発生する可能性がある

Azure Synapse リソースを削除しても、次のリソースが引き続き存在する可能性があります。 これらを削除するまで、これらのコストは発生し続けます。

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Azure Synapse で Azure 前払いクレジットを使用する 

Azure Synapse の料金は、Azure 前払い (旧称: 年額コミットメント) のクレジットを使用して支払うことができます。 ただし、Azure 前払いのクレジットを使用して、サードパーティの製品やサービス (Azure Marketplace からのものを含む) の料金を支払うことはできません。

## <a name="review-estimated-costs-in-the-azure-portal"></a>Azure portal で推定料金を検討する

Azure Synapse Analytics のリソースを作成すると、推定コストが表示されます。 ワークスペースには、ワークスペースと共に作成されたサーバーレス SQL プールがあります。 サーバーレス SQL プールでは、クエリを実行するまで料金は発生しません。 ワークスペース内に、専用 SQL プールやサーバーレス Apache Spark プールなどのその他のリソースを作成する必要があります。

<ResourceName> を作成し、推定価格を表示するには:

1. Azure portal でサービスに移動します。
2. リソースを作成します。
3. 概要に表示される推定料金を確認します。
4. リソースの作成を完了します。

![リソース作成時の推定コストの表示例](./media/plan-manage-costs/create-workspace-cost.png)


Azure サブスクリプションに使用制限がある場合は、Azure により、クレジット額を超える支出が防止されます。 Azure リソースを作成して使用するときに、クレジットが使用されます。 クレジットの上限に達すると、その請求期間の残りの期間は、デプロイしたリソースが無効にされます。 クレジットの上限は変更できませんが、上限を取り除くことは可能です。 使用制限の詳細については、「[Azure の使用制限](../cost-management-billing/manage/spending-limit.md)」を参照してください。

## <a name="monitor-costs"></a>コストを監視する

Azure Synapse リソースを使用すると、コストが発生します。 Azure リソース使用のユニット コストは、期間 (秒、分、時間、日数) やユニット使用量 (バイト、メガバイトなど) によって異なります。Azure Synapse でリソースの使用を開始すると、すぐにコストが発生します。[コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)でそのコストを確認できます。

コスト分析を使用すると、異なる時間間隔の Azure Synapse Analytics のコストを、グラフと表で表示できます。 たとえば、日単位、現在の月、以前の月、年単位などがあります。 予算や予想コストを基準としてコストを表示することもできます。 時間経過を示す、より長い期間のビューに切り替えると、支出の傾向を特定するのに役立ちます。 超過出費が発生した可能性のある時期を確認できます。 予算を作成したら、それを超えた場所も簡単に確認できます。

コスト分析で Aure Synapse のコストを表示するには:

1. Azure portal にサインインします。
2. Azure portal で、サブスクリプションまたはリソース グループのスコープを開き、メニューの **[コスト分析]** を選択します。 たとえば、 **[サブスクリプション]** に移動し、一覧からサブスクリプションを選択して、メニューから **[コスト分析]** を選択します。 コスト分析で別のスコープに切り替えるには、 **[スコープ]** を選択します。
3. 既定では、最初のドーナツ グラフにサービスのコストが表示されます。 グラフの [Azure Synapse] というラベルが付いた領域を選択します。

最初にコスト分析を開いたときに、実際の月額コストが表示されます。 月単位のすべての使用コストを示す例を次に示します。

![サブスクリプションの累積コストを示す例](./media/plan-manage-costs/actual-monthly-costs.png)

- Azure Synapse などの 1 つのサービスのコストに絞り込むには、 **[フィルターの追加]** を選択してから、 **[サービス名]** を選択します。 次に、 **[Azure Synapse Analytics]** を選択します。

Azure Synapse のみのコストの表示例を次に示します。

![ServiceName の累積コストを示す例](./media/plan-manage-costs/filtered-monthly-costs.png)

前の例では、サービスの現在のコストが表示されます。 Azure リージョン (場所) 別およびリソース グループ別の Azure Synapse のコストも表示されます。 ここでは、コストを自分で調べることができます。

## <a name="create-budgets"></a>予算を作成する

[予算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成して、コストを管理し、異常な支出や浪費のリスクについて、関係者に自動的に通知する[アラート](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 

監視の粒度をさらに細かく示す必要がある場合は、Azure の特定のリソースまたはサービスに対するフィルターを使用して予算を作成できます。 フィルターを使用すると、追加のコストがかかる新しいリソースが誤って作成されないようにすることができます。 予算を作成するときのフィルター オプションの詳細については、[グループとフィルターのオプション](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関する記事を参照してください。

## <a name="export-cost-data"></a>コスト データのエクスポート

また、ストレージ アカウントに[コスト データをエクスポート](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)することもできます。 これは、自分や他のユーザーがコストに関する追加のデータ分析を行う必要がある場合に便利です。 たとえば、財務チームは、Excel や Power BI を使用してデータを分析できます。 日単位、週単位、または月単位のスケジュールでコストをエクスポートし、カスタムの日付範囲を設定することができます。 コスト データのエクスポートは、推奨されるコスト データセット取得方法です。


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Azure Synapse のコストを管理して削減するその他の方法 

### <a name="serverless-sql-pool"></a>サーバーレス SQL プール

サーバーレス SQL プールのコストの詳細については、「[Azure Synapse Analytics でのサーバーレス SQL プールのコスト管理](./sql/data-processed.md)」を参照してください。

### <a name="dedicated-sql-pool"></a>専用 SQL プール

リソースが使用されていないときに一時停止することで、専用 SQL プールのコストを制御できます。 たとえば、夜間と週末にデータベースを使用しない場合、その期間にデータベースを一時停止して、日中に再開することができます。 詳細については、「[Azure portal を使用して専用 SQL プールのコンピューティングを一時停止および再開する](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)」を参照してください。

### <a name="serverless-apache-spark-pool"></a>サーバーレス Apache Spark プール

サーバーレス Apache Spark プールのコストを制御するには、サーバーレス Apache Spark 自動一時停止機能を有効にし、必要に応じてタイムアウト値を設定します。  開発に Synapse Studio を使用する場合、セッションをアクティブなままにするために、Studio によりキープ アライブ メッセージが送信されます。これも構成可能なため、自動一時停止の短いタイムアウト値を設定します。  終了すると、セッションが閉じ、タイムアウト値に達すると Apache Spark プールが自動的に一時停止します。
 
開発時には、さまざまなサイズの複数の Apache Spark プール定義を作成します。  Apache Spark プール定義の作成は無料であり、使用量に対してのみ課金されます。  Azure Synapse での Apache Spark 使用量は、仮想コア時間単位で課金され、分単位で比例配分されます。  たとえば、コードの開発と検証には小さいプール サイズを使用し、パフォーマンス テストにはより大きなプール サイズを使用します。


### <a name="data-integration---pipelines-and-data-flows"></a>データ統合 - パイプラインとデータ フロー 

データ統合のコストの詳細については、「[Azure Data Factory のコストを計画および管理する](../data-factory/plan-manage-costs.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Cost Management を使用してクラウドへの投資を最適化する](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)方法について説明します。
- [コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を使用してコストを管理する方法について詳細に説明します。
- [予期しないコストを回避](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)する方法について説明します。
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) のガイド付き学習コースを受講します。
- [Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md) のコストの計画と管理について確認します。