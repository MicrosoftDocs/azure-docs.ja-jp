---
title: Azure Synapse SQL リソースを使用して統計の作成と更新を行う
description: Synapse SQL のクエリ最適化統計の作成と更新に関するレコメンデーションと例。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 25c92437b350d7329c340fe1ea13b3df40e231ba
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87020601"
---
# <a name="statistics-in-synapse-sql"></a>Synapse SQL の統計

この記事では、次の Synapse SQL リソースを使用してクエリ最適化統計を作成および更新するためのレコメンデーションと例を示します: SQL プールと SQL オンデマンド (プレビュー)。

## <a name="statistics-in-sql-pool"></a>SQL プールの統計

### <a name="why-use-statistics"></a>統計を使用する理由

SQL プール リソースがデータに関する情報を多く持っているほど、クエリを高速に実行できます。 SQL プールにデータを読み込んだ後、データに関する統計を収集することは、クエリ最適化のために実行できる最も重要なことの 1 つです。  

SQL プール クエリ オプティマイザーは、コストベースのオプティマイザーです。 オプティマイザーでは、さまざまなクエリ プランのコストが比較されて、最も低コストのプランが選択されます。 多くの場合、それは最も高速に実行されるプランが選択されます。

たとえば、クエリでフィルター処理されている日付に対して返されるのは 1 行であるとオプティマイザーで推定されると、1 つのプランが選択されます。 選択された日付で返されるのが 100 万行であると推定された場合は、別のプランが返されます。

### <a name="automatic-creation-of-statistics"></a>統計の自動作成

データベースの AUTO_CREATE_STATISTICS オプションが `ON` に設定されている場合、SQL プールでは足りない統計に対して受信ユーザー クエリが分析されます。  統計が足りない場合、クエリ オプティマイザーでは、クエリ述語または結合条件内の個々の列で統計を作成します。 

この関数は、クエリ プランに対するカーディナリティ評価を改善するために使用されます。

> [!IMPORTANT]
> 既定では､統計の自動作成は有効です｡

データ ウェアハウスで AUTO_CREATE_STATISTICS が構成されているかどうかは、次のコマンドを実行することで確認できます。

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

データ ウェアハウスで AUTO_CREATE_STATISTICS が有効になっていない場合は、次のコマンドでこのプロパティを有効にすることをお勧めします。

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

結合が含まれること、または述語が存在することが検出されると、次のステートメントによって統計の自動作成がトリガーされます。

- SELECT
- INSERT-SELECT
- CTAS
- UPDATE
- DELETE
- EXPLAIN

> [!NOTE]
> 一時テーブルや外部テーブルに対して自動作成の統計が生成されることはありません｡

統計の自動作成は同期的に行われます。 そのため、列に統計がない場合、クエリのパフォーマンスが多少低下することがあります。 1 つの列の統計を作成する時間は、テーブルのサイズに依存します。

明らかなパフォーマンスの低下を回避するには、システムをプロファイルする前にベンチマーク用ワークロードを実行することによって、統計を先に作成しておく必要があります。

> [!NOTE]
> 統計の作成は、各ユーザー コンテキスト内の [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) にログ記録されます。

自動統計が作成されると､_WA_Sys_<16 進 8 桁の列 ID>_<16 進 8 桁のテーブル ID> の形式でログ記録されます。 作成済みの統計は、[DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) コマンドを実行して表示できます。

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

table_name は、表示する統計が格納されているテーブルの名前であり、これが外部テーブルであることはありません。 target は、統計情報を表示するターゲットのインデックス、統計、または列の名前です。

### <a name="update-statistics"></a>統計を更新します。

ベスト プラクティスの 1 つが、新しい日付が追加されるたびに日付列の統計を更新することです。 新しい行がデータ ウェアハウスに読み込まれるたびに、新しい読み込みの日付またはトランザクションの日付が追加されます。 これらの追加によってデータの分布が変わり、統計が古くなります。

顧客テーブルの国または地域の列の統計は更新する必要がないと考えられます。通常、値の分布は変わらないためです。 顧客間で分布が一定であると仮定すると、テーブル バリエーションに新しい行を追加しても、データの分布が変わることはありません。

ただし、データ ウェアハウスに 1 つの国または地域しか含まれておらず、新しい国または地域のデータを取り込む場合は、国または地域列の統計を更新する必要があります。

