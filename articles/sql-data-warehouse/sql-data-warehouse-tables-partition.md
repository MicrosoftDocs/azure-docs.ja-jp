---
title: "SQL Data Warehouse でのテーブルのパーティション分割 | Microsoft Docs"
description: "Azure SQL Data Warehouse でのテーブルのパーティション分割の概要です。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 6cef870c-114f-470c-af10-02300c58885d
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
ms.openlocfilehash: 0d2ff3ce90c355ba63f3fb66982baa621091ae6e
ms.lasthandoff: 02/16/2017


---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>SQL Data Warehouse でのテーブルのパーティション分割
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

パーティション分割は、クラスター化列ストア、クラスター化インデックス、ヒープなど、SQL Data Warehouse のすべてのテーブル型でサポートされます。  パーティション分割は、ハッシュ分散とラウンド ロビン分散の両方を含むあらゆる種類のディストリビューションでもサポートされます。  パーティション分割により、データはより小さなグループに分割されます。ほとんどの場合、パーティション分割は日付列で実行されます。

## <a name="benefits-of-partitioning"></a>パーティション分割のメリット
パーティション分割をすると、データのメンテナンスとクエリのパフォーマンスでメリットを得ることができます。  両方のメリットを得られるか、片方のみかは、データの読み込み方法と、同じ列を両方の目的で使用できるかどうかによります。その理由は、パーティション分割を実行できるのが 1 つの列のみであるためです。

### <a name="benefits-to-loads"></a>読み込みに対するメリット
SQL Data Warehouse でパーティション分割する主なメリットは、パーティションの削除、切り替え、および結合の使用による、データの読み込みの効率性とパフォーマンスの向上です。  ほとんどの場合、データは、データがデータベースに読み込まれる順序に密接に関連付けられている日付列でパーティション分割されます。  データを保持するためにパーティションを使用する最大のメリットの 1 つが、トランザクション ログの回避です。  単にデータを挿入、更新、または削除するのは最も簡単なアプローチですが、少しの配慮と労力を注いで読み込みプロセス中にパーティション分割を使用すると、大幅にパフォーマンスを向上できます。

テーブルのセクションを手早く削除したり置き換えたりするには、パーティションの切り替えを使用できます。  たとえば、売上のファクト テーブルに過去 36 か月のデータのみが含まれるとします。  毎月末に、最も古い月の売上データがテーブルから削除されます。  このデータは、最も古い月のデータを削除する delete ステートメントを使用して削除できます。  ただし、大量のデータを delete ステートメントで行単位で削除すると、非常に長い時間がかかるだけでなく、問題が生じた場合に、トランザクションが巨大なことにより、ロールバックに時間がかかるリスクが生じる可能性があります。  より最適なアプローチは、単にデータの最も古いパーティションを削除する方法です。  個々の行の削除に時間がかかる可能性がある場合、パーティション全体を削除すると、わずかな時間で終了する可能性があります。

### <a name="benefits-to-queries"></a>クエリに対するメリット
パーティション分割により、クエリのパフォーマンスを向上させることもできます。  クエリで、パーティション分割された列にフィルターを適用すると、限定されたパーティションのみにスキャンを制限でき、対象のパーティションがより小さなデータのサブセットになる可能性があるため、フル テーブル スキャンを回避できます。  クラスター化列ストア インデックスの導入では、述語の削除によるパフォーマンスのメリットはあまりありませんが、クエリにメリットをもたらす場合があります。  たとえば、売上のファクト テーブルが販売日フィールドを使用して 36 か月にパーティション分割されている場合は、販売日をフィルター処理するクエリによって、フィルターと一致しないパーティションの検索を省略できます。

## <a name="partition-sizing-guidance"></a>パーティションのサイズ設定のガイダンス
パーティション分割を使用すると一部のシナリオのパフォーマンスが向上する可能性がありますが、パーティションが **多すぎる** テーブルを作成すると、特定の状況でパフォーマンスが低下する可能性があります。  これらの問題は、特にクラスター化列ストア テーブルに当てはまります。  パーティション分割が役立つように、パーティション分割を使用する時期と作成するパーティション数を把握することが重要です。  パーティションの数が多すぎるかどうかについて厳格なルールはなく、データと、同時に読み込むパーティションの数によります。  ただし一般的な経験則として、数千ではなく、数十から数百のパーティションを追加することを検討してください。

