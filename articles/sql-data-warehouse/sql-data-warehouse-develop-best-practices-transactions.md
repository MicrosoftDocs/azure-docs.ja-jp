---
title: Azure SQL Data Warehouse のトランザクションの最適化 | Microsoft Docs
description: ロールバックに長時間かかるリスクを最小限に抑えながら、Azure SQL Data Warehouse でトランザクション コードのパフォーマンスを最適化する方法について説明します。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/19/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2299c526dd63eb8e8772661ee8fae66153fc36c3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479672"
---
# <a name="optimizing-transactions-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse でのトランザクションの最適化
ロールバックに長時間かかるリスクを最小限に抑えながら、Azure SQL Data Warehouse でトランザクション コードのパフォーマンスを最適化する方法について説明します。

## <a name="transactions-and-logging"></a>トランザクションとログ記録
トランザクションは、リレーショナル データベース エンジンの重要な要素です。 SQL Data Warehouse では、データに変更を加える際にトランザクションを使用します。 これらのトランザクションは、明示的に指定することも、暗黙的に指定することもできます。 INSERT ステートメント、UPDATE ステートメント、および DELETE ステートメントはすべて、暗黙的なトランザクションの例です。 明示的なトランザクションでは、BEGIN TRAN、COMMIT TRAN、または ROLLBACK TRAN を使用します。 明示的なトランザクションは、一般的には、複数の変更ステートメントを関連付けて 1 つのアトミック単位にする必要がある場合に使用します。 

Azure SQL Data Warehouse では、トランザクション ログを使用してデータベースに変更をコミットします。 ディストリビューションには、それぞれ独自のトランザクション ログがあります。 トランザクション ログの書き込みは自動で行われるため、 手動で構成する必要はありません。 ただし、このプロセスでは書き込みが保証されず、システムにオーバーヘッドが加わります。 この影響を最小限に抑えるには、トランザクションの効率を考慮してコードを記述してください。 トランザクションの効率が良いコードは、大きく分けて 2 つのカテゴリに分類されます。

* できるだけ、最小ログ記録コンストラクトを使用する
* 単独で実行時間の長いトランザクションを避けるために、範囲を制限したバッチを使用してデータを処理する
* 特定のパーティションに対する大規模な変更に対しては、パーティション切り替えパターンを使用する

## <a name="minimal-vs-full-logging"></a>最小ログ記録と完全ログ記録の比較
完全にログに記録される操作では、トランザクション ログにすべての行の変更が記録されるのに対して、最小限のログが記録される操作ではエクステントの割り当てとメタデータの変更のみが記録されます。 そのため、最小ログ記録では、障害の発生後、または明示的な要求 (ROLLBACK TRAN) によってトランザクションをロールバックするために必要な情報のみが記録されます。 最小ログ記録操作では、トランザクション ログに記録される情報がはるかに少なくなるため、同じサイズの完全ログ記録操作よりも処理速度が速くなります。 また、トランザクション ログへの書き込みが少なくなるため、生成されるログ データの量も少なくなり、I/O 効率が高くなります。

トランザクションの安全上の制限は、完全ログ記録操作にのみ適用されます。

> [!NOTE]
> 最小ログ記録操作は、明示的なトランザクションに含めることができます。 割り当て構造に対する変更はすべて記録されるため、最小ログ記録操作のロールバックが可能です。 
> 
> 

## <a name="minimally-logged-operations"></a>最小ログ記録操作
次の操作は、最小ログ記録が可能です。

* CREATE TABLE AS SELECT ([CTAS](sql-data-warehouse-develop-ctas.md))
* INSERT..SELECT
* CREATE INDEX
* ALTER INDEX REBUILD
* DROP INDEX
* TRUNCATE TABLE
* DROP TABLE
* ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> 内部データの移動操作 (BROADCAST や SHUFFLE など) は、トランザクションの安全上の制限の影響を受けません。
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>一括読み込みを使用した最小ログ記録
CTAS と INSERT...SELECT は、どちらも一括読み込み操作です。 ただし、どちらもターゲットのテーブル定義や読み込みシナリオによる影響を受けます。 次の表で、一括操作が完全に記録される場合と最小で記録される場合について説明します。  

| プライマリ インデックス | 読み込みシナリオ | ログ モード |
| --- | --- | --- |
| ヒープ |任意 |**最小** |
| クラスター化インデックス |空のターゲット テーブル |**最小** |
| クラスター化インデックス |読み込まれる行がターゲットの既存のページと重複しない |**最小** |
| クラスター化インデックス |読み込まれる行がターゲットの既存のページと重複する |完全 |
| クラスター化列ストア インデックス |パーティションに合わせて整列されたディストリビューションあたりのバッチ サイズが 102,400 以上 |**最小** |
| クラスター化列ストア インデックス |パーティションに合わせて整列されたディストリビューションあたりのバッチ サイズが 102,400 未満 |完全 |

セカンダリ インデックスや非クラスター化インデックスを更新するための書き込みは常に完全ログ記録操作である点に注意してください。

