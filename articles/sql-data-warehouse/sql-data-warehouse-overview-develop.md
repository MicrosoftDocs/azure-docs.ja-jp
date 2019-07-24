---
title: Azure でのデータ ウェアハウスの開発に関するリソース | Microsoft Docs
description: SQL Data Warehouse に関する開発コンセプト、設計上の決定、推奨事項、およびコーディング技法。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 08/29/2018
ms.author: kevinvngo
ms.reviewer: igorstan
ms.openlocfilehash: 8c04e0409faa3b63a8a2957284ac7aa96740ae03
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468378"
---
# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>SQL Data Warehouse の設計上の決定と コーディング技法
SQL Data Warehouse に関する主要な設計上の決定、推奨事項、およびコーディング技法をより深く理解するために、開発に関する次の記事に目を通してください。

## <a name="key-design-decisions"></a>主要な設計上の決定
次の記事には、SQL Data Warehouse を使用して分散データウェア ハウスを開発するための概念と設計に関する決定事項が概説されています。

* [接続][connections]
* [コンカレンシー][concurrency]
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

## <a name="next-steps"></a>次の手順
詳細な参照情報については、[SQL Data Warehouse ステートメント](sql-data-warehouse-reference-tsql-statements.md)に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[concurrency]: ./resource-classes-for-workload-management.md
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


<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
