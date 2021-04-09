---
title: T-SQL ループを使用する
description: Azure Synapse Analytics の Synapse SQL での T-SQL ループの使用、カーソルの置換、関連ソリューションの開発に関するヒントを紹介します。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0d83e1305a851bf6bafb6c4c79f5caf73f8e44b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120888"
---
# <a name="use-t-sql-loops-with-synapse-sql-in-azure-synapse-analytics"></a>Azure Synapse Analytics の Synapse SQL で T-SQL ループを使用する

この記事では、Synapse SQL での T-SQL ループの使用、カーソルの置換、関連ソリューションの開発に関する重要なヒントを提供します。

## <a name="purpose-of-while-loops"></a>WHILE ループの目的

Synapse SQL では、ステートメント ブロックを繰り返し実行するための [WHILE](/sql/t-sql/language-elements/while-transact-sql?preserve-view=true&view=sql-server-ver15) ループがサポートされています。 この WHILE ループは、指定された条件が true の場合に限り、またはコードが BREAK キーワードを使用してループを終了するまで実行されます。 

Synapse SQL のループは、SQL コードで定義されているカーソルを置き換える際に便利です。 また、SQL コードで記述されているほとんどすべてのカーソルは、高速順方向、読み取り専用など豊富です。 そのため、WHILE ループはカーソルの置換の優れた代替手段です。

## <a name="replace-cursors-in-synapse-sql"></a>Synapse SQL でのカーソルの置換

詳細に入る前に、次の質問について考慮する必要があります。"セット ベースの操作を使用するよう、このカーソルを書き換えることができますか?" 多くの場合、答えは "はい" であり、この方法が最適です。 セット ベースの操作は、1 行ずつの反復的なアプローチをとるよりも速く実行されます。

高速順方向の読み取り専用カーソルは、ループ構造で簡単に置き換えられます。 次のコードは、シンプルな例です。 このコード例は、データベース内のすべてのテーブルの統計を更新します。 ループ内のテーブルを反復処理することで、各コマンドは順番に実行されます。

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

開発についてのその他のヒントは、[開発の概要](develop-overview.md)に関するページをご覧ください。