---
title: "SQL Data Warehouse のテーブルの概要 | Microsoft Docs"
description: "Azure SQL Data Warehouse テーブルの概要です。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 2114d9ad-c113-43da-859f-419d72604bdf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess;jrj
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fe62d672ebd4c6d9f5e161e337dbace0c80f67a5


---
# <a name="overview-of-tables-in-sql-data-warehouse"></a>SQL Data Warehouse のテーブルの概要
> [!div class="op_single_selector"]
> * [概要][概要]
> * [データ型][データ型]
> * [分散][分散]
> * [Index][Index]
> * [Partition][Partition]
> * [統計][統計]
> * [一時的な][一時]
> 
> 

SQL Data Warehouse でテーブルを作成してみるのは簡単です。  基本的な [CREATE TABLE][CREATE TABLE] 構文は、他のデータベースで既に馴染みのある一般的な構文に従います。  テーブルを作成するには、単にテーブルと列に名前を付けて、各列のデータ型を定義するだけです。  他のデータベースでテーブルを作成したことがある場合は、次のコードに見覚えがあると思います。

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

上の例では、FirstName と LastName の 2 つの列を持つ Customers という名前のテーブルが作成されます。  各列は、データ型 VARCHAR(25) で定義されており、データは 25 文字に制限されます。  テーブルなどの基本的な属性は、ほぼ他のデータベースと同じです。  データ型は、列ごとに定義して、データの整合性を確保します。  インデックスは、I/O を減らすことでパフォーマンスを向上させるために追加できます。  パーティション分割は、データを変更する必要があるときに、パフォーマンスを向上させるために追加できます。

SQL Data Warehouse のテーブルの[名前の変更][RENAME]は次のように実行します。

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>分散テーブル
SQL Data Warehouse のような分散システムで新たに導入された基本的な属性が、 **ディストリビューション列**です。  ディストリビューション列は、その名前のとおりです。  データをバックグラウンドで分散または分割する方法を決定する列のことです。  ディストリビューション列を指定しないでテーブルを作成すると、テーブルは **ラウンド ロビン**を使用して自動的に分散されます。  一部のシナリオではラウンド ロビン テーブルで十分ですが、ディストリビューション列を定義すると、クエリを実行するときのデータ移動を大幅に削減してパフォーマンスを最適化できます。  ディストリビューション列を選択する方法の詳細については、 [テーブルの分散][分散] に関するページを参照してください。

## <a name="indexing-and-partitioning-tables"></a>テーブルのインデックス作成とパーティション分割
SQL Data Warehouse を使用するスキルが高くなり、パフォーマンスの最適化が必要になると、テーブル設計の詳細について学ぶ必要性が生じます。  詳細については、[テーブルのデータ型][データ型]、[テーブルの分散][分散]、[テーブルのインデックス作成][Index]、[テーブルのパーティション分割][Partition]に関する各記事をご覧ください。

## <a name="table-statistics"></a>テーブルの統計
統計は、SQL Data Warehouse で最高のパフォーマンスを実現するために非常に重要です。  Azure SQL Database で期待していたかもしれませんが、SQL Data Warehouse では統計はまだ自動的に作成および更新されないため、[統計][統計]に関する記事を読んでください。この記事は、クエリで最高のパフォーマンスを得るために読む必要がある最も重要な記事の 1 つです。

## <a name="temporary-tables"></a>一時テーブル
一時テーブルは、ログオン時間中にのみ存在するテーブルであり、他のユーザーが表示することはできません。  一時テーブルは、一時的な結果を他のユーザーが確認できないようにし、クリーンアップの必要性を減らすこともできる優れた手段です。  一時テーブルはローカル ストレージも活用するため、一部の操作でパフォーマンスを向上させることができます。  一時テーブルの詳細については、 [一時テーブル][一時] に関する記事を参照してください。

