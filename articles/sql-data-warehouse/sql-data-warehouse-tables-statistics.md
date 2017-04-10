---
title: "SQL Data Warehouse のテーブルの統計の管理 | Microsoft Docs"
description: "Azure SQL Data Warehouse のテーブルの統計の概要です。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b2b99ec031ea26b4ab19e7327da035788661a0a8
ms.lasthandoff: 12/08/2016


---
# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>SQL Data Warehouse のテーブルの統計の管理
> [!div class="op_single_selector"]
> * [概要][Overview]
> * [データ型][Data Types]
> * [分散][Distribute]
> * [インデックス][Index]
> * [パーティション][Partition]
> * [統計][Statistics]
> * [一時][Temporary]
> 
> 

SQL Data Warehouse がご使用のデータについてより多くを把握していればいるほど、それだけ速くデータにクエリを実行できます。  データに関する情報を SQL Data Warehouse に伝えるには、データに関する統計情報を収集します。  データに関する統計情報を取得することが、クエリの最適化のために実行できる最も重要なことの 1 つです。  統計は、SQL Data Warehouse のクエリの最適なプランの作成に利用できます。  これは、SQL Data Warehouse のクエリ オプティマイザーがコストに基づくオプティマイザーであるためです。  つまり、さまざまなクエリ プランのコストを比較し、コストが最も低いプランを選択します。選択されたプランが、最も高速に実行されるプランでもあります。

統計は、単一列、複数列、またはテーブルのインデックスに作成することができます。  統計は、値の範囲と選択性をキャプチャするヒストグラムに格納されます。  これは、オプティマイザーがクエリの JOIN、GROUP BY、HAVING、WHERE の各句 を評価する必要がある場合に特に重要となります。  たとえば、オプティマイザーによって、クエリでフィルター処理している日付が 1 行を返すと評価された場合、選択した日付が 100 万行を返すと評価された場合とではまったく異なるプランを選択することができます。  統計を作成することは非常に重要ですが、テーブルの現在の状態をその統計に "正確に" ** 反映することは、同じくらい重要です。  最新の統計情報を取得すると、オプティマイザーによって適切なプランが確実に選択されることになります。  オプティマイザーによって作成されたプランの有効性は、データの統計によって決まります。

統計の作成および更新の処理は、現在は手動のプロセスですが、操作は非常に簡単です。  この点が、単一列とインデックスに関する統計が自動的に作成および更新される SQL Server と異なります。  次の情報を使用すると、データの統計情報の管理を大幅に自動化できます。 

## <a name="getting-started-with-statistics"></a>統計の概要
 統計を開始する簡単な方法は、すべての列のサンプリングされた統計を作成することです。  統計情報を最新の状態に維持することは同様に重要なため、保守的なアプローチでは、毎日または各読み込みの後に統計を更新します。 パフォーマンスと統計を作成および更新するコストの間には常にトレードオフの関係が存在します。  すべての統計を管理するのは時間がかかりすぎる場合は、統計を作成する列や頻繁に更新する列を限定することをお勧めします。  たとえば、読み込み後に新しい値が追加される可能性があるため、日付列は日次で更新することができます。 さらに、JOIN、GROUP BY、HAVING、WHERE 句で使用されている列の統計を作成すると、最も大きなメリットが得られます。  SELECT 句のみで使用される列を多数持つテーブルがある場合、これらの列の統計情報が役に立たない場合があります。統計情報が役立つ列のみを特定する努力を少しするだけで、統計情報を維持するために必要な時間を短縮できます。

## <a name="multi-column-statistics"></a>複数列統計
単一列で統計を作成するだけでなく、複数列統計からクエリにとってのメリットを得られる場合がります。  複数列統計は、列のリストで作成される統計です。  この統計には、リストの最初の列の単一列統計に加え、密度と呼ばれる列間の相関関係情報が含まれます。  たとえば、2 つの列で別のテーブルと結合されているテーブルがある場合、SQL Data Warehouse が 2 つの列のリレーションシップを認識すると、プランはさらに最適化されます。   複合結合やグループ化などの一部の操作では、複数列統計によってクエリのパフォーマンスを向上させることができます。

## <a name="updating-statistics"></a>統計の更新
統計の更新は、データベース管理ルーチンの重要な一部です。  データベース内のデータの分布が変わったら、統計を更新する必要があります。  統計情報が古いと、クエリのパフォーマンスが最適化されなくなります。