> [!IMPORTANT]
> SQL Data Warehouse には 60 のディストリビューションがあります。 そのため、すべての行が均等に分散されると仮定すると、1 つのパーティションに格納される場合、クラスター化列ストア インデックスに書き込む際に最小ログ記録が適用されるには、バッチに 6,144,000 行以上を含める必要があります。 テーブルがパーティション分割されていて、行がパーティション境界をまたいで挿入される場合は、すべての行が均等に分散されると仮定すると、パーティション境界あたり 6,144,000 行を含める必要があります。 各ディストリビューション内の各パーティションに含める行は、ディストリビューションへの挿入に対する最小ログ記録のしきい値である 102,400 行を超える必要があります。
> 
> 

クラスター化インデックスを持つ空でないテーブルにデータを読み込むと、完全ログ記録の行と最小ログ記録の行が混在する場合がよくあります。 クラスター化インデックスは、ページのバランス木 (B ツリー) です。 既に書き込みが行われているページに別のトランザクションの行が含まれている場合、この書き込みは完全ログ記録になります。 一方、ページが空の場合は、そのページへの書き込みは最小ログ記録になります。

## <a name="optimizing-deletes"></a>削除の最適化
DELETE は完全ログ記録操作です。  テーブルまたはパーティションから大量のデータを削除する必要がある場合は、残しておきたいデータを `SELECT` する方が合理的です。これは、最小ログ記録操作として実行できます。  データを選択するには、[CTAS](sql-data-warehouse-develop-ctas.md) を使用して新しいテーブルを作成します。  テーブルを作成したら、[RENAME](/sql/t-sql/statements/rename-transact-sql) を使用して、古いテーブルを新しく作成したテーブルに置き換えます。

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>更新の最適化
UPDATE は完全ログ記録操作です。  テーブルまたはパーティション内の多数の行を更新する必要がある場合は、[CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) などの最小ログ記録操作を使用すると、効率が大幅に向上することがよくあります。

次の例では、最小ログ記録ができるように、テーブルの完全更新を CTAS に変換しています。

この例では、テーブル内の売上に割引金額をさかのぼって追加しています。

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> 大きなテーブルを作成し直す場合は、SQL Data Warehouse のワークロード管理機能が役立ちます。 詳細については、[「ワークロード管理用のリソース クラス](resource-classes-for-workload-management.md)」を参照してください。
> 
> 

## <a name="optimizing-with-partition-switching"></a>パーティションの切り替えを使用した最適化
[テーブル パーティション](sql-data-warehouse-tables-partition.md)内で大規模な変更を加える場合は、パーティション切り替えパターンを使用すると効率的です。 データが大幅に変更されていて、複数のパーティションにまたがっている場合は、それらのパーティションを反復処理すると同じ結果を得られます。

パーティション切り替えを実行する手順は次のとおりです。

1. 空の out パーティションを作成します。
2. "更新" を CTAS で実行します。
3. 既存のデータを out テーブルからスイッチ アウトします。
4. 新しいデータをスイッチ インします。
5. データをクリーンアップします。

ただし、切り替えるパーティションを識別できるように、次のヘルパー プロシージャを作成します。  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id] 
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

このプロシージャによって、コードの再利用が最大限に活かされ、パーティション切り替えの例がコンパクトになっています。

次のコードは、上記で説明した、完全なパーティション切り替えルーチンを実現する手順を示しています。

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>小さなバッチを使用した最小ログ記録
大規模なデータ変更操作の場合、操作をチャンクやバッチに分割して、作業単位のスコープを設定すると効率的になることがあります。

次のコードは、実践的な例を示しています。 この手法を強調するために、バッチ サイズは小さい数値に設定されていますが、 実際には、バッチ サイズはもっと大きい値になります。 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>一時停止とスケールのガイダンス
Azure SQL Data Warehouse では、データ ウェアハウスの[一時停止、再開、およびスケーリング](sql-data-warehouse-manage-compute-overview.md)をオンデマンドで実行できます。 SQL Data Warehouse の一時停止またはスケールを実行すると、実行中のトランザクションは直ちに終了し、開いているトランザクションはロールバックされる点を理解しておくことが重要です。 一時停止操作やスケール操作の前にワークロードによって時間のかかるデータ変更が発行されており、完了していない場合は、この作業を元に戻す必要があります。 この元に戻す操作によって、Azure SQL Data Warehouse データベースを一時停止またはスケーリングするのに長時間かかる場合があります。 

> [!IMPORTANT]
> `UPDATE` と `DELETE` はどちらも完全ログ記録操作であるため、これらの元に戻す/再実行操作には、同等の最小ログ記録操作よりもはるかに長い時間のかかることがあります。 
> 
> 

最善の策としては、実行中のデータ変更トランザクションが完了してから、SQL Data Warehouse の一時停止またはスケールを実行します。 ただし、このシナリオは、常に実用的であるわけではありません。 ロールバックに長時間かかる可能性を軽減するのに役立つ次のオプションを検討してください。

* 長時間かかる操作を [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) を使用して書き換える
* 操作をチャンクに分割し、行のサブセットに対して実行する

## <a name="next-steps"></a>次の手順
分離レベルとトランザクションの制限の詳細については、「 [SQL Data Warehouse のトランザクション](sql-data-warehouse-develop-transactions.md) 」を参照してください。  その他のベスト プラクティスの概要については、「[Azure SQL Data Warehouse のベスト プラクティス](sql-data-warehouse-best-practices.md)」を参照してください。

