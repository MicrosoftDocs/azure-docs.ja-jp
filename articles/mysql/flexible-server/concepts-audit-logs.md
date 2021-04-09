---
title: 監査ログ - Azure Database for MySQL - フレキシブル サーバー
description: Azure Database for MySQL フレキシブル サーバーで使用できる監査ログについて説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 1232a0753c988f5a28ebba28f9819aa67ce28603
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101718745"
---
# <a name="track-database-activity-with-audit-logs-in-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL フレキシブル サーバーで監査ログを使用してデータベース アクティビティを追跡する

> [!IMPORTANT] 
> Azure Database for MySQL フレキシブル サーバーは現在、パブリック プレビュー段階にあります

Azure Database for MySQL フレキシブル サーバーでは、ユーザーが監査ログを構成することができます。 監査ログは、接続、管理、DDL、DML の各イベントなど、データベースレベルのアクティビティを追跡するために使用できます。 これらの種類のログは、通常、コンプライアンスのために使用されます。

## <a name="configure-audit-logging"></a>監査ログを構成する

>[!IMPORTANT]
> サーバーのパフォーマンスに大きな影響を与えないように、監査のために必要なイベントの種類とユーザーのみをログに記録することをお勧めします。

監査ログは、既定では無効になっています。 有効にするには、`audit_log_enabled` サーバー パラメーターを "*ON*" に設定します。 これは、Azure portal または Azure CLI を使用して構成することができます <!-- add link to server parameter-->. 

監査ログの動作を制御するために調整できるその他のパラメーターには、次のようなものがあります。

- `audit_log_events`: 記録するイベントを制御します。 特定の監査イベントについては、次のを参照してください。
- `audit_log_include_users`:ログ記録の対象となる MySQL ユーザー。 このパラメーターの既定値は空で、すべてのユーザーがログに記録されます。 優先順位は、`audit_log_exclude_users` より高くなっています。 パラメーターの最大長は 512 文字です。
- `audit_log_exclude_users`:ログ記録から除外する MySQL ユーザー。 パラメーターの最大長は 512 文字です。

> [!NOTE]
> `audit_log_include_users` は、`audit_log_exclude_users` よりも優先順位が高くなっています。 たとえば、`audit_log_include_users` = `demouser` かつ `audit_log_exclude_users` = `demouser`の場合、`audit_log_include_users` の優先度が高いので、ユーザーは監査ログに含まれます。

| **Event** | **説明** |
|---|---|
| `CONNECTION` | - 接続開始 (成功または失敗) <br> - 異なるユーザーとパスワードを使用した、セッション中でのユーザーの再認証 <br> - 接続終了 |
| `DML_SELECT`| SELECT クエリ |
| `DML_NONSELECT` | INSERT、DELETE、UPDATE クエリ |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | "DROP DATABASE" のようなクエリ |
| `DCL` | "GRANT PERMISSION" のようなクエリ |
| `ADMIN` | "SHOW STATUS" のようなクエリ |
| `GENERAL` | DML_SELECT、DML_NONSELECT、DML、DDL、DCL、および ADMIN のすべて |
| `TABLE_ACCESS` | テーブル読み取りステートメント。たとえば、SELECT、INSERT INTO ...SELECT <br> - テーブル削除ステートメント。たとえば、DELETE、TRUNCATE TABLE <br> - テーブル挿入ステートメント。たとえば、INSERT、REPLACE <br> - テーブル更新ステートメント。たとえば、UPDATE |

## <a name="access-audit-logs"></a>監査ログにアクセスする

