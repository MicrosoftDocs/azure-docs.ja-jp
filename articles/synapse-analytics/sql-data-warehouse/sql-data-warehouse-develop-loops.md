---
title: T-SQL ループの使用
description: T-SQL ループを使用したソリューション開発と、Synapse SQL プールでのカーソルの置換に関するヒント。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 25dad01a54b6ffe08656379340f58e0fe70ec666
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213416"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>Synapse SQL プールでの T-SQL のループの使用

この記事では、T-SQL ループを使用して、カーソルを置換する SQL プール ソリューション開発のヒントについて説明します。

## <a name="purpose-of-while-loops"></a>WHILE ループの目的

Synapse SQL プールでは、ステートメント ブロックを繰り返し実行するための [WHILE](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ループがサポートされています。 この WHILE ループは、指定された条件が true の場合に限り、またはコードが BREAK キーワードを使用してループを終了するまで実行されます。

ループは、SQL コードで定義されているカーソルを置き換えるために便利です。 また、SQL コードで記述されているほとんどすべてのカーソルは、高速順方向、読み取り専用など豊富です。 そのため、WHILE ループはカーソルの置換の優れた代替手段です。

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Synapse SQL プールでのカーソルの置換

ただし、これを利用する前に次の質問をご検討してください:"セット ベースの操作を使用するよう、このカーソルを書き換えることができますか?"

多くの場合、答えは "はい" であり、この方法が最適です。 セット ベースの操作は、1 行ずつの反復的なアプローチをとるよりも速く実行されます。

高速順方向の読み取り専用カーソルは、ループ構造で簡単に置き換えることができます。 次の例は、シンプルな例です。 このコード例は、データベース内のすべてのテーブルの統計を更新します。 ループ内のテーブルを反復処理することで、各コマンドは順番に実行されます。

最初に、個々のステートメントを識別するための一意の行番号が含まれている、一時テーブルを作成します。

```sql
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

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

1 つずつ実行するステートメントをループします。

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

最後に、最初の手順で作成した一時テーブルを削除します。

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>次のステップ

開発についてのその他のヒントは、[開発の概要](sql-data-warehouse-overview-develop.md)に関するページをご覧ください。
