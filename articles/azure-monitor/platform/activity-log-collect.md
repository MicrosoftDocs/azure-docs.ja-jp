---
title: Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する | Microsoft Docs
description: Azure Monitor のログに Azure アクティビティ ログを収集し、監視ソリューションを使用して、すべての Azure サブスクリプションにわたって Azure アクティビティ ログの分析や検索ができます。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 40025e2347fa80d2717ba292f6e3d74f5dda8e5c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931405"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure Monitor の Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する

> [!WARNING]
> リソース ログの収集方法と同様に、診断設定を使用してアクティビティ ログを Log Analytics ワークスペースに収集できるようになりました。 「[Collect and analyze Azure activity logs in Log Analytics workspace in Azure Monitor (Azure Monitor の Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する)](diagnostic-settings-subscription.md)」を参照してください。

[Azure アクティビティ ログ](activity-logs-overview.md)は、Azure サブスクリプションで発生したサブスクリプションレベルのイベントを分析します。 この記事では、アクティビティ ログを Log Analytics ワークスペースに収集する方法と、このデータを分析するためのログ クエリおよびビューを提供する Activity Log Analytics [監視ソリューション](../insights/solutions.md)の使用方法について説明します。 

アクティビティ ログを Log Analytics ワークスペースに接続すると、次のような利点があります。

- 複数の Azure サブスクリプションのアクティビティ ログを 1 つの場所に統合して分析できるようにします。
- アクティビティ ログのエントリを 90 日を超えて保存します。
- アクティビティ ログ データを、Azure Monitor によって収集されたその他の監視データと関連付けます。
- [ログ クエリ](../log-query/log-query-overview.md)を使用して複雑な分析を実行し、アクティビティ ログのエントリから詳細な分析情報を得ます。

## <a name="connect-to-log-analytics-workspace"></a>Log Analytics ワークスペースに接続する
1 つのワークスペースを、同じ Azure テナント内の複数のサブスクリプションのアクティビティ ログに接続できます。 複数のテナントをまたいだ収集については、「[Azure アクティビティ ログを異なる Azure Active Directory テナント内のサブスクリプションにまたがる Log Analytics ワークスペースに収集する](activity-log-collect-tenants.md)」を参照してください。

> [!IMPORTANT]
> Microsoft.OperationalInsights と Microsoft.OperationsManagement リソース プロバイダーがサブスクリプションに登録されていない場合、次の手順でエラーが発生することがあります。 これらのプロバイダーの登録については、「[Azure リソース プロバイダーと種類](../../azure-resource-manager/resource-manager-supported-services.md)」を参照してください。

アクティビティ ログを Log Analytics ワークスペースに接続するには、次の手順を使用します。

1. Azure portal の **[Log Analytics ワークスペース]** メニューから、アクティビティ ログを収集するためのワークスペースを選択します。
1. ワークスペースのメニューの **[ワークスペースのデータ ソース]** セクションで、 **[Azure アクティビティ ログ]** を選択します。
1. 接続するサブスクリプションをクリックします。

    ![Workspaces](media/activity-log-export/workspaces.png)

