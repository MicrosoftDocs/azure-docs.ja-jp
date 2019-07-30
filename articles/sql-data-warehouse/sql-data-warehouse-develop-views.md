---
title: Azure SQL Data Warehouse で T-SQL ビューを使う | Microsoft Docs
description: ソリューション開発のための、Azure SQL Data Warehouse での T-SQL ビューの使用に関するヒント。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8a770e66120e69271744942899186ece39b2a3c3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479518"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse のビュー
ソリューション開発のための、Azure SQL Data Warehouse での T-SQL ビューの使用に関するヒント。 

## <a name="why-use-views"></a>ビューを使用する理由
ビューをさまざまな方法で使用して、ソリューションの品質を向上させることができます。  この記事では、ビューによってソリューションを強化する方法の例をいくつか取り上げます。また、考慮する必要がある制限事項についても説明します。


> [!IMPORTANT]
> 新しい具体化されたビューの構文については、[CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) に関するページをご覧ください。  詳しくは、[リリース ノート](/azure/sql-data-warehouse/release-notes-10-0-10106-0)のページをご覧ください。
>


> [!NOTE]
> この記事では CREATE VIEW の構文は説明していません。 詳細については、[CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql)のドキュメントを参照してください。
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

ただし、この方法により、ユーザーのビューに、テーブルが表示または表示されなかったり、[テーブルが存在しません] というエラーメッセージが表示されたりすることがあります。 ビューを使用すると、基になるオブジェクトの名前は変更されますが、ユーザーに一貫性のあるプレゼンテーション レイヤーを提供できます。 ビュー経由でのデータへのアクセスを提供することで、ユーザーが基になるテーブルを表示可能である必要はなくなります。 このレイヤーでは、データ ウェアハウスの設計者がデータ モデルを発展させることが可能な状況を確保したうえで、一貫性のあるユーザー エクスペリエンスを提供します。 基になるテーブルを発展させることで、設計者は CTAS を使用して、データの読み込みプロセスにおけるパフォーマンスを最大限に活かすことが可能になります。   

## <a name="performance-optimization"></a>パフォーマンスの最適化
ビューを利用して、パフォーマンスに最適化された結合をテーブル間に適用することもできます。 たとえば、ビューで結合条件の一部として冗長分散キーを組み込むことで、データの移動を最小限に抑えることができます。 ビューの利点として、特定のクエリ ヒントや結合ヒントを強制できることも挙げられます。 この方法でビューを使用すると、結合が常に最適な方法で実行されることが保証され、ユーザーに依存せずに結合を適切に作成できます。

## <a name="limitations"></a>制限事項
SQL Data Warehouse のビューはメタデータ専用として保管されます。 そのため、次のオプションは使用できません。

* スキーマ バインド オプションはありません
* ビューでベース テーブルを更新することはできません
* 一時テーブルでビューを作成することはできません
* EXPAND / NOEXPAND ヒントはサポートされていません
* SQL Data Warehouse のインデックス付きビューはありません

## <a name="next-steps"></a>次の手順
開発に関するその他のヒントについては、「 [SQL Data Warehouse development overview (SQL Data Warehouse の開発の概要)](sql-data-warehouse-overview-develop.md)」をご覧ください。


