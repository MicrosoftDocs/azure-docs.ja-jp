---
title: group by オプションの使用
description: Azure Synapse Analytics での専用 SQL プールの group by オプションの実装に関するヒント。
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3f0879aa9b6f9e084d0c51f0bb371740d333c1b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683258"
---
# <a name="group-by-options-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics での専用 SQL プールの group by オプション

この記事では、専用 SQL プールで GROUP BY オプションを実装するためのヒントを紹介します。

## <a name="what-does-group-by-do"></a>GROUP BY で行われる操作

[GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL 句は、行のサマリー セットにデータを集計します。 GROUP BY には、専用 SQL プールではサポートされないオプションがいくつかあります。 これらのオプションには、次の回避策があります。

* GROUP BY with ROLLUP
* GROUPING SETS
* GROUP BY with CUBE

## <a name="rollup-and-grouping-sets-options"></a>Rollup および Grouping Sets オプション

ここで最も簡単な選択肢は、明示的な構文に頼るのではなく、UNION ALL を使用してロールアップを実行することです。 結果はまったく同じです。

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

UNION ALL アプローチを使用して、GROUP BY WITH CUBE を作成することができます。 問題は、コードがすぐに複雑で扱いにくくなることです。 この問題を軽減するために、次のより高度なアプローチを使用することができます。

前の例を使用すると、最初の手順は、作成したい集計のすべてのレベルを定義する 'cube' を定義することです。

2 つの派生テーブルの CROSS JOIN によってすべてのレベルが生成されるため、それをメモしておいてください。 コードの残りの部分は、書式設定用です。

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

CTAS の結果を次の図に示します。

![Cube によるグループ化](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

2 番目の手順は、中間結果を格納するためのターゲット テーブルを指定することです。

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

3 番目に、集計を実行する列のキューブをループします。 #Cube 一時テーブルのすべての行に対して、クエリが 1 回実行されます。 T#Results 一時テーブルに結果が格納されます。

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

最後に、#Results 一時テーブルから読み取ることで、結果を戻すことができます。

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

コードをセクションに分割し、ループ構造を生成することによって、コードの管理と保守が容易になります。

## <a name="next-steps"></a>次のステップ

開発についてのその他のヒントは、[開発の概要](sql-data-warehouse-overview-develop.md)に関するページをご覧ください。
