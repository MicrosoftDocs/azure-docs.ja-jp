---
title: 分散テーブルを変更する - Hyperscale (Citus) - Azure Database for PostgreSQL
description: 分散テーブルを作成および変更するための SQL コマンド - Azure portal を使用した Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: cf9f9ca5b8690a38c6e5aa6f519378c0a2e3a4f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026439"
---
# <a name="distribute-and-modify-tables"></a>テーブルの分散と変更

## <a name="distributing-tables"></a>テーブルの分散

分散テーブルを作成するには、まずテーブル スキーマを定義する必要があります。 それを行うには、通常の PostgreSQL テーブルの場合と同様に、[CREATE TABLE](http://www.postgresql.org/docs/current/static/sql-createtable.html) ステートメントを使用してテーブルを定義できます。

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    actor jsonb,
    org jsonb,
    created_at timestamp
);
```

次に、create\_distributed\_table() 関数を使用して、テーブルのディストリビューション列を指定したり、ワーカー シャードを作成したりできます。

```sql
SELECT create_distributed_table('github_events', 'repo_id');
```

この関数呼び出しは、Hyperscale (Citus) に (列の値をハッシュすることによって) github\_events テーブルを repo\_id 列に分散させる必要があることを通知します。 この関数はまた、citus.shard\_count および citus.shard\_replication\_factor 構成値を使用してワーカー ノード上にシャードを作成します。

これにより、合計 citus.shard\_count 個のシャードが作成されます。ここで、各シャードはハッシュ領域の一部を所有し、既定の citus.shard\_replication\_factor 構成値に基づいてレプリケートされます。 ワーカー上に作成されたシャード レプリカには、コーディネーター上のテーブルと同じテーブル スキーマ、インデックス、制約定義が割り当てられます。 レプリカが作成されると、この関数は、コーディネーター上のすべての分散メタデータを保存します。

作成された各シャードには一意のシャード ID が割り当てられ、そのレプリカのシャード ID はすべて同じになります。 シャードは、ワーカー ノード上で \'tablename\_shardid\' という名前の通常の PostgreSQL テーブルとして表されます。ここで、tablename は分散テーブルの名前であり、shardid は割り当てられた一意の ID です。 個々のシャードでコマンドを表示または実行するには、ワーカー postgres インスタンスに接続できます。

これで、分散テーブルにデータを挿入し、それに対してクエリを実行する準備ができました。 また、[テーブルとシャードの DDL](reference-hyperscale-functions.md#table-and-shard-ddl) のリファレンスで、このセクションで使用される UDF の詳細を参照することもできます。

### <a name="reference-tables"></a>参照テーブル

上記の方法では、テーブルを複数の水平方向のシャードに分散させます。  それとは別に、テーブルを 1 つのシャードに分散させ、そのシャードをすべてのワーカー ノードにレプリケートする方法があります。 この方法で分散されたテーブルは、*参照テーブル* と呼ばれます。 これらは、クラスター内の複数のノードによって頻繁にアクセスされる必要があるデータを格納するために使用されます。

参照テーブルの一般的な候補には、次のものがあります。

-   より大きな分散テーブルと結合する必要がある小さなテーブル。
-   テナント ID 列がないか、またはテナントに関連付けられていないマルチテナント アプリ内のテーブル。 (または、移行中には、テナントに関連付けられている一部のテーブルも該当します。)
-   複数の列にまたがる一意制約が必要であり、かつ十分に小さなテーブル。

たとえば、マルチテナント e コマース サイトで、そのいずれかの店舗の取引の消費税を計算する必要があるとします。 税金の情報は、どのテナントにも限定されません。 それを共有テーブルに配置することは理にかなっています。 米国中心の参照テーブルは次のようになります。

```postgresql
-- a reference table

CREATE TABLE states (
  code char(2) PRIMARY KEY,
  full_name text NOT NULL,
  general_sales_tax numeric(4,3)
);

-- distribute it to all workers

