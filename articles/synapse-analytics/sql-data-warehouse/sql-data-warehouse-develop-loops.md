---
title: T-SQL ループの使用
description: T-SQL ループを使用したソリューション開発と、Azure Synapse Analytics の専用 SQL プールでのカーソルの置換に関するヒント。
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
ms.openlocfilehash: a6c4eb98d77ece6e6ae130fd57cc263ee7e5ca64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683224"
---
# <a name="using-t-sql-loops-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics で専用 SQL プールに T-SQL ループを使用する

この記事では、T-SQL ループを使用してカーソルを置換する専用 SQL プール ソリューション開発のヒントについて説明します。

## <a name="purpose-of-while-loops"></a>WHILE ループの目的

Azure Synapse の専用 SQL プールでは、ステートメント ブロックを繰り返し実行するための [WHILE](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ループがサポートされています。 この WHILE ループは、指定された条件が true の場合に限り、またはコードが BREAK キーワードを使用してループを終了するまで実行されます。

ループは、SQL コードで定義されているカーソルを置き換えるために便利です。 また、SQL コードで記述されているほとんどすべてのカーソルは、高速順方向、読み取り専用など豊富です。 そのため、WHILE ループはカーソルの置換の優れた代替手段です。

## <a name="replacing-cursors-in-dedicated-sql-pool"></a>専用 SQL プールでのカーソルの置換

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
