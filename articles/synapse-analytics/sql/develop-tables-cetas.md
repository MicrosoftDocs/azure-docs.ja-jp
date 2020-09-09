---
title: Synapse SQL での CETAS
description: Synapse SQL での CETAS の使用
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f3e53ac189e0d612b09c362e82ba5bc2fe5fec8d
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83696834"
---
# <a name="cetas-with-synapse-sql"></a>Synapse SQL での CETAS

SQL プールまたは SQL オンデマンド (プレビュー) では、CREATE EXTERNAL TABLE AS SELECT (CETAS) を使用して、次のタスクを実行できます。  

- 外部テーブルを作成する
- Transact-SQL SELECT ステートメントの結果を並行して次にエクスポートする

  - Hadoop
  - Azure Storage BLOB
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-sql-pool"></a>SQL プールでの CETAS

SQL プールの場合、CETAS の使用法と構文については、[CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) に関する記事をご覧ください。 また、SQL プールを使用した CTAS のガイダンスについては、[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) に関する記事を参照してください。

## <a name="cetas-in-sql-on-demand"></a>SQL オンデマンドでの CETAS

SQL オンデマンド リソースを使用しているときは、CETAS を使用して外部テーブルを作成し、クエリ結果を Azure Storage Blob または Azure Data Lake Storage Gen2 にエクスポートします。

## <a name="syntax"></a>構文

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>引数

*[ [ *database_name* . [ *schema_name* ] . ] | *schema_name* . ] *table_name**

作成するテーブルの 1 つから 3 つの部分で構成される名前。 外部テーブルの場合、SQL オンデマンドではテーブルのメタデータのみが格納されます。 SQL オンデマンドでは、実際のデータの移動または格納は行われません。

LOCATION = *'path_to_folder'*

外部データ ソースで SELECT ステートメントの結果を書き込む場所を指定します。 ルート フォルダーは、外部データ ソースで指定されたデータの場所です。 LOCATION は、フォルダーを指す必要があり、末尾に / を付ける必要があります。 例: aggregated_data/

DATA_SOURCE = *external_data_source_name*

外部データが格納される場所を含む外部データ ソース オブジェクトの名前を指定します。 外部データ ソースを作成するには、[CREATE EXTERNAL DATA SOURCE (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source) を使用します。

FILE_FORMAT = *external_file_format_name*

外部データ ファイルの形式を含む、外部ファイル形式オブジェクトの名前を指定します。 外部ファイル形式を作成するには、[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format) を使用します。 現在サポートされているのは、FORMAT='PARQUET' の外部ファイル形式のみです。

WITH *<common_table_expression>*

共通テーブル式 (CTE) と呼ばれる一時的な名前付き結果セットを指定します。 詳細については、「[WITH common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)」を参照してください。

SELECT <select_criteria>

SELECT ステートメントの結果を新しいテーブルに追加します。 *select_criteria* は、新しいテーブルにコピーするデータを決定する SELECT ステートメントの本文です。 SELECT ステートメントについては、「[SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)」を参照してください。

> [!NOTE]
> CETAS の SELECT 部分での ORDER BY 句はサポートされていません。

## <a name="permissions"></a>アクセス許可

CETAS を機能させるには、フォルダーの内容を一覧表示し、LOCATION フォルダーに書き込むためのアクセス許可が必要です。

## <a name="examples"></a>例

これらの例では、CETAS を使用して、年および州別に集計された総人口を、population_ds データソース内の aggregated_data フォルダーに保存します。

このサンプルは、前に作成した資格情報、データ ソース、および外部ファイル形式に依存しています。 [外部テーブル](develop-tables-external-tables.md)に関するドキュメントを参照してください。 同じデータ ソース内の別のフォルダーにクエリ結果を保存するには、LOCATION 引数を変更します。 

結果を別のストレージ アカウントに保存するには、DATA_SOURCE 引数に別のデータ ソースを作成して使用します。

> [!NOTE]
> 次のサンプルでは、パブリックの Azure オープン データ ストレージ アカウントを使用します。 これは読み取り専用です。 これらのクエリを実行するには、書き込みアクセス許可があるデータ ソースを指定する必要があります。

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

次のサンプルでは、CETAS のソースとして外部テーブルを使用します。 これは、以前に作成した資格情報、データ ソース、外部ファイル形式、および外部テーブルに依存しています。 [外部テーブル](develop-tables-external-tables.md)に関するドキュメントを参照してください。

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>サポートされるデータ型

CETAS を使用して、次の SQL データ型の結果セットを格納できます。

- binary
- varbinary
- char
- varchar
- date
- time
- datetime2
- decimal
- numeric
- float
- real
- bigint
- INT
- smallint
- tinyint
- bit

> [!NOTE]
> LOB は CETAS では使用できません。

次のデータ型は、CETAS の SELECT 部分では使用できません。

- nchar
- nvarchar
- DATETIME
- smalldatetime
- datetimeoffset
- money
- smallmoney
- UNIQUEIDENTIFIER

## <a name="next-steps"></a>次のステップ

[Azure Synapse 外部テーブルの Apache Spark](develop-storage-files-spark-tables.md) のクエリの実行を試すことができます。
