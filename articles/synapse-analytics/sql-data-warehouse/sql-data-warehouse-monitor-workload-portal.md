---
title: ワークロードを監視する - Azure portal
description: Azure portal を使用して Synapse SQL を監視する
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.openlocfilehash: 3fb81ffc24ed6073e5398b14b7f490a0f63df84f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585589"
---
# <a name="monitor-workload---azure-portal"></a>ワークロードを監視する - Azure portal

この記事では、Azure portal を使用してワークロードを監視する方法について説明します。 これには、[Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/) のログ分析を使用して、クエリの実行とワークロードの傾向を調べるように Azure Monitor ログを設定することが含まれます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
- SQL プール:SQL プールのログを収集します。 SQL プールがプロビジョニングされていない場合は、[SQL プールの作成](./load-data-from-azure-blob-storage-using-copy.md)の手順を参照してください。

## <a name="create-a-log-analytics-workspace"></a>Log Analytics ワークスペースの作成

Log Analytics ワークスペースの参照ブレードに移動し、ワークスペースを作成します

![Log Analytics ワークスペース](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![スクリーンショットに、[追加] を選択できる Log Analytics ワークスペースが示されています。](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![スクリーンショットに、値を入力できる Log Analytics ワークスペースが示されています。](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

ワークスペースの詳細については、次の[ドキュメント](../../azure-monitor/logs/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)を参照してください。

## <a name="turn-on-resource-logs"></a>リソース ログをオンにする

SQL プールからログを出力するように診断設定を構成します。 ログは、テレメトリ ビューで構成されています。これは、よく使用されるパフォーマンス トラブルシューティングの DMV に相当します。 現在サポートされているビューは次のとおりです。

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

![リソース ログの有効化](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Azure Storage、Stream Analytics、または Log Analytics にログを出力できます。 このチュートリアルでは、Log Analytics を選択します。

![ログを指定する](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Log Analytics に対してクエリを実行する

次の操作を実行できる Log Analytics ワークスペースに移動します。

- ログ クエリを使用してログを分析し、再利用するためのクエリを保存する
- 再利用するためのクエリを保存する
- ログ アラートを作成する
- ダッシュ ボードにクエリ結果をピン留めする

ログ クエリの機能の詳細については、次の[ドキュメント](/azure/data-explorer/kusto/query/?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)を参照してください。

![Log Analytics ワークスペース エディター](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Log Analytics ワークスペース クエリ](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>サンプル ログ クエリ

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>次のステップ

Azure Monitor ログを設定し、構成したので、チームで共有できるように[Azure ダッシュボードをカスタマイズ](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)します。