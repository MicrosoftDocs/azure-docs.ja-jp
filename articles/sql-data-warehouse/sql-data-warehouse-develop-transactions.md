---
title: "SQL Data Warehouse のトランザクション | Microsoft Docs"
description: "ソリューション開発のための、Azure SQL Data Warehouse でのトランザクションの実装に関するヒント。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: ae621788-e575-41f5-8bfe-fa04dc4b0b53
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: a0582c71e786ae5365e39a5f161b63e946435b2e
ms.lasthandoff: 03/10/2017


---
# <a name="transactions-in-sql-data-warehouse"></a>SQL Data Warehouse のトランザクション
予想される通り、SQL Data Warehouse では、トランザクションは、データ ウェアハウスのワークロードの一部としてサポートされます。 ただし、SQL Data Warehouse のパフォーマンスを大規模に維持できるように、SQL Server と比べて一部の機能が制限されています。 この記事では、相違点について説明し、その他の制限事項を示します。 

## <a name="transaction-isolation-levels"></a>トランザクション分離レベル
SQL Data Warehouse では、ACID トランザクションを実装しています。 ただし、トランザクション サポートの分離は、 `READ UNCOMMITTED` に制限されており、これを変更することはできません。 データのダーティ リードが問題である場合、これを防ぐためのさまざまなコーディング方法を実装できます。 最も一般的な方法では、CTAS とテーブル パーティションの切り替え (スライディング ウィンドウ パターンとよく呼ばれます) の両方を利用して、ユーザーがまだ準備中のデータを照会できないようにします。 データを事前にフィルター処理するビューも、よく使用される方法です。  

## <a name="transaction-size"></a>トランザクション サイズ
1 回のデータ変更トランザクションには規模の面で制限があります。 現在、この制限は "ディストリビューションあたり" で適用されます。 このため、割り当ての合計を算出するには、制限とディストリビューション数を掛ける必要があります。 トランザクションの最大行数の近似値を求めるには、ディストリビューション上限を各列の合計サイズで割ります。 列の長さが異なる場合、最大サイズではなく列の平均長を利用することを検討してください。

下の表では、次のような想定が行われています。

* データは均等に分散されました 
* 行の平均長は 250 バイトです

| [DWU][DWU] | ディストリビューションあたりの上限 (GiB) | ディストリビューション数 | 最大トランザクション サイズ (GiB) | # ディストリビューションあたりの行数 | トランザクションあたりの最大行数 |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

トランザクション サイズ制限は、トランザクションあたりまたは操作あたりで適用されます。 すべての同時実行トランザクションにまたいで適用されることはありません。 そのため、このデータ量をログに書き込むことが各トランザクションに許可されます。 

ログに書き込まれるデータ量を最適化および最小化する方法については、[トランザクションのベスト プラクティス][Transactions best practices]に関する記事を参照してください。

> [!WARNING]
> 最大トランザクション サイズは、HASH または ROUND_ROBIN で分散し、データが均等に分散されたテーブルでのみ達成できます。 トランザクションによりデータが書き込まれ、分散が傾斜する場合、最大トランザクション サイズに到達する前に上限に到達する可能性があります。
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>トランザクションの状態
SQL Data Warehouse では、XACT_STATE() 関数の値 -2 を使用して、失敗したトランザクションを報告します。 これは、トランザクションが失敗し、ロールバックのためにのみマークされていることを意味します。

> [!NOTE]
> 失敗したトランザクションを示すために XACT_STATE 関数の -2 を使用するのは、SQL Server とは異なる動作です。 SQL Server では、コミットできないトランザクションを表すために値 -1 を使用します。 SQL Server では、コミット不可としてマークしなくても、トランザクション内で一部のエラーを許容できます。 たとえば、`SELECT 1/0` はエラーになりますが、トランザクションが強制的にコミット不可状態になることはありません。 また、SQL Server では、コミットできないトランザクションでの読み取りも許可されます。 ただし、SQL Data Warehouse では、これを行うことはできません。 SQL Data Warehouse のトランザクションの内部でエラーが発生した場合、自動的に -2 状態が入力されます。このため、ステートメントがロールバックされるまで、SELECT ステートメントをそれ以上実行することができなくなります。 したがって、コードを変更する必要がある場合は、アプリケーション コードを調べて、XACT_STATE() を使用しているかどうかを確認することが重要です。
> 
> 

たとえば、SQL Server では、次のようなトランザクションを目にすることがあります。

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

コードを上記の状態のままにしておくと、次のエラー メッセージが表示されます。

メッセージ 111233、レベル 16、状態 1、ライン 1 111233; 111233; 現在のトランザクションは中止され、保留中の変更はすべてロールバックされています。 原因: rollback-only 状態のトランザクションが、DDL、DML、または SELECT ステートメントの前に明示的にロールバックされませんでした。

ERROR_* 関数の出力も得られません。

SQL Data Warehouse では、コードを少し変更する必要があります。

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

想定される動作が見られるようになりました。 トランザクションのエラーが管理され、ERROR_* 関数が予想通りの値を示します。

変更点をまとめると、`CATCH` ブロック内でエラー情報の読み取り前にトランザクションの `ROLLBACK` が発生するようになりました。

## <a name="errorline-function"></a>Error_Line() 関数
SQL Data Warehouse では、ERROR_LINE() 関数を実装およびサポートしていないことにも注意してください。 この関数がコードに含まれている場合は、SQL Data Warehouse に準拠するために削除する必要があります。 代わりに、コードでクエリ ラベルを使用して同等の機能を実装します。 この機能の詳細については、[ラベル][LABEL]に関する記事を参照してください。

## <a name="using-throw-and-raiserror"></a>THROW と RAISERROR の使用
THROW は、SQL Data Warehouse で例外を発生させるための最新の実装ですが、RAISERROR もサポートされています。 ただし、注意が必要な相違点がいくつかあります。

* THROW では、ユーザー定義のエラー メッセージの番号として、100,000 ～ 150,000 の範囲の番号を指定することはできません。
* RAISERROR のエラー メッセージは 50,000 に固定されています。
* sys.messages の使用はサポートされていません。

## <a name="limitiations"></a>制限事項
SQL Data Warehouse には、トランザクションに関連する他の制限事項があります。

制限事項は次のとおりです。

* 分散トランザクションは使用できません。
* 入れ子になったトランザクションは使用できません。
* セーブ ポイントは使用できません。
* トランザクションに名前を付けることはできません。
* トランザクションにマークを付けることはできません。
* ユーザー定義トランザクション内では `CREATE TABLE` のような DDL はサポートされません。

## <a name="next-steps"></a>次のステップ
トランザクションの最適化の詳細については、[トランザクションのベスト プラクティス][Transactions best practices]に関するページを参照してください。  SQL Data Warehouse のその他のベスト プラクティスについては、[SQL Data Warehouse のベスト プラクティス][SQL Data Warehouse best practices]に関するページを参照してください。

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Transactions best practices]: ./sql-data-warehouse-develop-best-practices-transactions.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->

