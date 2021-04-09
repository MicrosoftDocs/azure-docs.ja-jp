---
title: SQL 関数 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Hyperscale (Citus) SQL API の関数
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: f324ef44d002f50bf27c08072e904c1d92b5512f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026235"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>Hyperscale (Citus) SQL API の関数

このセクションでは、Hyperscale (Citus) によって提供されるユーザー定義関数の参照情報について説明します。 これらの関数は、標準の SQL コマンド以外に追加の分散機能を Hyperscale (Citus) に提供するのに役立ちます。

> [!NOTE]
>
> 以前のバージョンの Citus エンジンを実行している Hyperscale (Citus) サーバー グループでは、以下に示す機能の一部が提供されない場合があります。

## <a name="table-and-shard-ddl"></a>テーブルおよびシャードの DDL

### <a name="create_distributed_table"></a>create\_distributed\_table

create\_distributed\_table() 関数は、分散テーブルを定義し、それがハッシュ分散テーブルの場合はそのシャードを作成するために使用されます。 この関数は、テーブル名、ディストリビューション列、およびオプションの分散方法を受け取り、適切なメタデータを挿入してテーブルを分散済みとしてマークします。 分散方法が指定されていない場合、関数は既定で 'hash' 分散となります。 テーブルがハッシュで分散されている場合、関数により、シャード数とシャード レプリケーション係数の構成値に基づいて、ワーカー シャードも作成されます。 テーブルに行が含まれている場合、それらは自動的にワーカー ノードに分散されます。

この関数は、master\_create\_distributed\_table() に続く master\_create\_worker\_shards() の使用を置き換えます。

#### <a name="arguments"></a>引数

**table\_name:** 分散させる必要があるテーブルの名前。

**distribution\_column:** テーブルを分散させる際に基づく列。

**distribution\_type:** (省略可能) テーブルの分散に使用される方法。 許容される値は append または hash で、既定値は 'hash' です。

**colocate\_with:** (省略可能) 現在のテーブルを別のテーブルのコロケーション グループに含めます。 既定では、テーブルが同じ型の列によって分散され、同じシャード数を持ち、レプリケーション係数が同じ場合にそのテーブルは併置されます。 `colocate_with` に使用できる値は `default`、新しいコロケーション グループを開始する場合の `none`、またはそのテーブルと併置する別のテーブルの名前です。  ([テーブル コロケーション](concepts-hyperscale-colocation.md)に関するページを参照してください。)

`colocate_with` の既定値を使用すると、暗黙的にコロケーションが行われることに注意してください。 [コロケーション](concepts-hyperscale-colocation.md)は、テーブルが関連付けられている場合や結合される場合に便利です。  ただし、2 つのテーブルが互いに関連付けられておらず、ディストリビューション列に同じデータ型を使用する場合、誤って併置すると[シャード再調整](howto-hyperscale-scale-rebalance.md)中のパフォーマンスが低下する可能性があります。  テーブルのシャードは、\"カスケード形式\" で不必要に関連付けられて移動されます。

新しい分散テーブルが他のテーブルに関連付けられていない場合は、`colocate_with => 'none'` を指定することをお勧めします。

#### <a name="return-value"></a>戻り値

該当なし

#### <a name="example"></a>例

この例では、github\_events テーブルが、repo\_id 列に基づいて hash で分散される必要があることをデータベースに通知しています。

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>create\_reference\_table

create\_reference\_table() 関数は、小さな参照またはディメンション テーブルを定義するために使用されます。 この関数はテーブル名を受け取り、すべてのワーカー ノードにレプリケートされる 1 つのシャードのみを含む分散テーブルを作成します。

#### <a name="arguments"></a>引数

**table\_name:** 分散する必要がある小さなディメンションまたは参照テーブルの名前。

#### <a name="return-value"></a>戻り値

該当なし

#### <a name="example"></a>例

この例では、nation テーブルを参照テーブルとして定義する必要があることをデータベースに通知しています

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>upgrade\_to\_reference\_table

