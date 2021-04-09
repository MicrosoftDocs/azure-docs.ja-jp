---
title: SQL プールを使用した T-SQL ビュー
description: Azure Synapse Analytics の専用 SQL プールとサーバーレス SQL プールで T-SQL ビューを使用して、ソリューションを開発するためのヒントを紹介します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 9f52c3fd1284ce7e55680d051c5292361067fad9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673995"
---
# <a name="t-sql-views-with-dedicated-sql-pool-and-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics の専用 SQL プールとサーバーレス SQL プールを使用した T-SQL ビュー

この記事では、Azure Synapse Analytics の専用 SQL プールとサーバーレス SQL プールで T-SQL ビューを使用して、ソリューションを開発するためのヒントを紹介します。

## <a name="why-use-views"></a>ビューを使用する理由

ビューをさまざまな方法で使用して、ソリューションの品質を向上させることができます。  この記事では、ビューによってソリューションを強化する方法の例をいくつか取り上げます。また、考慮する必要がある制限事項についても説明します。

### <a name="sql-pool---create-view"></a>SQL プール - ビューの作成

> [!NOTE]
> この記事では CREATE VIEW の構文は説明していません。 詳細については、[CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true)のドキュメントを参照してください。

## <a name="architectural-abstraction"></a>アーキテクチャの抽象化

一般的なアプリケーション パターンでは、データを読み込みながら、[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) (CTAS) を使用し、その後にオブジェクトの名前変更パターンを使用してテーブルを再作成します。

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

この方法では、ユーザーのビューにテーブルが表示または表示されなかったり、[テーブルが存在しません] というエラー メッセージが表示されたりすることに注意してください。 ビューを使用すると、基になるオブジェクトの名前は変更された場合でも、ユーザーに一貫性のあるプレゼンテーション レイヤーを提供できます。

ビュー経由でのデータへのアクセスを提供することで、ユーザーが基になるテーブルを表示可能である必要はなくなります。 一貫性のあるユーザー エクスペリエンスに加えて、このレイヤーでは、分析の設計者がデータ モデルを発展させることができます。 基になるテーブルを発展させることができることで、設計者は CTAS を使用して、データの読み込みプロセス中のパフォーマンスを最大限に高めることができます。

## <a name="performance-optimization"></a>パフォーマンスの最適化

ビューを使用して、パフォーマンスが最適化された結合をテーブル間に適用することもできます。 たとえば、ビューで結合条件の一部として冗長分散キーを組み込むことで、データの移動を最小限に抑えることができます。

特定のクエリ ヒントや結合ヒントを適用することも T-SQL ビューを使用するもう 1 つの利点です。 そのため、ビューの機能によって、結合が常に最適な方法で実行されるようになります。 ユーザーは、使用している結合の適切な構造を覚えておく必要がなくなります。

## <a name="limitations"></a>制限事項

Synapse SQL のビューはメタデータとしてのみ格納されます。 そのため、次のオプションは使用できません。

* スキーマ バインド オプションはありません
* ビューを通してベース テーブルを更新することはできません
* 一時テーブルのビューを作成することはできません
* EXPAND/NOEXPAND ヒントはサポートされていません
* Synapse SQL にはインデックス付きビューはありません

## <a name="next-steps"></a>次のステップ

開発に関するその他のヒントについては、[Synapse SQL の開発の概要](develop-overview.md)に関する記事をご覧ください。



