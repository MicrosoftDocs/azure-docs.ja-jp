---
title: "Azure でのデータ ウェアハウスの開発に関するリソース | Microsoft Docs"
description: "SQL Data Warehouse に関する開発コンセプト、設計上の決定、推奨事項、およびコーディング技法。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: 
ms.assetid: 996e3afc-c21c-4e21-b9df-997f953f6dfd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: develop
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2548f779767635865daf790d301d86feff573a29
ms.openlocfilehash: 37344b7916d8ceb2ad3b6a34df9fc8681af4dff7
ms.lasthandoff: 01/24/2017


---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>SQL Data Warehouse の設計上の決定と コーディング技法
SQL Data Warehouse に関する主要な設計上の決定、推奨事項、およびコーディング技法をより深く理解するために、開発に関する次の記事に目を通してください。

## <a name="key-design-decisions"></a>主要な設計上の決定
次の記事には、SQL Data Warehouse を使用した分散データ ウェアハウスの開発を理解するのに必要ないくつかの主な概念と設計上の決定事項が概説されています。

* [接続][connections]
* [同時実行][concurrency]
* [トランザクション][transactions]
* [ユーザー定義スキーマ][user-defined schemas]
* [テーブルの分散][table distribution]
* [テーブルのインデックス][table indexes]
* [テーブルのパーティション][table partitions]
* [CTAS][CTAS]
* [統計][statistics]

## <a name="development-recommendations-and-coding-techniques"></a>開発における推奨事項とコーディング技法
次の記事には、SQL Data Warehouse を開発するための具体的なコーディング技法、ヒント、および推奨事項が概説されています。

* [ストアド プロシージャ][stored procedures]
* [ラベル][labels]
* [ビュー][views]
* [一時テーブル][temporary tables]
* [動的 SQL][dynamic SQL]
* [ループ][looping]
* [オプションでのグループ化][group by options]
* [変数の代入][variable assignment]

## <a name="next-steps"></a>次のステップ
開発記事に目を通したら、SQL Data Warehouse でサポートされる構文の詳細について、「[Transact-SQL リファレンス][Transact-SQL reference]」をご覧ください。

<!--Image references-->

<!--Article references-->
[concurrency]: ./sql-data-warehouse-develop-concurrency.md
[connections]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamic SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[group by options]: ./sql-data-warehouse-develop-group-by-options.md
[labels]: ./sql-data-warehouse-develop-label.md
[looping]: ./sql-data-warehouse-develop-loops.md
[statistics]: ./sql-data-warehouse-tables-statistics.md
[stored procedures]: ./sql-data-warehouse-develop-stored-procedures.md
[table distribution]: ./sql-data-warehouse-tables-distribute.md
[table indexes]: ./sql-data-warehouse-tables-index.md
[table partitions]: ./sql-data-warehouse-tables-partition.md
[temporary tables]: ./sql-data-warehouse-tables-temporary.md
[transactions]: ./sql-data-warehouse-develop-transactions.md
[user-defined schemas]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[views]: ./sql-data-warehouse-develop-views.md
[Transact-SQL reference]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->

