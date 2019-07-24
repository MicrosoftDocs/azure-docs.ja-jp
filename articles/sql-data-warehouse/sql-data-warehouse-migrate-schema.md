---
title: SQL Data Warehouse へのスキーマの移行 | Microsoft Docs
description: ソリューション開発のための Azure SQL Data Warehouse へのスキーマの移行に関するヒント。
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: 4139ea776f6947eeacf4620c3676606d6535dd2b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55461686"
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>SQL Data Warehouse にスキーマを移行する
SQL Data Warehouse に SQL スキーマを移行するためのガイダンス。 

## <a name="plan-your-schema-migration"></a>スキーマの移行を計画する

移行を計画するときは、[テーブルの概要][table overview]を参照して、統計情報、分散、パーティション分割、インデックスなど、テーブル設計の考慮事項を理解してください。  また、[サポートされていないテーブルの機能][unsupported table features]とその回避策も示します。

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>ユーザー定義のスキーマを使ってデータベースを統合する

既存のワークロードにはおそらく複数のデータベースがあるはずです。 たとえば、SQL Server のデータ ウェアハウスは、ステージング データベース、データ ウェアハウス データベース、複数のデータ マート データベースで構成されていることがあります。 このトポロジでは、各データベースは個別にセキュリティ ポリシーを持つ独立したワークロードとして動作します。

これに対して、SQL Data Warehouse では、データ ウェアハウスの全ワークロードを 1 つのデータベース内で実行します。 データベース間の結合は許可されていません。 そのため、SQL Data Warehouse では、データ ウェアハウスで使われるすべてのテーブルが 1 つのデータベースに格納されていることが求められます。

ユーザー定義のスキーマを使って、既存のワークロードを 1 つのデータベースに統合することをお勧めします。 例については、「[SQL Data Warehouse のユーザー定義のスキーマ](sql-data-warehouse-develop-user-defined-schemas.md)」をご覧ください。

## <a name="use-compatible-data-types"></a>互換性のあるデータ型を使う
SQL Data Warehouse と互換性があるようにデータ型を変更します。 サポートされているデータ型とサポートされていないデータ型の一覧については、「[SQL Data Warehouse のテーブルのデータ型][data types]」をご覧ください。 こちらのトピックでは、サポートされていない型の回避方法が示されています。 また、SQL Data Warehouse でサポートされていない既存の型を識別するクエリも提供されています。

## <a name="minimize-row-size"></a>行のサイズを最小にする
最高のパフォーマンスのため、テーブルの行の長さを最小限に抑えます。 行の長さが短いほどパフォーマンスが向上するので、データに対応できる最小のデータ型を使います。 

PolyBase では、テーブルの行の幅は 1 MB に制限されています。  PolyBase で SQL Data Warehouse にデータを読み込む場合は、行の幅の最大が1 MB 未満になるようにテーブルを更新します。 

<!--
- For example, this table uses variable length data but the largest possible size of the row is still less than 1 MB. PolyBase will load data into this table.

- This table uses variable length data and the defined row width is less than one MB. When loading rows, PolyBase allocates the full length of the variable-length data. The full length of this row is greater than one MB.  PolyBase will not load data into this table.  

-->

## <a name="specify-the-distribution-option"></a>分散オプションを指定する
SQL Data Warehouse は分散型データベースです。 各テーブルは、コンピューティング ノード間に分散つまりレプリケートされます。 データの分散方法を指定できるテーブル オプションがあります。 選択肢は、ラウンドロビン、レプリケート、ハッシュ分散です。 それぞれに長所と短所があります。 分散オプションを指定しないと、SQL Data Warehouse は既定でラウンドロビンを使います。

- ラウンドロビン方式が既定値です。 ラウンドロビンは使い方が最も簡単で、可能な限り速くデータを読み込みますが、結合にデータの移動が必要であるため、クエリのパフォーマンスが低下します。
- レプリケートは、テーブルのコピーを各コンピューティング ノードに格納します。 レプリケート テーブルは、結合と集計にデータの移動を必要としないため、高いパフォーマンスを得られます。 余分な記憶域が必要なので、小さなテーブルに最適です。
- ハッシュ分散は、ハッシュ関数を使ってすべてのノードに行を分散します。 ハッシュ分散テーブルは、大きなテーブルで高いクエリ パフォーマンスが得られるように設計されているため、SQL Data Warehouse の核心です。 このオプションでは、データの分散に最適な列を選択するための計画が必要です。 ただし、最初に最適な列を選択しなくても、異なる列にデータを簡単に再分散できます。 

各テーブルに最善の分散オプションを選択する方法については、「[SQL Data Warehouse のテーブルの分散](sql-data-warehouse-tables-distribute.md)」をご覧ください。


## <a name="next-steps"></a>次の手順
SQL Data Warehouse にデータベース スキーマを正常に移行したら、次の記事のいずれかに進みます。

* [データの移行][Migrate your data]
* [コードの移行][Migrate your code]

SQL Data Warehouse のベスト プラクティスの詳細については、「[Azure SQL Data Warehouse のベスト プラクティス][best practices]」を参照してください。

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types

<!--MSDN references-->


<!--Other Web references-->
