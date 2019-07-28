---
title: Azure SQL Database へのオンライン移行に関する既知の問題と移行の制限事項に関する記事 | Microsoft Docs
description: Azure SQL Database へのオンライン移行に関する既知の問題と移行の制限事項について学習します。
services: database-migration
author: hjtoland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/09/2019
ms.openlocfilehash: 00ed2f20884c3cd8f49307bd726f14f3007f884f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60534427"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-db"></a>Azure SQL DB へのオンライン移行に関する既知の問題と移行の制限事項

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

**対処法**

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

**対処法**

1. 次のクエリを使用して、hierarchyid データ型の列を含むユーザー テーブルを検索します。

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ``` 

2. 移行するテーブルを指定する **[Configure migration settings]\(移行設定の構成\)** ブレードからこれらのテーブルを除外します。

3. 移行アクティビティを再実行します。

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>"全体のデータの読み込み" または "増分データ同期" 中のスキーマ内のアクティブ トリガーに関する各種の整合性違反による移行エラー

**対処法**

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

**対処法**

32 KB を超える LOB 列がある場合は、[Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) でエンジニアリング チームに相談してください。

### <a name="issues-with-timestamp-columns"></a>タイムスタンプ列に関する問題

**症状:**

DMS は、ソースのタイムスタンプ値を移行しません。代わりに、DMS は、ターゲットのテーブルに新しいタイムスタンプ値を生成します。

**対処法**

DMS でソース テーブルに格納されている正確なタイムスタンプ値を移行する必要がある場合は、[Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) でエンジニアリング チームに相談してください。

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>データ移行エラーが発生した場合、データベースの詳細な状態ブレードに詳細が表示されません。

**症状:**

データベースの詳細状態ビューに移行エラーが表示された場合、上部のリボンの **[Data migration errors]\(データ移行エラー\)** リンクを選択しても移行エラーに関する詳細が表示されないことがあります。

![データ移行エラー時に詳細が表示されない例](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**対処法**

特定のエラーの詳細を取得するには、次の手順に従います。

1. データベースの詳細状態ブレードを閉じて、移行アクティビティ画面を表示します。

     ![移行アクティビティ画面](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. **[エラー詳細の参照]** を選択して、移行エラーのトラブルシューティングに役立つ特定のエラー メッセージを表示します。
