---
title: 既知の問題:SQL Database へのオンライン移行
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service を使用した Azure SQL Database へのオンライン移行に関する既知の問題と移行の制限について説明します。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: da0c00f0b4a8f2c49996fbcb9b34ee4a1ab65273
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85856648"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Azure SQL Database へのオンライン移行に関する既知の問題と移行の制限事項

ここでは、SQL Server から Azure SQL Database へのオンライン移行に関する既知の問題点と制限事項について説明します。

> [!IMPORTANT]
> SQL Server から Azure SQL Database へのオンライン移行では、SQL_variant データ型の移行はサポートされていません。

### <a name="migration-of-temporal-tables-not-supported"></a>テンポラル テーブルの移行はサポートされていません

**症状:**

ソース データベースが 1 つ以上のテンポラル テーブルで構成されている場合、"全体のデータの読み込み" 操作中にデータベースの移行が失敗し、次のメッセージが表示されることがあります。

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![テンポラル テーブルのエラーの例](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**回避策**

次の手順に従います。

1. 次のクエリを使用して、ソース スキーマ内のテンポラル テーブルを検索します。

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. 移行するテーブルを指定する **[Configure migration settings]\(移行設定の構成\)** ブレードからこれらのテーブルを除外します。

3. 移行アクティビティを再実行します。

**リソース**

詳細については、「[テンポラル テーブル](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017)」を参照してください。

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>テーブルの移行には、hierarchyid データ型を持つ 1 つまたは複数の列が含まれます

**症状:**

"全体のデータの読み込み" 操作中に、"ntext が hierarchyid と非互換である" ことを示す SQL 例外が表示されることがあります。

![hierarchyid のエラーの例](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**回避策**

次の手順に従います。

1. 次のクエリを使用して、hierarchyid データ型の列を含むユーザー テーブルを検索します。

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. 移行するテーブルを指定する **[Configure migration settings]\(移行設定の構成\)** ブレードからこれらのテーブルを除外します。

3. 移行アクティビティを再実行します。

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>"全体のデータの読み込み" または "増分データ同期" 中のスキーマ内のアクティブ トリガーに関する各種の整合性違反による移行エラー

**回避策**

次の手順に従います。

1. 次のクエリを使用して、ソース データベースで現在アクティブなトリガーを検索します。

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. 「[DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017)」に記載されている手順を使用して、ソース データベースのトリガーを無効にします。

3. 移行アクティビティを再実行します。

### <a name="support-for-lob-data-types"></a>LOB データ型のサポート

**症状:**

ラージ オブジェクト (LOB) 列の長さが 32 KB を超える場合、ターゲットにおいてデータが切り捨てられることがあります。 次のクエリを使用して、LOB 列の長さを確認できます。

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**回避策**

32 KB を超える LOB 列がある場合は、[Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) でエンジニアリング チームに相談してください。

### <a name="issues-with-timestamp-columns"></a>タイムスタンプ列に関する問題

**症状:**

Azure Database Migration Service によって、ソース タイムスタンプ値が移行されるのではなく、ターゲット テーブルに新しいタイムスタンプ値が生成されます。

**回避策**

ソース テーブルに格納されている正確なタイムスタンプ値を移行するために Azure Database Migration Service が必要な場合は、[Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) でエンジニアリング チームに相談してください。

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>データ移行エラーが発生した場合、データベースの詳細な状態ブレードに詳細が表示されません

**症状:**

データベースの詳細状態ビューに移行エラーが表示された場合、上部のリボンの **[Data migration errors]\(データ移行エラー\)** リンクを選択しても移行エラーに関する詳細が表示されないことがあります。

![データ移行エラー時に詳細が表示されない例](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**回避策**

特定のエラーの詳細を表示するには、次の手順を実行します。

1. データベースの詳細状態ブレードを閉じて、移行アクティビティ画面を表示します。

     ![移行アクティビティ画面](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. **[エラー詳細の参照]** を選択して、移行エラーのトラブルシューティングに役立つ特定のエラー メッセージを表示します。

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>geography データ型が SQLDB オンライン移行でサポートされていません

**症状:**

移行が失敗し、次のテキストを含むエラー メッセージが表示されます。

```output
"** encountered a fatal error", "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode.
```

**回避策**

Azure Database Migration Service では Azure SQL Database へのオフライン移行で geography データ型がサポートされているのに対し、オンライン移行では、geography データ型がサポートされていません。 このデータベースのオンライン移行に Azure Database Migration Service を使用するには、ソースのデータ型をサポートされている型に変更するための代替手段を試してください。

### <a name="supported-editions"></a>サポートされているエディション

**症状:**

移行が失敗し、次のテキストを含むエラー メッセージが表示されます。

```output
Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].
```

**回避策**

Azure Database Migration Service を使用した Azure SQL Database へのオンライン移行のサポートは、Enterprise、Standard、Developer の各エディションにのみ拡張されています。 移行プロセスを開始する前に、サポートされているエディションを使用していることを確認してください。
