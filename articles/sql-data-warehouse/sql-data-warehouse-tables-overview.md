---
title: テーブルの設計 - Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Data Warehouse でのテーブル設計の概要。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: d97326430eebcaea64770e99c26ab593b51d5847
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68476744"
---
# <a name="designing-tables-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse でのテーブルの設計

Azure SQL Data Warehouse でのテーブル設計について重要な概念を説明します。 

## <a name="determine-table-category"></a>テーブル カテゴリを決定する 

[スター スキーマ](https://en.wikipedia.org/wiki/Star_schema)は、データをファクト テーブルとディメンション テーブルに編成します。 一部のテーブルは、ファクト テーブルまたはディメンション テーブルに移動する前に統合またはステージング データに使用されます。 テーブルを設計する際には、テーブルのデータがファクト、ディメンション、統合のいずれのテーブルに属するかを決定します。 この決定は、適切なテーブル構造体および配布を通知します。 

- **ファクト テーブル**には、一般にトランザクション システムで生成された後、データ ウェアハウスに読み込まれる定量的データが含まれています。 たとえば、小売業では販売トランザクションを毎日生成した後、そのデータを分析のためにデータ ウェアハウス ファクト テーブルに読み込みます。

- **ディメンション テーブル**には、変化する可能性はあるが通常は変更頻度が低い属性データが含まれます。 たとえば、顧客の名前と住所はディメンション テーブルに格納され、その顧客のプロファイルが変更された場合にのみ更新されます。 大規模なファクト テーブルのサイズを最小限に抑えるために、顧客の名前と住所をファクト テーブルのすべての行に格納する必要はありません。 代わりに、ファクト テーブルとディメンション テーブルで顧客 ID を共有できます。 クエリで 2 つのテーブルを結合して、顧客のプロファイルとトランザクションに関連付けることができます。 

- **統合テーブル**は、統合またはステージング データの場所を提供します。 統合テーブルは、通常のテーブル、外部テーブル、または一時テーブルとして作成できます。 たとえば、ステージング テーブルにデータを読み込み、ステージングでデータの変換を実行してから、データを運用環境テーブルに挿入できます。

## <a name="schema-and-table-names"></a>スキーマとテーブルの名前
スキーマは、同じ方法で使用されるテーブルをグループ化するのに良い方法です。  複数のデータベースをオンプレミス ソリューションから SQL Data Warehouse に移行する場合は、ファクト テーブル、ディメンション テーブル、および統合テーブルのすべてを SQL Data Warehouse 内の 1 つのスキーマに移行すると一番うまくいきます。 たとえば、すべてのテーブルを、wwi と呼ばれる 1 つのスキーマ内の [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap) サンプル データ ウェアハウスに格納できます。 次のコードは、wwi と呼ばれる[ユーザー定義スキーマ](/sql/t-sql/statements/create-schema-transact-sql)を作成します。

```sql
CREATE SCHEMA wwi;
```

SQL Data Warehouse 内のテーブルの構成を表示するには、テーブル名のプレフィックスとして fact、dim、および int を使用できます。 次の表に、WideWorldImportersDW のスキーマ名とテーブル名の一部を示します。  

| WideWorldImportersDW テーブル  | テーブルの種類 | SQL Data Warehouse |
|:-----|:-----|:------|:-----|
| City | Dimension | wwi.DimCity |
| 順序 | ファクト | wwi.FactOrder |


## <a name="table-persistence"></a>テーブルの永続性 

テーブルは、データを Azure Storage に永続的に格納するか、Azure Storage に一時的に格納するか、またはデータ ウェアハウスの外部にあるデータ ストアに格納するかのいずれかです。

### <a name="regular-table"></a>通常のテーブル

通常のテーブルは、データ ウェアハウスの一部として Azure Storage にデータを格納します。 セッションが開いているかどうかに関係なく、テーブルとデータが保持されます。  この例では、2 つの列を含む通常のテーブルを作成します。 

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>一時テーブル
一時テーブルは、セッション中のみ存在します。 一時テーブルを使用して、一時的な結果を他のユーザーが確認できないようにしたり、クリーンアップの必要性を減らしたりすることもできます。  一時テーブルはローカル ストレージを使用して高速のパフォーマンスを提供します。  詳しくは、[一時テーブル](sql-data-warehouse-tables-temporary.md)に関する記事をご覧ください。

### <a name="external-table"></a>外部テーブル
外部テーブルは、Azure Storage BLOB または Azure Data Lake Store にあるデータを指します。 CREATE TABLE AS SELECT ステートメントと組み合わせて使用する場合は、外部テーブルから選択するとデータが SQL Data Warehouse にインポートされます。 このため、外部テーブルはデータを読み込むのに役立ちます。 読み込みのチュートリアルについては、「[PolyBase を使用して Azure Blob Storage から Azure SQL Data Warehouse にデータを読み込む](load-data-from-azure-blob-storage-using-polybase.md)」をご覧ください。

## <a name="data-types"></a>データの種類
SQL Data Warehouse では、最もよく使用されるデータ型がサポートされています。 サポートされるデータ型の一覧については、CREATE TABLE ステートメントの「[data types in CREATE TABLE reference (CREATE TABLE 内のデータ型のリファレンス)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes)」を参照してください。 データ型の使用に関するガイダンスについては、「[データ型](sql-data-warehouse-tables-data-types.md)」を参照してください。

## <a name="distributed-tables"></a>分散テーブル
SQL Data Warehouse の基本的特徴は、[複数のディストリビューション](massively-parallel-processing-mpp-architecture.md#distributions)にわたるテーブルを格納して操作できる方法にあります。  SQL Data Warehouse では、データを分散するための 3 つの方法 (ラウンド ロビン (既定値)、ハッシュ、レプリケート) がサポートされています。

### <a name="hash-distributed-tables"></a>ハッシュ分散テーブル
ハッシュ分散テーブルは、ディストリビューション列内の値に基づいて行を分散します。 ハッシュ分散テーブルは、大規模なテーブルのクエリでハイパフォーマンスを達成するように設計されています。 ディストリビューション列を選択する際に検討すべきいくつかの要素があります。 

詳細については、「[Design guidance for distributed tables (分散テーブルの設計ガイダンス)](sql-data-warehouse-tables-distribute.md)」を参照してください。

### <a name="replicated-tables"></a>レプリケート テーブル
レプリケート テーブルには、すべてのコンピューティング ノードで使用可能なテーブルの完全なコピーが含まれています。 レプリケート テーブルの結合ではデータ移動が不要であるため、レプリケート テーブルではクエリが高速に実行されます。 ただし、レプリケーションには余分なストレージが必要であり、大きなテーブルには適していません。 

詳しくは、[レプリケート テーブルを使用するための設計ガイダンス](design-guidance-for-replicated-tables.md)に関する記事をご覧ください。

### <a name="round-robin-tables"></a>ラウンドロビン テーブル
ラウンドロビン テーブルは、すべてのディストリビューションにわたって均等にテーブル行を分散させます。 これらの行は、ランダムに分散されます。 ラウンドロビン テーブルへのデータの読み込みは高速です。  ただし、クエリは他の分散方法より多くのデータ移動を要求できます。 

詳細については、「[Design guidance for distributed tables (分散テーブルの設計ガイダンス)](sql-data-warehouse-tables-distribute.md)」を参照してください。

### <a name="common-distribution-methods-for-tables"></a>テーブルの一般的な分散方法
テーブル カテゴリは、多くの場合、テーブルの分散について選択するオプションを決定します。 

| テーブル カテゴリ | 推奨される分散オプション |
|:---------------|:--------------------|
| ファクト           | クラスター化列ストア インデックスによるハッシュ分散を使用します。 2 つのハッシュ テーブルが同じディストリビューション列に結合される場合にパフォーマンスが向上します。 |
| Dimension      | 小さなテーブルにはレプリケートを使用します。 各コンピューティング ノードに保存するにはテーブルが大きすぎる場合は、ハッシュ分散を使用します。 |
| ステージング        | ステージング テーブルにはラウンド ロビンを使用します。 CTAS での読み込みが高速です。 データがステージング テーブルに格納されたら、INSERT...SELECT を使用してデータを運用環境テーブルに移動します。 |

## <a name="table-partitions"></a>テーブルのパーティション
パーティション テーブルでは、データ範囲に基づいてテーブル行が格納され、操作が実行されます。 たとえば、day、month、または year でテーブルをパーティション分割できます。 クエリ スキャンをあるパーティション内のデータに制限するパーティション除外によってクエリ パフォーマンスを向上させることができます。 パーティションを切り替えてデータを維持することもできます。 SQL Data Warehouse のデータは既に分散されているため、パーティションが多すぎるとクエリ パフォーマンスが低下することがあります。 詳しくは、[パーティション分割のガイダンス](sql-data-warehouse-tables-partition.md)に関する記事をご覧ください。  空でないテーブル パーティションにパーティションを切り替えるとき、既存のデータが切り詰められる場合は、[ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) ステートメントに TRUNCATE_TARGET オプションを使用することを検討してください。 以下のコードは、既存のデータを上書きして、変換された 1 日のデータを SalesFact にスイッチインします。 

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>列ストア インデックス
既定では、SQL Data Warehouse には、テーブルがクラスター化列ストア インデックスとして格納されます。 この形式のデータ ストレージでは、大きなテーブルに対する高いデータ圧縮およびクエリ パフォーマンスが実現されます。  クラスター化列ストア インデックスは、通常は最適な選択肢ですが、場合によっては、クラスター化インデックスまたはヒープが適切なストレージ構造体の場合もあります。  ヒープ テーブルは、最終的なテーブルに変換されるステージング テーブルなどの一時的なデータを読み込むのに特に役立ちます。

列ストア機能の一覧については[列ストア インデックスの新機能](/sql/relational-databases/indexes/columnstore-indexes-what-s-new)に関する記事をご覧ください。 列ストア インデックスのパフォーマンスを向上させるには、[列ストア インデックスの行グループの品質の最大化](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)に関する記事をご覧ください。

## <a name="statistics"></a>統計
クエリ オプティマイザーでは、クエリ実行のプランの作成時に列レベルの統計が使用されます。 クエリのパフォーマンスを向上させるには、個々の列、特にクエリの結合で使用される列の統計を作成することが重要です。 [統計の作成](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic)は、自動的に行われます。  ただし、統計の更新は自動的には行われません。 大量の行が追加または変更された後に統計を更新します。 たとえば、読み込みの後に統計を更新します。 詳しくは、[統計のガイダンス](sql-data-warehouse-tables-statistics.md)に関する記事をご覧ください。

## <a name="commands-for-creating-tables"></a>テーブルを作成するためのコマンド
テーブルは、新しい空のテーブルとして作成することができます。 テーブルを作成し、SELECT ステートメントの結果を使用して値を設定することもできます。 テーブルを作成するための T-SQL コマンドを次に示します。

| T-SQL ステートメント | 説明 |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | すべてのテーブル列およびオプションを定義して空のテーブルを作成します。 |
| [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) | 外部テーブルを作成します。 テーブルの定義は、SQL Data Warehouse に格納されます。 テーブル データは Azure Blob Storage または Azure Data Lake Store に格納されます。 |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) | SELECT ステートメントの結果を使用して新しいテーブルに値が設定されます。 テーブルの列とデータ型は、SELECT ステートメントの結果に基づきます。 データをインポートするには、このステートメントで外部テーブルから選択できます。 |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql) | 外部の場所に SELECT ステートメントの結果をエクスポートして、新しい外部テーブルを作成します。  その場所は Azure Blob Storage または Azure Data Lake Store です。 |

