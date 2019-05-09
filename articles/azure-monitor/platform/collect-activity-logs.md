---
title: Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する | Microsoft Docs
description: Azure アクティビティ ログ ソリューションを使用すると、すべての Azure サブスクリプションにわたって Azure アクティビティ ログの分析や検索ができます。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: magoedte
ms.openlocfilehash: 4476bb0a5a343fd43ce5ed70cf0e493d0ccae0e9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59505636"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor の Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する

![Azure アクティビティ ログのシンボル](./media/collect-activity-logs/activity-log-analytics.png)

Activity Log Analytics ソリューションは、すべての Azure サブスクリプションにわたる [Azure アクティビティ ログ](activity-logs-overview.md)の分析や検索に役立ちます。 Azure アクティビティ ログでは、サブスクリプションのリソースに対して実行された操作に関する洞察が得られます。 アクティビティ ログではサブスクリプションのイベントが報告されるため、以前は*監査ログ*または*操作ログ*と呼ばれていました。

アクティビティ ログを使用すると、サブスクリプションのリソースに対して行われた書き込み操作 (PUT、POST、DELETE) すべてについて、*いつ*、*誰が*、*何を*行ったのかを確認できます。 さらに、操作の状態など、重要性の大きなプロパティを確認することもできます。 アクティビティ ログには、読み取り (GET) 操作や、クラシック デプロイ モデルを使用するリソースに対する操作は含まれません。

Azure アクティビティ ログを Log Analytics ワークスペースに接続すると、次のことができます。

- 事前定義のビューを使用してアクティビティ ログを分析する
- 複数の Azure サブスクリプションから得られるアクティビティ ログを分析したり検索したりする
- アクティビティ ログを 90 日より長く保持する<sup>1</sup>
- アクティビティ ログを Azure の他のプラットフォーム データおよびアプリケーション データと関連付ける
- 状態ごとに集計された運用のアクティビティを確認する
- 各 Azure サービスで発生するアクティビティの傾向を表示する
- すべての Azure リソースに関する承認の変更をレポートする
- リソースに影響する停止またはサービスの正常性の問題を特定する
- ログ検索を使用して、ユーザー アクティビティ、自動スケール操作、承認の変更、サービスの正常性を、使用している環境から得られる他のログや指標と関連付ける

<sup>1</sup>Azure Monitor では、Free レベルの場合でも、ご自身の Azure アクティビティ ログが既定で 90 日間 Log Analytics ワークスペースに保持されます。 また、ワークスペース リテンション期間の設定が 90 日未満の場合もそうです。 ワークスペースのリテンション期間が 90 日より長い場合、アクティビティ ログはワークスペースのリテンション期間に基づいて保持されます。

Log Analytics ワークスペースでは、アクティビティ ログを無料で収集し、90 日間無料でログを保管します。 90 日より長くログを保管する場合は、90 日より長く保管されているデータについてデータ リテンション期間の料金が請求されます。

Free 価格レベルの場合は、アクティビティ ログに毎日のデータ従量課金は適用されません。

## <a name="connected-sources"></a>接続先ソース

他のほとんどの Azure Monitor ソリューションとは異なり、アクティビティ ログのデータはエージェントによって収集されません。 ソリューションで使用されるデータはすべて、Azure から直接収集されます。

| 接続先ソース | サポートされています | 説明 |
| --- | --- | --- |
| [Windows エージェント](agent-windows.md) | いいえ  | ソリューションでは、Windows エージェントの情報は収集しません。 |
| [Linux エージェント](../learn/quick-collect-linux-computer.md) | いいえ  | ソリューションでは、Linux エージェントの情報は収集しません。 |
| [System Center Operations Manager 管理グループ](om-agents.md) | いいえ  | このソリューションでは、Operations Manager 管理グループに報告を行うエージェントから情報を収集しません。 |
| [Azure Storage アカウント](collect-azure-metrics-logs.md) | いいえ  | ソリューションでは、Azure Storage の情報は収集しません。 |

## <a name="prerequisites"></a>前提条件

Azure アクティビティ ログの情報にアクセスするには、Azure サブスクリプションが必要です。

また、このソリューションでは、次の 2 つのリソース プロバイダーがサブスクリプションに登録されている必要もあります。

1. Microsoft.OperationalInsights
2. Microsoft.OperationsManagement

これらを登録する方法、または登録されていることを確認する方法については、「[Azure リソース プロバイダーと種類](../../azure-resource-manager/resource-manager-supported-services.md)」を参照してください

## <a name="configuration"></a>構成

ワークスペースの Activity Log Analytics ソリューションを構成するには、次の手順を実行します。

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