ベスト プラクティスの 1 つが、新しい日付が追加されるたびに日付列の統計を更新することです。  新しい行がデータ ウェアハウスに読み込まれるたびに、新しい読み込みの日付またはトランザクションの日付が追加されます。 これらによってデータの分布が変わり、統計が古くなります。 一方、顧客テーブルの国列の統計は更新する必要がないと考えられます。一般的に値の分布は変わらないためです。 顧客間で分布が一定であると仮定すると、テーブル バリエーションに新しい行を追加しても、データの分布が変わることはありません。 ただし、データ ウェアハウスに 1 つの国しか含まれておらず、新しい国のデータを取り込んで複数の国のデータが格納されるようになった場合は、国列の統計を更新する必要があることは明らかです。

クエリのトラブルシューティングを行うときに最初に尋ねる質問の 1 つが、「統計は最新の状態ですか」というものです。

この質問は、データの経過時間で答えられるものではありません。 基になるデータに重要な変更がない場合は、最新の統計オブジェクトが非常に古い可能性があります。 行数が大幅に変わった場合や、特定の列の値の分布で重大な変更があった場合は、"その後で" ** 統計を更新する必要があります。  

なお、 **SQL Server** (SQL Data Warehouse ではありません) によって統計が自動更新されるのは次の場合です。

* テーブルに行が含まれていない場合に、行を追加すると、自動更新された統計が表示されます。
* 500 行未満で開始されるテーブルに 500 を超える行を追加すると (たとえば、499 から開始されるテーブルに 500 行を追加すると、合計 999 行になります)、自動更新が表示されます。 
* 500 行を超える場合、自動更新された統計を表示するには、追加の 500 行と、テーブル サイズの 20% を追加する必要があります。

前回の統計が更新されてからテーブル内のデータが変更されたかどうかを判断するための DMV がないため、統計情報の経過期間がわかると、全体像の一部を把握できます。  以下のクエリでは、それぞれのテーブルで統計情報が最後に更新された時刻を確認できます。  

> [!NOTE]
> 指定した列の値の分布に重要な変更がある場合は、最後に更新された時刻に関係なく統計を更新する必要があります。  
> 
> 

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

たとえば、データ ウェアハウスの日付列では、通常、統計を頻繁に更新する必要があります。 新しい行がデータ ウェアハウスに読み込まれるたびに、新しい読み込みの日付またはトランザクションの日付が追加されます。 これらによってデータの分布が変わり、統計が古くなります。  一方、顧客テーブルの性別列の統計は更新する必要がないと考えられます。 顧客間で分布が一定であると仮定すると、テーブル バリエーションに新しい行を追加しても、データの分布が変わることはありません。 ただし、データ ウェアハウスに 1 つの性別しか含まれておらず、新しい要件によって複数の性別が含まれるようになった場合は、性別列の統計を更新する必要があることは明らかです。

詳細については、MSDN の「[統計][Statistics]」をご覧ください。

## <a name="implementing-statistics-management"></a>統計管理の実装
多くの場合、読み込みの終わりに統計が確実に更新されるように、データ読み込みプロセスを拡張することが推奨されます。 テーブルのサイズや値の分布が変わる頻度が最も高いのがデータの読み込み時です。 したがって、これが管理プロセスを実装する論理的な場所となります。

読み込みプロセスで統計を更新する際の基本原則は、次のとおりです。

* 読み込まれた各テーブルに更新された統計オブジェクトが少なくとも 1 つは含まれていることを確認します。 これにより、統計の更新の一環として、テーブル サイズ (行数とページ数) 情報が更新されます。
* JOIN、GROUP BY、ORDER BY、DISTINCT の各句に関与している列を重視します。
* トランザクションの日付などの "昇順キー" 列の値は、統計ヒストグラムに含まれないため、これらの列の更新頻度を増やすことを検討します。
* 静的な分布列の更新頻度を減らすことを検討します。
* 各統計オブジェクトは系列で更新されることに注意してください。 特に、多数の統計オブジェクトが含まれた幅の広いテーブルでは、 `UPDATE STATISTICS <TABLE_NAME>` を実装するだけでは十分とはいえない場合があります。

> [!NOTE]
> [昇順キー] の詳細については、SQL Server 2014 の基数推定モデルに関するホワイト ペーパーをご覧ください。
> 
> 

詳細については、MSDN の[基数推定][Cardinality Estimation]に関するページをご覧ください。

## <a name="examples-create-statistics"></a>例: 統計の作成
以下の例では、さまざまなオプションを使用して統計を作成する方法を示します。 各列に使用するオプションは、データの特性とクエリでの列の使用方法によって異なります。

### <a name="a-create-single-column-statistics-with-default-options"></a>A. 既定のオプションを使用した単一列統計の作成
列の統計を作成するには、統計オブジェクトの名前と列の名前を指定するだけです。

