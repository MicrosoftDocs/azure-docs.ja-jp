---
title: SQL Database 監査ログの形式
description: Azure SQL Database 監査ログの構成方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: reference
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 06/03/2020
ms.openlocfilehash: f5c176db4f679c79bb42c6ceb46b3588e9440874
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572218"
---
# <a name="sql-database-audit-log-format"></a>SQL Database 監査ログの形式

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database の監査](auditing-overview.md)では、データベース イベントを追跡し、それを Azure ストレージ アカウントの監査ログに書き込むか、ダウンストリーム処理および分析のためイベント ハブまたは Log Analytics に送信します。

## <a name="naming-conventions"></a>名前付け規則

### <a name="blob-audit"></a>BLOB 監査

Azure BLOB ストレージに格納されている監査ログは、Azure ストレージ アカウント内の `sqldbauditlogs` と呼ばれるコンテナーに格納されています。 コンテナー内のディレクトリ階層は、`<ServerName>/<DatabaseName>/<AuditName>/<Date>/` という形式になります。 BLOB ファイル名の形式は `<CreationTime>_<FileNumberInSession>.xel` です。ここで `CreationTime` は UTC `hh_mm_ss_ms` 形式で、`FileNumberInSession` はセッション ログが複数の BLOB ファイルにまたがる場合、実行中のインデックスになります。

たとえば、`Server1` にあるデータベース `Database1` の場合、選択可能な有効なパスは以下のようになります：

`Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel`

[読み取り専用レプリカ](read-scale-out.md)の監査ログは、同じコンテナーに格納されます。 コンテナー内のディレクトリ階層は、`<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/` という形式になります。 BLOB ファイル名は同じ形式を共有します。 読み取り専用レプリカの監査ログは、同じコンテナーに格納されます。


### <a name="event-hub"></a>イベント ハブ