**クラスター化列ストア** テーブルでパーティションを作成するときに、各パーティションに取り込まれる行数が重要になります。  クラスター化列ストア テーブルの圧縮とパフォーマンスを最適化するためには、ディストリビューションおよびパーティションあたり少なくとも 100 万行が必要です。  SQL Data Warehouse では、パーティションが作成される前に、各テーブルが 60 個の分散データベースに既に分割されています。  テーブルに追加されるすべてのパーティション分割は、バックグラウンドで作成されたディストリビューションに追加されたものです。  この例を使用して、売上のファクト テーブルに 36 か月のパーティションが含まれる場合、SQL Data Warehouse に 60 のディストリビューションがあるとすると、売上のファクト テーブルは 1 か月あたり 6 千万行、すべての月を指定する場合は 21 億行を含む必要があります。  テーブルに含まれる行が、パーティションごとの推奨される最小の行数よりも大幅に少ない場合、パーティションあたりの行数を増やすためにパーティション数を少なくすることを検討する必要があります。  また、[インデックス作成][Index]に関する記事も参照してください。この記事には、クラスター化列ストア インデックスの質を評価するために SQL Data Warehouse で実行できるクエリについて記載されています。

## <a name="syntax-difference-from-sql-server"></a>SQL Server との構文の違い
SQL Data Warehouse では、SQL Server とは少し異なり、パーティションの簡略化された定義を導入しています。  パーティション関数とパーティション構成は、SQL Server のものであるため、SQL Data Warehouse では使用されません。  代わりに、必要なのは、パーティション分割された列と境界点を特定することだけです。  パーティション分割の構文は、SQL Server と若干異なる場合がありますが、基本的な概念は同じです。  SQL Server および SQL Data Warehouse は、テーブルごとに 1 つのパーティション列をサポートします。このパーティション列で、範囲指定によるパーティションを指定することができます。  パーティション分割の詳細については、「[パーティション テーブルとパーティション インデックス][Partitioned Tables and Indexes]」をご覧ください。

次の例は、SQL Data Warehouse のパーティション分割が指定された [CREATE TABLE][CREATE TABLE] ステートメントを示しています。FactInternetSales テーブルが OrderDateKey 列でパーティション分割されます。

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>SQL Server からのパーティション分割の移行
SQL Server のパーティション定義を SQL Data Warehouse に単純に移行するには、次の操作を行います。

* SQL Server の[パーティション構成][partition scheme]を除去します。
* [パーティション関数][partition function]の定義を CREATE TABLE に追加します。

パーティション分割されたテーブルを SQL Server インスタンスから移行する場合、各パーティションに含まれる行数を調査するうえで以下の SQL が役立つ場合があります。  SQL Data Warehouse で同じパーティション分割の粒度を使用する場合、パーティションごとの行数が 60 の倍数で減少することに注意してください。  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>ワークロード管理
テーブル パーティションを決定する際の考慮事項の最後の部分として、[ワークロード管理][workload management]があります。  SQL Data Warehouse のワークロード管理は、主にメモリと同時実行の管理です。  SQL Data Warehouse では、リソース クラスによって、クエリの実行中に各ディストリビューションに割り当てられる最大メモリが管理されます。  理論的には、パーティションは、クラスター化列ストア インデックスの作成に必要なメモリなどのその他の要因を考慮してサイズ変更されます。  多くのメモリを割り当てると、クラスター化列ストア インデックスのメリットが大きくなります。  したがって、パーティション インデックスの再構築でメモリ不足にならないようにする必要があります。 クエリで使用できるメモリの量を増やすには、既定のロール smallrc から largerc などの他のいずれかのロールに切り替えます。

ディストリビューションごとのメモリの割り当てに関する情報は、リソース ガバナーの動的管理ビューを照会することで入手できます。 実際には、メモリ許可は次の数値よりも少なくなります。 ただし、データ管理操作のためにパーティションのサイズを設定するときに、これがある程度の目安になります。  非常に大きなリソース クラスで提供されるメモリ許可を上回るパーティション サイズに設定しないようにしてください。 パーティションのサイズがこの数値を上回ると、メモリ負荷のリスクが生じ、最適に圧縮できなくなります。

