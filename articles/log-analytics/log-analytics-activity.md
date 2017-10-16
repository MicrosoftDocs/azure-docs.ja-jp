---
title: "Log Analytics を使用した Azure アクティビティ ログの表示 | Microsoft Docs"
description: "Azure アクティビティ ログ ソリューションを使用すると、すべての Azure サブスクリプションにわたって Azure アクティビティ ログの分析や検索ができます。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.openlocfilehash: 1ad56a54f094f3c314596b3a7c9fecd09647d065
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="view-azure-activity-logs"></a>Azure アクティビティ ログの表示

![Azure アクティビティ ログのシンボル](./media/log-analytics-activity/activity-log-analytics.png)

Activity Log Analytics ソリューションは、すべての Azure サブスクリプションにわたる [Azure アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)の分析や検索に役立ちます。 Azure アクティビティ ログでは、サブスクリプションのリソースに対して実行された操作に関する洞察が得られます。 アクティビティ ログではサブスクリプションのイベントが報告されるため、以前は*監査ログ*または*操作ログ*と呼ばれていました。

アクティビティ ログを使用すると、サブスクリプションのリソースに対して行われた書き込み操作 (PUT、POST、DELETE) すべてについて、*いつ*、*誰が*、*何を*行ったのかを確認できます。 さらに、操作の状態など、重要性の大きなプロパティを確認することもできます。 アクティビティ ログには、読み取り (GET) 操作や、クラシック デプロイメント モデルを使用するリソースに対する操作は含まれません。

Azure アクティビティ ログを Log Analytics に接続すると、次のことができます。

- 事前定義のビューを使用してアクティビティ ログを分析する
- 複数の Azure サブスクリプションから得られるアクティビティ ログを分析したり検索したりする
- アクティビティ ログを 90 日より長く保持する<sup>1</sup>
- アクティビティ ログを Azure の他のプラットフォーム データおよびアプリケーション データと関連付ける
- 状態ごとに集計された運用のアクティビティを確認する
- 各 Azure サービスで発生するアクティビティの傾向を表示する
- すべての Azure リソースに関する承認の変更をレポートする
- リソースに影響する停止またはサービスの正常性の問題を特定する
- ログ検索を使用して、ユーザー アクティビティ、自動スケール操作、承認の変更、サービスの正常性を、使用している環境から得られる他のログや指標と関連付ける

<sup>1</sup>既定では、Log Analytics は、Free レベルの場合でも Azure アクティビティ ログを 90 日間保持します。 また、ワークスペース リテンション期間の設定が 90 日未満の場合もそうです。 ワークスペースのリテンション期間が 90 日より長い場合、アクティビティ ログはワークスペースのリテンション期間の間保持されます。

Log Analytics では、アクティビティ ログを無料で収集し、90 日間無料でログを保管します。 90 日より長くログを保管する場合は、90 日より長く保管されているデータについてデータ リテンション期間の料金が請求されます。

Free 価格レベルの場合は、アクティビティ ログに毎日のデータ従量課金は適用されません。

## <a name="connected-sources"></a>接続先ソース

他のほとんどのログ分析ソリューションとは異なり、アクティビティ ログのデータはエージェントによって収集されません。 ソリューションで使用されるデータはすべて、Azure から直接収集されます。

| 接続されているソース | サポートの有無 | 説明 |
| --- | --- | --- |
| [Windows エージェント](log-analytics-windows-agents.md) | いいえ | ソリューションでは、Windows エージェントの情報は収集しません。 |
| [Linux エージェント](log-analytics-linux-agents.md) | なし | ソリューションでは、Linux エージェントの情報は収集しません。 |
| [SCOM 管理グループ](log-analytics-om-agents.md) | いいえ | ソリューションでは、接続された SCOM 管理グループ内のエージェントの情報は収集しません。 |
| [Azure Storage アカウント](log-analytics-azure-storage.md) | いいえ | ソリューションでは、Azure Storage の情報は収集しません。 |

## <a name="prerequisites"></a>前提条件

- Azure アクティビティ ログの情報にアクセスするには、Azure サブスクリプションが必要です。

## <a name="configuration"></a>構成

ワークスペースの Activity Log Analytics ソリューションを構成するには、次の手順を実行します。

