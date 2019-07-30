---
title: Azure SQL Data Warehouse リリース ノート 2018 年 6 月 | Microsoft Docs
description: Azure SQL Data Warehouse のリリース ノート。
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/23/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 4348a634fd5b2b33f36d8e79f28caf659b82ccf4
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626147"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>Azure SQL Data Warehouse の新機能 2018 年 6 月
Azure SQL Data Warehouse では、継続的に機能強化を図っています。 この記事では、2018 年 6 月に導入された新しい機能と変更点について説明します。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="user-defined-restore-points"></a>ユーザー定義の復元ポイント
SQL Data Warehouse では 8 時間の目標復旧時点 (RPO) を保証するために、データ ウェアハウスのスナップショットが 8 時間ごとに自動的に取得されます。 このようなスナップショットの自動化により、データ ウェアハウス操作の管理上の負担が軽減されますが、ビジネスニーズに基づいて、重要な場面でスナップショットを作成する必要があります。 たとえば、データ ウェアハウスへの大量のデータの読み込みまたは新しいスクリプトのデプロイの直前にスナップショットを作成して、操作の直前の復元ポイントを有効にすることができます。 

SQL Data Warehouse では、[New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint) コマンドレットにより、[ユーザー定義の復元ポイント](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/)をサポートするようになりました。

```powershell
New-AzSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>列 レベルのセキュリティ
データ ウェアハウス内のデータ アクセスおよびセキュリティを管理することは、顧客やパートナーと信頼をビルドするために不可欠です。 SQL Data Warehouse では、データ ウェアハウスを再設計しなくてもテーブル内の特定の列へのユーザー アクセスを制限することで機密データを表示するアクセス許可を調整できる、[列レベルのセキュリティ (CLS) をサポートする](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/)ようになりました。

CLS では、標準の [GRANT](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) T-SQL ステートメントを使用することによって、ユーザーの実行コンテキストまたはグループ メンバーシップに基づいて、テーブルの列へのアクセスを制御できます。 アクセス制限のロジックは、別のアプリケーションのデータから離れた場所ではなく、データベース層そのものに配置されているため、全般的なセキュリティ実装が簡素化されます。


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="objectschemaname"></a>OBJECT_SCHEMA_NAME
[OBJECT_SCHEMA_NAME()](https://docs.microsoft.com/sql/t-sql/functions/object-schema-name-transact-sql) 関数は、スキーマ スコープ オブジェクトのデータベース スキーマ名を返します。 この関数は、オブジェクト スキーマの検証を実行するときに ETL ツールの共通関数となります。 

```sql
SELECT
    OBJECT_SCHEMA_NAME([object_id]) [table_schema]
    , [name]                        [table_name]
FROM
    [sys].[tables];
```

**結果のサンプル**
```
table_schema    | table_name
-----------------------------
dbo               customer
dbo               lineitem
dbo               nation
dbo               orders
```

## <a name="support-for-the-systimezoneinfo-view"></a>Sys.time_zone_info ビューのサポート
[Sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) ビューは、Azure SQL Data Warehouse 内でサポートされているタイム ゾーンに関する情報を返します。

```sql
SELECT * FROM [sys].[time_zone_info];
```

**結果のサンプル**
```
name                            | current_utc_offset | is_currently_dst
-------------------------------------------------------------------------
Pacific Standard Time (Mexico)    -07:00               1
US Mountain Standard Time         -07:00               0
Mountain Standard Time (Mexico)   -06:00               1
Central Standard Time             -05:00               1
```

## <a name="auto-stats-operations-appear-in-sysdmpdwexecrequests-behavior-change"></a>sys.dm_pdw_exec_requests に表示される Auto Stats 操作 (動作変更)

[統計の自動作成](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic)の導入に伴い、Azure SQL Data Warehouse ではクエリの実行を最適化するために統計を生成します。 2018 年 6 月リリースには、 [CREATE STATISTICS](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) 操作が実行されたときにレコードを [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) に追加することによって、統計が自動生成されるタイミングを監視する機能が追加されています。

```sql
SELECT
    [start_time]
    , [end_time]
    , [command]
FROM
    [sys].[dm_pdw_exec_requests]
WHERE
    [command] LIKE 'CREATE STATISTICS _WA_Sys%';
```
**結果のサンプル**
```
start_time                | end_time                | command
------------------------------------------------------------------------------------------------------------------------------
2018-06-06 19:06:26.173    2018-06-06 19:06:26.173    CREATE STATISTICS _WA_Sys_00000001_63D998CC ON dbo.LineItem(l_orderkey);
```

## <a name="next-steps"></a>次の手順
SQL Data Warehouse の概要については学習したので、次は [SQL Data Warehouse の迅速な作成方法について学習してください。][create a SQL Data Warehouse]. If you are new to Azure, you may find the [Azure glossary][Azure glossary] が新しい用語を調べるには役立ちます。 または、次の SQL Data Warehouse リソースも確認できます。  

* [顧客の成功事例]
* [ブログ]
* [機能に関する要求]
* [ビデオ]
* [Customer Advisory Team のブログ]
* [Stack Overflow フォーラム]
* [Twitter]


[ブログ]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team のブログ]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[顧客の成功事例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[機能に関する要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow フォーラム]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[ビデオ]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