1. **[接続]** をクリックして、選択したワークスペースにサブスクリプションのアクティビティ ログを接続します。 サブスクリプションが既に別のワークスペースに接続されている場合、最初に **[切断]** をクリックして切断します。

    ![ワークスペースを接続する](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Log Analytics ワークスペースで分析する
アクティビティ ログを Log Analytics ワークスペースに接続すると、ワークスペースにあり、[ログ クエリ](../log-query/log-query-overview.md)で取得できる **AzureActivity** という名前のテーブルにエントリが書き込まれるようになります。 このテーブルの構造は[ログ エントリのカテゴリ](activity-logs-overview.md#categories-in-the-activity-log)によって異なります。 各カテゴリの説明については、「[Azure アクティビティ ログのイベント スキーマ](activity-log-schema.md)」を参照してください。

## <a name="activity-logs-analytics-monitoring-solution"></a>Activity Logs Analytics 監視ソリューション
Azure Log Analytics 監視ソリューションには、Log Analytics ワークスペースのアクティビティ ログ レコードを分析するための、複数のログ クエリとビューが含まれています。

### <a name="install-the-solution"></a>ソリューションをインストールする
**Activity Log Analytics** ソリューションをインストールするには、「[監視ソリューションをインストール](../insights/solutions.md#install-a-monitoring-solution)」するの手順を使用します。 追加の構成は必要ありません。

### <a name="use-the-solution"></a>ソリューションの使用
監視ソリューションには Azure portal の **[Monitor]\(監視\)** メニューからアクセスします。 **[Insights]\(インサイト\)** セクションで **[More]\(詳細\)** を選択して、ソリューション タイルのある **[概要]** ページを開きます。 **[Azure アクティビティ ログ]** タイルには、ワークスペース内の **AzureActivity** レコードの数が表示されます。

![Azure アクティビティ ログのタイル](media/collect-activity-logs/azure-activity-logs-tile.png)


**[Azure アクティビティ ログ]** タイルをクリックして、 **[Azure アクティビティ ログ]** ビューを開きます。 ビューには、次の表の視覚化パーツが含まれています。 それぞれのパーツには、指定された時間範囲について、そのパーツの基準に該当する項目が 10 個まで表示されます。 パーツの下部にある **[すべて表示]** をクリックすると、すべての一致するレコードを返すログ クエリを実行できます。

![Azure のアクティビティ ログのダッシュボード](media/collect-activity-logs/activity-log-dash.png)

| 視覚化パーツ | 説明 |
| --- | --- |
| [Azure Activity Log Entries] \(Azure のアクティビティ ログ エントリ) | 選択した日付範囲の Azure アクティビティ ログ エントリ レコード合計上位の棒グラフが表示され、アクティビティの呼び出し元上位 10 個のリストも表示されます。 棒グラフをクリックすると、`AzureActivity` のログ検索が実行されます。 呼び出し元の項目をクリックするとログ検索が実行され、その項目のアクティビティ ログ エントリがすべて返されます。 |
| [Activity Logs by Status] \(状態ごとのアクティビティ ログ) | 選択した日付範囲の Azure アクティビティ ログ状態のドーナツ グラフと、上位 10 件の状態レコードの一覧を表示します。 `AzureActivity | summarize AggregatedValue = count() by ActivityStatus` のログ クエリを実行するには、グラフをクリックします。 状態の項目をクリックするとログ検索が実行され、その状態レコードのアクティビティ ログ エントリがすべて返されます。 |
| [Activity Logs by Resource] \(リソースごとのアクティビティ ログ) | アクティビティ ログのあるリソースの合計数が表示され、上位 10 個のリソースと各リソースのレコード カウントも表示されます。 合計領域をクリックすると、`AzureActivity | summarize AggregatedValue = count() by Resource` のログ検索が実行され、ソリューションで使用可能なすべての Azure リソースが表示されます。 リソースをクリックするとログ クエリが実行され、そのリソースのアクティビティ レコードがすべて返されます。 |
| [Activity Logs by Resource Provider] \(リソース プロバイダーごとのアクティビティ ログ) | アクティビティ ログを生成するリソース プロバイダーの合計数が表示され、上位 10 個も表示されます。 合計領域をクリックすると、`AzureActivity | summarize AggregatedValue = count() by ResourceProvider` のログ クエリが実行され、Azure のリソース プロバイダーがすべて表示されます。 リソース プロバイダーをクリックするとログ クエリが実行され、プロバイダーのアクティビティ レコードがすべて返されます。 |

## <a name="next-steps"></a>次の手順

- [アクティビティ ログ](activity-logs-overview.md)の詳細を確認します。
- [Azure Monitor データ プラットフォーム](data-platform.md)の詳細を確認します。
- [ログ クエリ](../log-query/log-query-overview.md)を使用して、アクティビティ ログの詳細情報を表示します。
