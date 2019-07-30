---
title: Azure Database for MySQL の監査ログ
description: Azure Database for MySQL で利用できる監査ログと、各種ログ レベルを有効にするため利用可能なパラメーターについて説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 86750cea5e7f0d4726f3e0e9a03795ef2a602d8b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443842"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Azure Database for MySQL の監査ログ

Azure Database for MySQL では、ユーザーは監査ログを使用できます。 監査ログは、データベースレベルのアクティビティを追跡するために使用でき、コンプライアンスのためによく使用されます。

> [!IMPORTANT]
> 監査ログ機能は現在、プレビュー段階です。

## <a name="configure-audit-logging"></a>監査ログを構成する

既定では、監査ログは無効です。 有効にするには、`audit_log_enabled` を ON に設定します。

調整できるその他のパラメーターは次のとおりです。

- `audit_log_events`: 記録するイベントを制御します。 特定の監査イベントについては、次のを参照してください。
- `audit_log_exclude_users`:ログ記録から除外する MySQL ユーザー。 最大で 4 人のユーザーを指定できます。 パラメーターの最大長は 256 文字です。

| **Event** | **説明** |
|---|---|
| `CONNECTION` | - 接続開始 (成功または失敗) <br> - 異なるユーザーとパスワードを使用するセッション中のユーザーの再認証 <br> - 接続終了 |
| `DML_SELECT`| SELECT クエリ |
| `DML_NONSELECT` | INSERT、DELETE、UPDATE クエリ |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | "DROP DATABASE" のようなクエリ |
| `DCL` | "GRANT PERMISSION" のようなクエリ |
| `ADMIN` | "SHOW STATUS" のようなクエリ |
| `GENERAL` | DML_SELECT、DML_NONSELECT、DML、DDL、DCL、および ADMIN のすべて |
| `TABLE_ACCESS` | - MySQL 5.7 でのみ利用可能 <br> テーブル読み取りステートメント。たとえば、SELECT、INSERT INTO ...SELECT <br> - テーブル削除ステートメント。たとえば、DELETE、TRUNCATE TABLE <br> - テーブル挿入ステートメント。たとえば、INSERT、REPLACE <br> - テーブル更新ステートメント。たとえば、UPDATE |

## <a name="access-audit-logs"></a>監査ログにアクセスする

監査ログは、Azure Monitor の診断ログと統合されます。 MySQL サーバーで監査ログを有効にしたら、Azure Monitor ログ、Event Hubs、または Azure Storage にそれらを出力できます。 Azure portal で診断ログを有効にする方法の詳細については、[監査ログに関するポータルの記事](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)を参照してください。

## <a name="diagnostic-logs-schemas"></a>診断ログのスキーマ

次のセクションでは、イベントの種類に基づいて MySQL 監査ログによって出力される内容について説明します。 出力方法に応じて、含まれるフィールドとそれらが表示される順序が異なることがあります。

### <a name="connection"></a>接続

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
| `event_subclass_s` | `CONNECT`、`DISCONNECT`、`CHANGE USER` (MySQL 5.7 でのみ利用可能) |
| `connection_id_d` | MySQL によって生成された一意の接続 ID |
| `host_s` | 空白 |
| `ip_s` | MySQL に接続しているクライアントの IP アドレス |
| `user_s` | クエリを実行しているユーザーの名前 |
| `db_s` | 接続先のデータベースの名前 |
| `\_ResourceId` | リソース URI |

### <a name="general"></a>全般

以下のスキーマは、GENERAL、DML_SELECT、DML_NONSELECT、DML、DDL、DCL、および ADMIN の各イベントの種類に適用されます。

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
| `event_time` | UNIX タイムスタンプのクエリ開始秒 |
| `error_code_d` | エラー コード (クエリが失敗した場合)。 `0` は、エラーなしを意味します |
| `thread_id_d` | クエリを実行したスレッドの ID |
| `host_s` | 空白 |
| `ip_s` | MySQL に接続しているクライアントの IP アドレス |
| `user_s` | クエリを実行しているユーザーの名前 |
| `sql_text_s` | 完全なクエリ テキスト |
| `\_ResourceId` | リソース URI |

### <a name="table-access"></a>テーブル アクセス

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

## <a name="next-steps"></a>次の手順

- [Azure portal で監査ログを構成する方法](howto-configure-audit-logs-portal.md)