統計更新のレコメンデーションは次の通りです｡

|||
|-|-|
| **統計の更新の頻度**  | 控えめ: 毎日 </br> データを読み込むか変換した後 |
| **サンプリング** |  行数が 10 億未満の場合は、既定のサンプリング (20%) が使用されます。 </br> 10 億行を超えると、2% のサンプリングが使用されます。 |

### <a name="determine-last-statistics-update"></a>統計の最終更新日を特定する

クエリのトラブルシューティングを行うときに最初に尋ねる質問の 1 つが、「**統計は最新の状態ですか**」というものです。

この質問は、データの経過時間で答えられるものではありません。 基になるデータに重要な変更がない場合は、最新の統計オブジェクトが古い可能性があります。 行数が大幅に変わった場合や、列の値の分布に大きな変化が起きた場合は、"*その後で*" 統計を更新する必要があります。

前回の統計が更新されてからテーブル内のデータが変更されたかどうかを判断するために利用できる動的管理ビューはありません。 統計情報の経過期間がわかると、全体像の一部を把握できます。 

以下のクエリでは、それぞれのテーブルで統計情報が最後に更新された時刻を確認できます。

> [!NOTE]
> 列の値の分布に重要な変更がある場合は、最後に更新された時刻に関係なく統計を更新する必要があります。

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

たとえば、データ ウェアハウスの**日付列**では、通常、統計を頻繁に更新する必要があります。 新しい行がデータ ウェアハウスに読み込まれるたびに、新しい読み込みの日付またはトランザクションの日付が追加されます。 これらの追加によってデータの分布が変わり、統計が古くなります。

顧客テーブルの性別列の統計は更新する必要がないと考えられます。 顧客間で分布が一定であると仮定すると、テーブル バリエーションに新しい行を追加しても、データの分布が変わることはありません。

ただし、データ ウェアハウスに 1 つの性別しか含まれておらず、新しい要件によって複数の性別が含まれるようになった場合は、性別列の統計を更新する必要があります。 

詳細については、「[統計](/sql/relational-databases/statistics/statistics)」の記事を参照してください。

### <a name="implement-statistics-management"></a>統計管理の実装

多くの場合、読み込みの終わりに統計が確実に更新されるように、データ読み込みプロセスを拡張することが推奨されます。 テーブルのサイズと値の分布のどちらかまたは両方が変わる頻度が最も高いのがデータの読み込み時です。 そのため、読み込みプロセスは、管理プロセスを実装する論理的な場所となります。

読み込みプロセスで統計を更新する際の基本原則は、次のとおりです。

- 読み込まれた各テーブルに更新された統計オブジェクトが少なくとも 1 つは含まれていることを確認します。 このプロセスにより、統計の更新の一環として、テーブル サイズ (行数とページ数) 情報が更新されます。
- JOIN、GROUP BY、ORDER BY、DISTINCT の各句に関与している列を重視します。
- トランザクションの日付などの "昇順キー" 列の値は、統計ヒストグラムに含まれないため、これらの列の更新頻度を増やすことを検討します。
- 静的な分布列の更新頻度を減らすことを検討します。
- 各統計オブジェクトは順序どおりに更新されることに注意してください。 特に、多数の統計オブジェクトが含まれた幅の広いテーブルでは、 `UPDATE STATISTICS <TABLE_NAME>` を実装するだけでは十分とはいえない場合があります。

詳細については、「[カーディナリティ推定](/sql/relational-databases/performance/cardinality-estimation-sql-server)」を参照してください。

### <a name="examples-create-statistics"></a>例 :統計を作成する

以下の例では、さまざまなオプションを使用して統計を作成する方法を示します。 各列に使用するオプションは、データの特性とクエリでの列の使用方法によって異なります。

#### <a name="create-single-column-statistics-with-default-options"></a>既定のオプションを使用した単一列統計の作成

列の統計を作成するには、統計オブジェクトの名前と列の名前を指定します。
次の構文では、既定のオプションをすべて使用しています。 既定で、SQL プールでは統計を作成するときに、テーブルの **20%** がサンプリングされます。

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

次に例を示します。

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>すべての列の検査による単一列統計の作成

