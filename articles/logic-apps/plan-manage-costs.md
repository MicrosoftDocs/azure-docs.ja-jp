---
title: Azure Logic Apps のコストの管理を計画する
description: Azure portal のコスト分析を使用して、Azure Logic Apps のコストを計画および管理する方法を学習します。
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 01/29/2021
ms.openlocfilehash: 44351497ed58c8d49404c094f6800b52186edabb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709633"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Azure Logic Apps のコストを計画および管理する

この記事は、Azure Logic Apps ストレージのコストを計画および管理するのに役立ちます。 このサービスを使用してリソースを作成または追加する前に、Azure 料金計算ツールを使用してコストを見積もってください。 Logic Apps リソースの使用を開始したら、[Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) を使用して予算を設定し、コストを監視できます。 対処した方がよい領域を識別するために、予測コストを確認し、支出の傾向を監視することもできます。

Logic Apps のコストは、Azure の請求にある月額料金の一部でしかないことにご注意ください。 この記事では、Logic Apps のコストを見積もって管理する方法について説明しますが、課金は、Azure サブスクリプションで使用されるすべての Azure サービスとリソース (サードパーティのサービスを含む) に対して行われます。 Logic Apps のコスト管理に慣れたら、同様の方法を適用して、サブスクリプションで使用されているすべての Azure サービスのコストを管理できます。