次の構文では、既定のオプションをすべて使用しています。 既定では、SQL Data Warehouse は統計を作成するときに、テーブルの 20% をサンプリングします。

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

次に例を示します。

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="b-create-single-column-statistics-by-examining-every-row"></a>B. すべての列の検査による単一列統計の作成
ほとんどの場合、20% という既定のサンプリング レートで十分です。 ただし、サンプリング レートを調整することもできます。

テーブル全体をサンプリングするには、次の構文を使用します。

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

次に例を示します。

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="c-create-single-column-statistics-by-specifying-the-sample-size"></a>C. サンプル サイズを指定した単一列統計の作成
サンプル サイズをパーセントで指定することもできます。

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="d-create-single-column-statistics-on-only-some-of-the-rows"></a>D. 一部の行のみの単一列統計の作成
テーブルの一部の行の統計を作成することもできます。 これは、フィルター選択された統計と呼ばれます。

たとえば、大規模なパーティション テーブルの特定のパーティションのクエリを計画するときに、フィルター選択された統計を使用できます。 パーティション値のみで統計を作成すると、統計の精度が向上するので、クエリのパフォーマンスが向上します。

次の例では、値の範囲の統計を作成します。 パーティションの値の範囲に一致する値を簡単に定義できます。

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> クエリ オプティマイザーが分散クエリ プランを選択するときに、フィルター選択された統計の使用も考慮されるようにするには、クエリが統計オブジェクトの定義の範囲内である必要があります。 前の例では、クエリの WHERE 句で col1 の値として 2000101 ～ 20001231 の値を指定する必要があります。
> 
> 

### <a name="e-create-single-column-statistics-with-all-the-options"></a>E. すべてのオプションを使用した単一列統計の作成
オプションは組み合わせることができます。 次の例では、カスタム サンプル サイズを指定してフィルター選択された統計オブジェクトを作成します。

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

詳細については、MSDN の [CREATE STATISTICS][CREATE STATISTICS] に関するページをご覧ください。

### <a name="f-create-multi-column-statistics"></a>F. 複数列統計の作成
複数列統計を作成するには、これまでの例を使用するだけですが、複数の列を指定します。

> [!NOTE]
> クエリ結果の行数の推定に使用されるヒストグラムは、統計オブジェクト定義に示されている最初の列にのみ使用できます。
> 
> 

次の例では、ヒストグラムは *product\_category* で使用されます。 列間の統計は、*product\_category* と *product\_sub_c\ategory* で計算されます。

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

*product\_category* と *product\_sub\_category* の間には相関関係があるため、これらの列に同時にアクセスする場合は複数列統計が役立ちます。

### <a name="g-create-statistics-on-all-the-columns-in-a-table"></a>G. テーブルのすべての列の統計の作成
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

### <a name="h-use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>H. ストアド プロシージャを使用した、データベース内のすべての列の統計の作成
SQL Data Warehouse には、SQL Server の [sp_create_stats][] に相当するシステム ストアド プロシージャはありません。 このストアド プロシージャは、まだ統計がないデータベースのすべての列の単一列統計オブジェクトを作成します。

これは、データベースの設計を開始する際に役立ちます。 ニーズに合わせて、このオブジェクトを自由に変更できます。

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

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
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
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

このプロシージャを使用して、テーブルのすべての列の統計を作成するには、プロシージャを呼び出すだけです。

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>例: 統計の更新
統計を更新するには、次の操作を行います。

1. 統計オブジェクトを 1 つ更新します。 更新する統計オブジェクトの名前を指定します。
2. テーブルのすべての統計オブジェクトを更新します。 特定の統計オブジェクトではなく、テーブルの名前を指定します。

### <a name="a-update-one-specific-statistics-object"></a>A. 1 つの特定の統計オブジェクトの更新
特定の統計オブジェクトを更新するには、次の構文を使用します。

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

次に例を示します。

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

特定の統計オブジェクトを更新することで、統計を管理するために必要な時間とリソースを最小限に抑えることができます。 ただし、この場合、更新する最適な統計オブジェクトの選択について少し検討する必要があります。

### <a name="b-update-all-statistics-on-a-table"></a>B. テーブルのすべての統計の更新
テーブルのすべての統計オブジェクトを更新する簡単な方法を次に示します。

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

次に例を示します。

```sql
UPDATE STATISTICS dbo.table1;
```

これは使いやすいステートメントです。 このステートメントはテーブルのすべての統計を更新するので、必要以上の処理が実行される可能性があります。 パフォーマンスが問題でない場合は、これが、統計が最新の状態であることを保証する最も簡単で最も包括的な方法であることは確かです。

