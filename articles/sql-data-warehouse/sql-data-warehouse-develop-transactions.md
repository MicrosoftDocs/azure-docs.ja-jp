---
title: Azure SQL Data Warehouse でのトランザクションの使用 | Microsoft Docs
description: ソリューション開発のための、Azure SQL Data Warehouse でのトランザクションの実装に関するヒント。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/22/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 7f00f8a25d0abf3af6d76b372b44145546a79879
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479603"
---
# <a name="using-transactions-in-sql-data-warehouse"></a>SQL Data Warehouse でのトランザクションの使用
ソリューション開発のための、Azure SQL Data Warehouse でのトランザクションの実装に関するヒント。

## <a name="what-to-expect"></a>ウィザードの内容
予想される通り、SQL Data Warehouse では、トランザクションは、データ ウェアハウスのワークロードの一部としてサポートされます。 ただし、SQL Data Warehouse のパフォーマンスを大規模に維持できるように、SQL Server と比べて一部の機能が制限されています。 この記事では、相違点について説明し、その他の制限事項を示します。 

## <a name="transaction-isolation-levels"></a>トランザクション分離レベル
SQL Data Warehouse では、ACID トランザクションを実装しています。 ただし、トランザクション サポートの分離レベルは、READ UNCOMMITTED に制限されており、このレベルは変更できません。 READ UNCOMMITTED で問題がある場合、これを防ぐためのさまざまなコーディング方法を実装できます。 最も一般的な方法では、CTAS とテーブル パーティションの切り替え (スライディング ウィンドウ パターンとよく呼ばれます) の両方を使用して、ユーザーがまだ準備中のデータを照会できないようにします。 データを事前にフィルター処理するビューも、よく使用される方法です。  

## <a name="transaction-size"></a>トランザクション サイズ
1 回のデータ変更トランザクションには規模の面で制限があります。 この制限は、ディストリビューションごとに適用されます。 このため、割り当ての合計を算出するには、制限とディストリビューション数を掛ける必要があります。 トランザクションの最大行数の近似値を求めるには、ディストリビューション上限を各列の合計サイズで割ります。 列の長さが異なる場合、最大サイズではなく列の平均長を利用することを検討してください。

下の表では、次のような想定が行われています。

* データは均等に分散されました 
* 行の平均長は 250 バイトです

## <a name="gen2"></a>Gen2

| [DWU](sql-data-warehouse-overview-what-is.md) | ディストリビューションあたりの上限 (GB) | ディストリビューション数 | 最大トランザクション サイズ (GB) | ディストリビューションあたりの行数 | トランザクションあたりの最大行数 |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4,500 |300,000,000 |18,000,000,000 |
| DW15000c |112.5 |60 |6,750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900,000,000 |54,000,000,000 |

## <a name="gen1"></a>Gen1

| [DWU](sql-data-warehouse-overview-what-is.md) | ディストリビューションあたりの上限 (GB) | ディストリビューション数 | 最大トランザクション サイズ (GB) | ディストリビューションあたりの行数 | トランザクションあたりの最大行数 |
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

ログに書き込まれるデータ量を最適化および最小化する方法については、[トランザクションのベスト プラクティス](sql-data-warehouse-develop-best-practices-transactions.md)に関する記事をご覧ください。

> [!WARNING]
> 最大トランザクション サイズは、HASH または ROUND_ROBIN で分散し、データが均等に分散されたテーブルでのみ達成できます。 トランザクションによりデータが書き込まれ、分散が傾斜する場合、最大トランザクション サイズに到達する前に上限に到達する可能性があります。
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>トランザクションの状態
SQL Data Warehouse では、XACT_STATE() 関数の値 -2 を使用して、失敗したトランザクションを報告します。 この値は、トランザクションが失敗し、ロールバックのためにのみマークされていることを意味します。

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
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

上記のコードでは、次のようなエラー メッセージが発生します。

メッセージ 111233、レベル 16、状態 1、ライン 1 111233; 111233; 現在のトランザクションは中止され、保留中の変更はすべてロールバックされています。 原因: rollback-only 状態のトランザクションが、DDL、DML、または SELECT ステートメントの前に明示的にロールバックされませんでした。

ERROR_* 関数の出力は得られません。

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

変更点をまとめると、CATCH ブロック内でエラー情報の読み取り前にトランザクションの ROLLBACK が発生するようになりました。

## <a name="errorline-function"></a>Error_Line() 関数
SQL Data Warehouse では、ERROR_LINE() 関数を実装およびサポートしていないことにも注意してください。 この関数がコードに含まれている場合は、SQL Data Warehouse に準拠するために削除する必要があります。 代わりに、コードでクエリ ラベルを使用して同等の機能を実装します。 詳しくは、[ラベル](sql-data-warehouse-develop-label.md)に関する記事をご覧ください。

## <a name="using-throw-and-raiserror"></a>THROW と RAISERROR の使用
THROW は、SQL Data Warehouse で例外を発生させるための最新の実装ですが、RAISERROR もサポートされています。 ただし、注意が必要な相違点がいくつかあります。

* THROW では、ユーザー定義のエラー メッセージの番号として、100,000 ～ 150,000 の範囲の番号を指定することはできません。
* RAISERROR のエラー メッセージは 50,000 に固定されています。
* sys.messages の使用はサポートされていません。

## <a name="limitations"></a>制限事項
SQL Data Warehouse には、トランザクションに関連する他の制限事項があります。

制限事項は次のとおりです。

* 分散トランザクションは使用できません。
* 入れ子になったトランザクションは使用できません。
* セーブ ポイントは使用できません。
* トランザクションに名前を付けることはできません。
* トランザクションにマークを付けることはできません。
* ユーザー定義トランザクション内では CREATE TABLE のような DDL はサポートされません。

## <a name="next-steps"></a>次の手順
トランザクションの最適化の詳細については、 [トランザクションのベスト プラクティス](sql-data-warehouse-develop-best-practices-transactions.md)に関するページを参照してください。 SQL Data Warehouse のその他のベスト プラクティスについては、[SQL Data Warehouse のベスト プラクティス](sql-data-warehouse-best-practices.md)に関する記事をご覧ください。