## <a name="aligning-source-data-with-the-data-warehouse"></a>ソース データをデータ ウェアハウスに配置する

データ ウェアハウス テーブルは、別のデータ ソースからデータを読み込むことで設定されます。 読み込みの実行を成功させるには、ソース データ内の列の数とデータ型が、データ ウェアハウス内のテーブル定義と合致している必要があります。 配置するデータの取得は、テーブルの設計の最大の難関となる可能性があります。 

データが複数のデータ ストアから読み込まれる場合は、データ ウェアハウスにデータを読み込み、統合テーブルに格納できます。 データが統合テーブルに格納されたら、SQL Data Warehouse の機能を使用して変換操作を実行できます。 データの準備ができたら、それを運用テーブルに挿入できます。

## <a name="unsupported-table-features"></a>サポートされていないテーブルの機能
SQL Data Warehouse では他のデータベースで提供されるテーブル機能の多くがサポートされますが、すべてサポートされるわけではありません。  次の一覧は、SQL Data Warehouse でサポートされていないテーブル機能の一部を示しています。

- PRIMARY KEY、FOREIGN KEY、UNIQUE、CHECK の各 [テーブル制約](/sql/t-sql/statements/alter-table-table-constraint-transact-sql)

- [計算列](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql)
- [インデックス付きビュー](/sql/relational-databases/views/create-indexed-views)
- [シーケンス](/sql/t-sql/statements/create-sequence-transact-sql)
- [スパース列](/sql/relational-databases/tables/use-sparse-columns)
- 代理キー。 [Identity](sql-data-warehouse-tables-identity.md)で実装されます。
- [シノニム](/sql/t-sql/statements/create-synonym-transact-sql)
- [トリガー](/sql/t-sql/statements/create-trigger-transact-sql)
- [一意のインデックス](/sql/t-sql/statements/create-index-transact-sql)
- [ユーザー定義型](/sql/relational-databases/native-client/features/using-user-defined-types)

## <a name="table-size-queries"></a>テーブル サイズのクエリ
60 個の各ディストリビューションでテーブルによって消費される領域と行を簡単に識別する方法の 1 つが、[DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) です。

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

ただし、DBCC コマンドを使用できるのは極めて限定的です。  動的管理ビュー (DMV) には、DBCC コマンドよりも詳しい情報が表示されます。 このビューの作成から開始します。

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

次のクエリはテーブルごとに行と領域を返します。  これにより、最大規模のテーブルや、各テーブルがラウンド ロビン、レプリケート、ハッシュ分散のいずれかであるかを確認できます。  ハッシュ分散テーブルの場合、クエリによってディストリビューション列が表示されます。  

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

## <a name="next-steps"></a>次の手順
データ ウェアハウスにテーブルを作成した後、次の手順はテーブルへのデータの読み込みです。  読み込みのチュートリアルについては、「[Azure SQL Data Warehouse へのデータの読み込み](load-data-wideworldimportersdw.md)」を参照してください。
