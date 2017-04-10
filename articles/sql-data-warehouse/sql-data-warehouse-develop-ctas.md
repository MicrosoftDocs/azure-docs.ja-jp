---
title: "SQL Data Warehouse の CREATE TABLE AS SELECT (CTAS) | Microsoft Docs"
description: "ソリューションの開発のために、Azure SQL Data Warehouse の CREATE TABLE AS SELECT (CTAS) ステートメントでコーディングする際のヒントです。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 68ac9a94-09f9-424b-b536-06a125a653bd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 01/30/2017
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 68655fff239bfd76f93ab9177d161d9534cbb901
ms.openlocfilehash: 150113dda95ab021dd7ad8696b5886373ba982b8
ms.lasthandoff: 01/31/2017


---
# <a name="create-table-as-select-ctas-in-sql-data-warehouse"></a>SQL Data Warehouse での CREATE TABLE AS SELECT (CTAS)
Create table as select ( `CTAS` ) は利用可能な最重要 T-SQL 機能のうちの&1; つです。 これは SELECT ステートメントの出力に基づいて新しいテーブルを作成する完全に並列化された操作です。 `CTAS` はテーブルのコピーを最も簡単かつすばやく作成する方法です。 このドキュメントには `CTAS`の例とベスト プラクティスの両方が記載されています。

## <a name="selectinto-vs-ctas"></a>SELECT..INTO とCTAS
`CTAS` を `SELECT..INTO` の高機能バージョンであると見なすことができます。

`SELECT..INTO` ステートメントの簡単な例を次に示します。

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

上記の例では、Azure SQL Data Warehouse にテーブルの規定値があるため、`[dbo].[FactInternetSales_new]` は CLUSTERED COLUMNSTORE INDEX を伴う ROUND_ROBIN 分散テーブルとして作成されます。

しかし `SELECT..INTO` では、操作の一部として分散方法とインデックスの種類のいずれも変更することはできません。 ここで、`CTAS` の出番です。

上記のステートメントを `CTAS` に変換するのはとても簡単です。

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,    CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

`CTAS` を使用することで、テーブル データの分散とテーブル タイプの両方を変更できます。 

> [!NOTE]
> `CTAS` 操作でインデックスを変更しようとしており、ソース テーブルがハッシュ分散されている場合、同じ分散列とデータ タイプを維持すると、`CTAS` 操作が最も適切に実行されます。 これにより、操作中に分散をまたがるデータ移動が回避されるため、効率が上がります。
> 
> 

## <a name="using-ctas-to-copy-a-table"></a>CTAS を使用したテーブルのコピー
`CTAS` の最も一般的な用途の&1; つは、DDL を変更できるようにテーブルのコピーを作成することです。 たとえば、もともと `ROUND_ROBIN` として作成したテーブルを列の分散テーブルに変更する場合、`CTAS` を使用して分散列を変更します。 `CTAS` を使用すると、パーティション分割、インデックス作成、列の型を変更することもできます。

たとえば、`CREATE TABLE` で分散列が指定されなかったので、分散された `ROUND_ROBIN` の既定の分散タイプを使用して、このテーブルを作成したとします。

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

クラスター化列ストア テーブルのパフォーマンスの利点を得られるように、クラスター化列ストア インデックスでこのテーブルの新しいコピーを作成します。 また、この列で結合が予想され、ProductKey での結合の間のデータを移動したくないので、このテーブルを ProductKey に分散させます。 最後に OrderDateKey にパーティションを追加して、古いパーティションをドロップして古いデータをすぐに削除できるようにします。 次に示すのが、古いテーブルを新しいテーブルにコピーする CTAS ステートメントです。

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

最後に、テーブルの名前を変更して新しいテーブルにスワップし、古いテーブルをドロップできます。

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [!NOTE]
> Azure SQL Data Warehouse は、統計の自動作成または自動更新をまだサポートしていません。  クエリから最高のパフォーマンスを取得するには、最初の読み込み後またはそれ以降のデータの変更後に、すべてのテーブルのすべての列で統計を作成することが重要です。  統計の詳細については、開発トピック グループの[統計][Statistics]に関するトピックを参照してください。
> 
> 

## <a name="using-ctas-to-work-around-unsupported-features"></a>CTAS を使用したサポートされていない機能の回避
`CTAS` を使用して、下記のサポートされていない多くの機能を回避することもできます。 この機能は、ユーザーのコードに対応できるというだけでなく、SQL Data Warehouse 上でより高速に実行されるという効果があります。 これは、完全に並列化された設計により可能になりました。 CTAS で対処できるシナリオは次のとおりです。

* ANSI JOINS を使用した UPDATE    
* ANSI JOIN を使用した DELETE
* MERGE ステートメント

> [!NOTE]
> 「まず最初に CTAS」を検討しましょう。 `CTAS` を使用して問題を解決できると思われる場合は、たとえ追加データを結果的に書き込むことになったとしても、それが一般的に最善の対処方法です。
> 
> 

