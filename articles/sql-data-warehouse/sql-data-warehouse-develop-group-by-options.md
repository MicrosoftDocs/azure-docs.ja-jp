---
title: "SQL Data Warehouse の Group By オプション | Microsoft Docs"
description: "ソリューション開発のための Azure SQL Data Warehouse での Group By オプションの実装に関するヒント。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: f95a1e43-768f-4b7b-8a10-8a0509d0c871
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e8c486ca190ae2af650a7fa17843c84538dde8d9


---
# <a name="group-by-options-in-sql-data-warehouse"></a>SQL Data Warehouse の Group By オプション
[GROUP BY][GROUP BY] 句は、行のサマリー セットにデータを集計するのに使用します。 また、機能を拡張するオプションもありますが、Azure SQL Data Warehouse で直接サポートされていないので、対処する必要があります。

オプションは次のとおりです。

* GROUP BY with ROLLUP
* GROUPING SETS
* GROUP BY with CUBE

## <a name="rollup-and-grouping-sets-options"></a>Rollup および Grouping Sets オプション
ここで最も簡単なオプションは、 `UNION ALL` を使用して、明示的な構文に頼る代わりに、ロールアップを実行します。 結果はまったく同じです。

`ROLLUP` オプションを使用した Group By ステートメントの例は、以下のとおりです。

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

ROLLUP を使用して、次の集計を要求しました。

* 国とリージョン
* 国
* 総計

これを置き換えるには、 `UNION ALL`を使用して、同じ結果を返すために明示的に必要な集計を指定します。

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

GROUPING SETS の場合、同一のプリンシパルを採用し、見たい集計レベルの UNION ALL セクションのみ作成する必要があります。

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

CTAS の結果が、以下のように表示されます。

![][1]

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

## <a name="next-steps"></a>次のステップ
開発に関するその他のヒントについては、[「開発の概要」][開発の概要]をご覧ください。

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[GROUP BY]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->



<!--HONumber=Nov16_HO3-->