SELECT create_reference_table('states');
```

これで、ショッピング カートの税金の計算などのクエリはネットワーク オーバーヘッドなしに `states` テーブルで結合することができ、より適切な検証を行うために状態コードに外部キーを追加できます。

テーブルを 1 つのレプリケートされたシャードとして分散することに加えて、`create_reference_table` UDF は、それを Hyperscale (Citus) メタデータ テーブル内の参照テーブルとしてマークします。 Hyperscale (Citus) では、この方法でマークされたテーブルへの変更に対して 2 フェーズ コミット ([2PC](https://en.wikipedia.org/wiki/Two-phase_commit_protocol)) が自動的に実行されます。これにより、強力な一貫性が保証されます。

シャード数が 1 つの分散テーブルがある場合は、次のように、それを認識される参照テーブルになるようにアップグレードできます。

```postgresql
SELECT upgrade_to_reference_table('table_name');
```

参照テーブルの使用の別の例については、[マルチテナント データベースのチュートリアル](tutorial-design-database-hyperscale-multi-tenant.md)に関するページを参照してください。

### <a name="distributing-coordinator-data"></a>コーディネーター データの分散

既存の PostgreSQL データベースが Hyperscale (Citus) クラスターのコーディネーター ノードに変換される場合は、そのテーブル内のデータを効率的に、かつアプリケーションの中断を最小限に抑えて分散させることができます。

前に説明した `create_distributed_table` 関数は空のテーブルと空でないテーブルの両方で動作し、後者の場合は、テーブル行をクラスター全体に自動的に分散させます。 データがコピーされているかどうかは、次のメッセージ \"通知: ローカル テーブル\...からデータをコピーしています\" の存在でわかります。次に例を示します。

```postgresql
CREATE TABLE series AS SELECT i FROM generate_series(1,1000000) i;
SELECT create_distributed_table('series', 'i');
NOTICE:  Copying data from local table...
 create_distributed_table
 --------------------------

 (1 row)
```

データが移行されている間はテーブルへの書き込みがブロックされ、関数がコミットされると、保留中の書き込みは分散クエリとして処理されます。 (関数が失敗した場合、このクエリは再びローカルになります。)読み取りは通常どおりに続行でき、関数がコミットされると分散クエリになります。

テーブル A と B を分散させる場合 (ここで、A には B への外部キーが含まれています) は、最初にキー宛先テーブル B を分散させます。 それを間違った順序で行うと、エラーが発生します。

```
ERROR:  cannot create foreign key constraint
DETAIL:  Referenced table must be a distributed table or a reference table.
```

正しい順序で分散させることができない場合は、外部キーを削除し、テーブルを分散させてから、外部キーを再作成します。

外部データベース (Amazon RDS など) から Hyperscale (Citus) クラウドにデータを移行する場合は、最初に `create_distributed_table` を使用して Hyperscale (Citus) の分散テーブルを作成し、次にそのテーブルにデータをコピーします。
分散テーブルへのコピーにより、コーディネーター ノード上の領域不足が回避されます。

## <a name="colocating-tables"></a>テーブルの併置

コロケーションとは、関連情報を同じマシン上に保持することを示します。 これにより、効率的なクエリを可能にしながら、データセット全体に対する水平方向のスケーラビリティを利用できます。 詳細については、[コロケーション](concepts-hyperscale-colocation.md)に関するページを参照してください。

テーブルはグループで併置されます。 テーブルのコロケーション グループの割り当てを手動で制御するには、`create_distributed_table` のオプションの `colocate_with` パラメーターを使用します。 テーブルのコロケーションが必要ない場合は、このパラメーターを省略します。 この既定値は `'default'` です。これは、このテーブルを、ディストリビューション列の種類、シャード数、レプリケーション係数が同じである他の既定のコロケーション テーブルとグループ化します。 この暗黙的なコロケーションを中断または更新する場合は、`update_distributed_table_colocation()` を使用できます。

```postgresql
-- these tables are implicitly co-located by using the same
-- distribution column type and shard count with the default
-- co-location group

SELECT create_distributed_table('A', 'some_int_col');
SELECT create_distributed_table('B', 'other_int_col');
```

新しいテーブルが、その予定される暗黙的なコロケーション グループ内の他のテーブルに関連していない場合は、`colocated_with => 'none'` を指定します。

```postgresql
-- not co-located with other tables

SELECT create_distributed_table('A', 'foo', colocate_with => 'none');
```

同じグループ内のシャードはまとめて移動する必要があるため、関連のないテーブルを独自のコロケーション グループに分割すると、[シャードの再調整](howto-hyperscale-scale-rebalance.md)のパフォーマンスが向上します。

テーブルが実際に関連している場合 (たとえば、結合される場合など)、それらを明示的に併置することは理にかなっています。 適切なコロケーションの利点は、どのオーバーヘッドの再調整よりも重要です。

複数のテーブルを明示的に併置するには、1 つのテーブルを分散させてから、他のテーブルをそのコロケーション グループに配置します。 次に例を示します。

```postgresql
-- distribute stores
SELECT create_distributed_table('stores', 'store_id');

