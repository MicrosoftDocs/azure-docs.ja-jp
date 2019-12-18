---
title: Table コロケーション - ハイパースケール (Citus) - Azure Database for PostgreSQL
description: より高速なクエリのために関連情報をまとめて保存する方法
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967339"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – Hyperscale (Citus) でのテーブル コロケーション

コロケーションとは、同じノード上に関連情報をまとめて格納することを意味します。 ネットワーク トラフィックを使用せずに必要なすべてのデータを入手できる場合に、クエリは高速化できます。 関連データを異なるノードに併置することで、各ノードでクエリを並列に効率的に実行できます。

## <a name="data-colocation-for-hash-distributed-tables"></a>ハッシュ分散テーブルへのデータ コロケーション

Azure Database for PostgreSQL - Hyperscale (Citus) では、ディストリビューション列の値のハッシュが、シャードのハッシュ範囲に収まる場合、行がシャードに格納されます。 同じハッシュ範囲のシャードは常に同じノードに配置されます。 ディストリビューション列の値が同じ行は、テーブル全体で常に同じノードに置かれます。

![シャード](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>コロケーションの実例

マルチテナント Web アナリティクス SaaS に含まれる可能性がある次のテーブルがあるとします。

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

ここで、顧客向けのダッシュボードで発行される可能性があるクエリに応答したいと考えます。 サンプル クエリは、"テナント 6 の '/blog' で始まるすべてのページに対する過去 1 週間の訪問数を返す" です。

データが単一サーバー デプロイ オプションにあった場合は、SQL で提供される豊富なリレーショナル操作セットを使用してクエリを簡単に表現できました。

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

このクエリの[ワーキング セット](https://en.wikipedia.org/wiki/Working_set)がメモリに収まる限り、単一サーバー テーブルは適切なソリューションです。 Hyperscale (Citus) デプロイ オプションによるデータ モデルのスケーリングの機会について考えてみましょう。

### <a name="distribute-tables-by-id"></a>ID によるテーブルの分散

単一サーバーのクエリは、テナント数と各テナントに格納されているデータが増えると同時に、速度低下が始まります。 ワーキング セットがメモリに収まらなくなり、CPU がボトルネックになります。

この場合、Hyperscale (Citus) を使用して、多数のノード間でデータをシャード化できます。 シャード化することになった場合に行う必要がある最初の最も重要な選択は、ディストリビューション列です。 イベント テーブルに `event_id` を使用し、`page` テーブルに `page_id` を使用した単純な選択から始めましょう。

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

さまざまな worker 間でデータが分散されている場合、単一の PostgreSQL ノードの場合と同様に、結合を実行できません。 代わりに 2 つのクエリを発行する必要があります。

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

その後、2 つの手順の結果をアプリケーションで結合する必要があります。

クエリの実行では、ノード間に分散しているシャード内のデータを参照する必要があります。

![非効率的なクエリ](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

この場合、データの分散によって、大きな欠点が生じます。

-   各シャードに対するクエリと複数のクエリの実行によるオーバーヘッド。
-   クライアントに多くの行を返す Q1 のオーバーヘッド。
-   Q2 が大きくなる。
-   複数の手順でクエリを記述する必要性によって、アプリケーションの変更が必要である。

データが分散されているため、クエリを並列化できます。 これは、クエリが実行する作業の量が、多くのシャードに照会するオーバーヘッドより大幅に大きい場合にのみ有益です。

### <a name="distribute-tables-by-tenant"></a>テナントによるテーブルの分散

Hyperscale (Citus) では、同じディストリビューション列の値を持つ行が、同じノード上に置かれることが保証されます。 最初からやり直して、`tenant_id` をディストリビューション列としてテーブルを作成できます。

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

これで Hyperscale (Citus) は、変更 (Q1) なく、元の単一サーバー クエリに応答できます。

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

tenant_id でのフィルターと結合のため、Hyperscale (Citus) は、その特定のテナントのデータを格納する併置されたシャードのセットを使用して、クエリ全体に応答できることを認識します。 単一の PostgreSQL ノードは、1 手順でクエリに応答できます。

![優れたクエリ](media/concepts-hyperscale-colocation/colocation-better-query.png)

場合によっては、クエリとテーブル スキーマを変更して、一意の制約にテナント ID を含めて、条件を結合する必要があります。 通常これは簡単な変更で済みます。

## <a name="next-steps"></a>次の手順

- [マルチテナントのチュートリアル](tutorial-design-database-hyperscale-multi-tenant.md)で、テナント データを併置する方法について参照してください
