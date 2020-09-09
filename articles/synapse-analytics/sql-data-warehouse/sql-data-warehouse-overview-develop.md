---
title: Azure Synapse Analytics で Synapse SQL プールを開発するためのリソース
description: SQL Data Warehouse に関する開発コンセプト、設計上の決定、レコメンデーション、およびコーディング技法。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a3c0d7924fb550761d050c9c404b1065c7d3cf72
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85211495"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics の Synapse SQL プールの設計上の決定とコーディング技法 
 この記事では、Azure Synapse の SQL プールに関する主要な設計上の決定、レコメンデーション、およびコーディング技法をより深く理解するために、追加のリソースについて説明します。

## <a name="key-design-decisions"></a>主要な設計上の決定
次の記事では、Azure Synapse の SQL プール機能を使用して分散データウェア ハウスを開発するための概念と設計上の意思決定について説明しています。

* [connections](../sql/connect-overview.md)
* [concurrency](resource-classes-for-workload-management.md)
* [トランザクション](sql-data-warehouse-develop-transactions.md)
* [ユーザー定義スキーマ](sql-data-warehouse-develop-user-defined-schemas.md)
* [テーブルのディストリビューション](sql-data-warehouse-tables-distribute.md)
* [テーブルのインデックス](sql-data-warehouse-tables-index.md)
* [テーブルのパーティション](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [統計](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>開発における推奨事項とコーディング技法
次の記事では、SQL プールを開発するための具体的なコーディング技法、ヒント、およびレコメンデーションについて説明しています。

* [ストアド プロシージャ](sql-data-warehouse-develop-stored-procedures.md)
* [ラベル](sql-data-warehouse-develop-label.md)
* [views](sql-data-warehouse-develop-views.md)
* [一時テーブル](sql-data-warehouse-tables-temporary.md)
* [動的 SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [ループ](sql-data-warehouse-develop-loops.md)
* [オプションでのグループ化](sql-data-warehouse-develop-group-by-options.md)
* [変数の代入](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>次のステップ
詳細な参照情報については、[T-SQL ステートメント](sql-data-warehouse-reference-tsql-statements.md)に関するページをご覧ください。
