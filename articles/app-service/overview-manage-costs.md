---
title: App Service のコスト管理を計画する
description: Azure portal でコスト分析を使用して、Azure App Service のコストを計画および管理する方法を学習します。
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: ada4c1991a57c8252247c9617e097dc82cb3b4a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593986"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>Azure App Service のコストを計画および管理する

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

この記事では、Azure App Service のコストを計画および管理する方法について説明します。 コストを見積もるサービスのリソースを追加する前に、まず、App Service のコストの計画に役立つ Azure 料金計算ツールを使用します。 次に、Azure リソースを追加するときに、推定コストを確認します。 App Service リソースの使用を開始した後、[Cost Management](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 機能を使用して、予算の設定とコストの監視を行います。 また、予想コストを確認し、支出の傾向を特定して、対処が必要な領域を特定することもできます。Azure App Service のコストは、Azure の請求に記載された月額料金の一部にすぎません。 この記事では、App Service のコストを計画し、管理する方法について説明しますが、サードパーティのサービスを含め、Azure サブスクリプションで使用されるすべての Azure サービスとリソースに対して課金されます。

## <a name="relevant-costs-for-app-service"></a>App Service の関連コスト

App Service は Azure インフラストラクチャ上で実行され、コストが発生します。 追加のインフラストラクチャでコストが発生する可能性があることを理解しておくことが重要です。 デプロイされたリソースに変更を加える場合は、このコストを管理する必要があります。

### <a name="costs-that-accrue-with-azure-app-service"></a>Azure App Service で発生するコスト

App Service で使用する機能に応じて、コストが発生する次のリソースが作成される場合があります。

- **App Service プラン**  App Service アプリをホストするために必要です。
- **Isolated レベル**  App Service Environment には、[Virtual Network](../virtual-network/index.yml) が必要です。
- **バックアップ**  バックアップを作成するには、[ストレージ アカウント](../storage/index.yml)が必要です。
- **診断ログ**  ログ オプションとして [ストレージ アカウント](../storage/index.yml)を選択するか、[Azure Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) と統合します。
- **App Service 証明書**  Azure で購入する証明書は、[Azure Key Vault](../key-vault/index.yml) で保持する必要があります。

App Service のその他のコスト リソースは次のとおりです (詳細については、「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」を参照してください)。

- [App Service ドメイン](manage-custom-dns-buy-domain.md)  自動更新を有効にした場合、サブスクリプションはドメイン登録に対して年単位で課金されます。
- [App Service 証明書](configure-ssl-certificate.md#import-an-app-service-certificate)  購入時の 1 回限りの料金。 保護するサブドメインが複数ある場合は、複数の標準の証明書の代わりに 1 つのワイルドカードの証明書を購入することにより、コストを削減できます。
- [IP ベースの証明書のバインド](configure-ssl-bindings.md#create-binding)  バインディングは、アプリ レベルで証明書に対して構成されます。 バインディングごとにコストが発生します。 **Standard** レベル以上では、最初の IP ベースのバインディングには課金されません。

### <a name="costs-that-might-accrue-after-resource-deletion"></a>リソースの削除後に発生する可能性があるコスト

App Service プラン内のすべてのアプリを削除しても、プランの料金は、構成された価格レベルとインスタンスの数に基づいて引き続き発生します。 不要な料金を回避するには、プランを削除するか、**Free** レベルにスケール ダウンします。

Azure App Service リソースを削除した後、関連する Azure サービスのリソースは引き続き存在する可能性があります。 これらを削除するまで、これらのコストは発生し続けます。 以下に例を示します。

- **Isolated** レベルの App Service プラン用に作成した Virtual Network
- バックアップまたは診断ログを格納するために作成したストレージ アカウント
- App Service 証明書を格納するために作成した Key Vault
- 診断ログを送信するために作成した Log Analytic 名前空間
- まだ有効期限が切れていない App Service の[インスタンスまたはスタンプ予約](#azure-reservations)

### <a name="using-monetary-credit-with-azure-app-service"></a>Azure App Service で金額クレジットを使用する

Azure App Service の料金は、Azure 前払い (旧称: 年額コミットメント) のクレジットを使用して支払うことができます。 ただし、Azure 前払いのクレジットを使用して、サードパーティの製品やサービス (Azure Marketplace からのものを含む) の料金を支払うことはできません。

## <a name="estimate-costs"></a>コストの見積もり

App Service のコストを事前に見積もり、最適化する簡単な方法は、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用することです。

料金計算ツールを使用するには、 **[製品]** タブで **[App Service]** をクリックします。次に、下にスクロールして計算ツールを操作します。 次のスクリーンショットは一例であり、現在の価格は反映されていません。

![Azure 料金計算ツールの推定コストを示す例](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>Azure portal で推定料金を検討する

App Service アプリまたは App Service プランを作成すると、推定コストが表示されます。

アプリを作成し、見積もり価格を表示するには、次の手順を実行します。

1. [作成] ページで、 **[App Service プラン]** まで下にスクロールして、 **[新規作成]** をクリックします。
1. 名前を指定し、 **[OK]** をクリックします。
1. **[SKU とサイズ]** の横にある **[サイズの変更]** をクリックします。
1. 概要に表示される推定料金を確認します。 次のスクリーンショットは一例であり、現在の価格は反映されていません。

    ![portal で各価格レベルの推定コストを確認する](media/overview-manage-costs/pricing-estimates.png)

Azure サブスクリプションに使用制限がある場合は、Azure により、クレジット額を超える支出が防止されます。 Azure リソースを作成して使用するときに、クレジットが使用されます。 クレジットの上限に達すると、その請求期間の残りの期間は、デプロイしたリソースが無効にされます。 クレジットの上限は変更できませんが、上限を取り除くことは可能です。 使用制限の詳細については、「[Azure の使用制限](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。

## <a name="optimize-costs"></a>コストを最適化する

Basic レベルでは、App Service アプリは、それらをホストする App Service プランによって課金されます。 App Service のデプロイに伴うコストは、いくつかの主な要因によって異なります。

- **価格レベル**  App Service プランの SKU とも呼ばれます。 レベルが高いほど、より多くの CPU コア、メモリ、ストレージ、機能、またはそれらの組み合わせが提供されます。
- **インスタンス数**  Dedicated レベル (Basic 以上) はスケールアウトでき、スケールアウトされたインスタンスごとにコストが発生します。
- **スタンプ料金**  Isolated レベルでは、ホストされているアプリまたは worker のインスタンスの数に関係なく、App Service Environment に対して一律料金が発生します。

App Service プランでは、複数のアプリをホストできます。 デプロイに応じて、1 つの App Service プランでホストするアプリの数を増やす (つまり、アプリをホストする App Service プランを減らす) ことにより、コストを節約できます。

詳細については、「[Azure App Service プランの概要](overview-hosting-plans.md)」を参照してください

### <a name="non-production-workloads"></a>非運用ワークロード

発生するコストを低くまたは最小限に抑えて App Service またはソリューションをテストするには、2 つのエントリレベルの価格レベル (**Free** および **Shared**) を使用することから始めます。これらは、共有インスタンスでホストされます。 専用インスタンスでパフォーマンスを向上させてアプリをテストするには、**Basic** レベルにアップグレードできます。これは、Windows と Linux の両方のアプリをサポートします。 

> [!NOTE]
> **Azure Dev/Test 価格**  より高いレベル (**Isolated** を除くすべてのレベル) を必要とする運用前のワークロードをテストするために、Visual Studio サブスクリプション ユーザーは大幅な割引を提供する [Azure Dev/Test 価格](https://azure.microsoft.com/pricing/dev-test/)を利用することもできます。
>
> **Free** と **Shared** の両方のレベル、および Azure Dev/Test 価格の割引には、返金制度を備えた SLA は設定されていません。

### <a name="production-workloads"></a>運用ワークロード

運用ワークロードには、専用の **Standard** 価格レベル以上が推奨されます。 レベルが高くなると価格も高くなりますが、メモリとストレージが増加し、ハードウェアのパフォーマンスが向上するため、コンピューティング インスタンスあたりのアプリ密度が高くなります。 これは、同じ数のアプリに対するインスタンス数が少なくなるため、コストが削減されることを意味します。 実際、**Premium V3**(**Isolated** レベル以外で最上位のレベル) は、大規模にアプリにサービスを提供するためのコスト効率の最も高い方法です。 節約額を増やすために、[Premium V3 の予約](#azure-reservations)で大幅な割引を受けることができます。

> [!NOTE]
> **Premium V3** では、Windows コンテナーと Linux コンテナーの両方がサポートされます。 

希望する価格レベルを選択したら、アイドル状態のインスタンスを最小限に抑える必要があります。 スケールアウト配置では、使用率が低いコンピューティング インスタンスで無駄なコストが発生する可能性があります。 **Standard** レベル以上で利用可能な [自動スケールを構成する](../azure-monitor/autoscale/autoscale-get-started.md)必要があります。 スケールアウト スケジュールおよびメトリックベースのスケールアウト ルールを作成すると、いつでも、料金は実際に必要なインスタンスに対してのみ発生します。

### <a name="azure-reservations"></a>Azure の予約

1 年以上にわたって既知の必要最小限のコンピューティング インスタンスを利用する予定の場合、**Premium V3** レベルを利用し、それらのインスタンスを 1 年または 3 年単位で予約することにより、インスタンスのコストが大幅に削減されます。 毎月、インスタンスあたり最大 55% のコストを削減できます。 2 種類の予約を利用できます。

- **Windows (またはプラットフォーム依存しない)**  サブスクリプション内の Windows または Linux インスタンスに適用できます。
- **Linux 固有**  サブスクリプション内の Linux インスタンスにのみ適用できます。

予約インスタンスの価格は、最大で予約したインスタンス数まで、サブスクリプション内の適用可能なインスタンスに適用されます。 予約インスタンスは請求に関するものであり、特定のコンピューティング インスタンスに関連付けられません。 予約期間中はいつでも、実行したインスタンス数が予約数よりも少ない場合でも、予約インスタンスの料金を支払うことになります。 予約期間中はいつでも、実行したインスタンス数が予約数よりも多い場合、追加インスタンスに対して通常の発生料金を支払うことになります。

**Isolated** レベル (App Service Environment) でも、割引価格の 1 年間および 3 年間の予約がサポートされます。 詳細については、[Azure App Service に予約割引を適用する方法](../cost-management-billing/reservations/reservation-discount-app-service.md)に関する記事を参照してください。

## <a name="monitor-costs"></a>コストを監視する

App Service で Azure リソースを使用する場合、コストが発生します。 Azure リソースの使用のユニット コストは、期間 (秒、分、時間、日) によって異なります。 App Service の使用が開始されるとすぐにコストが発生し、[コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)でコストを確認できます。

コスト分析を使用すると、さまざまな期間について、App Service のコストをグラフや表で表示できます。 たとえば、日単位、現在の月、以前の月、年単位などがあります。 予算や予想コストを基準としてコストを表示することもできます。 時間経過を示す、より長い期間のビューに切り替えると、支出の傾向を特定するのに役立ちます。 超過出費が発生した可能性のある時期を確認できます。 予算を作成したら、それを超えた場所も簡単に確認できます。
    
コスト分析で App Service のコストを表示するには、次の手順を実行します。

1. Azure portal にサインインします。
2. Azure portal でスコープを開き、メニューから **[コスト分析]** を選択します。 たとえば、 **[サブスクリプション]** に移動し、一覧からサブスクリプションを選択して、メニューから **[コスト分析]** を選択します。 コスト分析で別のスコープに切り替えるには、 **[スコープ]** を選択します。
3. 既定では、最初のドーナツ グラフにサービスのコストが表示されます。 グラフの [App Service] というラベルが付いた領域を選択します。

最初にコスト分析を開いたときに、実際の月額コストが表示されます。 月単位のすべての使用コストを示す例を次に示します。

![サブスクリプションの累積コストを示す例](media/overview-manage-costs/all-costs.png)

App Service などの 1 つのサービスのコストに絞り込むには、 **[フィルターの追加]** を選択してから、 **[サービス名]** を選択します。 次に、 **[App Service]** を選択します。

App Service のみのコストを表示する例を次に示します。

![ServiceName の累積コストを示す例](media/overview-manage-costs/service-specific-costs.png)

前の例では、サービスの現在のコストが表示されます。 Azure リージョン (場所) 別およびリソース グループ別の App Service のコストも表示されます。 ここでは、コストを自分で調べることができます。

## <a name="create-budgets"></a>予算を作成する

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

[予算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成して、コストを管理し、異常な支出や浪費のリスクについて、関係者に自動的に通知する[アラート](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 

監視の粒度をさらに細かく示す必要がある場合は、Azure の特定のリソースまたはサービスに対するフィルターを使用して予算を作成できます。 フィルターを使用すると、追加のコストがかかる新しいリソースが誤って作成されないようにすることができます。 予算を作成するときに使用可能なフィルター オプションの詳細については、[グループとフィルターのオプション](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関する記事を参照してください。

## <a name="export-cost-data"></a>コスト データのエクスポート

また、ストレージ アカウントに[コスト データをエクスポート](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)することもできます。 これは、自分や他のユーザーがコストに関する追加のデータ分析を行う必要がある場合に便利です。 たとえば、財務チームは、Excel や Power BI を使用してデータを分析できます。 日単位、週単位、または月単位のスケジュールでコストをエクスポートし、カスタムの日付範囲を設定することができます。 コスト データのエクスポートは、推奨されるコスト データセット取得方法です。

## <a name="next-steps"></a>次のステップ

- Azure Storage での価格のしくみについて詳しく説明します。 「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」をご覧ください。
- [Azure Cost Management を使用してクラウドへの投資を最適化する](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)方法について説明します。
- [コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を使用してコストを管理する方法について詳細に説明します。
- [予期しないコストを回避](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)する方法について説明します。
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) のガイド付き学習コースを受講します。

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->
