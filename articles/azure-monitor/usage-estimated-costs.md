---
title: Azure Monitor での使用量と推定コストの監視
description: Azure Monitor の [使用量と推定コスト] ページの利用プロセスの概要
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lagayhar
ms.reviewer: Dale.Koetke
ms.openlocfilehash: c4aded73334e38539e1c671831fe812a9525698c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048677"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Azure Monitor での使用量と推定コストの監視

> [!NOTE]
> この記事では、複数の Azure 監視機能全体の使用量と推定コストを表示する方法について説明します。 Azure Monitor の特定のコンポーネントに関連する記事は次のとおりです。
> - 「[Azure Monitor ログで使用量とコストを管理する](logs/manage-cost-storage.md)」では、データ保持期間を変更してコストを管理する方法、およびデータの使用状況を分析してアラートを作成する方法について説明します。
> - 「[Application Insights の使用量とコストを管理する](app/pricing.md)」では、Application Insights でデータ使用状況を分析する方法について説明します。

## <a name="azure-monitor-pricing-model"></a>Azure Monitor の価格モデル

基本的な Azure Monitor 課金モデルは、クラウドに適した使用量ベースの価格設定 (従量課金制) です。 支払いは使用した分だけで済みます。 価格の詳細は、[アラート、メトリック、通知](https://azure.microsoft.com/pricing/details/monitor/)、[Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)、および [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) で確認できます。 

ログ データの従量課金制モデルに加えて、Log Analytics には容量予約があります。これにより、従量課金制の料金と比較して 25% も節約できます。 容量予約の価格を選択すると、1 日あたり 100 GB から予約を購入できます。 予約レベルを超える使用量は、従量課金制で請求されます。 容量予約の価格の詳細については、[こちら](https://azure.microsoft.com/pricing/details/monitor/)を参照してください。

一部のお客様は、[従来の Log Analytics 価格レベル](logs/manage-cost-storage.md#legacy-pricing-tiers)および[従来の Enterprise Application Insights 価格レベル](app/pricing.md#legacy-enterprise-per-node-pricing-tier)を利用できます。 

## <a name="understanding-your-azure-monitor-costs"></a>Azure Monitor のコストの把握

コストの把握には、2 つのフェーズがあります。 1 つ目は、Azure Monitor を監視ソリューションとして考える場合です。 

### <a name="estimating-the-costs-to-manage-your-environment"></a>ご利用の環境を管理するためのコストの見積もり

Azure Monitor ログをまだ使用していない場合は、[Azure Monitor 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=monitor)を使用して、Azure Monitor を使用する場合のコストを見積もることができます。 まず、[検索] ボックスに「Azure Monitor」と入力し、結果として表示される Azure Monitor タイルをクリックします。 ページを下へスクロールして Azure Monitor に移動し、[種類] ドロップダウンでいずれかのオプションを選択します。

- メトリック クエリとアラート  
- Log Analytics
- Application Insights

これらのそれぞれで、料金計算ツールは、予想される使用量に基づいてコストを見積もるのに役立ちます。

たとえば、Log Analytics では、VM の数と、各 VM から収集すると予想されるデータ量 (GB) を入力できます。 通常、1 GB から 3 GB のデータ月が、一般的な Azure VM から取り込まれています。 既に Azure Monitor ログを評価している場合は、独自の環境からのデータ統計を使用することができます。 [監視対象の VM の数](logs/manage-cost-storage.md#understanding-nodes-sending-data)と[ご利用のワークスペースによって取り込まれているデータ量](logs/manage-cost-storage.md#understanding-ingested-data-volume)を特定する方法については、以下を参照してください。

Application Insights でも同様に、"アプリケーションのアクティビティに基づいてデータ量を見積もる" 機能を有効にした場合、アプリケーションに関する情報を (クライアント側テレメトリを収集する場合、1 か月あたりの要求数とページ ビュー数) を入力すると、類似アプリケーションによって収集されたデータ量の中央値と 90 パーセンタイル量が表示されます。 これらのアプリケーションでの Application Insights の構成は範囲が広いので (既定のサンプリングを使用しているものや、サンプルを使用していないものなど)、サンプリングを使用して、取り込まれるデータの量が中央値のレベルよりはるかに少なくなるように制御できます。 しかし、これは、他の似た顧客が行っていることを理解するための出発点となります。 Application Insights のコストの見積もりについて、[さらに学習](app/pricing.md#estimating-the-costs-to-manage-your-application)してください。

### <a name="understanding-your-usage-and-estimated-costs"></a>使用量と推定コストの把握

Azure Monitor を使用して、使用量を理解し、追跡することが重要です。これを容易にする豊富なツールが用意されています。 

Azure では、[Azure Cost Management と課金](../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json)ハブに便利な機能が多数用意されています。 **[Azure Cost Management + Billing]** ハブを開いた後、**[コスト管理]** をクリックし、[[スコープ]](../cost-management-billing/costs/understand-work-scopes.md) (調査対象のリソースのセット) を選択します。 

次に、過去 30 日間の Azure Monitor コストを確認するには、**[1 日あたりのコスト]** タイルをクリックし、[相対日付] で [過去 30 日間] を選択し、サービス名を選択するフィルターを追加します。

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. 洞察と分析

この結果、次のようなビューが表示されるようになります。

![Azure Cost Management のスクリーンショット](./media/usage-estimated-costs/010.png)

ここで、この累積コストの概要からドリルダウンして、[リソースごとのコスト] ビューで詳細を表示できます。 現在の価格レベルでは、Azure Log データは Log Analytics からのものか Application Insights からのものであるかにかかわらず、同じメーターのセットに対して課金されます。 Log Analytics の使用量と Application Insights の使用量のコストを区別するには、**[リソースの種類]** に対してフィルターを追加します。 すべての Application Insights コストを表示するには、[リソースの種類] を "microsoft.insights/components" でフィルター処理します。Log Analytics コストの場合は、[リソースの種類] を "microsoft.operationalinsights/workspaces" でフィルター処理します。 

使用量の詳細は、[Azure portal で使用量をダウンロード](../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal)して確認できます。 ダウンロードしたスプレッドシートでは、Azure リソースごとに、1 日あたりの使用量を確認できます。 この Excel スプレッドシートでは、Application Insights のリソースからの使用量を検索することができます。それには、まず、[測定カテゴリ] 列でフィルター処理を行って "Application Insights" と "Log Analytics" を表示し、次に [インスタンス ID] 列に対するフィルター ("contains microsoft.insights/components") を追加します。  すべての Azure Monitor コンポーネントに対して 1 つのログ バックエンドがあるため、ほとんどの Application Insights の使用量は、メーターでは Log Analytics の測定カテゴリで報告されます。  Application Insights の測定カテゴリで報告されるのは、従来の価格レベルおよび複数ステップ Web テストでの Application Insights リソースのみです。  使用量は "消費量" 列に表示され、各エントリの単位は "測定単位" 列に表示されます。  詳細については、「[Microsoft Azure の課金内容を確認する](../cost-management-billing/understand/review-individual-bill.md)」を参照してください。 

> [!NOTE]
> **Azure Cost Management と課金** ハブの **[コスト管理]** を使用することは、監視コストを幅広く理解するための推奨アプローチです。  [Log Analytics](logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs) および [Application Insights](app/pricing.md#understand-your-usage-and-estimate-costs) の **[使用量と推定コスト]** エクスペリエンスでは、Azure Monitor のこれらの各部分のより深い分析情報が提供されます。

Azure Monitor の使用量を表示するためのもう 1 つのオプションは、[監視] ハブの **[使用量と推定コスト]** ページです。 このページには、[アラート、メトリック、通知](https://azure.microsoft.com/pricing/details/monitor/)、[Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)、[Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) などの中心的な監視機能の使用量が表示されます。 2018 年 4 月より前の価格プランを利用しているお客様の場合は、Insights と Analytics 経由で購入した Log Analytics の使用量も含まれています。

ユーザーは、サブスクリプションごとに集計された過去 31 日間のリソース使用量をこのページで確認できます。 `Drill-ins` には、その 31 日間の使用傾向が表示されます。 この推定を行うためには大量のデータを集計する必要があるため、ページが完全に読み込まれるまでしばらくお待ちください。

監視された使用量とその推定コストの表示例を次に示します。

![使用量と推定コストを表示しているポータルのスクリーンショット](./media/usage-estimated-costs/001.png)

[月間使用量] 列にあるリンクをクリックすると、過去 31 日間にわたる使用量の傾向を示すグラフが表示されます。 

![[Included per node]\(ノードごとに含まれる\) の棒グラフのスクリーンショット](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Operations Management Suite のサブスクリプション エンタイトルメント

Microsoft Operations Management Suite E1 および E2 を購入されたお客様は、[Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) と [Application Insights](app/pricing.md) でのノード単位のデータ インジェストが可能です。 特定のサブスクリプションで Log Analytics ワークスペースまたは Application Insights リソースの権利を得るには: 

- Log Analytics ワークスペースは、"ノードごとの (OMS)" 価格レベルを使用してください。
- Application Insights リソースは、"Enterprise" 価格レベルを使用する必要があります。

お客様の組織が購入されたスイートのノード数によっては、一部のサブスクリプションを従量課金制 (GB あたり) 価格レベルへ移行した方が有益な場合がありますが、移行の前に慎重に検討するようにしてください。

> [!WARNING]
> 組織が現在の Microsoft Operations Management Suite E1 および E2 を持っている場合は、通常、Log Analytics のワークスペースを "ノードごと (OMS)" 価格レベルで使用し、Application Insights リソースを "Enterprise" 価格レベルで使用することをお勧めします。 
>