## <a name="ansi-join-replacement-for-update-statements"></a>UPDATE ステートメントの代わりに使用する ANSI JOIN
UPDATE または DELETE を実行するための ANSI JOIN 構文を使用して、2 つ以上のテーブルを結合するのは、複雑な更新方法だと思われるかもしれません。

このテーブルを更新しなければならない状況だと想定します。

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

元のクエリは次のようだったとします。

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

SQL Data Warehouse では、`UPDATE` ステートメントの `FROM` 句で ANSI JOIN がサポートされていないため、このコードは若干の変更を加えないとコピーできません。

`CTAS` と暗黙的結合を組み合わせて使用することで、このコードを置き換えます。

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>DELETE ステートメントの代わりに使用する ANSI JOIN 
最善のデータ削除方法は `CTAS` を使用することである場合があります。 単にデータを削除するのではなく、保持したいデータを選択するということです。 これは ANSI JOIN 構文を使用する `DELETE` ステートメントの場合に特に当てはまります。SQL Data Warehouse では、`DELETE` ステートメントの `FROM` 句で ANSI JOIN がサポートされていないためです。

変換された DELETE ステートメントの例を次に示します。

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>MERGE ステートメントの代わりに使用
少なくとも部分的に `CTAS`を使用することにより、MERGE ステートメントを置き換えることができます。 `INSERT` および `UPDATE` を&1; つのステートメントに統合できます。 削除されたレコードはすべて、2 つ目のステートメントで閉じる必要があります。

`UPSERT` の例を次に示します。

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>CTAS での推奨事項: 明示的にデータ型および出力の null 値の許容を示す
コードを移行するときに、このようなコーディング パターンが見られる場合があります。

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

このコードを CTAS に移行することが正しいと思われるかもしれません。 しかし、これには問題が潜んでいます。

次のコードからは、同じ結果が得られないからです。

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

「result」の列では、式のデータ型と null 値の許容が引き継がれます。 注意しないと、これによって結果にわずかな差異が出る場合があります。

次の例を試してください。

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

結果 (result) に格納される値が異なります。 result 列の永続化された値が他の式で使用されるため、エラーの深刻度が増します。

![][1]

これは、データを移行する場合に特に重要になります。 2 番目のクエリのほうが正確ではありますが、問題があります。 ソース システムと比較するとデータが異なるため、移行中の整合性に疑問が出てきます。 これは、「正しくない」結果が実は正しいという珍しいケースなのです。

この&2; つの結果に差異があるのは、暗黙の型変換が原因です。 1 つ目の例の場合、テーブルで列が定義されています。 行が挿入されると、暗黙の型変換が実行されます。 2 つ目の例では、列のデータ型が式で定義されているため、暗黙の型変換は実行されません。 2 つ目の例の列が null 許容の列として定義されている一方で、1 つ目の例の列はそのように定義されていないことにも注意してください。 1 つ目の例でテーブルが作成されるとき、列の null 値の許容は明示的に定義されました。 2 つ目の例では、列の null 値の許容は定義されていないため、既定で null 定義になります。  

これらの問題を解決するには、`CTAS` ステートメントの `SELECT` 部分に、型の変換と null 値の許容を明示的に設定する必要があります。 これらのプロパティは、CREATE TABLE  部分で設定することはできません。

次の例で、このコードの修正方法を示しています。

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

以下の点に注意してください。

* CAST または CONVERT を使用することができます
* ISNULL は、COALESCE ではなく NULLability を強制するために使用されます
* ISNULL は最も外側の関数です
* ISNULL の 2 つ目の部分は定数 (つまり 0) です

> [!NOTE]
> null 値の許容を正しく設定するために、`COALESCE` ではなく `ISNULL` を使用することは重要です。 `COALESCE` は決定的関数ではありませんので、式の結果は必ず null を許容します。 `ISNULL` は異なります。 ISNULL は確定的な関数です。 そのため、 `ISNULL` 関数の&2; 番目の部分が定数またはリテラルの場合、結果の値は NOT NULL になります。
> 
> 

このヒントは、計算の整合性を確保する上で役立つだけではありません。 テーブル パーティションの切り替えでも重要になります。 このテーブルが、ファクトとして定義されていると考えてください。

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

ただし、値フィールドは計算された式で、ソース データの一部ではありません。

パーティション分割されたデータセットを作成するには、次を実行してください。

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

クエリは、問題なく実行されます。 問題が発生するのは、パーティションの切り替えを実行するときです。 テーブルの定義が一致しないためです。 テーブルの定義を一致させるには、CTAS を変更する必要があります。

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

そのため、CTAS では、型の一貫性と null 値の許容プロパティを保持することが、エンジニアリング上のベスト プラクティスだということがわかります。 計算の整合性を維持するのに役立つほか、パーティションの切り替えも確実に実行できるからです。

[CTAS][CTAS] の使用方法に関する詳細については、MSDN を参照してください。 これは、Azure SQL Data Warehouse で最も重要なステートメントの&1; つです。 よく理解しておいてください。

## <a name="next-steps"></a>次のステップ
開発に関するその他のヒントについては、[開発の概要][development overview]のページをご覧ください。

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[CTAS]: https://msdn.microsoft.com/library/mt204041.aspx

<!--Other Web references-->