監査ログは、Azure Monitor の診断設定と統合されます。 MySQL フレキシブル サーバーで監査ログを有効にしたら、Azure Monitor ログ、Event Hubs、または Azure Storage にそれらを出力できます。 診断設定の詳細については、[診断ログのドキュメント](../../azure-monitor/essentials/platform-logs-overview.md)を参照してください。 Azure portal で診断設定を有効にする方法の詳細については、[監査ログに関するポータルの記事](how-to-configure-audit-logs-portal.md#set-up-diagnostics)を参照してください。

次のセクションでは、イベントの種類に基づく MySQL 監査ログの出力について説明します。 出力方法に応じて、含まれるフィールドとそれらが表示される順序が異なることがあります。

### <a name="connection"></a>Connection

| **プロパティ** | **説明** |
|---|---|
| `TenantId` | テナント ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | ログが記録されたときのタイムスタンプ (UTC) |
| `Type` | ログの種類。 常に `AzureDiagnostics` |
| `SubscriptionId` | サーバーが属するサブスクリプションの GUID |
| `ResourceGroup` | サーバーが属するリソース グループの名前 |
| `ResourceProvider` | リソース プロバイダーの名前。 常に `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | リソース URI |
| `Resource` | サーバーの名前 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | サーバーの名前 |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` |
| `connection_id_d` | MySQL によって生成された一意の接続 ID |
| `host_s` | 空白 |
| `ip_s` | MySQL に接続しているクライアントの IP アドレス |
| `user_s` | クエリを実行しているユーザーの名前 |
| `db_s` | 接続先のデータベースの名前 |
| `\_ResourceId` | リソース URI |

### <a name="general"></a>全般

以下のスキーマは、GENERAL、DML_SELECT、DML_NONSELECT、DML、DDL、DCL、および ADMIN の各イベントの種類に適用されます。

> [!NOTE]
> `sql_text_s` の場合、2048 文字を超えたログは切り捨てられます。

| **プロパティ** | **説明** |
|---|---|
| `TenantId` | テナント ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | ログが記録されたときのタイムスタンプ (UTC) |
| `Type` | ログの種類。 常に `AzureDiagnostics` |
| `SubscriptionId` | サーバーが属するサブスクリプションの GUID |
| `ResourceGroup` | サーバーが属するリソース グループの名前 |
| `ResourceProvider` | リソース プロバイダーの名前。 常に `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | リソース URI |
| `Resource` | サーバーの名前 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | サーバーの名前 |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`、`ERROR`、`RESULT` (MySQL 5.6 でのみ利用可能) |
| `event_time` | UTC タイムスタンプのクエリの開始時刻 |
| `error_code_d` | エラー コード (クエリが失敗した場合)。 `0` は、エラーなしを意味します |
| `thread_id_d` | クエリを実行したスレッドの ID |
| `host_s` | 空白 |
| `ip_s` | MySQL に接続しているクライアントの IP アドレス |
| `user_s` | クエリを実行しているユーザーの名前 |
| `sql_text_s` | 完全なクエリ テキスト |
| `\_ResourceId` | リソース URI |

### <a name="table-access"></a>テーブル アクセス

> [!NOTE]
> `sql_text_s` の場合、2048 文字を超えたログは切り捨てられます。

| **プロパティ** | **説明** |
|---|---|
| `TenantId` | テナント ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | ログが記録されたときのタイムスタンプ (UTC) |
| `Type` | ログの種類。 常に `AzureDiagnostics` |
| `SubscriptionId` | サーバーが属するサブスクリプションの GUID |
| `ResourceGroup` | サーバーが属するリソース グループの名前 |
| `ResourceProvider` | リソース プロバイダーの名前。 常に `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | リソース URI |
| `Resource` | サーバーの名前 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | サーバーの名前 |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`、`INSERT`、`UPDATE`、または `DELETE` |
| `connection_id_d` | MySQL によって生成された一意の接続 ID |
| `db_s` | アクセスしたデータベースの名前 |
| `table_s` | アクセスしたテーブルの名前 |
| `sql_text_s` | 完全なクエリ テキスト |
| `\_ResourceId` | リソース URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Azure Monitor ログのログを分析する

監査ログが診断ログによって Azure Monitor ログにパイプされたら、監査されたイベントの詳細な分析を実行できます。 使用を開始する際に役立つサンプル クエリを以下にいくつか示します。 以下を、お使いのサーバー名で更新してください。

- 特定のサーバーの GENERAL イベントを一覧表示する

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- 特定のサーバーの CONNECTION イベントを一覧表示する

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- 特定のサーバーの監査されたイベントを集計する

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- 特定のサーバーの監査イベントの種類の分布をグラフ化する

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- 監査ログに対して診断ログが有効になっているすべての MySQL サーバーで監査されたイベントを一覧表示する

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>次のステップ
- [低速クエリ ログ](concepts-slow-query-logs.md)の詳細情報
- [Azure portal](how-to-configure-audit-logs-portal.md) の監査クエリ ログを構成する
<!-- - [How to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)-->