upgrade\_to\_reference\_table() 関数は、シャード数が 1 である既存の分散テーブルを受け取り、認識されている参照テーブルにアップグレードします。 この関数を呼び出すと、そのテーブルは [create_reference_table](#create_reference_table) で作成したかのようになります。

#### <a name="arguments"></a>引数

**table\_name:** 参照テーブルとして分散される分散テーブル (シャード数 = 1) の名前。

#### <a name="return-value"></a>戻り値

該当なし

#### <a name="example"></a>例

この例では、nation テーブルを参照テーブルとして定義する必要があることをデータベースに通知しています

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>mark\_tables\_colocated

mark\_tables\_colocated() 関数は、分散テーブル (ソース) と他のテーブルのリスト (ターゲット) を受け取り、ターゲットをソースと同じコロケーション グループに配置します。 ソースがまだグループに含まれていない場合、この関数によってこれが作成され、ソースとターゲットが割り当てられます。

テーブルの併置は、[create_distributed_table](#create_distributed_table) の `colocate_with` パラメーターを使用してテーブルの分散時に行うべきですが、必要に応じて `mark_tables_colocated` により後で処理を行うこともできます。

#### <a name="arguments"></a>引数

**source\_table\_name:** コロケーション グループを持ち、それにターゲットが一致するように割り当てられる分散テーブルの名前。

**target\_table\_names:** 分散ターゲット テーブルの名前の配列。空にすることはできません。 これらの分散テーブルの次の内容は、ソース テーブルと一致している必要があります。

> -   distribution メソッド
> -   ディストリビューション列の型
> -   レプリケーションの種類
> -   シャード数

上記のいずれも当てはまらない場合は、Hyperscale (Citus) によってエラーが発生します。 たとえば、ディストリビューション列の型が異なるテーブル `apples` と `oranges` を併置しようとした場合は、次のようになります。

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>戻り値

該当なし

#### <a name="example"></a>例

この例では、`products` と `line_items` を `stores` と同じコロケーション グループに配置します。 この例では、これらのテーブルはすべて型が一致する列 (おそらく \"store id\") で分散されていることを前提としています。

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>create\_distributed\_function

コーディネーター ノードからワーカーに関数を伝達し、分散実行用にマークします。 コーディネーターで分散関数が呼び出されると、Hyperscale (Citus) は\"分散引数\"の値を使用して、関数を実行するワーカー ノードを選択します。 ワーカーで関数を実行すると並列処理が増加し、待機時間が短くなるようにコードをシャード内のデータに近づけることができます。

分散関数の実行中に、Postgres 検索パスはコーディネーターからワーカーに伝達されません。そのため分散関数コードでは、データベース オブジェクトの名前を完全修飾する必要があります。 また、関数によって出力された通知もユーザーに表示されません。

#### <a name="arguments"></a>引数

**function\_name:** 分散される関数の名前。 PostgreSQL では複数の関数が同じ名前を持つことができるため、名前には関数のパラメーターの型をかっこで囲んで指定する必要があります。 たとえば、`'foo(int)'` は `'foo(int, text)'` とは異なります。

**distribution\_arg\_name:** (省略可能) 分散に使用される引数の名前。 便宜上 (または関数の引数に名前がない場合) は、`'$1'` のような位置指定プレースホルダーを使用できます。 このパラメーターが指定されていない場合、`function_name` で指定された関数が単にワーカーに作成されます。 ワーカー ノードが今後追加される場合、関数もそこに自動的に作成されます。

**colocate\_with:** (省略可能) 分散された関数が分散テーブル (または、より一般的にはコロケーション グループ) に対して読み取りまたは書き込みを行う場合は、`colocate_with` パラメーターを使用してそのテーブルの名前を指定してください。 その後、関数の各呼び出しは、関連するシャードを含むワーカー ノードで実行されます。

#### <a name="return-value"></a>戻り値

該当なし

#### <a name="example"></a>例

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

## <a name="metadata--configuration-information"></a>メタデータ / 構成情報

### <a name="master_get_table_metadata"></a>master\_get\_table\_metadata

master\_get\_table\_metadata() 関数を使用すると、分散テーブルのディストリビューション関連のメタデータを返すことができます。 このメタデータには、リレーション ID、ストレージの種類、分散方法、ディストリビューション列、レプリケーション数、最大シャード サイズ、およびテーブルのシャード配置ポリシーが含まれます。 この関数は裏では、Hyperscale (Citus) メタデータ テーブルに対してクエリを実行して必要な情報を取得し、ユーザーに返す前にタプルに連結します。

#### <a name="arguments"></a>引数

**table\_name:** メタデータをフェッチする分散テーブルの名前。

#### <a name="return-value"></a>戻り値

次の情報を含むタプル。

**logical\_relid:** 分散テーブルの OID。 pg\_class のシステム カタログ テーブル内の relfilenode 列を参照します。

**part\_storage\_type:** テーブルに使用するストレージの種類。 't' (標準テーブル)、'f' (外部テーブル)、または 'c' (列指向テーブル) を指定できます。

**part\_method:** テーブルに使用される分散方法。 'a' (append)、または 'h' (hash) を指定できます。

**part\_key:** テーブルのディストリビューション列。

**part\_replica\_count:** 現在のシャード レプリケーション数。

**part\_max\_size:** 現在の最大シャード サイズ (バイト単位)。

**part\_placement\_policy:** テーブルのシャードを配置するために使用されるシャード配置ポリシー。 1 (ローカル ノード優先) または 2 (ラウンドロビン) を指定できます。

#### <a name="example"></a>例

次の例では、github\_events テーブルのテーブル メタデータをフェッチして表示します。

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>get\_shard\_id\_for\_distribution\_column

Hyperscale (Citus) は、行のディストリビューション列の値とテーブルの分散方法に基づいて、分散テーブルのすべての行をシャードに割り当てます。 ほとんどの場合、この正確なマッピングはデータベース管理者は無視してもかまわない低レベルの細かい内容です。 ただし、手動によるデータベース メンテナンス タスクの場合や、興味を満たすための場合には、行のシャードを決定すると便利です。 `get_shard_id_for_distribution_column` 関数は、ハッシュ分散テーブルと範囲分散テーブル、および参照テーブルに対してこの情報を提供します。 append 分散では機能しません。

#### <a name="arguments"></a>引数

**table\_name:** 分散テーブル。

**distribution\_value:** ディストリビューション列の値。

#### <a name="return-value"></a>戻り値

Hyperscale (Citus) によって、指定されたテーブルのディストリビューション列の値に関連付けられるシャード ID。

#### <a name="example"></a>例

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>column\_to\_column\_name

`pg_dist_partition` の `partkey` 列をテキストの列の名前に変換します。 この変換は、分散テーブルのディストリビューション列を特定するのに役立ちます。

詳細については、[ディストリビューション列の選択](concepts-hyperscale-choose-distribution-column.md)に関するページを参照してください。

#### <a name="arguments"></a>引数

**table\_name:** 分散テーブル。

**column\_var\_text:** `pg_dist_partition` テーブル内の `partkey` の値。

#### <a name="return-value"></a>戻り値

`table_name` のディストリビューション列の名前。

#### <a name="example"></a>例

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

出力:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>citus\_relation\_size

指定された分散テーブルのすべてのシャードによって使用されているディスク領域を取得します。
このディスク領域には\"メインのフォーク\"のサイズが含まれますが、シャードの可視性マップと空き領域マップは除外されます。

#### <a name="arguments"></a>引数

**logicalrelid:** 分散テーブルの名前です。

#### <a name="return-value"></a>戻り値

bigint としてのサイズ (バイト単位)。

#### <a name="example"></a>例

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>citus\_table\_size

指定された分散テーブルのすべてのシャードによって使用されているディスク領域を、インデックスを除外して取得します (ただし、TOAST、空き領域マップ、可視性マップを含む)。

#### <a name="arguments"></a>引数

**logicalrelid:** 分散テーブルの名前です。

#### <a name="return-value"></a>戻り値

bigint としてのサイズ (バイト単位)。

#### <a name="example"></a>例

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>citus\_total\_relation\_size

すべてのインデックスと TOAST データを含む、指定された分散テーブルのすべてのシャードによって使用されている合計ディスク領域を取得します。

#### <a name="arguments"></a>引数

**logicalrelid:** 分散テーブルの名前です。

#### <a name="return-value"></a>戻り値

bigint としてのサイズ (バイト単位)。

#### <a name="example"></a>例

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>citus\_stat\_statements\_reset

[citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) からすべての行を削除します。
この関数は、`pg_stat_statements_reset()` からは独立して動作します。 すべての統計をリセットするには、両方の関数を呼び出します。

#### <a name="arguments"></a>引数

該当なし

#### <a name="return-value"></a>戻り値

なし

## <a name="server-group-management-and-repair"></a>サーバー グループの管理と修復

### <a name="master_copy_shard_placement"></a>master\_copy\_shard\_placement

変更コマンドまたは DDL 操作中にシャードの配置を更新できなかった場合は、非アクティブとしてマークされます。 その場合は master\_copy\_shard\_placement 関数を呼び出すことで、正常な配置データを使用して非アクティブなシャードの配置を修復できます。

シャードを修復するために、最初に関数によって異常なシャードの配置が削除され、コーディネーターのスキーマを使用して再作成されます。 シャードの配置が作成されると、関数によって正常な配置からデータがコピーされ、メタデータが更新されて新しいシャードの配置が正常としてマークされます。 この関数を使用すると、修復中のあらゆる同時変更からシャードが確実に保護されます。

#### <a name="arguments"></a>引数

**shard\_id:** 修復するシャードの ID。

**source\_node\_name:** 正常なシャードの配置が存在するノードの DNS 名 (\"ソース\" ノード)。

**source\_node\_port:** データベース サーバーがリッスンしているソース ワーカー ノードのポート。

**target\_node\_name:** 無効なシャードの配置が存在するノードの DNS 名 (\"ターゲット\" ノード)。

**target\_node\_port:** データベース サーバーがリッスンしているターゲット ワーカー ノードのポート。

#### <a name="return-value"></a>戻り値

該当なし

#### <a name="example"></a>例

次の例では、ポート 5432 の 'bad\_host' 上で実行されているデータベース サーバー上に存在する、シャード 12345 の非アクティブなシャード配置を修復します。 修復には、ポートの 'good\_host' 上で実行されているサーバー上に存在する正常なシャード配置のデータが使用されます
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>master\_move\_shard\_placement

この関数を使用すると、指定されたシャード (およびそれに併置されているシャード) が、あるノードから別のノードに移動します。 通常は、データベース管理者によって直接呼び出されるのではなく、シャードの再調整時に間接的に使用されます。

データの移動には、ブロッキングと非ブロッキングの 2 つの方法があります。 ブロッキング アプローチとは、移動中に、シャードに対するすべての変更が一時停止されることを意味します。
2 番目の方法は、シャードへの書き込みのブロックを回避するために Postgres 10 の論理レプリケーションに依存しています。

移動操作が正常に完了すると、ソース ノードのシャードが削除されます。 いずれかの時点で移動が失敗した場合、この関数によってエラーがスローされ、ソース ノードとターゲット ノードは変更されないまま残されます。

#### <a name="arguments"></a>引数

**shard\_id:** 移動するシャードの ID。

**source\_node\_name:** 正常なシャードの配置が存在するノードの DNS 名 (\"ソース\" ノード)。

**source\_node\_port:** データベース サーバーがリッスンしているソース ワーカー ノードのポート。

**target\_node\_name:** 無効なシャードの配置が存在するノードの DNS 名 (\"ターゲット\" ノード)。

**target\_node\_port:** データベース サーバーがリッスンしているターゲット ワーカー ノードのポート。

**shard\_transfer\_mode:** (省略可能) レプリケーションの方法として、PostgreSQL 論理レプリケーションを使用するか、ワーカー間の COPY コマンドを使用するかを指定します。 指定できる値は、

> -   `auto`:論理レプリケーションが可能な場合はレプリカ ID を必要とします。それ以外の場合は、レガシの動作を使用します (例: シャード修復の場合は、PostgreSQL 9.6)。 これが既定値です。
> -   `force_logical`:テーブルにレプリカ ID がない場合でも、論理レプリケーションを使用します。 レプリケーション中は、テーブルに対して UPDATE/DELETE ステートメントを同時に実行することはできません。
> -   `block_writes`:主キーまたはレプリカ ID がないテーブルには、COPY (書き込みをブロックします) を使用します。

#### <a name="return-value"></a>戻り値

該当なし

#### <a name="example"></a>例

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>rebalance\_table\_shards

rebalance\_table\_shards() 関数は、指定されたテーブルのシャードを移動して、ワーカー間で均等に分散されるようにします。 この関数では、サーバー グループが指定されたしきい値内で分散されるようにするために、必要な移動の一覧が最初に計算されます。 次に、シャードの配置をソース ノードから宛先ノードに 1 つずつ移動し、対応するシャード メタデータを更新して移動を反映します。

シャードが\"均等に分散された\"かどうかを判断する際には、すべてのシャードにコストが割り当てられます。既定では、各シャードのコスト (値 1) は同じであるため、ワーカー間のコストが均等になるように分散することは、それぞれのシャードの数を均等化することと同じです。 定数コスト戦略は \"by\_shard\_count\" と呼ばれ、既定の再調整戦略です。

既定の戦略は、次のような場合に適しています。

*  シャードのサイズがほぼ同じである
*  シャードがほぼ同じ量のトラフィックを受け取る
*  ワーカー ノードのサイズと種類がすべて同じである
*  シャードが特定のワーカーにピン留めされていない

これらの前提条件のいずれかが当てはまらない場合、既定の再調整は不適切な計画となります。 この場合は、`rebalance_strategy` パラメーターを使用して戦略をカスタマイズできます。

rebalance\_table\_shards を実行する前に [get_rebalance_table_shards_plan](#get_rebalance_table_shards_plan) を呼び出して、実行するアクションを表示して確認することをお勧めします。

#### <a name="arguments"></a>引数

**table\_name:** (省略可能) シャードを再調整する必要があるテーブルの名前。 NULL の場合は、既存のすべてのコロケーション グループを再調整します。

**threshold:** (省略可能) ノード使用率の、平均使用率からの最大差を示す 0.0 から 1.0 の範囲の浮動小数点数。 たとえば 0.1 を指定すると、同じ数のシャード ±10% を維持するよう、シャード リバランサーによってすべてのノードの調整が行われます。
具体的には、シャード リバランサーはすべてのワーカー ノードの使用率を、(1 - しきい値) \* 平均\_使用率 \...(1
+ + しきい値) \* 平均\_使用率の範囲に収束させようとします。

**max\_shard\_moves:** (省略可能) 移動するシャードの最大数。

**excluded\_shard\_list:** (省略可能) 再調整操作中に移動しないシャードの識別子。

**shard\_transfer\_mode:** (省略可能) レプリケーションの方法として、PostgreSQL 論理レプリケーションを使用するか、ワーカー間の COPY コマンドを使用するかを指定します。 指定できる値は、

> -   `auto`:論理レプリケーションが可能な場合はレプリカ ID を必要とします。それ以外の場合は、レガシの動作を使用します (例: シャード修復の場合は、PostgreSQL 9.6)。 これが既定値です。
> -   `force_logical`:テーブルにレプリカ ID がない場合でも、論理レプリケーションを使用します。 レプリケーション中は、テーブルに対して UPDATE/DELETE ステートメントを同時に実行することはできません。
> -   `block_writes`:主キーまたはレプリカ ID がないテーブルには、COPY (書き込みをブロックします) を使用します。

**drain\_only:** (省略可能) true の場合、[pg_dist_node](reference-hyperscale-metadata.md#worker-node-table) で `shouldhaveshards` が false に設定されているワーカー ノードからシャードを移動します。他のシャードは移動しません。

**rebalance\_strategy:** (省略可能) [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) 内の戦略の名前。
この引数を省略した場合、関数では、表に示されているように既定の戦略が選択されます。

#### <a name="return-value"></a>戻り値

該当なし

#### <a name="example"></a>例

次の例では、github\_events テーブルのシャードを既定のしきい値内に再調整しようとしています。

```postgresql
SELECT rebalance_table_shards('github_events');
```

こちらの使用例では、ID 1 および 2 のシャードを移動せずに github\_events テーブルを再調整しようとしています。

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>get\_rebalance\_table\_shards\_plan

[rebalance_table_shards](#rebalance_table_shards) でのシャード移動の計画を、実行せずに出力します。
まれではありますが、get\_rebalance\_table\_shards\_plan では、同じ引数を使用して rebalance\_table\_shards を呼び出した場合と比べて若干異なる計画が出力される可能性があります。 これらは同時に実行されないため、サーバー グループに関する事実 (ディスク領域など) が、呼び出しの間で異なる場合があります。

#### <a name="arguments"></a>引数

rebalance\_table\_shards と同じ引数、すなわち relation、threshold、max\_shard\_moves、excluded\_shard\_list、drain\_only。 引数の意味については、その関数のドキュメントを参照してください。

#### <a name="return-value"></a>戻り値

次の列を含むタプル:

-   **table\_name**:シャードが移動するテーブル
-   **shardid**:問題のシャード
-   **shard\_size**:サイズ (バイト)
-   **sourcename**:ソース ノードのホスト名
-   **sourceport**:ソース ノードのポート
-   **targetname**:宛先ノードのホスト名
-   **targetport**:宛先ノードのポート

### <a name="get_rebalance_progress"></a>get\_rebalance\_progress

シャードの再調整が開始されると、`get_rebalance_progress()` 関数によって、関連するすべてのシャードの進行状況が一覧表示されます。 `rebalance_table_shards()` によって計画および実行された移動が監視されます。

#### <a name="arguments"></a>引数

該当なし

#### <a name="return-value"></a>戻り値

次の列を含むタプル:

-   **sessionid**:再調整モニターの Postgres PID
-   **table\_name**:シャードが移動しているテーブル
-   **shardid**:問題のシャード
-   **shard\_size**:サイズ (バイト)
-   **sourcename**:ソース ノードのホスト名
-   **sourceport**:ソース ノードのポート
-   **targetname**:宛先ノードのホスト名
-   **targetport**:宛先ノードのポート
-   **progress**:0 = 移動の待機中。1 = 移動中。2 = 完了

#### <a name="example"></a>例

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>citus\_add\_rebalance\_strategy

[pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) に行を追加します。

#### <a name="arguments"></a>引数

これらの引数の詳細については、`pg_dist_rebalance_strategy` 内の対応する列の値を参照してください。

**name:** 新しい戦略の識別子

**shard\_cost\_function:** 各シャードの \"コスト\" を決定するために使用される関数を特定します

**node\_capacity\_function:** ノード容量を測定する関数を特定します

**shard\_allowed\_on\_node\_function:** どのシャードをどのノードに配置できるかを決定する関数を特定します

**default\_threshold:** ノード間で累積シャード コストをどの程度正確に分散するかを調整する、浮動小数点のしきい値

**minimum\_threshold:** (省略可能) rebalance\_table\_shards() のしきい値引数に許容される最小値を保持するセーフガード列。 既定値は 0 です

#### <a name="return-value"></a>戻り値

該当なし

### <a name="citus_set_default_rebalance_strategy"></a>citus\_set\_default\_rebalance\_strategy

[pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) テーブルを更新して、引数で指定された戦略がシャードの再調整時に既定で選択されるように変更します。

#### <a name="arguments"></a>引数

**name:** pg\_dist\_rebalance\_strategy の戦略の名前

#### <a name="return-value"></a>戻り値

該当なし

#### <a name="example"></a>例

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>citus\_remote\_connection\_stats

citus\_remote\_connection\_stats() 関数は、各リモート ノードへのアクティブな接続の数を示します。

#### <a name="arguments"></a>引数

該当なし

#### <a name="example"></a>例

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="master_drain_node"></a>master\_drain\_node

master\_drain\_node() 関数は、指定されたノードから [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table) で `shouldhaveshards` が true に設定されている他のノードにシャードを移動します。 この関数は、サーバー グループからノードを削除してノードの物理サーバーをオフにする前に呼び出します。

#### <a name="arguments"></a>引数

**nodename:** ドレインされるノードのホスト名の名前。

**nodeport:** ドレインされるノードのポート番号。

**shard\_transfer\_mode:** (省略可能) レプリケーションの方法として、PostgreSQL 論理レプリケーションを使用するか、ワーカー間の COPY コマンドを使用するかを指定します。 指定できる値は、

> -   `auto`:論理レプリケーションが可能な場合はレプリカ ID を必要とします。それ以外の場合は、レガシの動作を使用します (例: シャード修復の場合は、PostgreSQL 9.6)。 これが既定値です。
> -   `force_logical`:テーブルにレプリカ ID がない場合でも、論理レプリケーションを使用します。 レプリケーション中は、テーブルに対して UPDATE/DELETE ステートメントを同時に実行することはできません。
> -   `block_writes`:主キーまたはレプリカ ID がないテーブルには、COPY (書き込みをブロックします) を使用します。

**rebalance\_strategy:** (省略可能) [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) 内の戦略の名前。
この引数を省略した場合、関数では、表に示されているように既定の戦略が選択されます。

#### <a name="return-value"></a>戻り値

該当なし

#### <a name="example"></a>例

1 つのノードを削除する一般的な手順を次に示します (例として、標準的な PostgreSQL ポートの "10.0.0.1")。

1.  ノードをドレインします。

    ```postgresql
    SELECT * from master_drain_node('10.0.0.1', 5432);
    ```

2.  コマンドが終了するまで待機します

3.  ノードを削除します

複数のノードをドレインする場合は、代わりに [rebalance_table_shards](#rebalance_table_shards) を使用することをお勧めします。 こうすることで、Hyperscale (Citus) による事前の計画が可能となり、シャードを最小回数で移動できます。

1.  削除する各ノードに対して次を実行します。

    ```postgresql
    SELECT * FROM master_set_node_property(node_hostname, node_port, 'shouldhaveshards', false);
    ```

2.  [rebalance_table_shards](#rebalance_table_shards) を使用して一度にすべてのドレインを実行します

    ```postgresql
    SELECT * FROM rebalance_table_shards(drain_only := true);
    ```

3.  ドレインの再調整が終了するまで待機します

4.  ノードを削除します

### <a name="replicate_table_shards"></a>replicate\_table\_shards

replicate\_table\_shards() 関数は、指定されたテーブルのレプリケートが不十分なシャードをレプリケートします。 この関数では、レプリケーションのためにフェッチできる、レプリケートが不十分なシャードと場所の一覧が最初に計算されます。 その後、関数はこれらのシャードをコピーし、対応するシャード メタデータを更新してコピーを反映させます。

#### <a name="arguments"></a>引数

**table\_name:** シャードをレプリケートする必要があるテーブルの名前。

**shard\_replication\_factor:** (省略可能) 各シャードで達成する必要のあるレプリケーション係数。

**max\_shard\_copies:** (省略可能) 目的のレプリケーション係数に達するまでにコピーするシャードの最大数。

**excluded\_shard\_list:** (省略可能) レプリケーション操作中にコピーしないシャードの識別子。

#### <a name="return-value"></a>戻り値

N/A

#### <a name="examples"></a>使用例

次の例では、github\_events テーブルのシャードを shard\_replication\_factor までレプリケートしようとしています。

```postgresql
SELECT replicate_table_shards('github_events');
```

こちらの例では、最大 10 個のシャード コピーにより、github\_events テーブルのシャードを目的のレプリケーション係数に到達させようとしています。 リバランサーは、目的のレプリケーション係数に到達させるために、その試行で最大 10 個のシャードをコピーします。

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>isolate\_tenant\_to\_new\_shard

この関数を使用すると、ディストリビューション列に特定の単一の値を持つ行を保持する新しいシャードが作成されます。 これは、マルチテナントの Hyperscale (Citus) ユース ケースで特に便利です。この場合、大規模なテナントを独自のシャード上に、そして最終的には独自の物理ノード上に単体で配置できます。

#### <a name="arguments"></a>引数

**table\_name:** 新しいシャードを取得するテーブルの名前。

**tenant\_id:** 新しいシャードに割り当てられるディストリビューション列の値。

**cascade\_option:** (省略可能) \"CASCADE\" に設定すると、現在のテーブルの [コロケーション グループ](concepts-hyperscale-colocation.md)内のすべてのテーブルからもシャードが分離されます。

#### <a name="return-value"></a>戻り値

**shard\_id:** この関数は、新しく作成されたシャードに割り当てられた一意の ID を返します。

#### <a name="examples"></a>例

テナント 135 の lineitems を保持する新しいシャードを作成します。

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>次の手順

* この記事の関数の多くは、システム [メタデータ テーブル](reference-hyperscale-metadata.md)を変更します
* [サーバー パラメーター](reference-hyperscale-parameters.md)により、一部の関数の動作をカスタマイズします
