---
title: クエリ ログの表示 - Azure Database for MySQL - フレキシブル サーバー
description: Azure Database for MySQL フレキシブル サーバーで使用できる低速クエリ ログについて説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: d311ea3158e1f9d53c51fe239103039849597d11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579191"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server-preview"></a>Azure Database for MySQL フレキシブル サーバーの低速クエリ ログ (プレビュー)

> [!IMPORTANT] 
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

Azure Database for MySQL フレキシブル サーバーでは、ユーザーは低速クエリ ログを構成してアクセスできます。 低速クエリ ログは既定で無効にされていますが、トラブルシューティング時に、パフォーマンスのボトルネックの特定で役立てるために有効にすることができます。

MySQL の低速クエリ ログの詳細については、MySQL エンジンのドキュメントの[低速クエリ ログに関するセクション](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)を参照してください。

## <a name="configure-slow-query-logging"></a>低速クエリ ログを構成する 
既定で、低速クエリ ログは無効です。 ログを有効にするには、`slow_query_log` サーバー パラメーターを *ON* に設定します。 これは、Azure portal または Azure CLI を使用して構成できます <!-- add link to server parameter-->. 

低速クエリ ログの動作を制御するために調整できるその他のパラメーターには、次のようなものがあります。

- **long_query_time**: クエリが完了までに `long_query_time` (秒) よりも長くかかる場合に、ログに記録します。 既定は 10 秒です。
- **log_slow_admin_statements**: 管理ステートメント (例: `ALTER_TABLE`、`ANALYZE_TABLE`) をログに記録するかどうかを決定します。
- **log_queries_not_using_indexes**: インデックスを使用していないクエリをログに記録するかどうかを決定します。
- **log_throttle_queries_not_using_indexes**: 低速クエリ ログに書き込むことができる、インデックスを使用していないクエリの数を制限します。 このパラメーターは `log_queries_not_using_indexes` が *ON* に設定されている場合に有効になります

> [!IMPORTANT]
> テーブルにインデックスが作成されていない場合、`log_queries_not_using_indexes` パラメーターと `log_throttle_queries_not_using_indexes` パラメーターを **ON** に設定すると、これらのインデックスが設定されていないテーブルに対して実行されるすべてのクエリが低速クエリ ログに書き込まれるため、MySQL のパフォーマンスに影響する可能性があります。

低速クエリ ログのパラメーターの完全な説明については、MySQL の[低速クエリ ログのドキュメント](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)を参照してください。

## <a name="access-slow-query-logs"></a>低速クエリ ログにアクセスする

低速クエリ ログは、Azure Monitor 診断設定と統合されています。 MySQL フレキシブル サーバーで低速クエリ ログを有効にしたら、そのログを Azure Monitor ログ、Event Hubs、または Azure Storage に出力できます。 診断設定の詳細については、[診断ログのドキュメント](../../azure-monitor/essentials/platform-logs-overview.md)を参照してください。 Azure portal で診断設定を有効にする方法の詳細については、[低速クエリ ログに関するポータルの記事](how-to-configure-slow-query-logs-portal.md#set-up-diagnostics)を参照してください。

次の表に、低速クエリ ログの出力を示します。 出力方法に応じて、含まれるフィールドとそれらが表示される順序が異なることがあります。

| **プロパティ** | **説明** |
|---|---|
| `TenantId` | テナント ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | ログが記録されたときのタイムスタンプ (UTC) |
| `Type` | ログの種類。 常に `AzureDiagnostics` |
| `SubscriptionId` | サーバーが属するサブスクリプションの GUID |
| `ResourceGroup` | サーバーが属するリソース グループの名前 |
| `ResourceProvider` | リソース プロバイダーの名前。 常に `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | リソース URI |
| `Resource` | サーバーの名前 |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | サーバーの名前 |
| `start_time_t` [UTC] | クエリの開始時刻 |
| `query_time_s` | クエリの実行にかかった合計時間 (秒) |
| `lock_time_s` | クエリのロックにかかった合計時間 (秒) |
| `user_host_s` | ユーザー名 |
| `rows_sent_s` | 送信された行の数 |
| `rows_examined_s` | 検査された行の数 |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | 挿入 ID |
| `sql_text_s` | クエリ全体 |
| `server_id_s` | サーバーの ID |
| `thread_id_s` | スレッド ID |
| `\_ResourceId` | リソース URI |

> [!Note]
> `sql_text_s` の場合、2048 文字を超えたログは切り捨てられます。

## <a name="analyze-logs-in-azure-monitor-logs"></a>Azure Monitor ログのログを分析する

低速クエリ ログが診断ログによって Azure Monitor ログにパイプされたら、低速クエリの詳細な分析を実行できます。 使用を開始する際に役立つサンプル クエリを以下にいくつか示します。 以下を、お使いのサーバー名で更新してください。

- 特定のサーバーで 10 秒を超えるクエリ

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- 特定のサーバーの長いクエリの上位 5 つを一覧表示する

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- 低速クエリを、特定のサーバーのクエリ時間の最小値、最大値、平均値、および標準偏差で要約する

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- 特定のサーバーの低速クエリの分布をグラフ化する

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- 診断ログが有効になっているすべての MySQL サーバーで 10 秒以上のクエリを表示する

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>次のステップ
- [監査ログ](concepts-audit-logs.md)の詳細を確認する
- [Azure portal](how-to-configure-slow-query-logs-portal.md) から低速クエリ ログを構成する
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->