監査イベントは、監査の構成中に定義された名前空間とイベント ハブに書き込まれ、[Apache Avro](https://avro.apache.org/) イベントの本体に入れられ、UTF-8 エンコードされた JSON の書式設定を使用して格納されます。 監査ログを読み取るために、[Avro Tools](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) またはこの形式を処理する同様のツールを使用できます。

### <a name="log-analytics"></a>Log Analytics

監査イベントは、監査の構成中に定義された Log Analytics ワークスペースである `AzureDiagnostics` カテゴリを持つテーブル `SQLSecurityAuditEvents`に書き込まれます。 Log Analytics 検索言語およびコマンドに関する有用な追加情報については、[Log Analytics 検索リファレンス](../../azure-monitor/logs/log-query-overview.md)に関するページをご覧ください。

## <a name="audit-log-fields"></a><a id="subheading-1"></a>監査ログのフィールド

| 名前 (BLOB) | 名前 (Event Hubs または Log Analytics) | 説明 | BLOB の種類 | Event Hubs または Log Analytics の種類 |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | アクションの ID | varchar (4) | string |
| action_name | action_name_s | アクションの名前 | 該当なし | string |
| additional_information | additional_information_s | XML として格納されている、イベントに関する追加情報 | nvarchar(4000) | string |
| affected_rows | affected_rows_d | クエリによって影響を受ける行の数 | bigint | INT |
| application_name | application_name_s| クライアント アプリケーションの名前 | nvarchar(128) | string |
| audit_schema_version | audit_schema_version_d | 常に 1 | INT | INT |
| class_type | class_type_s | 監査が発生する監査可能なエンティティの種類 | varchar(2) | string |
| class_type_desc | class_type_description_s | 監査が発生する監査可能なエンティティの説明 | 該当なし | string |
| client_ip | client_ip_s | クライアント アプリケーションのソース IP | nvarchar(128) | string |
| connection_id | 該当なし | サーバーの接続の ID | GUID | 該当なし |
| data_sensitivity_information | data_sensitivity_information_s | データベースにある分類済みの列に基づく、監査済みクエリが返す情報の種類と機密ラベル。 [Azure SQL Database のデータ検出と分類](data-discovery-and-classification-overview.md)の詳細を参照してください。 | nvarchar(4000) | string |
| database_name | database_name_s | アクションが発生したデータベース コンテキスト | sysname | string |
| database_principal_id | database_principal_id_d | アクションが実行されるデータベース ユーザー コンテキストの ID | INT | INT |
| database_principal_name | database_principal_name_s | アクションが実行されるデータベース ユーザー コンテキストの名前 | sysname | string |
| duration_milliseconds | duration_milliseconds_d | クエリ実行時間 (ミリ秒) | bigint | INT |
| event_time | event_time_t | 監査可能なアクションが発生する日付と時刻 | datetime2 | DATETIME |
| host_name | 該当なし | クライアント ホスト名 | string | 該当なし |
| is_column_permission | is_column_permission_s | 列レベルのアクセス許可であるかどうかを示すフラグ。 1 = true、0 = false | bit | string |
| 該当なし | is_server_level_audit_s | この監査がサーバー レベルであるかどうかを示すフラグ | 該当なし | string |
| object_ id | object_id_d | 監査が発生したエンティティの ID。 これには、サーバー オブジェクト、データベース、データベース オブジェクト、およびスキーマ オブジェクトが含まれます。 エンティティがサーバー自体である場合、または監査がオブジェクト レベルで実行されない場合は 0 です。 | INT | INT |
| object_name | object_name_s | 監査が発生したエンティティの名前。 これには、サーバー オブジェクト、データベース、データベース オブジェクト、およびスキーマ オブジェクトが含まれます。 エンティティがサーバー自体である場合、または監査がオブジェクト レベルで実行されない場合は 0 です。 | sysname | string |
| permission_bitmask | permission_bitmask_s | 該当する場合、許可、拒否、または取り消されたアクセス許可を表示します。 | varbinary(16) | string |
| response_rows | response_rows_d | 結果セットで返された行の数 | bigint | INT |
| schema_name | schema_name_s | アクションが発生したスキーマ コンテキスト。 監査がスキーマの外部で発生している場合は NULL です。 | sysname | string |
| 該当なし | securable_class_type_s | 監査対象の class_type にマップされるセキュリティ保護可能なオブジェクト | 該当なし | string |
| sequence_group_id | sequence_group_id_g | 一意識別子 | varbinary | GUID |
| sequence_number | sequence_number_d | 監査の書き込みバッファーには大きすぎて収まらない 1 つの監査レコード内のレコードのシーケンスを追跡します | INT | INT |
| server_instance_name | server_instance_name_s | 監査が発生したサーバー インスタンスの名前 | sysname | string |
| server_principal_id | server_principal_id_d | アクションが実行されるログイン コンテキストの ID | INT | INT |
| server_principal_name | server_principal_name_s | 現在のログイン | sysname | string |
| server_principal_sid | server_principal_sid_s | 現在のログインの SID | varbinary | string |
| session_id | session_id_d | イベントが発生したセッションの ID | smallint | INT |
| session_server_principal_name | session_server_principal_name_s | セッションのサーバー プリンシパル | sysname | string |
| statement | statement_s | 実行された T-SQL ステートメント (該当する場合) | nvarchar(4000) | string |
| succeeded | succeeded_s | イベントをトリガーしたアクションが成功したかどうかを示します。 ログインとバッチ以外のイベントの場合、操作ではなくアクセス許可のチェックが成功したか失敗したかのみを報告します。 1 = 成功、0 = 失敗 | bit | string |
| target_database_principal_id | target_database_principal_id_d | 許可、拒否、取り消し操作が実行されるデータベース プリンシパル。 該当しない場合は 0 | INT | INT |
| target_database_principal_name | target_database_principal_name_s | アクションの対象ユーザー。 該当しない場合は NULL です。 | string | string |
| target_server_principal_id | target_server_principal_id_d | 許可、拒否または取り消し操作が実行されるサーバー プリンシパル。 該当しない場合、0 を返します。 | INT | INT |
| target_server_principal_name | target_server_principal_name_s | アクションの対象ログイン。 該当しない場合は NULL です。 | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | 対象ログインのセキュリティ ID。 該当しない場合は NULL です。 | varbinary | string |
| transaction_id | transaction_id_d | SQL Server のみ (2016 年以降) - Azure SQL Database の場合は 0 | bigint | INT |
| user_defined_event_id | user_defined_event_id_d | sp_audit_write に引数として渡されたユーザー定義のイベント ID。 システム イベントの場合は NULL (既定値) で、ユーザー定義のイベントの場合は 0 以外です。 詳細については、「[sp_audit_write (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql)」を参照してください。 | smallint | INT |
| user_defined_information | user_defined_information_s | sp_audit_write に引数として渡されたユーザー定義の情報。 システム イベントの場合は NULL (既定値) で、ユーザー定義のイベントの場合は 0 以外です。 詳細については、「[sp_audit_write (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql)」を参照してください。 | nvarchar(4000) | string |

## <a name="next-steps"></a>次のステップ

詳細については、「[Azure SQL Database 監査](auditing-overview.md)」を参照してください