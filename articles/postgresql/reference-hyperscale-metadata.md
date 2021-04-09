---
title: システム テーブル – Hyperscale (Citus) - Azure Database for PostgreSQL
description: 分散クエリの実行のためのメタデータ
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 74403365fe48584fa5d1db0e349c9dfc3772d874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652856"
---
# <a name="system-tables-and-views"></a>システム テーブルとビュー

Hyperscale (Citus) では、サーバー グループ内の分散データに関する情報を含む特殊なテーブルが作成および管理されます。 コーディネーター ノードは、ワーカー ノードにまたがるクエリを実行する方法を計画するときに、これらのテーブルを調べます。

## <a name="coordinator-metadata"></a>コーディネーター メタデータ

Hyperscale (Citus) では、ディストリビューション列に基づいて、各分散テーブルが複数の論理的なシャードに分割されます。 コーディネーターはその後、これらのシャードの正常性と場所に関する統計や情報を追跡するためにメタデータ テーブルを管理します。

このセクションでは、これらの各メタデータ テーブルとそのスキーマについて説明します。
これらのテーブルは、コーディネーター ノードにログインした後、SQL を使用して表示したり、クエリを実行したりできます。

> [!NOTE]
>
> 以前のバージョンの Citus Engine を実行している Hyperscale (Citus) サーバー グループによって提供されるテーブルは、この後で示される一覧に、すべてが表示されているわけではない場合もあります。

### <a name="partition-table"></a>パーティション テーブル

pg\_dist\_partition テーブルには、データベース内のどのテーブルが分散されているかに関するメタデータが格納されます。 また、分散テーブルごとに、分散方法に関する情報とディストリビューション列に関する詳細情報も格納されます。

| Name         | Type     | Description                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclass | この行が対応する分散テーブル。 この値は、pg_class システム カタログ テーブル内の relfilenode 列を参照します。                                                                                                                   |
| partmethod   | char     | パーティション分割/分散に使用される方法。 さまざまな分散方法に対応するこの列の値は次のとおりです。追加: 'a'、ハッシュ: 'h'、参照テーブル: 'n'                                                                          |
| partkey      | text     | ディストリビューション列に関する詳細情報 (列番号、種類、その他の関連情報など)。                                                                                                                                      |
| colocationid | 整数 (integer)  | このテーブルが属しているコロケーション グループ。 同じグループ内のテーブルでは、他の最適化の間での併置結合や分散ロールアップが許可されます。 この値は、pg_dist_colocation テーブル内の colocationid 列を参照します。                      |
| repmodel     | char     | データ レプリケーションに使用される方法。 さまざまなレプリケーション方法に対応するこの列の値は次のとおりです。Citus ステートメント ベースのレプリケーション: 'c'、PostgreSQL ストリーミング レプリケーション: 's'、2 フェーズ コミット (参照テーブルの場合): 't' |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>シャード テーブル

pg\_dist\_shard テーブルには、テーブルの個々のシャードに関するメタデータが格納されます。 pg_dist_shard には、シャードがどの分散テーブルに属しているかに関する情報と、シャードのディストリビューション列に関する統計が含まれています。
追加分散テーブルの場合、これらの統計は、ディストリビューション列の最小/最大値に対応します。 ハッシュ分散テーブルの場合、これらは、そのシャードに割り当てられているハッシュ トークンの範囲です。 これらの統計は、SELECT クエリ中に関連のないシャードを排除するために使用されます。

| Name          | Type     | Description                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclass | この行が対応する分散テーブル。 この値は、pg_class システム カタログ テーブル内の relfilenode 列を参照します。                                                          |
| shardid       | bigint   | このシャードに割り当てられているグローバル一意識別子。                                                                                                                                           |
| shardstorage  | char     | このシャードに使用されるストレージの種類。 さまざまなストレージの種類が後の表で説明されています。                                                                                               |
| shardminvalue | text     | 追加分散テーブルの場合は、このシャード内のディストリビューション列の最小値 (包含)。 ハッシュ分散テーブルの場合は、そのシャードに割り当てられている最小のハッシュ トークン値 (包含)。 |
| shardmaxvalue | text     | 追加分散テーブルの場合は、このシャード内のディストリビューション列の最大値 (包含)。 ハッシュ分散テーブルの場合は、そのシャードに割り当てられている最大のハッシュ トークン値 (包含)。 |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>シャード ストレージの種類

pg\_dist\_shard 内の shardstorage 列は、このシャードに使用されるストレージの種類を示します。 さまざまなシャード ストレージの種類とその表現の概要を次に示します。

