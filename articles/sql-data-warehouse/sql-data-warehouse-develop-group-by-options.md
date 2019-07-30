---
title: Azure SQL Data Warehouse での Group By オプションの使用 | Microsoft Docs
description: ソリューション開発のための Azure SQL Data Warehouse での Group By オプションの実装に関するヒント。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2f6614f32c31338c9cf4f00307c475db4e02f553
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479648"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>SQL Data Warehouse の Group By オプション
ソリューション開発のための Azure SQL Data Warehouse での Group By オプションの実装に関するヒント。

## <a name="what-does-group-by-do"></a>GROUP BY で行われる操作

[GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL 句は、行のサマリー セットにデータを集計します。 GROUP BY には、SQL Data Warehouse ではサポートされていないオプションがいくつかあります。 サポートされないオプションには、対処法があります。

オプションは次のとおりです。

* GROUP BY with ROLLUP
* GROUPING SETS
* GROUP BY with CUBE

## <a name="rollup-and-grouping-sets-options"></a>Rollup および Grouping Sets オプション
ここで最も簡単な選択肢は、UNION ALL を使用して、明示的な構文に頼る代わりに、ロールアップを実行します。 結果はまったく同じです。

次の例では、ROLLUP オプションと共に GROUP BY ステートメントを使用しています。
```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

上の例では、ROLLUP を使用することで、次の項目の集計を要求しています。

* 国とリージョン
* Country
* 総計

ROLLUP を置き換えて同じ結果が戻るようにするには、次のように UNION ALL を使用して必要な集計を明示的に指定できます。

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

GROUPING SETS を置き換えるには、サンプルの原則が適用されます。 表示する集計レベルの UNION ALL セクションを作成するだけでかまいません。

## <a name="cube-options"></a>Cube オプション
UNION ALL アプローチを使用して、GROUP BY WITH CUBE を作成することができます。 問題は、コードがすぐに複雑で扱いにくくなることです。 これを防ぐために、次のより高度なアプローチを使用することができます。

上記の例を使用してみましょう。

まず、作成したい集計のすべてのレベルを定義する 'cube' を定義します。 2 つの派生テーブルに CROSS JOIN と書き込むことが重要です。 これによって、すべてのレベルが生成されます。 コードの残りの部分は、書式設定用です。

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

CTAS の結果を次に示します。

![Cube によるグループ化](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

2 番目に、中間結果を格納するターゲット テーブルを指定します。

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

3 番目に、集計を実行する列のキューブをループします。 クエリが、#Cube 一時テーブルで行ごとに 1 回実行され、#Results 一時テーブルに結果が格納されます。

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

最後に、#Results 一時テーブルから簡単に読み取ることによって結果を戻すことができます。

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

コードをセクションに分割し、ループ構造を生成することによって、コードの管理と保守が容易になります。

## <a name="next-steps"></a>次の手順
開発についてのその他のヒントは、[開発の概要](sql-data-warehouse-overview-develop.md)に関するページをご覧ください。