2. Activity Log Analytics ソリューションを有効にします。[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) から有効にするか、[ソリューション ギャラリーからの Log Analytics ソリューションの追加](../insights/solutions.md)に関するページで説明されている手順に従って有効にしてください。

2. Log Analytics ワークスペースに接続するようにアクティビティ ログを構成します。
    1. Azure Portal で、ワークスペースを選択し、**[Azure Activity log]**(Azure アクティビティ ログ) をクリックします。
    2. サブスクリプションごとに、サブスクリプションの名前をクリックします。  
        
        ![サブスクリプションの追加](./media/collect-activity-logs/add-subscription.png)
    
    3. *[SubscriptionName]*(SubscriptionName) ブレードで、**[Connect]**(接続) をクリックします。  
    
        ![サブスクリプションの接続](./media/collect-activity-logs/subscription-connect.png)

## <a name="using-the-solution"></a>ソリューションの使用

Activity Log Analytics ソリューションをワークスペースに追加すると、**[Azure Activity Logs]**(Azure アクティビティ ログ) タイルが [Overview] \(概要) ダッシュボードに追加されます。 このタイルには、ソリューションにアクセスできる Azure サブスクリプションの Azure アクティビティ レコードのカウント数が表示されます。

![Azure アクティビティ ログのタイル](./media/collect-activity-logs/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Azure アクティビティ ログの表示

**[Azure Activity Logs]**(Azure アクティビティ ログ) タイルをクリックして、**[Azure Activity Logs]**(Azure アクティビティ ログ) ダッシュボードを開きます。 ダッシュボードには、次の表に示したブレードが存在します。 それぞれのブレードには、特定のスコープと時間範囲について、そのブレードの基準に該当する項目が最大 10 個表示されます。 すべてのレコードを返すログ検索を実行するには、ブレードの一番下にある **[すべて表示]** をクリックするか、ブレード ヘッダーをクリックします。

アクティビティ ログ データは、ソリューションに接続するようにアクティビティ ログを構成した*後*はじめて表示されるので、その前にデータを表示することはできません。

| ブレード | 説明 |
| --- | --- |
| [Azure Activity Log Entries] \(Azure のアクティビティ ログ エントリ) | 選択した日付範囲の Azure アクティビティ ログ エントリ レコード合計上位の棒グラフが表示され、アクティビティの呼び出し元上位 10 個のリストも表示されます。 棒グラフをクリックすると、<code>AzureActivity</code> のログ検索が実行されます。 呼び出し元の項目をクリックするとログ検索が実行され、その項目のアクティビティ ログ エントリがすべて返されます。 |
| [Activity Logs by Status] \(状態ごとのアクティビティ ログ) | 選択した日付範囲の Azure アクティビティ ログ状態のドーナツ グラフが表示されます。 状態レコード上位 10 個のリストも表示されます。 グラフをクリックすると、<code>AzureActivity &#124; summarize AggregatedValue = count() by ActivityStatus</code> のログ検索が実行されます。 状態の項目をクリックするとログ検索が実行され、その状態レコードのアクティビティ ログ エントリがすべて返されます。 |
| [Activity Logs by Resource] \(リソースごとのアクティビティ ログ) | アクティビティ ログのあるリソースの合計数が表示され、上位 10 個のリソースと各リソースのレコード カウントも表示されます。 合計領域をクリックすると、<code>AzureActivity &#124; summarize AggregatedValue = count() by Resource</code> のログ検索が実行され、ソリューションで使用可能なすべての Azure リソースが表示されます。 リソースをクリックするとログ検索が実行され、そのリソースのアクティビティ レコードがすべて返されます。 |
| [Activity Logs by Resource Provider] \(リソース プロバイダーごとのアクティビティ ログ) | アクティビティ ログを生成するリソース プロバイダーの合計数が表示され、上位 10 個も表示されます。 合計領域をクリックすると、<code>AzureActivity &#124; summarize AggregatedValue = count() by ResourceProvider</code> のログ検索が実行され、Azure のリソース プロバイダーがすべて表示されます。 リソース プロバイダーをクリックするとログ検索が実行され、プロバイダーのアクティビティ レコードがすべて返されます。 |

![Azure のアクティビティ ログのダッシュボード](./media/collect-activity-logs/activity-log-dash.png)

## <a name="next-steps"></a>次の手順

- 特定のアクティビティが発生した場合に[アラート](../platform/alerts-metric.md)を作成します。
- [ログ検索](../log-query/log-query-overview.md)を使用して、アクティビティ ログの詳細情報を表示します。
