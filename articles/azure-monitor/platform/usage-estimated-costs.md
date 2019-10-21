---
title: Azure Monitor での使用量と推定コストの監視
description: Azure Monitor の [使用量と推定コスト] ページの利用プロセスの概要
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 787618b59cd18dd4c38892ddf0861808211671cb
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936618"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Azure Monitor での使用量と推定コストの監視

> [!NOTE]
> この記事では、Azure の異なる価格モデルの複数の監視機能全体の使用量と推定コストを表示する方法について説明します。 Azure Monitor の特定のコンポーネントに関連する記事は次のとおりです。
> - 「[Azure Monitor ログで使用量とコストを管理する](manage-cost-storage.md)」では、データ保持期間を変更してコストを管理する方法、およびデータの使用状況を分析してアラートを作成する方法について説明します。
> - 「[Application Insights の使用量とコストを管理する](../../azure-monitor/app/pricing.md)」では、Application Insights でデータ使用状況を分析する方法について説明します。

Azure Portal の [監視] ハブの **[使用量と推定コスト]** ページには、[アラート、メトリック、通知](https://azure.microsoft.com/pricing/details/monitor/)[Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)、[Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) などの中心的な監視機能の使用量が説明されています。 2018 年 4 月より前の価格プランを利用しているお客様の場合は、Insights と Analytics 経由で購入した Log Analytics の使用量も含まれています。

ユーザーは、サブスクリプションごとに集計された過去 31 日間のリソース使用量をこのページで確認できます。 ドリルインには、その 31 日間の使用傾向が表示されます。 この推定を行うためには大量のデータを集計する必要があるため、ページが完全に読み込まれるまでしばらくお待ちください。

監視された使用量とその推定コストの表示例を次に示します。

![使用量と推定コストを表示しているポータルのスクリーンショット](./media/usage-estimated-costs/001.png)

[月間使用量] 列にあるリンクをクリックすると、過去 31 日間にわたる使用量の傾向を示すグラフが表示されます。

![[Included per node]\(ノードごとに含まれる\) の棒グラフのスクリーンショット](./media/usage-estimated-costs/002.png)

次に示すのは同じような使用量とコストの概要です。 この例は 2018 年 4 月から適用される新しい使用量ベースの価格モデルを使用しているサブスクリプションを示しています。 ノード ベースの課金がなくなっていることに注意してください。 Log Analytics と Application Insights のデータの取り込みと保持が新しい共通メーターで報告されるようになりました。

![使用量と推定コストを表示しているポータルのスクリーンショット - 2018 年 4 月の価格](./media/usage-estimated-costs/003.png)

## <a name="pricing-model"></a>価格モデル

2018 年 4 月に[新しい監視の価格モデルがリリースされました](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)。  これは、クラウドに適した使用量に基づく料金 (従量課金制) であることを特徴としています。 ノード ベースに支払うのではなく、使用した分だけ支払います。 新しい価格モデルは、[アラート、メトリック、通知](https://azure.microsoft.com/pricing/details/monitor/)、[Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)、および [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) で利用できます。 

従量課金制モデルに加え、2019 年 9 月に Log Analytics に対して容量予約を追加しました。こちらを選択すると、従量課金制の価格に比べて 25% も節約できます。 容量予約の価格を選択すると、1 日あたり 100 GB から予約を購入できます。 予約レベルを超える使用量は、従量課金制で請求されます。 容量予約の価格の詳細については、[こちら](https://azure.microsoft.com/pricing/details/monitor/)を参照してください。

2018 年 4 月 2 日より後に Log Analytics または Application Insights の利用を開始するお客様は、この新しい料金プランのみを選択できます。 これらのサービスを既に利用しているお客様は、新しい価格モデルに移行するかどうかを選択できます。

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>新しい価格モデルの影響の評価
新しい価格モデルがお客様に与える影響は、お客様の監視の使用パターンによって異なります。 2018 年 4 月 2 日より前に既に Log Analytics または Application Insights を利用しているお客様は、Azure Monitor の **[使用量と推定コスト]** ページで、新しい価格モデルに移行した場合のコストの変動の推定を確認できます。 サブスクリプションを新しいモデルに移行する方法が示されます。 新しい価格モデルにより、ほとんどのお客様は、メリットが得られることになるでしょう。 これは、データの使用量が非常に多いお客様やコストが高いリージョンのお客様には当てはまらない可能性があります。

**[使用量と推定コスト]** ページで選択したサブスクリプションのコストの推定値を確認するには、ページの上部近くにある青いバナーを選択します。 新しい価格モデルを採用できるレベルはサブスクリプションであるため、この操作は 1 度に 1 つのサブスクリプションに対して実行することをお勧めします。

![Monitor の [使用量と推定コスト] ページの新しい価格モデル選択のスクリーンショット](./media/usage-estimated-costs/004.png)

新しいページは以前と似ていますが、緑色のバナーが表示されています。

![Monitor の [使用量と推定コスト] ページの現在の価格モデル選択のスクリーンショット](./media/usage-estimated-costs/005.png)

また、メーターも異なります。このページには、新しい価格モデルに対応する一連のメーターが表示されます。 次に示したのはその例です。

- Insight and Analytics\ノードごとの超過分
- Insight and Analytics\ノードごとに含まれる
- Application Insights\Basic 超過データ
- Application Insights\含まれるデータ

新しい価格モデルにはノード ベースで含まれるデータの割り当てがありません。 そのため、これらのデータ インジェスト メーターは、**共有サービス\データ インジェスト**という名前の新しい共通データ インジェスト メーターに結合されます。 

コストが高いリージョンで Log Analytics または Application Insights に取り込まれたデータには、別の変更があります。 こうしたコストが高いリージョンのデータは、新しいリージョン メーターを使用して表示されます。 たとえば、"**データ インジェスト (米国中西部)** " のように表示されます。

> [!NOTE]
> サブスクリプションあたりの推定コストは、Operations Management Suite (OMS) サブスクリプションのアカウントレベルでのノード単位の権利には考慮されません。 この場合は、アカウント担当者と新しい価格モデルについて、詳しく検討してください。

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>新しい価格モデルと Operations Management Suite のサブスクリプションの権利

Microsoft Operations Management Suite E1 および E2 を購入されたお客様は、[Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) と [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) でのノード単位のデータ インジェストが可能です。 特定のサブスクリプションで Log Analytics ワークスペースまたは Application Insights リソースの権利を得るには: 

- そのサブスクリプションの価格モデルが、2018 年 4 月以前の価格モデルである必要があります。
- Log Analytics ワークスペースは、"ノードごとの (OMS)" 価格レベルを使用してください。
- Application Insights リソースは、"Enterprise" 価格プランを使用してください。

お客様の組織が購入されたスイートのノード数によっては、一部のサブスクリプションを新しい価格モデルへ移行した方が有益な場合がありますが、移行の前に慎重に検討するようにしてください。 一般に、前述のように 2018 年 4 月より前のモデルを維持することをお勧めします。

> [!WARNING]
> お客様の組織が、Microsoft Operations Management Suite E1 および E2 を購入した場合、2018 年 4 月より前の価格モデルのサブスクリプションを維持することをお勧めします。 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>新しい価格モデルに移行するときの変更

新しい料金モデルは、Log Analytics と Application Insights の価格オプションを 1 つのレベル (またはプラン) のみに簡素化します。 サブスクリプションを新しい価格モデルに移動した場合:

- 各 Log Analytics の価格レベルを新しい 1 GB あたりのレベル (Azure Resource Manager では "pergb2018" と呼ばれます) に変更します。
- Application Insights リソースの Enterprise プランが Basic プランに変更されます。

コスト見積もりには、こうした変更の影響が示されます。

> [!WARNING]
> 重要な注意事項として、Azure Resource Manager または PowerShell を使用して [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) または [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) をサブスクリプションに展開している場合、新しい価格モデルに移動しています。 Log Analytics で “pergb2018” 以外または Application Insights で “Basic” 以外の価格レベル/プランを指定した場合、無効な価格レベル/プランを指定したために展開が失敗するのではなく、成功しますが**有効な価格レベル/プランのみを使用します** (Log Analytics Free レベルはこれに該当せず、価格レベルが無効であるというメッセージが表示されます)。
>

## <a name="moving-to-the-new-pricing-model"></a>新しい価格モデルへの移行

特定のサブスクリプションに新しい価格モデルを採用することにした場合は、各 Application Insights リソースにアクセスし、 **[使用量と推定コスト]** を開いて、Basic 価格レベルであることを確認してください。次に、各 Log Analytics ワークスペースに移動し、各 **[価格レベル]** ページを開いて、 **[1 GB あたり (2018)]** 価格レベルに変更します。 

> [!NOTE]
> 特定のサブスクリプション内のすべての Application Insights リソースと Log Analytics ワークスペースが最新の価格モデルを採用するという要件がなくなったため、柔軟性が高まり、構成が容易になりました。 

## <a name="automate-moving-to-the-new-pricing-model"></a>新しい価格モデルへの移行を自動化する

既に説明したように、サブスクリプション内のすべての監視リソースを新しい価格モデルに同時に移動するという要件がなくなったため、``migratetonewpricingmodel`` アクションは効果を持たなくなりました。 今では、Application Insights リソースと Log Analytics ワークスペースを個別に最新の価格レベルに移動できます。  

この変更の自動化についてのドキュメントがあります。Application Insights の場合は [Set-AzureRmApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/set-azurermapplicationinsightspricingplan) と ``-PricingPlan "Basic"`` を使用し、Log Analytics の場合は [Set-AzureRmOperationalInsightsWorkspace](https://docs.microsoft.com/powershell/module/AzureRM.OperationalInsights/Set-AzureRmOperationalInsightsWorkspace) と ``-sku "PerGB2018"`` を使用しています。 
