---
title: 分散データ – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL の分散テーブル、参照テーブル、ローカル テーブル、およびシャードについて説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7757fdb4953640597a805c3d74a9e1ef08ef2c07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86114501"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL での分散データ – Hyperscale (Citus)

この記事では、Azure Database for PostgreSQL – Hyperscale (Citus) の 3 つのテーブル型の概要を示します。
分散テーブルがシャードとして格納される方法と、シャードがノード上に配置される方法を示します。

## <a name="table-types"></a>テーブル型

Hyperscale (Citus) サーバー グループには 3 つのテーブル型があり、それぞれ別々の目的に使用されます。

### <a name="type-1-distributed-tables"></a>型 1:分散テーブル

最もよく使用される最初の型が分散テーブルです。 SQL ステートメントには通常のテーブルのように見えますが、ワーカー ノードにわたり水平方向にパーティション分割されます。 この意味は、テーブルの各行が異なるノードの、シャードと呼ばれるフラグメント テーブルに格納されるということです。

Hyperscale (Citus) では、クラスター全体で SQL だけではなく DDL ステートメントも実行されます。
分散テーブルのスキーマの変更は、ワーカーにわたるすべてのテーブルのシャードの更新にカスケーディングされます。

#### <a name="distribution-column"></a>ディストリビューション列

Hyperscale (Citus) では、アルゴリズムのシャーディングを使用して行がシャードに割り当てられます。 割り当ては、ディストリビューション列と呼ばれるテーブル列の値に基づいて決定論的に行われます。 クラスター管理者は、テーブルを配布するときに、この列を指定する必要があります。
パフォーマンスおよび機能には、適切な選択を行うことが重要です。

### <a name="type-2-reference-tables"></a>型 2:参照テーブル

参照テーブルは、コンテンツ全体が単一のシャードに集中される一種の分散テーブルです。 シャードは、すべてのワーカーにレプリケートされます。 いずれかのワーカーに対するクエリは、参照情報にローカルでアクセスでき、別のノードから行を要求するネットワーク オーバーヘッドは生じません。 行ごとに個別のシャードを区別する必要がないため、参照テーブルにはディストリビューション列はありません。

参照テーブルは通常小さく、任意のワーカー ノードで実行されるクエリに関連したデータの格納に使用されます。 たとえば、注文ステータスや製品カテゴリなどの列挙値があります。

### <a name="type-3-local-tables"></a>型 3:ローカル テーブル

Hyperscale (Citus) を使用する場合、接続するコーディネーター ノードは通常の PostgreSQL データベースです。 コーディネーター上に通常のテーブルを作成し、それらをシャードしないように選択できます。

ローカル テーブルの適切な候補は、結合クエリに参加していない小規模の管理用テーブルになります。 たとえば、アプリケーションのサインインと認証用のユーザー テーブルです。

## <a name="shards"></a>シャード

前のセクションでは、分散テーブルがワーカー ノード上にシャードとしてどのように格納されるかについて説明しました。 このセクションでは、さらに技術的な詳細について説明します。

コーディネーター上の `pg_dist_shard` メタデータ テーブルには、システム内の分散テーブルの各シャードの行が含まれます。 行は、ハッシュ領域 (shardminvalue、shardmaxvalue) 内の整数の範囲でシャード ID に一致します。

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

コーディネーター ノードでは、どのシャードが `github_events` の行を保持するかを判断する場合、行のディストリビューション列の値がハッシュされます。 その後、ノードにより、どのシャードの範囲にハッシュされた値が含まれるか調べられます。 範囲は、ハッシュ関数のイメージが disjoint 結合になるように定義されます。

### <a name="shard-placements"></a>シャードの配置

シャード 102027 が問題の行に関連付けられているとします。 行は、いずれかのワーカーにおいて、`github_events_102027` と呼ばれるテーブルで読み取られるか書き込まれます。 どのワーカーでしょうか。 これは、すべてメタデータ テーブルによって決定されます。 シャードからワーカーへのマッピングは、シャード配置と呼ばれます。

コーディネーター ノードでは、`github_events_102027` のような特定のテーブルを参照するフラグメントにクエリが再書き込みされ、これらのフラグメントが適切なワーカー上で実行されます。 シャード ID 102027 を保持しているノードを見つけるためにバックグラウンドで実行されるクエリの例を次に示します。

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

```output
┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

## <a name="next-steps"></a>次の手順

- 分散テーブルでの[ディストリビューション列の選択](concepts-hyperscale-choose-distribution-column.md)方法を確認します。