ほとんどの場合、20% という既定のサンプリング レートで十分です。 ただし、サンプリング レートを調整することもできます。 テーブル全体をサンプリングするには、次の構文を使用します。

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name])
    WITH FULLSCAN;
```

次に例を示します。

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>サンプル サイズを指定した単一列統計の作成

もう 1 つのオプションとして、サンプル サイズをパーセントで指定します。

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>一部の行のみの単一列統計の作成

テーブルの一部の行の統計を作成することもできます。これを、フィルター選択された統計と呼びます。

たとえば、大規模なパーティション テーブルの特定のパーティションのクエリを計画するときに、フィルター選択された統計を使用できます。 パーティション値のみで統計を作成すると、統計の精度が向上します。 また、クエリのパフォーマンスも向上します。

次の例では、値の範囲の統計を作成します。 パーティションの値の範囲に一致する値を簡単に定義できます。

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> クエリ オプティマイザーが分散クエリ プランを選択するときに、フィルター選択された統計の使用も考慮されるようにするには、クエリが統計オブジェクトの定義の範囲内である必要があります。 前の例では、クエリの WHERE 句で col1 の値として 2000101 ～ 20001231 の値を指定する必要があります。

#### <a name="create-single-column-statistics-with-all-the-options"></a>すべてのオプションを使用した単一列統計の作成

オプションは組み合わせることができます。 次の例では、カスタム サンプル サイズを指定してフィルター選択された統計オブジェクトを作成します。

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

詳細については、「[CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)」をご覧ください。

#### <a name="create-multi-column-statistics"></a>複数列統計の作成

複数列統計オブジェクトを作成するには、これまでの例を使用しますが、複数の列を指定します。

> [!NOTE]
> クエリ結果の行数の推定に使用されるヒストグラムは、統計オブジェクト定義に示されている最初の列にのみ使用できます。

次の例では、ヒストグラムは *product\_category* で使用されます。 列間の統計は、*product\_category* と *product\_sub_category* で計算されます。

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

*product\_category* と *product\_sub\_category* の間には相関関係が存在するため、これらの列に同時にアクセスする場合は複数列統計オブジェクトが役立ちます。

#### <a name="create-statistics-on-all-columns-in-a-table"></a>テーブルのすべての列の統計の作成

統計を作成する方法の 1 つとして、テーブルの作成後に CREATE STATISTICS コマンドを発行します。

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>ストアド プロシージャを使用した、データベース内のすべての列の統計の作成

SQL プールには、SQL Server の sp_create_stats に相当するシステム ストアド プロシージャはありません。 このストアド プロシージャは、まだ統計がないデータベースのすべての列の単一列統計オブジェクトを作成します。

次の例は、データベースの設計を開始する際に役立ちます。 ニーズに合わせて、この例を自由に変更できます。

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default, 2 Fullscan, 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

既定値を使用してテーブルのすべての列の統計を作成するには、ストアド プロシージャを実行します。

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

フルスキャンを使用してテーブルのすべての列の統計を作成するには、次のプロシージャを呼び出します。

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

テーブルのすべての列にサンプルの統計を作成するには、「3」およびサンプル率を入力します。 次のプロシージャでは、20% のサンプル レートを使用します。

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>例 :統計を更新します。

統計を更新するには、次の操作を行います。

- 統計オブジェクトを 1 つ更新します。 更新する統計オブジェクトの名前を指定します。
- テーブルのすべての統計オブジェクトを更新します。 特定の統計オブジェクトではなく、テーブルの名前を指定します。

#### <a name="update-one-specific-statistics-object"></a>1 つの特定の統計オブジェクトの更新

特定の統計オブジェクトを更新するには、次の構文を使用します。

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

次に例を示します。

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

特定の統計オブジェクトを更新することで、統計を管理するために必要な時間とリソースを最小限に抑えることができます。 このアクションでは、更新する最適な統計オブジェクトを選択するために、少し検討する必要があります。

#### <a name="update-all-statistics-on-a-table"></a>テーブルのすべての統計を更新する

テーブルのすべての統計オブジェクトを更新する簡単な方法を次に示します。

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

次に例を示します。

```sql
UPDATE STATISTICS dbo.table1;
```

UPDATE STATISTICS ステートメントは簡単に使用できます。 このステートメントはテーブルの*すべて*の統計を更新するので、必要以上の処理が実行されます。 

パフォーマンスが問題でない場合は、この手法が、統計が最新の状態であることを保証する最も簡単で最も包括的な方法です。

> [!NOTE]
> テーブルのすべての統計を更新する場合、SQL プールでは、統計オブジェクトごとにテーブルのスキャンを実行してサンプリングします。 テーブルが大きく、多数の列と統計が含まれている場合は、ニーズに基づいて個々の統計を更新する方が効率的です。

`UPDATE STATISTICS` プロシージャの実装については、[一時テーブル](develop-tables-temporary.md)に関する記事をご覧ください。 実装方法は前述の `CREATE STATISTICS` プロシージャと若干異なりますが、結果は同じです。
完全な構文については、「[UPDATE STATISTICS ](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)」を参照してください。

### <a name="statistics-metadata"></a>統計のメタデータ

統計に関する情報を確認する際に使用できるシステム ビューとシステム関数がいくつかあります。 たとえば、統計オブジェクトが古くなっているかどうかは、STATS_DATE() 関数を使用して判別できます。 STATS_DATE() を使用すると、統計が最後に作成または更新された日時を確認できます。

#### <a name="catalog-views-for-statistics"></a>統計のカタログ ビュー

次のシステム ビューは、統計に関する情報を提供します。

| カタログ ビュー | 説明 |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |列ごとに 1 行。 |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |データベース内のオブジェクトごとに 1 行。 |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |データベースのスキーマごとに 1 行。 |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |統計オブジェクトごとに 1 行。 |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |統計オブジェクトの列ごとに 1 行。 sys.columns にリンク。 |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |テーブル (外部テーブルを含む) ごとに 1 行。 |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |データ型ごとに 1 行。 |

#### <a name="system-functions-for-statistics"></a>統計のシステム関数

次のシステム関数は統計の操作に役立ちます。

| システム関数 | 説明 |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |統計オブジェクトの最終更新日。 |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |統計オブジェクトで認識される値の分布に関する概要レベルの情報と詳細情報。 |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>1 つのビューへの統計列と関数の統合

このビューには、統計に関連する列と、STATS_DATE() 関数の結果が一緒に表示されます。

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

### <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS() の例

DBCC SHOW_STATISTICS() は、統計オブジェクト内に保持されているデータを表示します。 このデータは 3 つの部分で提供されます。

- ヘッダー
- 密度ベクトル
- ヒストグラム

ヘッダーは、統計に関するメタデータです。 ヒストグラムには、統計オブジェクトの最初のキー列の値の分布が表示されます。 

密度ベクトルは、列間の相関関係を測定します。 SQL プールでは、統計オブジェクト内のデータを使用してカーディナリティ推定値を計算します。

#### <a name="show-header-density-and-histogram"></a>ヘッダー、密度、ヒストグラムの表示

次の簡単な例は、統計オブジェクトの 3 つの部分をすべて表示します。

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

次に例を示します。

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>DBCC SHOW_STATISTICS() の 1 つ以上の部分の表示

特定の部分だけを表示する場合は、`WITH` 句を使用して表示する部分を指定します。

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

次に例を示します。

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS() の相違点

SQL Server に比べ、SQL プールでは、`DBCC SHOW_STATISTICS()` がより厳密に実装されています。

- ドキュメントに記載されていない機能はサポートされていません。
- Stats_stream は使用できません。
- 統計データの特定のサブセットの結果を結合することはできません。 たとえば、STAT_HEADER JOIN DENSITY_VECTOR。
- メッセージを抑制するために、NO_INFOMSGS を設定することはできません。
- 統計名を囲む角かっこは使用できません。
- 列名を使用して、統計オブジェクトを識別することはできません。
- カスタム エラー 2767 はサポートされていません。

### <a name="next-steps"></a>次のステップ

クエリのパフォーマンスをさらに向上させるには、[ワークロードの監視](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関する記事を参照してください

## <a name="statistics-in-sql-on-demand-preview"></a>SQL オンデマンドの統計 (プレビュー)

統計は、特定のデータセット (ストレージ パス) の特定の列ごとに作成されます。

### <a name="why-use-statistics"></a>統計を使用する理由

SQL オンデマンド (プレビュー) がデータに関する情報を多く持っているほど、それに対するクエリを高速に実行できます。 データに関する統計情報を収集することが、クエリの最適化のために実行できる最も重要なことの 1 つです。 

SQL オンデマンドのクエリ オプティマイザーは、コストベースのオプティマイザーです。 オプティマイザーでは、さまざまなクエリ プランのコストが比較されて、最も低コストのプランが選択されます。 多くの場合、それは最も高速に実行されるプランが選択されます。 

たとえば、クエリでフィルター処理されている日付に対して返されるのは 1 行であるとオプティマイザーで推定されると、1 つのプランが選択されます。 選択された日付で返されるのが 100 万行であると推定された場合は、別のプランが返されます。

### <a name="automatic-creation-of-statistics"></a>統計の自動作成

SQL オンデマンドでは、足りない統計に対して受信ユーザー クエリが分析されます。 統計が足りない場合、クエリ オプティマイザーでは、クエリ述語または結合条件内の個々の列で統計を作成することで、クエリ プランに対するカーディナリティ評価が改善されます。

SELECT ステートメントによって統計の自動作成がトリガーされます。

> [!NOTE]
> Parquet ファイルについては、統計の自動作成が有効になっています。 CSV ファイルについては、CSV ファイルの統計の自動作成がサポートされるまでは、統計を手動で作成する必要があります。

統計の自動作成は同期的に行われるため、列に統計がない場合、クエリのパフォーマンスが多少低下することがあります。 1 つの列の統計を作成する時間は、ターゲットとされるファイルのサイズに依存します。

### <a name="manual-creation-of-statistics"></a>統計の手動作成

SQL オンデマンドでは、手動で統計を作成できます。 CSV ファイルについては、統計の自動作成が CSV ファイルに対して有効になっていないため、統計を手動で作成する必要があります。 

統計を手動で作成する方法については、次の例を参照してください。

### <a name="update-statistics"></a>統計を更新します。

ファイル内のデータの変更、ファイルの削除、ファイルの追加により、データの分布が変化し、統計が古くなります。 その場合、統計を更新する必要があります。

データが大幅に変更された場合、SQL オンデマンドによって統計が自動的に再作成されます。 統計が自動的に作成されるたびに、データセットの現在の状態 (ファイル パス、サイズ、最終変更日) も保存されます。

統計が古くなった場合、新しい統計が作成されます。 アルゴリズムによってデータが処理され、データセットの現在の状態と比較されます。 変更のサイズが特定のしきい値を超える場合、古い統計が削除され、新しいデータセットに再作成されます。

手動の統計は古いものとして宣言されることはありません。

> [!NOTE]
> Parquet ファイルについては、統計の自動再作成が有効になっています。 CSV ファイルについては、CSV ファイルの統計の自動作成がサポートされるまでは、統計を手動で削除して作成する必要があります。 統計を削除および作成する方法については、以下の例を確認してください。

クエリのトラブルシューティングを行うときに最初に尋ねる質問の 1 つが、「**統計は最新の状態ですか**」というものです。

行数が大幅に変わった場合や、列の値の分布で重大な変更があった場合は、"*その後で*" 統計を更新する必要があります。

> [!NOTE]
> 列の値の分布に重要な変更がある場合は、最後に更新された時刻に関係なく統計を更新する必要があります。

### <a name="implement-statistics-management"></a>統計管理の実装

ファイルの追加、削除、または変更によってデータが大幅に変更されたときに統計が確実に更新されるよう、データ パイプラインを拡張することができます。

統計を更新する際の基本原則は、次のとおりです。

- 更新された統計オブジェクトが少なくとも 1 つはデータセットに含まれていることを確認します。 これにより、統計の更新の一環として、サイズ (行数とページ数) 情報が更新されます。
- JOIN、GROUP BY、ORDER BY、DISTINCT の各句に関与している列を重視します。
- トランザクションの日付などの "昇順キー" 列の値は、統計ヒストグラムに含まれないため、これらの列の更新頻度を増やします。
- 静的な分布列の更新頻度を減らします。

詳細については、「[カーディナリティ推定](/sql/relational-databases/performance/cardinality-estimation-sql-server)」を参照してください。

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>例 :OPENROWSET パスの列の統計を作成する

以下の例では、さまざまなオプションを使用して統計を作成する方法を示します。 各列に使用するオプションは、データの特性とクエリでの列の使用方法によって異なります。

> [!NOTE]
> 現時点では、単一列統計のみを作成できます。
>
> プロシージャ sp_create_file_statistics の名前が sp_create_openrowset_statistics に変更されます。 public データベース ロールに sp_create_file_statistics と sp_drop_file_statistics に対する EXECUTE 権限があるときは、ADMINISTER BULK OPERATIONS 権限が public サーバー ロールに許可されます。 これは将来変更される可能性があります。

統計を作成するには、次のストアド プロシージャを使用します。

```sql
sys.sp_create_file_statistics [ @stmt = ] N'statement_text'
```

引数: [ @stmt = ] N'statement_text' - 統計に使用する列の値を返す Transact-SQL ステートメントを指定します。 TABLESAMPLE を使用して、使用するデータのサンプルを指定できます。 TABLESAMPLE が指定されていない場合、FULLSCAN が使用されます。

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> CSV サンプリングは現時点では機能しません。CSV に対しては FULLSCAN のみがサポートされています。

#### <a name="create-single-column-statistics-by-examining-every-row"></a>すべての列の検査による単一列統計の作成

列の統計を作成するには、統計が必要な列を返すクエリを指定します。

SQL オンデマンドで統計を作成するとき、特に指定がなければ、データセットで提供されるデータの 100% が既定で使用されます。

たとえば、population.csv ファイルに基づいたデータセットの year 列に対して既定のオプション (FULLSCAN) で統計を作成するには、次のようにします。

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>サンプル サイズを指定した単一列統計の作成

サンプル サイズはパーセントで指定できます。

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>例 :統計を更新します。

統計を更新するには、統計を削除して作成する必要があります。 統計を削除するには、次のストアド プロシージャを使用します。

```sql
sys.sp_drop_file_statistics [ @stmt = ] N'statement_text'
```

> [!NOTE]
> プロシージャ sp_drop_file_statistics の名前が sp_drop_openrowset_statistics に変更されます。 public データベース ロールに sp_create_file_statistics と sp_drop_file_statistics に対する EXECUTE 権限があるときは、ADMINISTER BULK OPERATIONS 権限が public サーバー ロールに許可されます。 これは将来変更される可能性があります。

引数: [ @stmt = ] N'statement_text' - 統計の作成時に使用したものと同じ Transact-SQL ステートメントを指定します。

population.csv ファイルに基づいたデータセットの year 列の統計を更新するには、統計を削除して作成する必要があります。

```sql
EXEC sys.sp_drop_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>例 :外部テーブル列の統計を作成する