> [!NOTE]
> テーブルのすべての統計を更新する場合、SQL Data Warehouse では、統計ごとにテーブルのスキャンを実行してサンプリングします。 テーブルが大きく、多数の列と統計が含まれている場合は、ニーズに基づいて個々の統計を更新する方が効率的です。
> 
> 

`UPDATE STATISTICS` プロシージャの実装については、[一時テーブル][Temporary]に関する記事をご覧ください。 実装方法は前述の `CREATE STATISTICS` プロシージャと若干異なりますが、最終的な結果は同じです。

完全な構文については、MSDN の [Update Statistics][Update Statistics] に関するページをご覧ください。

## <a name="statistics-metadata"></a>統計のメタデータ
統計に関する情報を確認する際に使用できるシステム ビューとシステム関数がいくつかあります。 たとえば、stats-date 関数を使用して、統計が最後に作成または更新されたのがいつであるかを確認することで、統計オブジェクトが古くなっているかどうかがわかります。

### <a name="catalog-views-for-statistics"></a>統計のカタログ ビュー
次のシステム ビューは、統計に関する情報を提供します。

| カタログ ビュー | Description |
|:--- |:--- |
| [sys.columns][sys.columns] |列ごとに 1 行。 |
| [sys.objects][sys.objects] |データベース内のオブジェクトごとに 1 行。 |
| [sys.schemas][sys.schemas] |データベースのスキーマごとに 1 行。 |
| [sys.stats][sys.stats] |統計オブジェクトごとに 1 行。 |
| [sys.stats_columns][sys.stats_columns] |統計オブジェクトの列ごとに 1 行。 sys.columns にリンク。 |
| [sys.tables][sys.tables] |テーブル (外部テーブルを含む) ごとに 1 行。 |
| [sys.table_types][sys.table_types] |データ型ごとに 1 行。 |

### <a name="system-functions-for-statistics"></a>統計のシステム関数
次のシステム関数は統計の操作に役立ちます。

| システム関数 | Description |
|:--- |:--- |
| [STATS_DATE][STATS_DATE] |統計オブジェクトの最終更新日。 |
| [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] |統計オブジェクトで認識される値の分布に関する概要レベルの情報と詳細情報を提供します。 |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>1 つのビューへの統計列と関数の統合
このビューには、統計に関連する列と、[STATS_DATE()][] 関数の結果が一緒に表示されます。

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
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
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>DBCC SHOW_STATISTICS() の例
DBCC SHOW_STATISTICS() は、統計オブジェクト内に保持されているデータを表示します。 このデータは 3 つの部分で提供されます。

1. ヘッダー
2. 密度ベクトル
3. ヒストグラム

ヘッダーには、統計に関するメタデータが含まれます。 ヒストグラムには、統計オブジェクトの最初のキー列の値の分布が表示されます。 密度ベクトルは、列間の相関関係を測定します。 SQLDW では、統計オブジェクト内のデータを使用して基数推定値を計算します。

### <a name="show-header-density-and-histogram"></a>ヘッダー、密度、ヒストグラムの表示
次の簡単な例は、統計オブジェクトの 3 つの部分をすべて表示します。

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

次に例を示します。

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>DBCC SHOW_STATISTICS(); の 1 つ以上の部分の表示
特定の部分だけを表示する場合は、`WITH` 句を使用して表示する部分を指定します。

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

次に例を示します。

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>DBCC SHOW_STATISTICS() の相違点
SQL Server に比べ、SQL Data Warehouse では、DBCC SHOW_STATISTICS() がより厳密に実装されています。

1. ドキュメントに記載されていない機能はサポートされていません。
2. Stats_stream は使用できません。
3. 統計データの特定のサブセットの結果を結合することはできません (STAT_HEADER JOIN DENSITY_VECTOR など)。
4. メッセージを抑制するために、NO_INFOMSGS を設定することはできません。
5. 統計名を囲む角かっこは使用できません。
6. 列名を使用して、統計オブジェクトを識別することはできません。
7. カスタム エラー 2767 はサポートされていません。

## <a name="next-steps"></a>次のステップ
MSDN の [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] に関するページで詳細を確認します。  [テーブルの概要][Overview]、[テーブルのデータ型][Data Types]、[テーブルの分散][Distribute]、[テーブルのインデックス作成][Index]、[テーブルのパーティション分割][Partition]、[一時テーブル][Temporary]に関する各記事で詳細を確認します。  [SQL Data Warehouse のベスト プラクティス][SQL Data Warehouse Best Practices]に関する記事でベスト プラクティスの詳細を確認します。  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->  
[Cardinality Estimation]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Statistics]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  