| ストレージ型 | shardstorage の値 | Description                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| TABLE        | 't'                | シャードに通常の分散テーブルに属するデータが格納されることを示します。         |
| COLUMNAR     | 'c'                | シャードに列指向データが格納されることを示します。 (分散 cstore_fdw テーブルによって使用されます) |
| FOREIGN      | 'f'                | シャードに外部データが格納されることを示します。 (分散 file_fdw テーブルによって使用されます)    |

### <a name="shard-placement-table"></a>シャード配置テーブル

pg\_dist\_placement テーブルは、ワーカー ノード上のシャード レプリカの場所を追跡します。 特定のノードに割り当てられているシャードの各レプリカは、シャード配置と呼ばれます。 このテーブルには、各シャード配置の正常性と場所に関する情報が格納されます。

| Name        | Type   | Description                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| shardid     | bigint | この配置に関連付けられているシャード識別子。 この値は、pg_dist_shard カタログ テーブル内の shardid 列を参照します。             |
| shardstate  | INT    | この配置の状態を示します。 さまざまなシャードの状態が後のセクションで説明されています。                                         |
| shardlength | bigint | 追加分散テーブルの場合は、ワーカー ノード上のシャード配置のサイズ (バイト単位)。 ハッシュ分散テーブルの場合は 0。            |
| placementid | bigint | 個々の配置の一意の自動生成された識別子。                                                                           |
| groupid     | INT    | ストリーミング レプリケーション モデルが使用されている場合、1 つのプライマリ サーバーと 0 以上のセカンダリ サーバーのグループを示します。 |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>シャード配置の状態

Hyperscale (Citus) では、配置ごとにシャードの正常性が管理されます。 ある配置のためにシステムが整合性のない状態になった場合、Citus は自動的にその配置を使用不可とマークします。 配置の状態は、pg_dist_shard_placement テーブルの shardstate 列内に記録されます。 さまざまなシャード配置の状態の概要を次に示します。

| 状態名 | shardstate の値 | Description                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| FINALIZED  | 1                | 新しいシャードが作成されたときの状態。 この状態のシャード配置は最新と見なされ、クエリの計画や実行で使用されます。                                                                                                                                                                                                                                                                                 |
| INACTIVE   | 3                | この状態のシャード配置は、同じシャードの他のレプリカと同期されていないため、非アクティブと見なされます。 この状態は、この配置に対する追加、変更 (INSERT、UPDATE、DELETE)、または DDL 操作が失敗したときに発生する場合があります。 クエリ プランナーは、計画および実行中にこの状態の配置を無視します。 ユーザーは、バックグラウンド アクティビティとして、これらのシャード内のデータを終了処理されたレプリカと同期できます。 |
| TO_DELETE  | 4                | Citus が master_apply_delete_command の呼び出しに応答してシャード配置を削除しようとして失敗した場合は、その配置がこの状態に移動されます。 ユーザーはその後、以降のバックグラウンド アクティビティとしてこれらのシャードを削除できます。                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>ワーカー ノード テーブル

pg\_dist\_node テーブルには、クラスター内のワーカー ノードに関する情報が含まれています。

| Name             | Type    | Description                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nodeid           | INT     | 個々のノードの自動生成された識別子。                                                                                                                                          |
| groupid          | INT     | ストリーミング レプリケーション モデルが使用されている場合、1 つのプライマリ サーバーと 0 以上のセカンダリ サーバーのグループを示すために使用される識別子。 既定では、nodeid と同じです。         |
| nodename         | text    | PostgreSQL ワーカー ノードのホスト名または IP アドレス。                                                                                                                                     |
| nodeport         | INT     | PostgreSQL ワーカー ノードがリッスンしているポート番号。                                                                                                                              |
| noderack         | text    | (省略可能) ワーカー ノードのラック配置の情報。                                                                                                                                 |
| hasmetadata      | boolean | 内部使用のために予約されています。                                                                                                                                                                 |
| isactive         | boolean | ノードがアクティブでシャード配置を受け付けているかどうか。                                                                                                                                     |
| noderole         | text    | ノードがプライマリまたはセカンダリのどちらであるか。                                                                                                                                                 |
| nodecluster      | text    | このノードが含まれているクラスターの名前。                                                                                                                                               |
| shouldhaveshards | boolean | false の場合は、既にそこに存在するシャードと併置されていない限り、シャードは再調整時にノードから移動 (ドレイン) され、新しい分散テーブルのシャードがそのノードに配置されることはありません。 |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>分散オブジェクト テーブル

