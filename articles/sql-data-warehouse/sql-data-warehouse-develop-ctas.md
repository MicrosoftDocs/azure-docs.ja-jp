---
title: Azure SQL Data Warehouse での CREATE TABLE AS SELECT (CTAS) | Microsoft Docs
description: ソリューションを開発するための、Azure SQL Data Warehouse での CREATE TABLE AS SELECT (CTAS) ステートメントに関する説明と例。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: 1b4ccd7742f8a84eec2d63a86e1387733d4c1864
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479693"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse での CREATE TABLE AS SELECT (CTAS)

この記事では、ソリューションを開発するための、Azure SQL Data Warehouse の CREATE TABLE AS SELECT (CTAS) T-SQL ステートメントについて説明します。 この記事では、コード例も示します。

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) (CTAS) ステートメントは、利用可能な最重要 T-SQL 機能のうちの 1 つです。 CTAS は、SELECT ステートメントの出力に基づいて新しいテーブルを作成する並列操作です。 CTAS は、単一のコマンドでデータを作成し、テーブルに挿入する最も簡単ですばやい方法です。

## <a name="selectinto-vs-ctas"></a>SELECT...INTO と CTAS

CTAS は、[SELECT...INTO](/sql/t-sql/queries/select-into-clause-transact-sql) ステートメントの、よりカスタマイズ可能なバージョンです。

SELECT...INTO の簡単な例を次に示します。

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

SELECT...INTO では、操作の一部として分散方法とインデックスの種類のいずれも変更することはできません。 既定の分散の種類に ROUND_ROBIN を、既定のテーブル構造に CLUSTERED COLUMNSTORE INDEX を使用して、`[dbo].[FactInternetSales_new]` を作成します。

一方、CTAS を使用すると、テーブル データの分散とテーブル構造の種類の両方を指定できます。 前の例を CTAS に変換するには:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
   ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [!NOTE]
> CTAS 操作でインデックスだけを変更しようとしており、ソース テーブルがハッシュ分散されている場合は、同じ分散列とデータ型を維持してください。 そうすることで、操作中に分散をまたがるデータ移動が回避されるため、効率が上がります。

## <a name="use-ctas-to-copy-a-table"></a>CTAS を使用したテーブルのコピー

CTAS の最も一般的な用途の 1 つは、DDL を変更するためにテーブルのコピーを作成することです。 たとえば、もともと `ROUND_ROBIN` として作成したテーブルを列の分散テーブルに変更するとします。 CTAS が、この分散列を変更する方法です。 CTAS を使用すると、パーティション分割、インデックス作成、または列の型を変更することもできます。

たとえば、`CREATE TABLE` で分散列を指定せず、既定の分散の種類である `ROUND_ROBIN` を使用して、このテーブルを作成したとします。

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

ここで、クラスター化列ストア テーブルのパフォーマンスを活かせるように、`Clustered Columnstore Index` でこのテーブルの新しいコピーを作成したいとします。 また、このテーブルを `ProductKey` で分散させたいとします。この列での結合が予想され、`ProductKey` での結合中にデータを移動したくないためです。 さらに、`OrderDateKey` にパーティションを追加して、古いパーティションをドロップすることによって古いデータをすぐに削除できるようにしたいとします。 次に示すのが、古いテーブルを新しいテーブルにコピーする CTAS ステートメントです。

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

## <a name="use-ctas-to-work-around-unsupported-features"></a>CTAS を使用したサポートされていない機能の回避

CTAS を使用して、下記のサポートされていない多くの機能を回避することもできます。 この方法は、多くの場合、コードが準拠するだけでなく、SQL Data Warehouse 上でより高速に実行されるため、有益です。 このパフォーマンスは、完全並列化設計の結果となります。 シナリオには以下が含まれます。

* ANSI JOINS を使用した UPDATE
* ANSI JOIN を使用した DELETE
* MERGE ステートメント

> [!TIP]
> "CTAS 優先" で考えましょう。 CTAS を使って問題を解決することは、結果として書き込むデータが増えたとしても、一般的には適切なアプローチです。

## <a name="ansi-join-replacement-for-update-statements"></a>UPDATE ステートメントの代わりに使用する ANSI JOIN

更新は、複雑になることがあります。 ANSI JOIN 構文を使用して UPDATE または DELETE を実行する、3 つ以上のテーブルを結合するような更新があります。

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

元のクエリは次の例のようなものです。

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

