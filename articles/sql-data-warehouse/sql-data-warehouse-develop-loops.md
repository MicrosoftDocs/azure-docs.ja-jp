---
title: "Azure SQL Data Warehouse で T-SQL のループを使う | Microsoft Docs"
description: "ソリューションを開発するための  Azure SQL Data Warehouse  でのTRANSACT-SQL のループと、カーソルの置換に関するヒント。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: f3384b81-b943-431b-bc73-90e47e4c195f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 43ab6a2f71ab51c50847b1ba5249f51c48e03fea
ms.openlocfilehash: d6409f1eb87787e5e023aa53b7b264116c9d8026
ms.lasthandoff: 01/24/2017


---
# <a name="loops-in-sql-data-warehouse"></a>SDL Data Warehouse でのループ
SQL Data Warehouse は、ステートメント ブロックを繰り返し実行するための [WHILE][WHILE] ループをサポートします。 これは、指定された条件が true の場合に限り、またはコードが `BREAK` キーワードを使用してループを終了するまで実行されます。 ループは、SQL コードで定義されているカーソルを置き換えるために特に便利です。 また、SQL コードで記述されているほとんどすべてのカーソルは、高速順方向、読み取り専用など豊富です。 そのため、カーソルを置き換える必要がある場合、 [WHILE] ループは多くの選択肢があります。

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>SQL Data Warehouse でのループの活用とカーソルの置換
ただし、始める前に、まず、自分で「このカーソルは、セット ベースの操作を使用して再作成できるか」と問いかけてください。 多くの場合、答えは「はい」であり、この方法が最適です。 セット ベースの操作は、1 行ずつの反復的なアプローチをとるよりも非常に速く実行されます。

高速順方向の読み取り専用カーソルは、ループ構造で簡単に置き換えることができます。 単純な例を次に示します。 このコード例は、データベース内のすべてのテーブルの統計を更新します。 ループ内のテーブルを反復処理することで、シーケンス内の各コマンドを実行できます。

最初に、個々のステートメントを識別するための一意の行番号が含まれている、一時テーブルを作成します。

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

次に、ループの実行に必要な変数を初期化します。

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

1 つずつ実行するステートメントをループします。

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

最後に、最初の手順で作成した一時テーブルを削除します。

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>次のステップ
開発に関するその他のヒントについては、「[開発の概要][development overview]」をご覧ください。

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[WHILE]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->