citus.pg\_dist\_object テーブルには、コーディネーター ノード上で作成され、ワーカー ノードに伝達された型や関数などのオブジェクトの一覧が含まれています。 管理者がクラスターに新しいワーカー ノードを追加すると、Hyperscale (Citus) は、自動的にその新しいノード上で (オブジェクトの依存関係を満たす正しい順序で) 分散オブジェクトのコピーを作成します。

| Name                        | Type    | Description                                          |
|-----------------------------|---------|------------------------------------------------------|
| classid                     | oid     | 分散オブジェクトのクラス                      |
| objid                       | oid     | 分散オブジェクトのオブジェクト ID                  |
| objsubid                    | 整数 (integer) | 分散オブジェクトのオブジェクト サブ ID (attnum など) |
| 型                        | text    | pg のアップグレード中に使用される安定したアドレスの一部   |
| object_names                | text[]  | pg のアップグレード中に使用される安定したアドレスの一部   |
| object_args                 | text[]  | pg のアップグレード中に使用される安定したアドレスの一部   |
| distribution_argument_index | 整数 (integer) | 分散関数/プロシージャでのみ有効      |
| colocationid                | 整数 (integer) | 分散関数/プロシージャでのみ有効      |

\"安定したアドレス\"は、特定のサーバーとは独立に、オブジェクトを一意に識別します。 Hyperscale (Citus) では、[pg\_identify\_object\_as\_address()](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE) 関数で作成された安定したアドレスを使用して、PostgreSQL のアップグレード中にオブジェクトが追跡されます。

`create_distributed_function()` によって `citus.pg_dist_object` テーブルにエントリがどのように追加されるかの例を次に示します。

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>コロケーション グループ テーブル

pg\_dist\_colocation テーブルには、どのテーブルのシャードがまとめて配置 (つまり[併置](concepts-hyperscale-colocation.md)) されるかに関する情報が含まれています。
同じコロケーション グループ内に 2 つのテーブルが存在する場合、Hyperscale (Citus) では確実に、同じパーティション値を持つシャードが同じワーカー ノードに配置されます。
コロケーションでは、結合の最適化、特定の分散ロールアップ、外部キーのサポートが有効になります。 シャードのコロケーションは、2 つのテーブル間でシャード数、レプリケーション係数、パーティション列の種類がすべて一致している場合に推定されます。ただし、必要に応じて、分散テーブルの作成時にカスタム コロケーション グループを指定できます。

| Name                   | Type | Description                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| colocationid           | INT  | この行が対応するコロケーション グループの一意識別子。          |
| shardcount             | INT  | このコロケーション グループ内のすべてのテーブルのシャード数。                          |
| replicationfactor      | INT  | このコロケーション グループ内のすべてのテーブルのレプリケーション係数。                  |
| distributioncolumntype | oid  | このコロケーション グループ内のすべてのテーブルのディストリビューション列の種類。 |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>リバランサー戦略テーブル

このテーブルによって、[rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) がシャードをどこに移動するかを決定するために使用できる戦略が定義されます。

| Name                           | Type    | Description                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | boolean | rebalance_table_shards should が既定でこの戦略を選択するかどうか。 この列を更新するには、citus_set_default_rebalance_strategy を使用します。             |
| shard_cost_function            | regproc | shardid を bigint として受け取り、そのコストの概念を実数型として返す必要があるコスト関数の識別子。                                |
| node_capacity_function         | regproc | nodeid を int として受け取り、そのノード容量の概念を実数型として返す必要がある容量関数の識別子。                          |
| shard_allowed_on_node_function | regproc | shardid bigint と nodeidarg int が与えられ、Citus がシャードをノードに格納できるかどうかを示すブール値を返す関数の識別子。 |
| default_threshold              | float4  | ノードをいっぱい、または空いていると見なすためのしきい値。これにより、rebalance_table_shards がいつシャードを移動しようとするかが決定されます。                    |
| minimum_threshold              | float4  | rebalance_table_shards() のしきい値引数が低すぎる値に設定されないようにするための保護機能。                                                  |

Hyperscale (Citus) のインストールでは、テーブル内に次の戦略が付属しています。

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

既定の戦略 (`by_shard_count`) では、すべてのシャードに同じコストが割り当てられます。 その効果として、ノードにまたがってシャード数が均等になります。 もう一方の定義済みの戦略 (`by_disk_size`) では、各シャードに、そのディスク サイズ (バイト数) と、それと併置されているシャードのディスク サイズを加えた値に一致するコストが割り当てられます。 ディスク サイズは `pg_total_relation_size` を使用して計算されるため、インデックスが含まれます。 この戦略では、すべてのノード上で同じディスク領域を実現しようとします。 0\.1 のしきい値に注意してください。これにより、ディスク領域の意味のない違いによって発生する不必要なシャードの移動が防止されます。

