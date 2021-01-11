---
title: ストアド プロシージャの使用
description: ソリューション開発のための Synapse SQL プール (データ ウェアハウス) でのストアド プロシージャの実装に関するヒント。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2b106a28514179d9be43b3c56ea8030eaf7ba13f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494997"
---
# <a name="use-stored-procedures-in-sql-pool"></a>SQL プールでのストアド プロシージャの使用

ソリューション開発のための Synapse SQL プール (データ ウェアハウス) でのストアド プロシージャの実装に関するヒント。

## <a name="what-to-expect"></a>ウィザードの内容

SQL プールでは、SQL Server で使用される多数の T-SQL 機能がサポートされています。 さらに重要なのは、ソリューションのパフォーマンスを最大限にするために使用できる、スケールアウト専用の機能が用意されていることです。

ただし、SQL プールのスケールとパフォーマンスを維持するために、動作が異なる機能もあれば、サポートされていない機能もあります。

## <a name="stored-procedures-in-sql-pool"></a>SQL プールでのストアド プロシージャ

ストアド プロシージャは、SQL コードをカプセル化し、データ ウェアハウスのデータの近くに格納するための優れた方法です。 ストアド プロシージャは、開発者がコードを管理しやすい単位にカプセル化して、ソリューションをモジュール化することに役立ち、コードの再利用性が大幅に促進されます。 ストアド プロシージャの柔軟性をさらに高めるために、各ストアド プロシージャはパラメーターを受け入れることもできます。

SQL プールでは、簡素化され、合理化されたストアド プロシージャの実装が提供されます。 SQL Server との最大の違いは、ストアド プロシージャがプリコンパイル済みコードではないことです。 データ ウェアハウスでは、大量のデータに対してクエリを実行するのにかかる時間に比べて、短いコンパイル時間で済みます。 より重要なことは、ストアド プロシージャのコードが大量のクエリに対して正しく最適化されることを確認することです。 目標は、数時間、数分、(数ミリ秒ではなく) 数秒節約することです。 したがって、ストアド プロシージャは、SQL ロジックのコンテナーと考えるとよいでしょう。

SQL プールでストアド プロシージャが実行されると、実行時に SQL ステートメントが解析され、変換され、最適化されます。 このプロセスの中で、各ステートメントが分散クエリに変換されます。 データに対して実行される SQL コードは、送信されるクエリとは異なります。

## <a name="nesting-stored-procedures"></a>ストアド プロシージャの入れ子

ストアド プロシージャが他のストアド プロシージャを呼び出すか、動的 SQL を実行する場合、内側のストアド プロシージャまたはコードの呼び出しは "入れ子になっている" といわれます。

SQL プールでは、最大 8 レベルの入れ子がサポートされます。 これは SQL Server と少し異なります。 SQL Server の入れ子レベルは 32 です。

最上位のストアド プロシージャの呼び出しは、入れ子レベル 1 になります。

```sql
EXEC prc_nesting
```

ストアド プロシージャから別の EXEC の呼び出しも行う場合、入れ子レベルは 2 に増えます。

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

次に 2 つ目の手順で何らかの動的 SQL を実行すると、入れ子レベルは 3 に増えます。

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

> [!NOTE]
> 現在、SQL プールでは、[@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) はサポートされていません。 入れ子レベルを追跡する必要があります。 8 つの入れ子レベルの制限を超過する可能性はほとんどありませんが、もし超過した場合は、入れ子レベルがこの制限内に収まるように、コードを修正する必要があります。

## <a name="insertexecute"></a>INSERT..EXECUTE

SQL プールでは、INSERT ステートメントでストアド プロシージャの結果セットを使用することはできません。 ただし、別の方法を使用できます。 例については、[一時テーブル](develop-tables-temporary.md)の記事を参照してください。

## <a name="limitations"></a>制限事項

SQL プールで実装されていない Transact-SQL ストアド プロシージャの側面がいくつかあります。

これらは次のとおりです。

* 一時ストアド プロシージャ
* 番号付きストアド プロシージャ
* 拡張ストアド プロシージャ
* CLR ストアド プロシージャ
* 暗号化オプション
* レプリケーション オプション
* テーブル値パラメーター
* 読み取り専用パラメーター
* 既定のパラメーター
* 実行コンテキスト
* return ステートメント

## <a name="next-steps"></a>次のステップ

開発についてのその他のヒントは、[開発の概要](develop-overview.md)に関するページをご覧ください。