## <a name="external-tables"></a>外部テーブル
外部テーブルは、PolyBase テーブルとも呼ばれ、SQL Data Warehouse からクエリを実行できますが、SQL Data Warehouse の外部のデータをポイントします。  たとえば、Azure BLOB ストレージのファイルを指す外部テーブルを作成できます。  外部テーブルを作成および照会する方法の詳細については、[PolyBase でのデータの読み込み] [PolyBase でのデータの読み込み]に関するページをご覧ください。  

## <a name="unsupported-table-features"></a>サポートされていないテーブルの機能
SQL Data Warehouse には、他のデータベースで提供されているのと同じテーブルの機能の多くが含まれますが、まだサポートされていない機能もあります。  まだサポートされていないテーブル機能の一部を以下の一覧に示します。

| サポートされていない機能 |
| --- |
| [ID プロパティ][ID プロパティ] ([代理キーの回避策の割り当て][代理キーの回避策の割り当て]をご覧ください) |
| PRIMARY KEY、FOREIGN KEY、UNIQUE、CHECK の各[テーブル制約][テーブル制約] |
| [一意のインデックス][一意のインデックス] |
| [計算列][計算列] |
| [スパース列][スパース列] |
| [ユーザー定義型][ユーザー定義型] |
| [シーケンス][シーケンス] |
| [トリガー][トリガー] |
| [インデックス付きビュー][インデックス付きビュー] |
| [シノニム][シノニム] |

## <a name="table-size-queries"></a>テーブル サイズのクエリ
60 個のディストリビューションのそれぞれで各テーブルによって消費される領域と行を簡単に識別する方法の 1 つが、[DBCC PDW_SHOWSPACEUSED][DBCC PDW_SHOWSPACEUSED] です。

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

ただし、DBCC コマンドを使用できるのは極めて限定的です。  動的管理ビュー (DMV) を使用すると、クエリ結果を詳しく表示できるだけでなく、非常にきめ細かく制御することができます。  まずこのビューを作成し、この記事やその他の記事にある多くのサンプルから参照します。

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>テーブル領域の概要
次のクエリはテーブルごとに行と領域を返します。  これは、最大規模のテーブルや、各テーブルがラウンド ロビンとハッシュ分散のどちらであるかを示す優れたクエリです。  ハッシュ分散テーブルの場合、ディストリビューション列も示されます。  ほとんどの場合、最大規模のテーブルは、クラスター化列ストア インデックスを持つハッシュ分散テーブルです。

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>ディストリビューションの種類別のテーブル領域
```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>インデックスの種類別のテーブル領域
```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>ディストリビューション領域の概要
```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>次のステップ
[テーブルのデータ型][データ型]、[テーブルの分散][分散]、[テーブルのインデックス作成][Index]、[テーブルのパーティション分割][Partition]、[テーブル統計の更新][統計]、[一時テーブル][一時]に関する各記事で詳細をご覧ください。  その他のベスト プラクティスについては、[SQL Data Warehouse のベスト プラクティス][SQL Data Warehouse のベスト プラクティス]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[概要]: ./sql-data-warehouse-tables-overview.md
[データ型]: ./sql-data-warehouse-tables-data-types.md
[分散]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[統計]: ./sql-data-warehouse-tables-statistics.md
[一時]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse のベスト プラクティス]: ./sql-data-warehouse-best-practices.md
[PolyBase でのデータの読み込み]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[ID プロパティ]: https://msdn.microsoft.com/library/ms186775.aspx
[代理キーの回避策の割り当て]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/18/assigning-surrogate-key-to-dimension-tables-in-sql-dw-and-aps/
[テーブル制約]: https://msdn.microsoft.com/library/ms188066.aspx
[計算列]: https://msdn.microsoft.com/library/ms186241.aspx
[スパース列]: https://msdn.microsoft.com/library/cc280604.aspx
[ユーザー定義型]: https://msdn.microsoft.com/library/ms131694.aspx
[シーケンス]: https://msdn.microsoft.com/library/ff878091.aspx
[トリガー]: https://msdn.microsoft.com/library/ms189799.aspx
[インデックス付きビュー]: https://msdn.microsoft.com/library/ms191432.aspx
[シノニム]: https://msdn.microsoft.com/library/ms177544.aspx
[一意のインデックス]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


