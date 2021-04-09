---
title: サーバー パラメーター - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Hyperscale (Citus) SQL API のパラメーター
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 07f966c7b0be542f848f1a0a4eaf2b5549735b4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91336243"
---
# <a name="server-parameters"></a>サーバー パラメーター

Hyperscale (Citus) の動作に影響を与えるサーバー パラメーターにはさまざまなものがあります (標準の PostgreSQL と Hyperscale (Citus) に固有の両方)。
これらのパラメーターは、Azure portal で Hyperscale (Citus) サーバー グループに対して設定できます。 **[設定]** カテゴリで、 **[ワーカー ノードのパラメーター]** または **[コーディネーター ノードのパラメーター]** を選択します。 これらのページでは、すべてのワーカー ノードの、またはコーディネーター ノードだけの、パラメーターを設定できます。

## <a name="hyperscale-citus-parameters"></a>Hyperscale (Citus) のパラメーター

> [!NOTE]
>
> 以前のバージョンの Citus Engine を実行している Hyperscale (Citus) サーバー グループでは、以下に示すパラメーターの一部が提供されない可能性があります。

### <a name="general-configuration"></a>全般構成

#### <a name="citususe_secondary_nodes-enum"></a>citus.use\_secondary\_nodes (列挙型)

SELECT クエリのノードを選択するときに使用するポリシーを設定します。 これが 'always' に設定されている場合、プランナーは、[pg_dist_node](reference-hyperscale-metadata.md#worker-node-table) で 'secondary' noderole とマークされているノードにのみクエリを実行します。

この列挙型でサポートされている値は次のとおりです。

-   **never:** (既定) すべての読み取りがプライマリ ノード上で実行されます。
-   **always:** 代わりにセカンダリ ノードに対して読み取りが実行され、挿入/更新ステートメントは無効になります。

#### <a name="cituscluster_name-text"></a>citus.cluster\_name (テキスト)

コーディネーター ノード プランナーに、どのクラスターを調整するかを通知します。 cluster\_name が設定されていると、プランナーは、そのクラスター内のワーカー ノードにのみクエリを実行します。

#### <a name="citusenable_version_checks-boolean"></a>citus.enable\_version\_checks (ブール値)

Hyperscale (Citus) のバージョンをアップグレードするには、サーバーの再起動 (新しい共有ライブラリを選択するため) と、その後の ALTER EXTENSION UPDATE コマンドが必要です。  この両方の手順を実行しないと、エラーまたはクラッシュが発生する可能性があります。
そのため、Hyperscale (Citus) ではコードと拡張機能のバージョンが一致することを検証し、一致しない場合はエラーが発生します。

この値は既定で true であり、コーディネーター上で有効です。 まれなケースとして、複雑なアップグレード プロセスではこのパラメーターを false に設定する必要があり、そのためこのチェックを無効にする場合があります。

#### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus.log\_distributed\_deadlock\_detection (ブール値)

分散デッドロックの検出に関連した処理をサーバー ログに記録するかどうか。 この既定値は false です。

#### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus.distributed\_deadlock\_detection\_factor (浮動小数点)

分散デッドロックを確認するまでの待ち時間を設定します。 特に、待ち時間は、この値に PostgreSQL の [deadlock\_timeout](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) 設定を掛けた値になります。 既定値は `2` です。 `-1` の値を指定すると、分散デッドロックの検出が無効になります。

#### <a name="citusnode_connection_timeout-integer"></a>citus.node\_connection\_timeout (整数)

`citus.node_connection_timeout` GUC は、接続の確立を待つ最大期間 (ミリ秒単位) を設定します。 Hyperscale (Citus) では、少なくとも 1 つのワーカー接続が確立される前にタイムアウトが経過した場合にエラーが発生します。 この GUC は、コーディネーターからワーカーへの接続と、ワーカー相互の接続に影響を与えます。

-   既定値: 5 秒
-   最小:10 ミリ秒
-   最大: 1 時間

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

### <a name="query-statistics"></a>クエリ統計

#### <a name="citusstat_statements_purge_interval-integer"></a>citus.stat\_statements\_purge\_interval (整数)

メンテナンス デーモンが `pg_stat_statements` で不一致のレコードを [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) から削除する頻度を設定します。 この構成値は、削除間の時間間隔 (秒単位) を設定し、既定値は 10 です。 0 の値を指定すると、削除が無効になります。

```psql
SET citus.stat_statements_purge_interval TO 5;
```

このパラメーターはコーディネーター上で有効であり、実行時に変更できます。

### <a name="data-loading"></a>データの読み込み

#### <a name="citusmulti_shard_commit_protocol-enum"></a>citus.multi\_shard\_commit\_protocol (列挙型)

ハッシュ分散テーブルで COPY を実行するときに使用するコミット プロトコルを設定します。 個々のシャード配置では、COPY 中にエラーが発生した場合はデータが取り込まれないようにするために、この COPY はトランザクション ブロックで実行されます。 ただし、COPY はすべての配置で成功するが、すべてのトランザクションがコミットする前に (ハードウェア) 障害が発生する特定のエラー ケースが存在します。 このパラメーターを使用すると、次のコミット プロトコルから選択することによって、そのケースでのデータ損失を防止できます。

-   **2pc:** (既定) シャード配置で COPY が実行されるトランザクションが最初に PostgreSQL の [2 フェーズ コミット](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html)を使用して準備され、次にコミットされます。 失敗したコミットは、COMMIT PREPARED または ROLLBACK PREPARED を使用して、それぞれ手動で回復または中止できます。
    2pc を使用する場合は、すべてのワーカー上で [max\_prepared\_transactions](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) を (通常は max\_connections と同じ値に) 増やす必要があります。
-   **1pc:** シャード配置で COPY が実行されるトランザクションが 1 回でコミットされます。 COPY がすべての配置で成功した後にコミットが失敗した場合は、データが失われる可能性があります (まれなケースです)。

#### <a name="citusshard_replication_factor-integer"></a>citus.shard\_replication\_factor (整数)

シャードのレプリケーション係数、つまり、シャードが配置されるノードの数を設定し、既定値は 1 です。 このパラメーターは実行時に設定でき、コーディネーター上で有効です。 このパラメーターの理想的な値は、クラスターのサイズやノード障害の頻度によって異なります。  たとえば、大規模なクラスターを実行し、ノード障害をより頻繁に観察する場合は、このレプリケーション係数を増やすこともできます。

#### <a name="citusshard_count-integer"></a>citus.shard\_count (整数)

ハッシュ パーティション テーブルのシャード数を設定し、既定値は 32 です。  この値は、ハッシュ パーティション テーブルを作成するときに [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF によって使用されます。 このパラメーターは実行時に設定でき、コーディネーター上で有効です。

#### <a name="citusshard_max_size-integer"></a>citus.shard\_max\_size (整数)

シャードが分割されるまでに達する最大サイズを設定し、既定値は 1 GB です。 1 つのシャードのソース ファイルのサイズ (これはステージングに使用されます) がこの構成値を超えた場合、データベースでは常に新しいシャードが作成されます。 このパラメーターは実行時に設定でき、コーディネーター上で有効です。

### <a name="planner-configuration"></a>プランナーの構成

#### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus.limit\_clause\_row\_fetch\_count (整数)

LIMIT 句の最適化のためにタスクごとにフェッチする行数を設定します。
場合によっては、LIMIT 句を含む SELECT クエリが、結果を生成するために各タスクからすべての行をフェッチする必要があることがあります。 これらの場合で、かつ近似によって意味のある結果が生成されるとき、この構成値は各シャードからフェッチする行数を設定します。 制限の近似は既定では無効であり、このパラメーターは -1 に設定されています。 この値は実行時に設定でき、コーディネーター上で有効です。

#### <a name="cituscount_distinct_error_rate-floating-point"></a>citus.count\_distinct\_error\_rate (浮動小数点)

Hyperscale (Citus) では、postgresql-hll 拡張機能を使用して count(distinct) の概数を計算できます。 この構成エントリは、count(distinct) を計算するときの望ましいエラー率を設定します。 既定値である 0.0 によって count(distinct) の近似が無効になり、1.0 では結果の精度に関する保証が得られません。 最適な結果を得るために、このパラメーターを 0.005 に設定することをお勧めします。 この値は実行時に設定でき、コーディネーター上で有効です。

#### <a name="citustask_assignment_policy-enum"></a>citus.task\_assignment\_policy (列挙型)

> [!NOTE]
> この GUC は、[shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) が 1 を超えている場合か、または [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables) に対するクエリにのみ適用されます。

ワーカーにタスクを割り当てるときに使用するポリシーを設定します。 コーディネーターは、シャードの場所に基づいてワーカーにタスクを割り当てます。 この構成値は、これらの割り当てを行うときに使用するポリシーを指定します。
現在、使用できるタスク割り当てポリシーには次の 3 つがあります。

-   **greedy:** 最長一致ポリシーは既定値であり、ワーカーにまたがってタスクを均等に分散させることを目的にしています。
-   **round-robin:** ラウンドロビン ポリシーは、異なるレプリカを交互に使用するラウンドロビン方式でワーカーにタスクを割り当てます。 このポリシーでは、ワーカーの数に比べてテーブルのシャード数が少ない場合にクラスター使用率が向上します。
-   **first-replica:** 最初のレプリカ ポリシーは、シャードの配置 (レプリカ) の挿入順序に基づいてタスクを割り当てます。 つまり、シャードのフラグメント クエリは、そのシャードの最初のレプリカを含むワーカーに割り当てられます。
    この方法では、どのシャードがどのノード上で使用されるかに関する強力な保証 (つまり、より強力なメモリ常駐の保証) が得られます。

このパラメーターは実行時に設定でき、コーディネーター上で有効です。

### <a name="intermediate-data-transfer"></a>中間データの転送

#### <a name="citusbinary_worker_copy_format-boolean"></a>citus.binary\_worker\_copy\_format (ブール値)

ワーカー間で中間データを転送するためにバイナリ コピー形式を使用します。
大規模なテーブルの結合中、Hyperscale (Citus) では動的に再パーティション分割し、異なるワーカー間でデータをシャッフルすることが必要になる場合があります。 既定では、このデータはテキスト形式で転送されます。 このパラメーターを有効にすると、データベースは PostgreSQL のバイナリ シリアル化形式を使用してこのデータを転送するよう指示されます。 このパラメーターはワーカー上で有効であり、postgresql.conf ファイルで変更する必要があります。 この構成ファイルを編集した後、ユーザーは SIGHUP シグナルを送信するか、またはサーバーを再起動してこの変更を有効にすることができます。

#### <a name="citusbinary_master_copy_format-boolean"></a>citus.binary\_master\_copy\_format (ブール値)

コーディネーターとワーカーの間でデータを転送するためにバイナリ コピー形式を使用します。 分散クエリを実行している場合、ワーカーは、その中間結果を最終的な集計のためにコーディネーターに転送します。 既定では、このデータはテキスト形式で転送されます。 このパラメーターを有効にすると、データベースは PostgreSQL のバイナリ シリアル化形式を使用してこのデータを転送するよう指示されます。
このパラメーターは実行時に設定でき、コーディネーター上で有効です。

#### <a name="citusmax_intermediate_result_size-integer"></a>citus.max\_intermediate\_result\_size (整数)

実行のためや、複雑なサブクエリのためにワーカー ノードにプッシュダウンできない CTE に対する中間結果の最大サイズ (KB 単位)。 既定値は 1 GB であり、-1 の値は制限がないことを示します。
制限を超えるクエリは取り消され、エラー メッセージが生成されます。

### <a name="ddl"></a>DDL (DDL)

#### <a name="citusenable_ddl_propagation-boolean"></a>citus.enable\_ddl\_propagation (ブール値)

DDL の変更をコーディネーターからすべてのワーカーに自動的に伝達するかどうかを指定します。 既定値は、true です。 一部のスキーマ変更ではテーブルに対するアクセスの排他的ロックが必要であるため、また自動伝達はすべてのワーカーに順番に適用されるため、Hyperscale (Citus) クラスターの応答性が一時的に低下する場合があります。 この設定を無効にして、変更を手動で伝達することを選択できます。

### <a name="executor-configuration"></a>エグゼキューターの構成

#### <a name="general"></a>全般

##### <a name="citusall_modifications_commutative"></a>citus.all\_modifications\_commutative

Hyperscale (Citus) では、動作の正確性を保証するために、可換法則が適用され、変更操作のための適切なロックが取得されます。 たとえば、INSERT ステートメントが別の INSERT ステートメントと可換であるが、UPDATE または DELETE ステートメントとは可換でないことを前提としています。 同様に、UPDATE または DELETE ステートメントが別の UPDATE または DELETE ステートメントとは可換でないことを前提としています。 この予防措置は、UPDATE や DELETE では Hyperscale (Citus) がより強力なロックを取得する必要があることを示しています。

INSERT や他の UPDATE と可換である UPDATE ステートメントがある場合は、このパラメーターを true に設定して、これらの可換性の前提を緩和できます。 このパラメーターが true に設定されている場合は、すべてのコマンドが可換であると見なされ、共有ロックを要求するため、全体的なスループットが向上する可能性があります。 このパラメーターは実行時に設定でき、コーディネーター上で有効です。

##### <a name="citusremote_task_check_interval-integer"></a>citus.remote\_task\_check\_interval (整数)

Hyperscale (Citus) が、タスク トラッカー エグゼキューターによって管理されるジョブの状態を確認する頻度を設定します。 この既定値は 10 ms です。 コーディネーターはワーカーにタスクを割り当てた後、各タスクの進行状況を定期的に確認します。 この構成値は、2 つの結果確認の間の時間間隔を設定します。 このパラメーターはコーディネーター上で有効であり、実行時に設定できます。

##### <a name="citustask_executor_type-enum"></a>citus.task\_executor\_type (列挙型)

Hyperscale (Citus) には、分散 SELECT クエリを実行するための 3 つの種類のエグゼキューターがあります。  この構成パラメーターを設定することによって、目的のエグゼキューターを選択できます。 このパラメーターで許容される値は次のとおりです。

-   **adaptive:** 既定値。 これは、複数のシャードにまたがる集計や併置結合が必要なクエリへの高速な応答のために最適です。
-   **task-tracker:** タスク トラッカー エグゼキューターは、ワーカー ノードにまたがるデータのシャッフルや効率的なリソース管理を必要とする実行時間の長い、複雑なクエリに最適です。
-   **real-time:** (非推奨) アダプティブ エグゼキューターと同様の目的に対応しますが、柔軟性が低く、ワーカー ノードへの接続の負荷は大きくなる可能性があります。

このパラメーターは実行時に設定でき、コーディネーター上で有効です。

##### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus.multi\_task\_query\_log\_level (列挙型) {#multi_task_logging}

複数のタスクを生成する (つまり、複数のシャードに達する) すべてのクエリのログ レベルを設定します。 このようなクエリに対してエラーまたは警告を選択してそれらを見つけ、tenant\_id フィルターを追加できるため、ログ記録はマルチテナント アプリケーションの移行中に有効です。 このパラメーターは実行時に設定でき、コーディネーター上で有効です。 このパラメーターの既定値は \'[オフ]\' です。

この列挙型でサポートされている値は次のとおりです。

-   **off:** 複数のタスクを生成する (つまり、複数のシャードにまたがる) すべてのクエリのログ記録を無効にします。
-   **debug:** DEBUG 重大度レベルでステートメントをログに記録します。
-   **log:** LOG 重大度レベルでステートメントをログに記録します。 ログ行には、実行された SQL クエリが含まれます。
-   **notice:** NOTICE 重大度レベルでステートメントをログに記録します。
-   **warning:** WARNING 重大度レベルでステートメントをログに記録します。
-   **error:** ERROR 重大度レベルでステートメントをログに記録します。

開発テスト中に `error` を使用し、実際の運用環境デプロイ中に `log` などのより低いログ レベルを使用すると有効な場合があります。
`log` を選択すると、データベース ログには複数タスク クエリが表示され、そのクエリ自体は \"STATEMENT\" の後に表示されます。

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

##### <a name="citusenable_repartition_joins-boolean"></a>citus.enable\_repartition\_joins (ブール値)

通常、アダプティブ エグゼキューターで再パーティション分割の結合を実行しようとすると、エラー メッセージで失敗します。  ただし、`citus.enable_repartition_joins` を true に設定すると、Hyperscale (Citus) はその結合をタスク トラッカー エグゼキューターで実行するように一時的に切り替えることができます。  既定値は false です。

#### <a name="task-tracker-executor-configuration"></a>タスク トラッカー エグゼキューターの構成

##### <a name="citustask_tracker_delay-integer"></a>citus.task\_tracker\_delay (整数)

このパラメーターは、タスク管理ラウンド間のタスク トラッカーのスリープ時間を設定し、既定値は 200 ms です。 タスク トラッカー プロセスは定期的にスリープ解除し、割り当てられているすべてのタスクを確認した後、これらのタスクをスケジュールして実行します。  その後、タスク トラッカーはこれらのタスクを再び確認する前に、一定の期間スリープ状態になります。
この構成値によって、そのスリープ期間の長さが決定されます。 このパラメーターはワーカー上で有効であり、postgresql.conf ファイルで変更する必要があります。 この構成ファイルを編集した後、ユーザーは SIGHUP シグナルを送信するか、またはサーバーを再起動してこの変更を有効にすることができます。

このパラメーターを小さくすると、管理ラウンド間の時間間隔が減るため、タスク トラッカー エグゼキューターによって発生する遅延を減少させることができます。
遅延の減少はシャード クエリが短い場合に有効であるため、それらの状態を定期的に更新します。

##### <a name="citusmax_assign_task_batch_size-integer"></a>citus.max\_assign\_task\_batch\_size (整数)

コーディネーター上のタスク トラッカー エグゼキューターは、タスクをバッチでワーカー上のデーモンに同期的に割り当てます。 このパラメーターは、1 つのバッチで割り当てるタスクの最大数を設定します。 より大きなバッチ サイズを選択すると、タスクの割り当てが高速になります。 ただし、ワーカーの数が多いと、すべてのワーカーにタスクを割り当てるまでの時間が長くなる可能性があります。  このパラメーターは実行時に設定でき、コーディネーター上で有効です。

##### <a name="citusmax_running_tasks_per_node-integer"></a>citus.max\_running\_task\_per\_node (整数)

タスク トラッカー プロセスは、必要に応じて、割り当てられているタスクをスケジュールして実行します。 この構成値は、特定の時点で 1 つのノード上で同時に実行するタスクの最大数を設定し、既定値は 8 です。

この制限により、多数のタスクが同時にディスクにアクセスすることはなくなるため、ディスク I/O の競合を回避するために役立ちます。 クエリがメモリまたは SSD から処理されている場合は、あまり気にせず max\_running\_task\_per\_node を増やすことができます。

##### <a name="cituspartition_buffer_size-integer"></a>citus.partition\_buffer\_size (整数)

パーティション操作に使用するバッファー サイズを設定し、既定値は 8 MB です。
Hyperscale (Citus) では、2 つの大規模なテーブルが結合される場合、テーブル データを複数のファイルに再パーティション分割できます。 このパーティション バッファーがいっぱいになると、再パーティション分割されたデータはディスク上のファイルにフラッシュされます。  この構成エントリは実行時に設定でき、ワーカー上で有効です。

#### <a name="explain-output"></a>EXPLAIN 出力

##### <a name="citusexplain_all_tasks-boolean"></a>citus.explain\_all\_tasks (ブール値)

既定では、分散クエリで [EXPLAIN](http://www.postgresql.org/docs/current/static/sql-explain.html) を実行している場合、Hyperscale (Citus) には 1 つの任意のタスクの出力が表示されます。 ほとんどの場合、EXPLAIN 出力はタスク間で同様の内容になります。 場合によっては、一部のタスクが異なって計画されるか、または実行時間が大幅に長くなることがあります。 これらの場合は、このパラメーターを有効にすると役立つことがあります。その後、EXPLAIN 出力にはすべてのタスクが含まれます。 すべてのタスクを説明すると、EXPLAIN の時間が長くなる可能性があります。

## <a name="postgresql-parameters"></a>PostgreSQL のパラメーター

* [DateStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-DATETIME-OUTPUT) - 日付と時刻の値の表示形式を設定します
* [IntervalStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-INTERVAL-OUTPUT) - 間隔の値の表示形式を設定します
* [TimeZone](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) - タイムスタンプを表示および解釈するためのタイム ゾーンを設定します
* [application_name](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) - 統計とログで報告されるアプリケーション名を設定します
* [array_nulls](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ARRAY-NULLS) - 配列への NULL 要素の入力を有効にします
* [autovacuum](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM) - 自動バキューム サブプロセスを開始します
* [autovacuum_analyze_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-SCALE-FACTOR) - 分析を開始するタプルの挿入、更新、または削除の数の、reltuples に対する割合
* [autovacuum_analyze_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-THRESHOLD) - 分析を開始するタプルの挿入、更新、または削除の最小数
* [autovacuum_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME) - 自動バキュームを実行する間にスリープする時間
* [autovacuum_vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY) - 自動バキュームでのバキュームのコスト遅延 (ミリ秒単位)
* [autovacuum_vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT) - 自動バキュームで遅延の前に使用可能なバキューム コストの量
* [autovacuum_vacuum_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-SCALE-FACTOR) - バキュームを開始するタプルの更新または削除の数の、reltuples に対する割合
* [autovacuum_vacuum_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-THRESHOLD) - バキュームを開始するタプルの更新または削除の最小数
* [autovacuum_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) - 各自動バキューム ワーカー プロセスで使用される最大メモリを設定します
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BACKEND-FLUSH-AFTER) - それを超えると以前に実行された書き込みがディスクにフラッシュされるようになるページ数
* [backslash_quote](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-BACKSLASH-QUOTE) - 文字列リテラルで "\'" が許可されるかどうかを設定します
* [bgwriter_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-DELAY) - ラウンド間のバックグラウンド ライターのスリープ時間
* [bgwriter_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-FLUSH-AFTER) - それを超えると以前に実行された書き込みがディスクにフラッシュされるようになるページ数
* [bgwriter_lru_maxpages](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MAXPAGES) - バックグラウンド ライターがラウンドごとにフラッシュする LRU ページの最大数
* [bgwriter_lru_multiplier](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MULTIPLIER) - ラウンドごとに解放される平均バッファー使用量の倍数
* [bytea_output](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-BYTEA-OUTPUT) - bytea の出力形式を設定します
* [check_function_bodies](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CHECK-FUNCTION-BODIES) - CREATE FUNCTION の間に関数本体をチェックします
* [checkpoint_completion_target](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET) - チェックポイントの間にダーティ バッファーのフラッシュに費やされた時間 (チェックポイント間隔に対する割合)
* [checkpoint_timeout](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) - 自動 WAL チェックポイントの間隔の最大時間を設定します
* [checkpoint_warning](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-WARNING) - チェックポイント セグメントがこれより頻繁に入力された場合、警告を有効にします
* [client_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) - クライアントの文字セットのエンコードを設定します
* [client_min_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-MIN-MESSAGES) - クライアントに送信されるメッセージ レベルを設定します
* [commit_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-COMMIT-DELAY) - トランザクションのコミットからディスクへの WAL のフラッシュまでの遅延 (マイクロ秒) を設定します
* [commit_siblings](https://www.postgresql.org/docs/12/runtime-config-wal.html#GUC-COMMIT-SIBLINGS) - commit_delay が実行されるようになる前の、同時に開かれているトランザクションの最小数を設定します
* [constraint_exclusion](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CONSTRAINT-EXCLUSION) - クエリを最適化するためにプランナーで制約を使用できるようにします
* [cpu_index_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-INDEX-TUPLE-COST) - インデックス スキャン中の各インデックス エントリの処理コストの、プランナーによる推定値を設定します
* [cpu_operator_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-OPERATOR-COST) - 演算子または関数の各呼び出しの処理コストの、プランナーによる推定値を設定します
* [cpu_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-TUPLE-COST) - 各タプル (行) の処理コストの、プランナーによる推定値設定します
* [cursor_tuple_fraction](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CURSOR-TUPLE-FRACTION) - 取得されるカーソルの行の割合の、プランナーによる推定値を設定します
* [deadlock_timeout](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-DEADLOCK-TIMEOUT) - デッドロックを確認する前にロックを待機する時間を設定します (ミリ秒単位)
* [debug_pretty_print](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.3.1.3) - 解析およびプランのツリーの表示をインデントします
* [debug_print_parse](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) - 各クエリの解析ツリーをログに記録します
* [debug_print_plan](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) - 各クエリの実行プランをログに記録します
* [debug_print_rewritten](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) - 各クエリの書き換えられた解析ツリーをログに記録します
* [default_statistics_target](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) - 既定の統計ターゲットを設定します
* [default_tablespace](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TABLESPACE) - テーブルとインデックスを作成する既定のテーブルスペースを設定します
* [default_text_search_config](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TEXT-SEARCH-CONFIG) - 既定のテキスト検索構成を設定します
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-DEFERRABLE) - 新しいトランザクションの既定の遅延可能状態を設定します
* [default_transaction_isolation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-ISOLATION) - 各新規トランザクションのトランザクション分離レベルを設定します
* [default_transaction_read_only](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-READ-ONLY) - 新しいトランザクションの既定の読み取り専用状態を設定します
* default_with_oids - 既定で OID を使用して新しいテーブルを作成します
* [effective_cache_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) - ディスク キャッシュのサイズに関するプランナーによる想定を設定します
* [enable_bitmapscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-BITMAPSCAN) - プランナーによるビットマップ スキャン プランの使用を有効にします
* [enable_gathermerge](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-GATHERMERGE) - プランナーによる収集マージ プランの使用を有効にします
* [enable_hashagg](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHAGG) - プランナーによるハッシュ済み集計プランの使用を有効にします
* [enable_hashjoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHJOIN) - プランナーによるハッシュ結合プランの使用を有効にします
* [enable_indexonlyscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN) - プランナーによるインデックスのみのスキャン プランの使用を有効にします
* [enable_indexscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN) - プランナーによるインデックス スキャン プランの使用を有効にします
* [enable_material](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MATERIAL) - プランナーによる具体化の使用を有効にします
* [enable_mergejoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MERGEJOIN) - プランナーによるマージ結合プランの使用を有効にします
* [enable_nestloop](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-NESTLOOP) - プランナーによる入れ子になったループ結合プランの使用を有効にします
* [enable_seqscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SEQSCAN) - プランナーによる順次スキャン プランの使用を有効にします
* [enable_sort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SORT) - プランナーによる明示的な並べ替え手順の使用を有効にします
* [enable_tidscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-TIDSCAN) - プランナーによる TID スキャン プランの使用を有効にします
* [escape_string_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ESCAPE-STRING-WARNING) - 通常の文字列リテラルにおける円記号のエスケープに関して警告します
* [exit_on_error](https://www.postgresql.org/docs/current/runtime-config-error-handling.html#GUC-EXIT-ON-ERROR) - エラー発生時にセッションを終了します
* [extra_float_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) - 浮動小数点値に対して表示される桁数を設定します
* [force_parallel_mode](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FORCE-PARALLEL-MODE) - 並列クエリ機能を強制的に使用します
* [from_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FROM-COLLAPSE-LIMIT) - それを超えるとサブクエリが折りたたまれない FROM リストのサイズを設定します
* [geqo](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO) -遺伝的クエリの最適化を有効にします
* [geqo_effort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-EFFORT) - GEQO: 他の GEQO パラメーターの既定値を設定するために処理が使用されます
* [geqo_generations](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-GENERATIONS) - GEQO: アルゴリズムの反復の回数
* [geqo_pool_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-POOL-SIZE) - GEQO: 母集団内の個人の数
* [geqo_seed](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SEED) - GEQO: ランダム パス選択のためのシード
* [geqo_selection_bias](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SELECTION-BIAS) - GEQO: 母集団内の選択圧
* [geqo_threshold](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-THRESHOLD) - それを超えると GEQO が使用される、FROM 項目数のしきい値を設定します
* [gin_fuzzy_search_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.5.2.2.1.3) - GIN による完全検索に対して許可される最大の結果を設定します
* [gin_pending_list_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.2.2.23.1.3) - GIN インデックスに対する保留中リストの最大サイズを設定します
* [idle_in_transaction_session_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) - アイドル状態のトランザクションの最大許容期間を設定します
* [join_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-JOIN-COLLAPSE-LIMIT) - それを超えると JOIN コンストラクトがフラット化されない FROM リストのサイズを設定します
* [lc_monetary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY) - 通貨金額の書式設定に使用するロケールを設定します
* [lc_numeric](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) - 数値の書式設定に使用するロケールを設定します
* [lo_compat_privileges](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-LO-COMPAT-PRIVILEGES) - 大きなオブジェクトでの特権チェックに対して下位互換性モードを有効にします
* [lock_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT) - ロックに対する待機の最大許容期間 (ミリ秒単位) を設定します。 0 を指定するとオフになります
* [log_autovacuum_min_duration](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#) - それを超えると自動バキューム アクションがログに記録される、最小実行時間を設定します
* [log_checkpoints](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) - 各チェックポイントをログに記録します
* [log_connections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS) - 成功した各接続をログに記録します
* [log_destination](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DESTINATION) - サーバー ログ出力の出力先を設定します
* [log_disconnections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS) - セッションの終了を、実行時間も含めてログに記録します
* [log_duration](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DURATION) - 完了した各 SQL ステートメントの実行時間をログに記録します
* [log_error_verbosity](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ERROR-VERBOSITY) - ログに記録されるメッセージの詳細度を設定します
* [log_lock_waits](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LOCK-WAITS) - 長いロック待機をログに記録します
* [log_min_duration_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT) - それを超えるとステートメントがログに記録される、最小実行時間 (ミリ秒単位) を設定します。 -1 を指定すると、ステートメント実行時間のログ記録は無効になります
* [log_min_error_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-ERROR-STATEMENT) - このレベル以上のエラーを生成したすべてのステートメントを、ログに記録します
* [log_min_messages](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-MESSAGES) - ログに記録されるメッセージ レベルを設定します
* [log_replication_commands](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-REPLICATION-COMMANDS) - 各レプリケーション コマンドをログに記録します
* [log_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT) - ログに記録されるステートメントの種類を設定します
* [log_statement_stats](https://www.postgresql.org/docs/current/runtime-config-statistics.html#id-1.6.6.12.3.2.1.1.3) - 各クエリについて、累積パフォーマンス統計情報をサーバー ログに書き込みます
* [log_temp_files](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES) - このキロバイト数より大きい一時ファイルの使用をログに記録します
* [maintenance_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) - メンテナンス操作に使用される最大メモリを設定します
* [max_parallel_workers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS) - 一度にアクティブにすることができる並列ワーカーの最大数を設定します
* [max_parallel_workers_per_gather](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) - Executor ノードあたりの並列プロセスの最大数を設定します
* [max_pred_locks_per_page](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-PAGE) - 述語ロックされるタプルの、ページあたりの最大数を設定します
* [max_pred_locks_per_relation](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-RELATION) - 述語ロックされるページとタプルの、リレーションあたりの最大数を設定します
* [max_standby_archive_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-ARCHIVE-DELAY) - アーカイブされた WAL データがホット スタンバイ サーバーで処理されているときの、それを超えるとクエリが取り消される最大遅延時間を設定します
* [max_standby_streaming_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-STREAMING-DELAY) - ストリーミングされた WAL データがホット スタンバイ サーバーで処理されているときの、それを超えるとクエリが取り消される最大遅延時間を設定します
* [max_sync_workers_per_subscription](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-SYNC-WORKERS-PER-SUBSCRIPTION) - サブスクリプションあたりのテーブル同期ワーカーの最大数
* [max_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MAX-WAL-SIZE) - チェックポイントがトリガーされる WAL サイズを設定します
* [min_parallel_index_scan_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-MIN-PARALLEL-INDEX-SCAN-SIZE) - 並列スキャンに対するインデックス データの最小量を設定します
* [min_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MIN-WAL-SIZE) - WAL を圧縮する最小サイズを設定します
* [operator_precedence_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-OPERATOR-PRECEDENCE-WARNING) - PostgreSQL 9.4 以降に意味が変更されたコンストラクトに対して警告を生成します
* [parallel_setup_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-SETUP-COST) - 並列クエリに対するワーカー プロセスの起動コストのプランナーによる推定値を設定します
* [parallel_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-TUPLE-COST) - ワーカーからマスター バックエンドに各タプル (行) を渡すコストのプランナーによる推定値を設定します
* [pg_stat_statements.save](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) - 異なるサーバー シャットダウンにまたがって pg_stat_statements の統計情報を保存します
* [pg_stat_statements.track](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) - pg_stat_statements で追跡するステートメントを選択します
* [pg_stat_statements.track_utility](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) - pg_stat_statements でユーティリティ コマンドを追跡するかどうかを選択します
* [quote_all_identifiers](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-QUOTE-ALL-IDENTIFIERS) - SQL フラグメントを生成するとき、すべての識別子を引用符で囲みます
* [random_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-RANDOM-PAGE-COST) - 順不同でフェッチされたディスク ページのコストのプランナーによる推定値を設定します
* [row_security](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-ROW-SECURITY) - 行のセキュリティを有効にします
* [search_path](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SEARCH-PATH) - スキーマで修飾されていない名前に対するスキーマ検索順序を設定します
* [seq_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-SEQ-PAGE-COST) - 順番にフェッチされたディスク ページのコストのプランナーによる推定値を設定します
* [session_replication_role](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SESSION-REPLICATION-ROLE) - トリガーと書き換え規則に対するセッションの動作を設定します
* [standard_conforming_strings](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.7.1.3) - "..." 文字列で円記号が文字どおりに処理されるようにします
* [statement_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT) - ステートメントの最大許容期間 (ミリ秒単位) を設定します。 0 を指定するとオフになります
* [synchronize_seqscans](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.8.1.3) - 同期順次スキャンを有効にします
* [synchronous_commit](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-SYNCHRONOUS-COMMIT) - 現在のトランザクションの同期レベルを設定します
* [tcp_keepalives_count](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-COUNT) - TCP keepalive の再送信の最大数
* [tcp_keepalives_idle](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-IDLE) - TCP keepalive の発行間隔の時間
* [tcp_keepalives_interval](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-INTERVAL) - TCP keepalive の再送信間隔の時間
* [temp_buffers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-TEMP-BUFFERS) - 各データベース セッションで使用される一時バッファーの最大数を設定します
* [temp_tablespaces](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TEMP-TABLESPACES) - 一時テーブルと並べ替えファイルに使用するテーブルスペースを設定します
* [track_activities](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-ACTIVITIES) - 実行しているコマンドに関する情報を収集します
* [track_counts](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS) - データベース アクティビティに関する統計情報を収集します
* [track_functions](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-FUNCTIONS) - データベース アクティビティに関する関数レベルの統計情報を収集します
* [track_io_timing](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-IO-TIMING) - データベース I/O アクティビティに関するタイミング統計情報を収集します
* [transform_null_equals](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-TRANSFORM-NULL-EQUALS) - "expr=NULL" を "expr IS NULL" として処理します
* [vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-DELAY) - バキュームのコスト遅延 (ミリ秒単位)
* [vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-LIMIT) - 遅延の前に使用可能なバキューム コストの量
* [vacuum_cost_page_dirty](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-DIRTY) - バキュームによってダーティになったページのバキューム コスト
* [vacuum_cost_page_hit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-HIT) - バッファー キャッシュで見つかったページのバキューム コスト
* [vacuum_cost_page_miss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-MISS) - バッファー キャッシュで見つからなかったページのバキューム コスト
* [vacuum_defer_cleanup_age](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-VACUUM-DEFER-CLEANUP-AGE) - VACUUM と HOT のクリーンアップを遅延する必要があるトランザクションの数 (ある場合)
* [vacuum_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-MIN-AGE) - VACUUM でテーブルの行を凍結する必要がある最小経過時間
* [vacuum_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-TABLE-AGE) - タプルを凍結するために VACUUM でテーブル全体をスキャンする必要がある経過時間
* [vacuum_multixact_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-MIN-AGE) - VACUUM でテーブルの行の MultiXactId を凍結する必要がある最小経過時間
* [vacuum_multixact_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-TABLE-AGE) - タプルを凍結するために VACUUM でテーブル全体をスキャンする必要がある Multixact の経過時間
* [wal_receiver_status_interval](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-RECEIVER-STATUS-INTERVAL) - プライマリに対する WAL レシーバーの状態レポートの最大間隔を設定します
* [wal_writer_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-DELAY) - WAL ライターで実行される WAL フラッシュの間の時間
* [wal_writer_flush_after](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-FLUSH-AFTER) - フラッシュをトリガーする、WAL ライターによって書き込まれた WAL の量
* [work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) - それを超えると一時ディスク ファイルへの書き込みが行われる、内部並べ替え操作とハッシュ テーブルによって使用されたメモリの量を設定します
* [xmlbinary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLBINARY) - バイナリ値を XML でエンコードする方法を設定します
* [xmloption](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLOPTION) - 暗黙的な解析およびシリアル化の操作で、XML データをドキュメントまたはコンテンツのフラグメントと見なすかどうかを設定します

## <a name="next-steps"></a>次の手順

* サーバー パラメーター以外の別の形式の構成として、Hyperscale (Citus) サーバー グループには[構成オプション](concepts-hyperscale-configuration-options.md)のリソースがあります。
* 基になる PostgreSQL データにも[構成パラメーター](http://www.postgresql.org/docs/current/static/runtime-config.html)があります。
