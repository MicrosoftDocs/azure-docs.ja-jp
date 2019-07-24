---
title: SQL Data Warehouse への SQL コードの移行 | Microsoft Docs
description: ソリューション開発のための Azure SQL Data Warehouse への SQL コードの移行に関するヒント
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: fae3ae16ee0100ad446c0b6c7851553a3376bb4f
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400975"
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>SQL Data Warehouse への SQL コードの移行

この記事では、他のデータベースから SQL Data Warehouse にコードを移行するときに一般に行う必要があるコードの変更について説明します。 一部の SQL Data Warehouse 機能は分散環境で機能するように設計されているため、大幅にパフォーマンスを向上できます。 ただし、パフォーマンスと拡張性を維持するには、一部の機能が使用できなくなる場合もあります。

## <a name="common-t-sql-limitations"></a>一般的な T-SQL の制限事項

SQL Data Warehouse でサポートされていない最も一般的な機能を次に示します。 リンクをクリックすると、サポートされていない機能に対する解決策が表示されます。

* [ANSI JOIN を使用した更新][ANSI joins on updates]
* [ANSI JOIN を使用した削除][ANSI joins on deletes]
* [MERGE ステートメント][merge statement]
* 複数データベースの JOIN
* [カーソル][cursors]
* [INSERT..EXEC][INSERT..EXEC]
* OUTPUT 句
* インライン ユーザー定義関数
* 複数ステートメント関数
* 共通テーブル式
* [再帰共通テーブル式 (CTE)](#Recursive-common-table-expressions-(CTE)
* CLR 関数およびプロシージャ
* $partition 関数
* テーブル変数
* テーブル値パラメーター
* 分散トランザクション
* コミット/ロールバック処理
* トランザクションの保存
* 実行コンテキスト (EXECUTE AS)
* [rollup/cube/grouping セット オプションによる句ごとのグループ化][group by clause with rollup / cube / grouping sets options]
* [8 を超える入れ子のレベル][nesting levels beyond 8]
* [ビューを使用した更新][updating through views]
* [動的 SQL 文字列の MAX 以外のデータ型][no MAX data type for dynamic SQL strings]

こうした制限の大部分は回避できます。 上記の関連する開発記事に説明が記載されています。

## <a name="supported-cte-features"></a>サポートされる CTE 機能

SQL Data Warehouse では、共通テーブル式 (CTE) が部分的にサポートされています。  現時点でサポートされている CTE 機能を次に示します。

* CTE は、SELECT ステートメントで指定できます。
* CTE は、CREATE VIEW ステートメントで指定できます。
* CTE は、CREATE TABLE  AS SELECT (CTAS) ステートメントで指定できます。
* CTE は、CREATE REMOTE TABLE AS SELECT (CRTAS) ステートメントで指定できます。
* CTE は、CREATE EXTERNAL TABLE AS SELECT (CETAS) ステートメントで指定できます。
* リモート テーブルは、CTE から参照できます。
* 外部テーブルは、CTE から参照できます。
* 複数の CTE クエリ定義は、CTE で定義できます。

## <a name="cte-limitations"></a>CTE の制限事項

SQL Data Warehouse での共通テーブル式の制限事項を次に示します。

* CTE の後ろには単一の SELECT ステートメントを続ける必要があります。 INSERT、UPDATE、DELETE、MERGE ステートメントはサポートされていません。
* 自己参照を含む共通テーブル式 (再帰共通テーブル式) は、サポートされていません (以下のセクションを参照してください)。
* CTE で、複数の WITH 句を指定することはできません。 たとえば、CTE_query_definition にサブクエリが含まれている場合、そのサブクエリに、別の CTE を定義する入れ子になった WITH 句を含めることはできません。
* TOP 句が指定されている場合を除き、ORDER BY 句は、CTE_query_definition で使用できません。
* バッチの一部であるステートメントで CTE を使用する場合は、前のステートメント末尾にセミコロンを付ける必要があります。
* Sp_prepare によって作成されるステートメントで使用される場合、CTE は PDW の他の SELECT ステートメントと同様に動作します。 ただし、CTE が sp_prepare で準備される CETAS の一部として使用される場合、バインドを sp_prepare に対して実装する方法によって、動作が SQL Server および他の PDW ステートメントとは異なる場合があります。 CTE を参照する SELECT が CTE に存在しない間違った列を使用している場合、sp_prepare はエラーを検出せずに渡されますが、代わりに sp_execute でエラーがスローされます。

## <a name="recursive-ctes"></a>再帰 CTE

再帰 CTE は、SQL Data Warehouse ではサポートされていません。  再帰 CTE の移行は複雑であるため、複数の手順に分けて実行することをお勧めします。 通常、再帰的な中間クエリの反復処理時に、ループを使用したり、一時テーブルに値を取り込んだりできます。 一時テーブルに値が取り込まれたら、単一の結果セットとしてデータを戻すことができます。 [rollup/cube/grouping セット オプションによる句ごとのグループ化][group by clause with rollup / cube / grouping sets options]に関する記事でも、`GROUP BY WITH CUBE` の解決に同様のアプローチを採用しています。

## <a name="unsupported-system-functions"></a>サポートされていないシステム関数

また、サポートされていないシステム関数もいくつかあります。 データ ウェアハウジングで一般的に使用されている主なものを次に示します。

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

これらの問題の一部は回避できます。

## <a name="rowcount-workaround"></a>@@ROWCOUNT 対処法

@@ROWCOUNT がサポートされていない問題を回避するには、sys.dm_pdw_request_steps から最後の行数を取得するストアド プロシージャを作成して、DML ステートメントの後で `EXEC LastRowCount` を実行することです。

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>次の手順

サポートされているすべての T-SQL ステートメントの一覧については、「[Transact-SQL トピック][Transact-SQL topics]」をご覧ください。

<!--Image references-->

<!--Article references-->
[ANSI joins on updates]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ANSI joins on deletes]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[merge statement]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Transact-SQL topics]: ./sql-data-warehouse-reference-tsql-statements.md

[cursors]: ./sql-data-warehouse-develop-loops.md
[group by clause with rollup / cube / grouping sets options]: ./sql-data-warehouse-develop-group-by-options.md
[nesting levels beyond 8]: ./sql-data-warehouse-develop-transactions.md
[updating through views]: ./sql-data-warehouse-develop-views.md
[use of select for variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[no MAX data type for dynamic SQL strings]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
