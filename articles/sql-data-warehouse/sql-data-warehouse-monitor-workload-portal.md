---
title: ワークロードを監視する - Azure portal | Microsoft Docs
description: Azure portal を使用して Azure SQL Data Warehouse を監視する
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 6c8ce090039e3d5cc85c86d920710294de2165f9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405708"
---
# <a name="monitor-workload---azure-portal"></a>ワークロードを監視する - Azure portal

この記事では、Azure portal を使用してワークロードを監視する方法について説明します。 ここでは、[Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/) のログ解析を使用して、クエリの実行とワークロードの傾向を調べるように Azure Monitor ログを設定します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
- Azure SQL Data Warehouse: SQL Data Warehouse のログを収集します。 SQL Data Warehouse がプロビジョニングしていない場合は、[SQL Data Warehouse の作成](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial)に関する記事の手順を参照してください。

## <a name="create-a-log-analytics-workspace"></a>Log Analytics ワークスペースの作成

Log Analytics ワークスペースの参照ブレードに移動し、ワークスペースを作成します 

![Log Analytics ワークスペース](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Analytics ワークスペースを追加する](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Analytics ワークスペースを追加する](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

ワークスペースの詳細については、次の[ドキュメント](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace)を参照してください。

## <a name="turn-on-diagnostic-logs"></a>診断ログを有効にする 

SQL Data Warehouse からログを生成する診断設定を構成します。 ログは、データ ウェアハウスのテレメトリ ビューで構成されています。これは、よく使用される SQL Data Warehouse でのパフォーマンス トラブルシューティングの DMV に相当します。 現在サポートされているビューは次のとおりです。

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![診断ログの有効化](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Azure Storage、Stream Analytics、または Log Analytics にログを出力できます。 このチュートリアルでは、Log Analytics を選択します。

![ログを指定する](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Log Analytics に対してクエリを実行する

次の操作を実行できる Log Analytics ワークスペースに移動します。

- ログ クエリを使用してログを分析し、再利用するためのクエリを保存する
- 再利用するためのクエリを保存する
- ログ アラートを作成する
- ダッシュ ボードにクエリ結果をピン留めする

ログ クエリの機能の詳細については、次の[ドキュメント](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)を参照してください。

![Log Analytics ワークスペース エディター](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Log Analytics ワークスペース クエリ](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

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
## <a name="next-steps"></a>次の手順

Azure Monitor ログを設定し、構成したので、チームで共有できるように[Azure ダッシュボードをカスタマイズ](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)します。