1. Activity Log Analytics ソリューションを有効にします。[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) から有効にするか、[ソリューション ギャラリーからの Log Analytics ソリューションの追加](log-analytics-add-solutions.md)に関するページで説明されている手順に従って有効にしてください。
2. Log Analytics ワークスペースに接続するようにアクティビティ ログを構成します。
    1. Azure Portal で、ワークスペースを選択し、**[Azure Activity log]**(Azure アクティビティ ログ) をクリックします。
    2. サブスクリプションごとに、サブスクリプションの名前をクリックします。  
        ![サブスクリプションの追加](./media/log-analytics-activity/add-subscription.png)
    3. *[SubscriptionName]*(SubscriptionName) ブレードで、**[Connect]**(接続) をクリックします。  
        ![サブスクリプションの接続](./media/log-analytics-activity/subscription-connect.png)

OMS ポータルを使用してソリューションを追加する場合は、次のタイルが表示されます。 Azure Portal にサインインして、Azure サブスクリプションをワークスペースに接続します。  
![評価の実行](./media/log-analytics-activity/tile-performing-assessment.png)

## <a name="using-the-solution"></a>ソリューションの使用

Activity Log Analytics ソリューションをワークスペースに追加すると、**[Azure Activity Logs]**(Azure アクティビティ ログ) タイルが [Overview] \(概要) ダッシュボードに追加されます。 このタイルには、ソリューションにアクセスできる Azure サブスクリプションの Azure アクティビティ レコードのカウント数が表示されます。

![Azure アクティビティ ログのタイル](./media/log-analytics-activity/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Azure アクティビティ ログの表示

**[Azure Activity Logs]**(Azure アクティビティ ログ) タイルをクリックして、**[Azure Activity Logs]**(Azure アクティビティ ログ) ダッシュボードを開きます。 ダッシュボードには、次の表に示したブレードが存在します。 それぞれのブレードには、特定のスコープと時間範囲について、そのブレードの基準に該当する項目が最大 10 個表示されます。 すべてのレコードを返すログ検索を実行するには、ブレードの一番下にある **[すべて表示]** をクリックするか、ブレード ヘッダーをクリックします。

アクティビティ ログ データは、ソリューションに接続するようにアクティビティ ログを構成した*後*はじめて表示されるので、その前にデータを表示することはできません。

| ブレード | Description |
| --- | --- |
| [Azure Activity Log Entries] \(Azure のアクティビティ ログ エントリ) | 選択した日付範囲の Azure アクティビティ ログ エントリ レコード合計上位の棒グラフが表示され、アクティビティの呼び出し元上位 10 個のリストも表示されます。 ログの検索を実行する横棒グラフをクリックして<code>Type=AzureActivity</code>です。 呼び出し元の項目をクリックするとログ検索が実行され、その項目のアクティビティ ログ エントリがすべて返されます。 |
| [Activity Logs by Status] \(状態ごとのアクティビティ ログ) | 選択した日付範囲の Azure アクティビティ ログ状態のドーナツ グラフが表示されます。 状態レコード上位 10 個のリストも表示されます。 ログの検索を実行するようにグラフをクリックして<code>Type=AzureActivity &#124; measure count() by ActivityStatus</code>です。 状態の項目をクリックするとログ検索が実行され、その状態レコードのアクティビティ ログ エントリがすべて返されます。 |
| [Activity Logs by Resource] \(リソースごとのアクティビティ ログ) | アクティビティ ログのあるリソースの合計数が表示され、上位 10 個のリソースと各リソースのレコード カウントも表示されます。 ログの検索を実行する合計領域をクリックして<code>Type=AzureActivity &#124; measure count() by Resource</code>ソリューションに使用できるすべての Azure リソースが示されます。 リソースをクリックするとログ検索が実行され、そのリソースのアクティビティ レコードがすべて返されます。 |
| [Activity Logs by Resource Provider] \(リソース プロバイダーごとのアクティビティ ログ) | アクティビティ ログを生成するリソース プロバイダーの合計数が表示され、上位 10 個も表示されます。 ログの検索を実行する合計領域をクリックして<code>Type=AzureActivity &#124; measure count() by ResourceProvider</code>、すべての Azure リソース プロバイダーが示されます。 リソース プロバイダーをクリックするとログ検索が実行され、プロバイダーのアクティビティ レコードがすべて返されます。 |

![Azure のアクティビティ ログのダッシュボード](./media/log-analytics-activity/activity-log-dash.png)

## <a name="next-steps"></a>次のステップ

- 特定のアクティビティが発生した場合に[アラート](log-analytics-alerts-creating.md)を作成します。
- [ログ検索](log-analytics-log-searches.md)を使用して、アクティビティ ログの詳細情報を表示します。