```sql
SELECT  rp.[name]                                AS [pool_name]
,       rp.[max_memory_kb]                        AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                    AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576                AS [mex_memory_gb]
,       rp.[max_memory_percent]                    AS [max_memory_percent]
,       wg.[name]                                AS [group_name]
,       wg.[importance]                            AS [group_importance]
,       wg.[request_max_memory_grant_percent]    AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups    wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools    rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>パーティションの切り替え
SQL Data Warehouse では、パーティションの分割、結合、および切り替えをサポートします。 これらの各機能は、[ALTER TABLE][ALTER TABLE] ステートメントを使用して実行されます。

2 つのテーブル間でパーティションを切り替えるには、それぞれの境界に合わせてパーティションが配置されていることと、テーブル定義が一致していることを確認する必要があります。 テーブルで値の範囲を適用する際に CHECK 制約は使用できないため、ソース テーブルにターゲットテーブルと同じパーティション境界が含まれている必要があります。 そうでない場合、パーティションのメタデータが同期されないため、パーティションの切り替えは失敗します。

### <a name="how-to-split-a-partition-that-contains-data"></a>データが含まれたパーティションを分割する方法
既にデータが含まれているパーティションを分割する最も効率的な方法は、 `CTAS` ステートメントを使用することです。 パーティション テーブルがクラスター化列ストアの場合、テーブルのパーティションを分割するには、パーティションを空にしておく必要があります。

各パーティションに行が 1 つ含まれた、パーティション分割された列ストア テーブルのサンプルを次に示します。

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [!NOTE]
> 統計オブジェクトを作成することによって、テーブルのメタデータをより正確なものにすることができます。 統計の作成を省略した場合は、SQL Data Warehouse では既定値が使用されます。 統計の詳細については､[統計][statistics]に関する記事をご覧ください。
> 
> 

次に、 `sys.partitions` カタログ ビューを使用して行数を照会できます。

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

このテーブルを分割しようとすると、次のエラーが発生します。

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

パーティションが空でないため、ALTER PARTITION ステートメントの Msg 35346, Level 15, State 1, Line 44 SPLIT 句が失敗しました。  テーブルに列ストア インデックスが存在する場合、分割できるのは空のパーティションだけです。 ALTER PARTITION ステートメントを発行する前に列ストア インデックスを無効にし、ALTER PARTITION が完了したら列ストア インデックスを再構築することを検討します。

ただし、 `CTAS` を使用して、データを保持する新しいテーブルを作成できます。

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

パーティション境界が配置されているので、切り替えが許可されます。 これにより、後で分割できる空のパーティションを含むソース テーブルをそのままにしておくことができます。

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

後は、 `CTAS` を使用して新しいパーティション境界に合わせてデータを配置し、データをメイン テーブルに切り替えるだけです。

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

データの移動が完了したら、ターゲット テーブルの統計に、それぞれのパーティションのデータの新しいディストリビューションが正確に反映されるように、統計を更新することをお勧めします。

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>テーブル パーティションのソース管理
ソース管理システムでテーブル定義が **古く** ならないように、次の方法を検討することをお勧めします。

1. パーティション値を含まないパーティション テーブルとしてテーブルを作成します。

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

1. `SPLIT` (分割) します。

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

この方法では、ソース管理のコードを静的なコードとして維持し、パーティション境界値は動的にすることが可能になるので、時間の経過に伴って、ウェアハウスとともに進化させることができます。

## <a name="next-steps"></a>次のステップ
[テーブルの概要][Overview]、[テーブルのデータ型][Data Types]、[テーブルの分散][Distribute]、[テーブルのインデックス作成][Index]、[テーブルの統計の管理][Statistics]、[一時テーブル][Temporary]に関する各記事で詳細を確認します。  [SQL Data Warehouse のベスト プラクティス][SQL Data Warehouse Best Practices]に関する記事でベスト プラクティスの詳細を確認します。

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[workload management]: ./sql-data-warehouse-develop-concurrency.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Partitioned Tables and Indexes]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[partition function]: https://msdn.microsoft.com/library/ms187802.aspx
[partition scheme]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->

