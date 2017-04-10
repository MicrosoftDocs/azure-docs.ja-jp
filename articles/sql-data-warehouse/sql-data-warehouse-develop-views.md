---
title: "Azure SQL Data Warehouse で T-SQL ビューを使う | Microsoft Docs"
description: "ソリューション開発のための、Azure SQL Data Warehouse での Transact-SQL ビューの使用に関するヒント。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: b5208f32-8f4a-4056-8788-2adbb253d9fd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 43ab6a2f71ab51c50847b1ba5249f51c48e03fea
ms.openlocfilehash: 902929fba9e2486d97323ab52505ba6db3fe0aab
ms.lasthandoff: 01/24/2017


---
# <a name="views-in-sql-data-warehouse"></a>SQL Data Warehouse のビュー
SQL Data Warehouse では、ビューが特に役立ちます。 ビューをさまざまな方法で使用して、ソリューションの品質を向上させることができます。  この記事では、ビューによってソリューションを強化する方法の例をいくつか取り上げます。また、考慮する必要がある制限事項についても説明します。

> [!NOTE]
> この記事では `CREATE VIEW` の構文は説明していません。 参考情報については、MSDN の [CREATE VIEW に関する記事][CREATE VIEW]をご覧ください。
> 
> 

## <a name="architectural-abstraction"></a>アーキテクチャの抽象化
一般的なアプリケーション パターンでは、データを読み込みながら、CREATE TABLE AS SELECT (CTAS) を使用し、その後にオブジェクトの名前変更パターンを使用してテーブルを再作成します。

次の例では、日付ディメンションに新しい日付レコードを追加します。 新しいテーブルの DimDate_New が最初に作成され、名前が変更され、テーブルの最初のバージョンに代わることに注意してください。

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

ただし、この方法により、ユーザーのビューに、テーブルが表示または表示されなかったり、[テーブルが存在しません] というエラーメッセージが表示されたりすることがあります。 ビューを使用すると、基になるオブジェクトの名前は変更されますが、ユーザーに一貫性のあるプレゼンテーション レイヤーを提供できます。 ビューからデータにアクセスできるようになったユーザーは、基になるテーブルを表示する必要はありません。 これにより、一貫性のあるユーザー エクスペリエンスを提供しながら、データ ウェアハウスの設計者は、データ モデルを進化させ、データの読み込みプロセスで CTAS を使用してパフォーマンスを最大限に高めることができます。    

## <a name="performance-optimization"></a>パフォーマンスの最適化
ビューを利用して、パフォーマンスに最適化された結合をテーブル間に適用することもできます。 たとえば、ビューで結合条件の一部として冗長分散キーを組み込むことで、データの移動を最小限に抑えることができます。  ビューの利点として、特定のクエリ ヒントや結合ヒントを強制できることも挙げられます。 この方法でビューを使用すると、結合が常に最適な方法で実行されることが保証され、ユーザーに依存せずに結合を適切に作成できます。

## <a name="limitations"></a>制限事項
SQL Data Warehouse のビューはメタデータ専用です。  そのため、次のオプションは使用できません。

* スキーマ バインド オプションはありません
* ビューでベース テーブルを更新することはできません
* 一時テーブルでビューを作成することはできません
* EXPAND / NOEXPAND ヒントはサポートされていません
* SQL Data Warehouse のインデックス付きビューはありません

## <a name="next-steps"></a>次のステップ
開発に関するその他のヒントについては、[SQL Data Warehouse の開発の概要][SQL Data Warehouse development overview]に関する記事をご覧ください。
`CREATE VIEW` の構文については、[CREATE VIEW に関する記事][CREATE VIEW]をご覧ください。

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CREATE VIEW]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->

