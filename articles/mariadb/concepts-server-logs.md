---
title: 低速クエリ ログ - Azure Database for MariaDB
description: Azure Database for MariaDB で利用できるログと、さまざまなログ記録レベルを有効にするため利用可能なパラメーターについて説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/6/2020
ms.openlocfilehash: 2e7e56616300566839fadef762c2165c8d989e6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570817"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Azure Database for MariaDB での低速クエリ ログ
Azure Database for MariaDB では、ユーザーは低速クエリ ログを使用できます。 トランザクション ログへのアクセスはサポートされていません。 低速クエリ ログは、トラブルシューティングの目的でパフォーマンスのボトルネックを特定するために使用できます。

低速クエリ ログについて詳しくは、[低速クエリ ログ](https://mariadb.com/kb/en/library/slow-query-log-overview/)に関する MariaDB のドキュメントをご覧ください。

サーバーで[クエリ ストア](concepts-query-store.md)が有効になっている場合、低速クエリ ログに "`CALL mysql.az_procedure_collect_wait_stats (900, 30);`" のようなクエリが記録されている可能性があります。 この動作は、クエリ ストア機能によってクエリに関する統計情報を収集するために必要です。 

## <a name="configure-slow-query-logging"></a>低速クエリ ログを構成する
既定では、低速クエリ ログは無効です。 有効にするには、`slow_query_log` を ON に設定します。 これは、Azure portal または Azure CLI を使用して有効にすることができます。 

調整できるその他のパラメーターは次のとおりです。

- **long_query_time**: long_query_time (秒単位) より長いクエリがある場合は、そのクエリが記録されます。 既定値は 10 秒です。
- **log_slow_admin_statements**: オンの場合は、slow_query_log に書き込まれるステートメントに、ALTER_TABLE や ANALYZE_TABLE などの管理ステートメントが含まれます。
- **log_queries_not_using_indexes**: インデックスを使用していないクエリを slow_query_log に記録するかどうかを決定します。
- **log_throttle_queries_not_using_indexes**:このパラメーターは、低速クエリ ログに書き込むことができる、インデックスを使用していないクエリの数を制限します。 このパラメーターは、Log_queries_not_using_indexes がオンに設定されている場合に有効です。
- **log_output**: "File" の場合、ローカル サーバー ストレージと Azure Monitor 診断ログの両方に低速クエリ ログの書き込みが許可されます。 "None" の場合、低速クエリ ログは Azure Monitor 診断ログのみに書き込まれます。 

> [!IMPORTANT]
> テーブルにインデックスが作成されていない場合は、`log_queries_not_using_indexes` パラメーターと `log_throttle_queries_not_using_indexes` パラメーターを ON に設定すると、インデックスが設定されていないテーブルに対して実行されるすべてのクエリが低速クエリ ログに書き込まれるため、MariaDB のパフォーマンスに影響する可能性があります。<br><br>
> 低速クエリのログ記録を長時間にわたって行う場合は、`log_output` を "None" に設定することをお勧めします。 "File" に設定すると、これらのログはローカル サーバー ストレージに書き込まれ、MariaDB のパフォーマンスに影響を与える可能性があります。 

低速クエリ ログのパラメーターの完全な説明については、MariaDB の[低速クエリ ログのドキュメント](https://mariadb.com/kb/en/library/slow-query-log-overview/)を参照してください。

## <a name="access-slow-query-logs"></a>低速クエリ ログにアクセスする
Azure Database for MariaDB の低速クエリ ログにアクセスするには、ローカル サーバー ストレージまたは Azure Monitor 診断ログの 2 つのオプションがあります。 これは `log_output` パラメーターを使用して設定します。

ローカル サーバー ストレージの場合は、Azure portal または Azure CLI を使用して、低速クエリ ログを一覧表示およびダウンロードできます。 Azure portal で、Azure portal 内のご使用のサーバーに移動します。 **[監視]** の見出しの下の、 **[サーバー ログ]** ページを選択します。 Azure CLI の詳細については、「[Configure and access server logs using Azure CLI (Azure CLI を使用したサーバー ログの構成とアクセス)](howto-configure-server-logs-cli.md)」を参照してください。 

Azure Monitor 診断ログを使用すると、低速クエリ ログを Azure Monitor ログ (Log Analytics)、Azure Storage、または Event Hubs にパイプすることができます。 詳細については、[以下](concepts-server-logs.md#diagnostic-logs)を参照してください。

## <a name="local-server-storage-log-retention"></a>ローカル サーバー ストレージ ログの保持期間
サーバーのローカル ストレージにログを記録する場合、ログは作成から最大 7 日間利用できます。 使用可能なログの合計サイズが 7 GB を超える場合は、空き領域を利用できるようになるまで、古いファイルから削除されます。

ログのローテーションは、24 時間ごとか 7 GB ごとのどちらか早い方のタイミングで行われます。

> [!Note]
> 上記のログ保持期間は、Azure Monitor 診断ログを使用してパイプ処理されたログには適用されません。 出力先のデータ シンク (Azure Storage など) の保持期間を変更することが できます。

## <a name="diagnostic-logs"></a>診断ログ
Azure Database for MariaDB は、Azure Monitor 診断ログと統合されます。 MariaDB サーバーで低速クエリ ログを有効にした場合、それらが Azure Monitor のログ、イベント ハブ、または Azure Storage に出力されるようにすることもできます。 診断ログを有効にする方法の詳細については、[診断ログのドキュメント](../azure-monitor/essentials/platform-logs-overview.md)の操作方法のセクションを参照してください。

次の表は、各ログの内容を説明しています。 出力方法に応じて、含まれるフィールドとそれらが表示される順序が異なることがあります。

| **プロパティ** | **説明** |
|---|---|
| `TenantId` | テナント ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | ログが記録されたときのタイムスタンプ (UTC) |
| `Type` | ログの種類。 常に `AzureDiagnostics` |
| `SubscriptionId` | サーバーが属するサブスクリプションの GUID |
| `ResourceGroup` | サーバーが属するリソース グループの名前 |
| `ResourceProvider` | リソース プロバイダーの名前。 常に `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | リソース URI |
| `Resource` | サーバーの名前 |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | サーバーの名前 |
| `start_time_t` [UTC] | クエリの開始時刻 |
| `query_time_s` | クエリの実行にかかった合計時間 |
| `lock_time_s` | クエリがロックされていた合計時間 |
| `user_host_s` | ユーザー名 |
| `rows_sent_s` | 送信された行の数 |
| `rows_examined_s` | 検査された行の数 |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | 挿入 ID |
| `sql_text_s` | クエリ全体 |
| `server_id_s` | サーバー ID |
| `thread_id_s` | スレッド ID |
| `\_ResourceId` | リソース URI |

> [!Note]
> `sql_text` の場合、2048 文字を超えたログは切り捨てられます。

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

- 診断ログが有効になっているすべての MariaDB サーバーで 10 秒以上のクエリを表示する

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>次の手順
- [Azure portal から低速クエリ ログを構成する方法](howto-configure-server-logs-portal.md)
- [Azure CLI から低速クエリ ログを構成する方法](howto-configure-server-logs-cli.md)