以下の例では、さまざまなオプションを使用して統計を作成する方法を示します。 各列に使用するオプションは、データの特性とクエリでの列の使用方法によって異なります。

> [!NOTE]
> 現時点では、単一列統計のみを作成できます。

列の統計を作成するには、統計オブジェクトの名前と列の名前を指定します。

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

引数: external_table 統計を作成する外部テーブルを指定します。

FULLSCAN すべての行をスキャンして統計を計算します。 FULLSCAN と SAMPLE 100 PERCENT は同じ結果になります。 SAMPLE オプションには FULLSCAN を使用できません。

SAMPLE number PERCENT テーブルやインデックス付きビューに含まれている行について、クエリ オプティマイザーで統計を作成する際に使用するおおよその割合または数を指定します。 number は 0 ～ 100 の範囲で指定できます。

FULLSCAN オプションには SAMPLE を使用できません。

> [!NOTE]
> CSV サンプリングは現時点では機能しません。CSV に対しては FULLSCAN のみがサポートされています。

#### <a name="create-single-column-statistics-by-examining-every-row"></a>すべての列の検査による単一列統計の作成

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>サンプル サイズを指定した単一列統計の作成

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>例 :統計を更新します。

統計を更新するには、統計を削除して作成する必要があります。 まず統計を削除します。

```sql
DROP STATISTICS census_external_table.sState
```

次に、統計を作成します。

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

## <a name="next-steps"></a>次のステップ

クエリ パフォーマンスのさらなる向上については、[SQL プールのベスト プラクティス](best-practices-sql-pool.md#maintain-statistics)に関するページを参照してください。