-- add to the same group as stores
SELECT create_distributed_table('orders', 'store_id', colocate_with => 'stores');
SELECT create_distributed_table('products', 'store_id', colocate_with => 'stores');
```

コロケーション グループに関する情報が [pg_dist_colocation](reference-hyperscale-metadata.md#colocation-group-table) テーブルに格納されているのに対して、[pg_dist_partition](reference-hyperscale-metadata.md#partition-table) はどのテーブルがどのグループに割り当てられているかを明らかにします。

## <a name="dropping-tables"></a>テーブルの削除

分散テーブルを削除するには、標準の PostgreSQL DROP TABLE コマンドを使用できます。 通常のテーブルと同様に、DROP TABLE では、ターゲット テーブルに対して存在するすべてのインデックス、ルール、トリガー、制約が削除されます。 さらに、ワーカー ノード上のシャードも削除され、それらのメタデータがクリーンアップされます。

```sql
DROP TABLE github_events;
```

## <a name="modifying-tables"></a>テーブルの変更

Hyperscale (Citus) では、多くの種類の DDL ステートメントが自動的に伝達されます。
コーディネーター ノード上の分散テーブルを変更すると、ワーカー上のシャードも更新されます。 他の DDL ステートメントには手動の伝達が必要であり、ディストリビューション列を変更するような他の特定のステートメントは禁止されています。
自動伝達に適していない DDL を実行しようとすると、エラーが発生し、コーディネーター ノード上のテーブルは変更されないままになります。

伝達される DDL ステートメントのカテゴリのリファレンスを次に示します。
自動伝達は、[構成パラメーター](reference-hyperscale-parameters.md#citusenable_ddl_propagation-boolean)を使用して有効または無効にすることができます。

### <a name="addingmodifying-columns"></a>列の追加/変更

Hyperscale (Citus) では、ほとんどの [ALTER TABLE](https://www.postgresql.org/docs/current/static/ddl-alter.html) コマンドが自動的に伝達されます。 列の追加やその既定値の変更は、単一マシンの PostgreSQL データベースで実行されたものとして動作します。

```postgresql
-- Adding a column

ALTER TABLE products ADD COLUMN description text;

-- Changing default value

ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