SQL Data Warehouse では、`UPDATE` ステートメントの `FROM` 句での ANSI JOIN がサポートされていないため、前の例を変更しないで使用することはできません。

CTAS と暗黙的結合を組み合わせて使用することで、前の例を置き換えることができます。

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

CTAS を使用するのが、データを削除する最適な方法である場合があります。特に、ANSI JOIN 構文が使用される `DELETE` ステートメントの場合です。 これは、SQL Data Warehouse では、`DELETE` ステートメントの `FROM` 句での ANSI JOIN がサポートされていないためです。 データを削除するのではなく、保持したいデータを選択します。

以下は、変換された `DELETE` ステートメントの例です。

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
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

CTAS を使用することにより、少なくとも部分的に、MERGE ステートメントを置き換えることができます。 `INSERT` および `UPDATE` を 1 つのステートメントに結合できます。 削除されたレコードを `SELECT` ステートメントから、制限し、結果から除外する必要があります。

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
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>明示的にデータ型および出力の null 値の許容を示す

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

次のコードでは、同じ結果が得られません。

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

「result」の列では、式のデータ型と null 値の許容が引き継がれます。 データ型を引き継ぐと、結果にわずかな差異が出る場合があります。

次の例を試してみてください。

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

結果 (result) に格納される値が異なります。 result 列の永続化された値が他の式で使用されるため、エラーの深刻度が増します。

![CTAS の結果のスクリーンショット](media/sql-data-warehouse-develop-ctas/ctas-results.png)

これは、データを移行する場合に重要になります。 2 番目のクエリの方が正確ではありますが、問題があります。 ソース システムと比較するとデータが異なるため、移行中の整合性に疑問が出てきます。 これは、「正しくない」結果が実は正しいという珍しいケースなのです。

2 つの結果に差異があるのは、暗黙の型変換が原因です。 1 つ目の例の場合、テーブルで列が定義されています。 行が挿入されると、暗黙の型変換が実行されます。 2 つ目の例では、列のデータ型が式で定義されているため、暗黙の型変換は実行されません。

2 つ目の例の列が null 許容の列として定義されている一方で、1 つ目の例ではそのように定義されていないことにも注意してください。 1 つ目の例でテーブルが作成されるとき、列の null 値の許容は明示的に定義されました。 2 つ目の例では、式に応じて決まるため、既定で null 定義になります。

これらの問題を解決するには、CTAS ステートメントの SELECT 部分に、型の変換と null 値の許容を明示的に設定する必要があります。 これらのプロパティを "CREATE TABLE" で設定することはできません。
コードの修正方法を次の例に示します。

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

以下の点に注意してください。

* CAST または CONVERT を使用できます。
* null 値の許容を強制するには、COALESCE ではなく ISNULL を使用してください。 以下の注意を参照してください。
* ISNULL は最も外側の関数です。
* ISNULL の 2 つ目の部分は定数 (0) です。

> [!NOTE]
> null 値の許容を正しく設定するために、COALESCE ではなく ISNULL を使用することは重要です。 COALESCE は決定論的関数ではないため、式の結果は必ず null を許容します。 ISNULL の場合は異なります。 これは決定論的です。 そのため、ISNULL 関数の 2 番目の部分が定数またはリテラルの場合、結果の値は NOT NULL になります。

計算の整合性を確保することは、テーブル パーティションの切り替えでも重要になります。 このテーブルをファクト テーブルとして定義したものとします。

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

ただし、金額フィールドは計算式です。 ソース データの一部ではありません。

パーティション分割されたデータセットを作成するために、次のコードを使用することが必要になる場合があります。

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

クエリは、問題なく実行されます。 問題が発生するのは、パーティションの切り替えを行うときです。 テーブルの定義が一致ません。 テーブルの定義を一致させるには、CTAS を変更して、`ISNULL` 関数を追加し、列の NULL 値の許容属性を保持します。

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

CTAS では、型の一貫性と null 値の許容プロパティを保持することが、エンジニアリング上のベスト プラクティスだということがわかります。 計算の整合性を維持するのに役立つほか、パーティションの切り替えも確実に実行できます。

CTAS は、SQL Data Warehouse で最も重要なステートメントの 1 つです。 よく理解しておいてください。 [CTAS のドキュメント](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)を参照してください。

## <a name="next-steps"></a>次の手順

開発に関するその他のヒントについては、 [開発の概要](sql-data-warehouse-overview-develop.md)に関するページをご覧ください。

