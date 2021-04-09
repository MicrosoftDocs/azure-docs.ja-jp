---
title: Azure Synapse Analytics SQL プールでトランザクションを使用する
description: この記事には、Synapse SQL プールでトランザクションを実装し、ソリューションを開発するためのヒントが記載されています。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/22/2019
ms.author: xiaoyul
ms.custom: azure-synapse
ms.reviewer: igorstan
ms.openlocfilehash: 8144c588d4b6794cadc0577bf63dabc2cc3e0efd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98677288"
---
# <a name="use-transactions-in-a-sql-pool-in-azure-synapse"></a>Azure Synapse の SQL プールでトランザクションを使用する 

この記事には、SQL プールでトランザクションを実装し、ソリューションを開発するためのヒントが記載されています。

## <a name="what-to-expect"></a>ウィザードの内容

予想される通り、SQL プールは、データ ウェアハウスのワークロードの一部としてトランザクションをサポートします。 ただし、SQL プールを大規模に維持できるように、SQL Server と比べて一部の機能が制限されています。 この記事ではその差について説明します。

## <a name="transaction-isolation-levels"></a>トランザクション分離レベル

SQL プールは、ACID トランザクションを実装します。 トランザクションサポートの分離レベルは、既定では READ UNCOMMITTED になります。  これは READ COMMITTED SNAPSHOT ISOLATION に変更できます。それには、マスター データベースに接続する際にユーザー SQL プールの READ_COMMITTED_SNAPSHOT データベース オプションをオンにします。  

有効になると、このデータベース内のすべてのトランザクションが READ COMMITTED SNAPSHOT ISOLATION の下で実行され、セッション レベルで READ UNCOMMITTED を設定しても受け入れられません。 詳細については、「[ALTER DATABASE の SET オプション (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」を確認してください。

## <a name="transaction-size"></a>トランザクション サイズ

1 回のデータ変更トランザクションには規模の面で制限があります。 この制限は、ディストリビューションごとに適用されます。 このため、割り当ての合計を算出するには、制限とディストリビューション数を掛ける必要があります。

トランザクションの最大行数の近似値を求めるには、ディストリビューション上限を各列の合計サイズで割ります。 列の長さが異なる場合、最大サイズではなく列の平均長を利用することを検討してください。

下の表では、2 つの想定が行われています。

* データは均等に分散されました
* 行の平均長は 250 バイトです

## <a name="gen2"></a>Gen2

| [DWU](./sql-data-warehouse-overview-what-is.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) | ディストリビューションあたりの上限 (GB) | ディストリビューション数 | 最大トランザクション サイズ (GB) | ディストリビューションあたりの行数 | トランザクションあたりの最大行数 |
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

| [DWU](./sql-data-warehouse-overview-what-is.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) | ディストリビューションあたりの上限 (GB) | ディストリビューション数 | 最大トランザクション サイズ (GB) | ディストリビューションあたりの行数 | トランザクションあたりの最大行数 |
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

## <a name="transaction-state"></a>トランザクションの状態

SQL プールでは、XACT_STATE() 関数を使い、値 -2 を使用して、失敗したトランザクションを報告します。 この値は、トランザクションが失敗し、ロールバックのためにのみマークされていることを意味します。

> [!NOTE]
> 失敗したトランザクションを示すために XACT_STATE 関数の -2 を使用するのは、SQL Server とは異なる動作です。 SQL Server では、コミットできないトランザクションを表すために値 -1 を使用します。 SQL Server では、コミット不可としてマークしなくても、トランザクション内で一部のエラーを許容できます。 たとえば、`SELECT 1/0` はエラーになりますが、トランザクションが強制的にコミット不可状態になることはありません。

また、SQL Server では、コミットできないトランザクションでの読み取りも許可されます。 ただし、SQL プールではこれを実行できません。 SQL プールのトランザクションでエラーが発生した場合は、-2 状態が自動的に入力され、ステートメントがロールバックされるま select ステートメントをそれ以上実行できなくなります。

このため、コードを変更する必要がある場合は、アプリケーション コードを調べて、XACT_STATE() を使用しているかどうかを確認することが重要です。

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

メッセージ 111233、レベル 16、状態 1、ライン 1 111233; 111233; 現在のトランザクションは中止され、保留中の変更はすべてロールバックされています。 この問題の原因は、rollback-only 状態のトランザクションが、DDL、DML、または SELECT ステートメントの前に明示的にロールバックされていないことです。

ERROR_* 関数の出力は得られません。

SQL プールでは、コードを少し変更する必要があります。

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
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
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

## <a name="error_line-function"></a>Error_Line() 関数

SQL プールでは、ERROR_LINE() 関数を実装またはサポートしていないことにも注意してください。 この関数がコードに含まれている場合は、SQL プールに準拠するために削除する必要があります。

代わりに、コードでクエリ ラベルを使用して同等の機能を実装します。 詳しくは、[ラベル](sql-data-warehouse-develop-label.md)に関する記事をご覧ください。

## <a name="using-throw-and-raiserror"></a>THROW と RAISERROR の使用

THROW は、SQL プールで例外を発生させるための最新の実装ですが、RAISERROR もサポートされています。 ただし、注意が必要な相違点がいくつかあります。

* THROW では、ユーザー定義のエラー メッセージの番号として、100,000 ～ 150,000 の範囲の番号を指定することはできません。
* RAISERROR のエラー メッセージは 50,000 に固定されています。
* sys.messages の使用はサポートされていません。

## <a name="limitations"></a>制限事項

SQL プールには、トランザクションに関する他の制限事項がいくつかあります。

制限事項は次のとおりです。

* 分散トランザクションは使用できません。
* 入れ子になったトランザクションは使用できません。
* セーブ ポイントは使用できません。
* トランザクションに名前を付けることはできません。
* トランザクションにマークを付けることはできません。
* ユーザー定義トランザクション内では CREATE TABLE のような DDL はサポートされません。

## <a name="next-steps"></a>次のステップ

トランザクションの最適化について詳しくは、[トランザクションのベスト プラクティス](sql-data-warehouse-develop-best-practices-transactions.md)に関する記事をご覧ください。 他の SQL プールのベスト プラクティスの詳細については、「[SQL プールのベスト プラクティス](sql-data-warehouse-best-practices.md)」を参照してください。