## <a name="prerequisites"></a>前提条件

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) では、ほとんどの Azure アカウントの種類がサポートされます。 サポートされているすべてのアカウントの種類を確認するには、「[Cost Management のデータを理解する](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。 コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。

Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関するページを参照してください。

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>課金モデルを確認する

Azure Logic Apps は、新しいリソースをデプロイすると[コストが発生する](https://azure.microsoft.com/pricing/details/logic-apps/) Azure インフラストラクチャ上で実行されます。 [Logic Apps サービスおよび関連する Azure リソースの課金モデル](logic-apps-pricing.md)を理解し、デプロイされたリソースに変更を加えたときにこれらの依存関係によって生じるコストを管理してください。

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Azure Logic Apps で通常発生するコスト

Logic Apps サービスでは、作成および使用するリソースに基づいて、さまざまな価格モデルが適用されます。

* マルチテナント Logic Apps サービスで作成して実行するロジック アプリのリソースでは、[従量課金モデル](../logic-apps/logic-apps-pricing.md#consumption-pricing)が使用されます。

* [統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) で作成して実行するロジック アプリのリソースでは、[固定価格モデル](../logic-apps/logic-apps-pricing.md#fixed-pricing)が使用されます。

ロジック アプリで使用するために作成するとコストが発生する、その他のリソースを次に示します。

* [統合アカウント](../logic-apps/logic-apps-pricing.md#integration-accounts)は、B2B 統合を構築するために作成してロジック アプリにリンクする別個のリソースです。 統合アカウントでは [固定価格モデル](../logic-apps/logic-apps-pricing.md#integration-accounts)が使用されます。この場合の料金は、使用する統合アカウントの種類または "*層*" に基づきます。

* [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) は、仮想ネットワーク内のリソースに直接アクセスする必要があるロジック アプリの配置場所として作成する別個のリソースです。 ISE では[固定価格モデル](../logic-apps/logic-apps-pricing.md#fixed-pricing)が使用されます。この場合の料金は、作成した ISE SKU とその他の設定に基づきます。

* [カスタム コネクタ](../logic-apps/logic-apps-pricing.md#consumption-pricing)は、ロジック アプリで使用するコネクタが事前に構築されていない REST API 用に作成する別個のリソースです。 カスタム コネクタの実行では、ISE で使用する場合を除き、[従量課金モデル](../logic-apps/logic-apps-pricing.md#consumption-pricing)が使用されます。

* マルチテナント Logic Apps サービスでは、[データ保持とストレージ消費](../logic-apps/logic-apps-pricing.md#data-retention)によって[固定価格モデル](../logic-apps/logic-apps-pricing.md#fixed-pricing)を使用するコストが発生します。 たとえば、実行履歴からの入力と出力はバックグラウンド ストレージに保持されます。これは、ロジック アプリから独立して作成、管理、アクセスするストレージ リソースとは異なります。

  ISE では、データ保持とストレージ消費によってコストは発生しません。

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>リソースの削除後にコストが発生する可能性がある

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

ロジック アプリを削除すると、Logic Apps サービスによって新しいワークフロー インスタンスの作成や実行は行われません。 ただし、進行中および保留中のすべての実行は完了するまで続けられます。 実行の数により、このプロセスには時間がかかる場合があります。 詳細については、[ロジック アプリの管理](manage-logic-apps-with-azure-portal.md#delete-logic-apps)に関するページを参照してください。

ロジック アプリを削除した後に以下のリソースがある場合、これらのリソースは存在し続け、削除されるまでコストが発生します。

* リソースに接続されるロジック アプリから独立して作成および管理する Azure リソース (Azure 関数アプリ、イベント ハブ、イベント グリッドなど)

* 統合アカウント

* 統合サービス環境 (ISE)

  [ISE を削除](ise-manage-integration-service-environment.md#delete-ise)した場合、関連付けられている Azure 仮想ネットワーク、サブネット、その他の関連リソースは存在し続けます。 ISE を削除した後、仮想ネットワークまたはサブネットの削除を試みることができるようになるまでに、最大で特定の時間数だけ待機しなければならない場合があります。

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Azure Logic Apps で年額クレジットを使用する

Azure Logic Apps の料金は、EA の年額コミットメントのクレジットを使用して支払うことができます。 ただし、EA の年額コミットメントのクレジットを使用して、サードパーティの製品やサービス (Azure Marketplace からのものを含む) の料金を支払うことはできません。

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>コストの見積もり

Azure Logic Apps を使用してリソースを作成する前に、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してコストを見積もります。 詳細については、[Azure Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md)に関するページを確認してください。

1. [Azure 料金計算ツールのページ](https://azure.microsoft.com/pricing/calculator/)の左側のメニューから、 **[統合]**  >  **[Azure Logic Apps]** の順に選択します。

   ![[Azure Logic Apps] が選択されている Azure 料金計算ツールを示すスクリーンショット。](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Azure Logic Apps 料金計算ツールが表示されるまで、ページを下にスクロールします。 Azure Logic Apps に直接関連する Azure リソースのさまざまなセクションに、使用する予定のリソースの数と、それらのリソースを使用する間隔の数を入力します。

   このスクリーンショットでは、計算ツールを使用したコストの見積もりの例を示しています。

   ![Azure 料金計算ツールの推定コストを示す例](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. 新しい関連リソースを作成して使用するときにコストの見積もりを更新するには、この計算ツールに戻り、ここでそれらのリソースを更新します。

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>予算とアラートを作成する

Azure アカウントまたはサブスクリプションのコストを事前に管理できるように、[Azure Cost Management and Billing](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) サービスと機能を使用して[予算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)と[アラート](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成できます。  予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。

予算とコストのしきい値と比較した支出に基づき、アラートによって自動的に、支出の異常と支出超過のリスクが関係者に通知されます。 監視の粒度をさらに細かくする必要がある場合は、Azure の特定のリソースまたはサービスに対するフィルターを使用した予算を作成することもできます。 フィルターを使用すると、追加のコストがかかる新しいリソースが誤って作成されないようにすることができます。 フィルターのオプションの詳細については、[グループとフィルターのオプション](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関するページを参照してください。

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>コストを監視する

リソース使用のユニット コストは、期間 (秒、分、時間、日数など) やユニット使用量 (バイト、メガバイトなど) によって異なります。 たとえば、日単位、現在の月、以前の月、年単位などがあります。 時間経過を示す、より長い期間のビューに切り替えると、支出の傾向を特定するのに役立ちます。 コスト分析機能を使用すると、さまざまな期間にわたるコストをグラフやテーブルとして表示できます。 予算とコスト予測を作成した場合は、予算を超過し、支出超過が発生した可能性がある箇所を簡単に見つけることもできます。

Azure で作成または使用開始したリソースに対するコストの発生が始まったら、こちらの方法でそれらのコストを確認および監視できます。

* Azure Monitor を使用して、[ロジック アプリの実行とストレージの消費を監視する](#monitor-billing-metrics)

* [Azure Cost Management and Billing](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) を使用して[コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を実行する

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>ロジック アプリの実行とストレージの消費を監視する

Azure Monitor を使用すると、特定のロジック アプリの、これらのメトリックを表示できます。

* 課金対象のアクションの実行
* 課金対象のトリガーの実行
* ネイティブ操作実行の利用状況に応じた課金
* Standard コネクタ実行の利用状況に応じた課金
* ストレージ消費の利用状況に応じた課金
* 課金対象の実行の合計数

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>実行とストレージ消費のメトリックを表示する

1. Azure Portal で、ご利用のロジック アプリを探して選択します。 ロジック アプリのメニューで、 **[監視]** から **[メトリック]** を選択します。

1. 右側のウィンドウのメトリック バーにある **[グラフのタイトル]** の下で、 **[メトリック]** の一覧を開き、目的のメトリックを選択します。

   > [!NOTE]
   > ストレージの消費は、ロジック アプリによって使用されるストレージ ユニット (GB) の数として測定され、課金されます。 ストレージの使用量が 500 MB 未満の実行は、[監視] ビューに表示されない場合がありますが、課金の対象です。

   ![[メトリック] の一覧が開かれた [メトリック] ウィンドウを示すスクリーンショット。](./media/logic-apps-pricing/select-metric.png)

1. ウィンドウの右上隅で、必要な期間を選択します。

1. その他のストレージ消費データ (具体的には、ロジック アプリの実行履歴のアクションの入力と出力のサイズ) を表示するには、[こちらの手順に従います](#view-input-output-sizes)。

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>実行履歴のアクションの入力と出力のサイズを表示する

1. Azure Portal で、ご利用のロジック アプリを探して選択します。

1. ロジック アプリのメニューで、 **[概要]** を選択します。

1. 右側のウィンドウで、 **[実行履歴]** から、入力と出力を表示する実行を選択します。

1. **[ロジック アプリの実行]** で、 **[実行の詳細]** を選択します。

1. **[ロジック アプリの実行の詳細]** ウィンドウで、各アクションの状態と持続時間を一覧表示しているアクション テーブルから、表示するアクションを選択します。

1. **[ロジック アプリのアクション]** ウィンドウで、アクションの入力と出力のサイズを確認します。 **[入力リンク]** と **[出力リンク]** で、それらの入力と出力へのリンクを確認します。

   > [!NOTE]
   > ループについては、上位レベルのアクションによってのみ、入力と出力のサイズが表示されます。 入れ子になったループ内のアクションについては、入力と出力のサイズはゼロでリンクは表示されません。

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Azure Cost Management and Billing を使用してコスト分析を実行する

Azure サブスクリプションなど、特定のスコープに基づいて Logic Apps サービスのコストを確認する場合は、[Azure Cost Management and Billing](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) の[コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)機能を使用できます。

1. Azure portal で、Azure サブスクリプションなど、目的のスコープを開きます。 左側のメニューの **[コスト管理]** で、 **[コスト分析]** を選択します。

   最初に [コスト分析] ウィンドウを開くと、上部のグラフには、サブスクリプションに含まれるサービス全体の、当月の実績と予測の使用コストが表示されます。

   ![Azure portal と [コスト分析] ウィンドウを示すスクリーンショット。サブスクリプションの実績と予測のコストの例が表示されている。](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > スコープを変更するには、 **[コスト分析]** ウィンドウのフィルター バーで、 **[スコープ]** フィルターを選択します。 **[スコープの選択]** ウィンドウで、目的のスコープに切り替えます。

   下部のドーナツ グラフには、Azure サービス別、Azure リージョン (場所) 別、リソース グループ別の現在のコストが示されています。

   ![Azure portal と [コスト分析] ウィンドウを示すスクリーンショット。サービス、リージョン、リソース グループ別のドーナツ グラフの例が表示されている。](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. サービスやリソースなど、特定の領域にグラフをフィルター処理するには、フィルター バーで **[フィルターの追加]** を選択します。

1. 左側の一覧でフィルターの種類 (たとえば、 **[サービス名]** ) を選択します。 右側の一覧でフィルター (たとえば、 **[ロジック アプリ]** ) を選択します。 完了したら、緑色のチェック マークを選択します。

   ![Azure portal と [コスト分析] ウィンドウを示すスクリーンショット。フィルターの選択が示されている。](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   たとえば、Logic Apps サービスの結果を次に示します。

   ![Azure portal と [コスト分析] ウィンドウを示すスクリーンショット。[ロジック アプリ] でフィルター処理された結果が表示されている。](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>コスト データのエクスポート

コストに関して追加のデータ分析を行う必要がある場合は、[コスト データをストレージ アカウントにエクスポート](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)することができます。 たとえば、財務チームが Excel や Power BI を使用してこのデータを分析できます。 日単位、週単位、または月単位のスケジュールでコストをエクスポートし、カスタムの日付範囲を設定することができます。 コスト データのエクスポートは、推奨されるコスト データセット取得方法です。

## <a name="other-ways-to-manage-and-reduce-costs"></a>コストを管理および削減するその他の方法

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

ロジック アプリと関連リソースのコストを削減できるように、こちらのオプションを試してください。

* 可能であれば、[組み込みのトリガーとアクション](../connectors/apis-list.md#built-in)を使用します。これにより、[マネージド コネクタのトリガーとアクション](../connectors/apis-list.md#managed-connectors)よりも実行ごとの実行コストが少なくなります。

  たとえば、[HTTP アクション](../connectors/connectors-native-http.md)を使用することによって、または [Azure Functions サービス](../azure-functions/functions-overview.md)を使用し、さらに[組み込みの Azure Functions アクション](../logic-apps/logic-apps-azure-functions.md)を使用して作成した関数を呼び出すことによって他のリソースにアクセスする場合、コストを削減できる可能性があります。 ただし、Azure Functions を使用することでもコストが発生するため、オプションを比較する必要があります。

* ワークフローを実行するための[正確なトリガー条件を指定](logic-apps-workflow-actions-triggers.md#trigger-conditions)します。

  たとえば、ターゲット Web サイトから内部サーバー エラーが返されたときに限ってトリガーが起動するように指定できます。 トリガーの JSON 定義で、`conditions` プロパティを使用して、トリガーの状態コードを参照する条件を指定します。

* トリガーにポーリング バージョンと Webhook バージョンがある場合は、Webhook バージョンを試してください。このバージョンでは、イベントを定期的にチェックするのではなく、指定されたイベントが発生するのを待ってから起動します。

* 別のサービスを介してロジック アプリを呼び出して、ワークフローの実行時にのみトリガーが起動されるようにします。

  たとえば、Azure Functions サービスを使用して作成して実行する関数からロジック アプリを呼び出すことができます。 例として、「[Azure Functions と Azure Service Bus を使用してロジック アプリを呼び出すか、またはトリガーする](logic-apps-scenario-function-sb-trigger.md)」を参照してください。

* 常に実行している必要のない[ロジック アプリを無効にする](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps)か、不要になった[ロジック アプリを削除](manage-logic-apps-with-azure-portal.md#delete-logic-apps)します。 可能であれば、常にアクティブにしておく必要はない他のリソースをすべて無効にしてください。

## <a name="next-steps"></a>次のステップ

* [Azure Cost Management でクラウド投資を最適化する](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [コスト分析を使用してコストを管理する](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [予想外のコストを防ぐ](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) のガイド付き学習コースを受講する