既存の列に、その名前の変更やそのデータ型の変更などの大きな変更を加えることもできます。 ただし、[ディストリビューション列](concepts-hyperscale-nodes.md#distribution-column)のデータ型は変更できません。
この列により、テーブル データが Hyperscale (Citus) クラスター経由でどのように分散されるかが決定されるため、そのデータ型を変更するにはデータを移動する必要があります。

それを行おうとすると、エラーが発生します。

```postgres
-- assumining store_id is the distribution column
-- for products, and that it has type integer

ALTER TABLE products
ALTER COLUMN store_id TYPE text;

/*
ERROR:  XX000: cannot execute ALTER TABLE command involving partition column
LOCATION:  ErrorIfUnsupportedAlterTableStmt, multi_utility.c:2150
*/
```

### <a name="addingremoving-constraints"></a>制約の追加/削除

Hyperscale (Citus) を使用すると、リレーショナル データベースの安全性 (データベースの制約を含む) を引き続き享受できます (PostgreSQL の[ドキュメント](https://www.postgresql.org/docs/current/static/ddl-constraints.html)を参照)。
分散システムの性質により、Hyperscale (Citus) では、ワーカー ノード間の一意性制約または参照整合性を相互参照しません。

併置された分散テーブル間に外部キーを設定するには、常に、そのキーにディストリビューション列を含めます。 ディストリビューション列を含めるには、それを複合キーにすることが必要になる場合があります。

外部キーは、次の状況で作成できます。

-   2 つのローカル (非分散) テーブルの間
-   2 つの参照テーブルの間
-   キーにディストリビューション列が含まれている場合は、2 つの[併置された](concepts-hyperscale-colocation.md)分散テーブルの間、または
-   [参照テーブル](concepts-hyperscale-nodes.md#type-2-reference-tables)を参照する分散テーブルとして

参照テーブルから分散テーブルへの外部キーはサポートされていません。

> [!NOTE]
>
> プライマリ キーと一意性制約には、ディストリビューション列が含まれている必要があります。 それらを非ディストリビューション列に追加すると、エラーが発生します。

次の例は、分散テーブルにプライマリ キーと外部キーを作成する方法を示しています。

```postgresql
--
-- Adding a primary key
-- --------------------

-- We'll distribute these tables on the account_id. The ads and clicks
-- tables must use compound keys that include account_id.

ALTER TABLE accounts ADD PRIMARY KEY (id);
ALTER TABLE ads ADD PRIMARY KEY (account_id, id);
ALTER TABLE clicks ADD PRIMARY KEY (account_id, id);

-- Next distribute the tables

SELECT create_distributed_table('accounts', 'id');
SELECT create_distributed_table('ads',      'account_id');
SELECT create_distributed_table('clicks',   'account_id');

--
-- Adding foreign keys
-- -------------------

-- Note that this can happen before or after distribution, as long as
-- there exists a uniqueness constraint on the target column(s) which
-- can only be enforced before distribution.

ALTER TABLE ads ADD CONSTRAINT ads_account_fk
  FOREIGN KEY (account_id) REFERENCES accounts (id);
ALTER TABLE clicks ADD CONSTRAINT clicks_ad_fk
  FOREIGN KEY (account_id, ad_id) REFERENCES ads (account_id, id);
```

同様に、一意性制約にディストリビューション列を含めます。

```postgresql
-- Suppose we want every ad to use a unique image. Notice we can
-- enforce it only per account when we distribute by account id.

ALTER TABLE ads ADD CONSTRAINT ads_unique_image
  UNIQUE (account_id, image_url);
```

NOT NULL 制約は、ワーカー間の参照を必要としないため、任意の列 (分散または非分散) に適用できます。

```postgresql
ALTER TABLE ads ALTER COLUMN image_url SET NOT NULL;
```

### <a name="using-not-valid-constraints"></a>NOT VALID 制約の使用

状況によっては、新しい行に制約を適用しながら、準拠していない既存の行を変更されないまま存続できるようにすると有効な場合があります。 Hyperscale (Citus) では、PostgreSQL の \"NOT VALID\" 制約の指定を使用して、CHECK 制約と外部キーに対してこの機能がサポートされています。

たとえば、ユーザー プロファイルを[参照テーブル](concepts-hyperscale-nodes.md#type-2-reference-tables)に格納するアプリケーションを考えてみます。

```postgres
-- we're using the "text" column type here, but a real application
-- might use "citext" which is available in a postgres contrib module

CREATE TABLE users ( email text PRIMARY KEY );
SELECT create_reference_table('users');
```

そのうちに、アドレス以外のいくつかの値がテーブルに挿入されるとします。

```postgres
INSERT INTO users VALUES
   ('foo@example.com'), ('hacker12@aol.com'), ('lol');
```

アドレスを検証しようとしても、PostgreSQL では通常、既存の行に対して失敗する CHECK 制約を追加することが許可されません。 ただし、NOT VALID とマークされた制約は許可 *されます*。

```postgres
ALTER TABLE users
ADD CONSTRAINT syntactic_email
CHECK (email ~
   '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$'
) NOT VALID;
```

これで、新しい行が保護されるようになりました。

```postgres
INSERT INTO users VALUES ('fake');

/*
ERROR:  new row for relation "users_102010" violates
        check constraint "syntactic_email_102010"
DETAIL:  Failing row contains (fake).
*/
```

後で、ピーク以外の時間帯に、データベース管理者は不適切な行を修正し、制約を再検証しようと試みることができます。

```postgres
-- later, attempt to validate all rows
ALTER TABLE users
VALIDATE CONSTRAINT syntactic_email;
```

PostgreSQL のドキュメントの「[ALTER TABLE](https://www.postgresql.org/docs/current/sql-altertable.html)」のセクションには、NOT VALID と VALIDATE CONSTRAINT に関するより詳細な情報が含まれています。

### <a name="addingremoving-indices"></a>インデックスの追加/削除

Hyperscale (Citus) では、[インデックス](https://www.postgresql.org/docs/current/static/sql-createindex.html)の追加と削除がサポートされています。

```postgresql
-- Adding an index

CREATE INDEX clicked_at_idx ON clicks USING BRIN (clicked_at);

-- Removing an index

DROP INDEX clicked_at_idx;
```

インデックスを追加すると、書き込みロックが取得されます。これは、マルチテナントの\"レコードのシステム\"では望ましくない場合があります。アプリケーションのダウンタイムを最小限に抑えるには、代わりに、インデックスを[同時に](https://www.postgresql.org/docs/current/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY)作成します。 この方法には、標準のインデックス構築より多くの合計作業が必要になり、完了までの時間が長くなります。 ただし、インデックスの構築中に通常の操作を続行できるため、この方法は運用環境で新しいインデックスを追加するために役立ちます。

```postgresql
-- Adding an index without locking table writes

CREATE INDEX CONCURRENTLY clicked_at_idx ON clicks USING BRIN (clicked_at);
```