#### <a name="creating-custom-rebalancer-strategies"></a>カスタム リバランサー戦略の作成

新しいシャード リバランサー戦略内で使用し、[citus_add_rebalance_strategy](reference-hyperscale-functions.md#citus_add_rebalance_strategy) 関数で [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) に登録できる関数の例を次に示します。

-   ホスト名のパターンによるノード容量の例外の設定:

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) で測定される、シャードにアクセスするクエリの数による再調整:

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   ノード (アドレス \'10.0.0.1\') 上での特定のシャード (10000) の分離:

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>クエリ統計テーブル

Hyperscale (Citus) では、クエリがどのように、だれのために実行されるかに関する統計のための `citus_stat_statements` が提供されます。 これは、クエリ速度に関する統計を追跡する PostgreSQL の [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) ビューに似ています (これとの結合も可能です)。

このビューでは、クエリをマルチテナント アプリケーション内の発信元のテナントまでトレースできます。これは、テナントの分離をいつ行うかの決定に役立ちます。

| Name          | Type   | Description                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| queryid       | bigint | 識別子 (pg_stat_statements の結合に適しています)                                   |
| userid        | oid    | クエリを実行したユーザー                                                           |
| dbid          | oid    | コーディネーターのデータベース インスタンス                                                 |
| query         | text   | 匿名化されたクエリ文字列                                                          |
| executor      | text   | 使用される Citus エグゼキューター: アダプティブ、リアルタイム、タスク トラッカー、ルーター、または挿入-選択 |
| partition_key | text   | ルーターで実行されたクエリ内のディストリビューション列の値、それ以外は NULL               |
| calls         | bigint | クエリが実行された回数                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

結果:

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

注意事項:

-   統計データはレプリケートされず、データベースのクラッシュやフェールオーバーが発生すると失われます。
-   `pg_stat_statements.max` GUC (既定値は 5000) で設定される限られた数のクエリを追跡します。
-   テーブルを切り詰めるには、`citus_stat_statements_reset()` 関数を使用します。

### <a name="distributed-query-activity"></a>分散クエリのアクティビティ

Hyperscale (Citus) では、分散クエリの結果を構築するために内部的に使用されるシャード固有のクエリなど、クラスター全体のクエリやロックを監視するための特殊なビューが提供されます。

-   **citus\_dist\_stat\_activity**: すべてのノード上で実行されている分散クエリを表示します。 `pg_stat_activity` のスーパーセットであり、後者が使用可能なすべての場所で使用できます。
-   **citus\_worker\_stat\_activity**: ワーカー上のクエリ (個々のシャードに対するフラグメント クエリを含む) を表示します。
-   **citus\_lock\_waits**: クラスター全体のブロックされたクエリ。

最初の 2 つのビューには、[pg\_stat\_activity](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW) のすべての列に加え、クエリを開始したワーカーのホスト/ポートと、クラスターのコーディネーター ノードのホスト/ポートが含まれています。

たとえば、分散テーブル内の行をカウントする場合を考えてみます。

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

このクエリが `citus_dist_stat_activity` に表示されることを確認できます。

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

このクエリには、すべてのシャードの情報が必要です。 一部の情報は、`localhost:9700` に格納されているシャード `users_table_102038` に存在します。 `citus_worker_stat_activity` ビューを調べることにより、このシャードにアクセスしているクエリを確認できます。

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

`query` フィールドは、カウントされるシャードからデータがコピーされていることを示しています。

> [!NOTE]
> ルーター クエリ (マルチテナント アプリケーション内のシングルテナントなど、`SELECT
> * FROM table WHERE tenant_id = X`) がトランザクション ブロックなしで実行された場合、citus\_worker\_stat\_activity の master\_query\_host\_name および master\_query\_host\_port 列は NULL になります。

`citus_lock_waits` がどのように動作するかを確認するために、ロックの状況を手動で生成できます。 まず、コーディネーターからテスト テーブルを設定します。

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

次に、コーディネーターで 2 つのセッションを使用して、次の一連のステートメントを実行できます。

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

その状況が `citus_lock_waits` ビューに表示されます。

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

この例では、クエリがコーディネーターで発信されましたが、このビューには (たとえば、Hyperscale (Citus) MX で実行されている) ワーカーで発信されたクエリ間のロックも一覧表示できます。

## <a name="next-steps"></a>次の手順

* 一部の [Hyperscale (Citus) 関数](reference-hyperscale-functions.md)によるシステム テーブルの変更方法を学習する
* [ノードとテーブル](concepts-hyperscale-nodes.md)の概念を確認する
