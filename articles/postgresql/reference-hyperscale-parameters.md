---
title: サーバー パラメーター – Hyperscale (Hyperscale (Citus) - Azure Database for PostgreSQL
description: Hyperscale (Citus) SQL API のパラメーター
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 57258540f3cd7b4c47b662b0885453cedd188e5b
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136620"
---
# <a name="server-parameters"></a>サーバー パラメーター

Hyperscale (Citus) の動作に影響を与えるサーバー パラメーターにはさまざまなものがあります (標準の PostgreSQL のパラメーターと Hyperscale (Citus) に固有のパラメーターの両方)。 PostgreSQL の構成パラメーターの詳細については、PostgreSQL のドキュメントの[実行時の構成](http://www.postgresql.org/docs/current/static/runtime-config.html)のセクションを参照してください。

このリファレンスの残りの部分は、Hyperscale (Citus) 固有の構成パラメーターについて説明することを目的にしています。 これらのパラメーターは、Azure portal の Hyperscale (Citus) サーバー グループの **[設定]** にある **[ワーカー ノードのパラメーター]** で設定できます。

> [!NOTE]
>
> 以前のバージョンの Citus Engine を実行している Hyperscale サーバー グループでは、後に一覧表示されているパラメーターの一部が提供されない可能性があります。

## <a name="general-configuration"></a>全般構成

### <a name="citususe_secondary_nodes-enum"></a>citus.use\_secondary\_nodes (列挙型)

SELECT クエリのノードを選択するときに使用するポリシーを設定します。 これが 'always' に設定されている場合、プランナーは、[pg_dist_node](reference-hyperscale-metadata.md#worker-node-table) で 'secondary' noderole とマークされているノードにのみクエリを実行します。

この列挙型でサポートされている値は次のとおりです。

-   **never:** (既定) すべての読み取りがプライマリ ノード上で実行されます。
-   **always:** 代わりにセカンダリ ノードに対して読み取りが実行され、挿入/更新ステートメントは無効になります。

### <a name="cituscluster_name-text"></a>citus.cluster\_name (テキスト)

コーディネーター ノード プランナーに、どのクラスターを調整するかを通知します。 cluster\_name が設定されていると、プランナーは、そのクラスター内のワーカー ノードにのみクエリを実行します。

### <a name="citusenable_version_checks-boolean"></a>citus.enable\_version\_checks (ブール値)

Hyperscale (Citus) のバージョンをアップグレードするには、サーバーの再起動 (新しい共有ライブラリを選択するため) と、その後の ALTER EXTENSION UPDATE コマンドが必要です。  この両方の手順を実行しないと、エラーまたはクラッシュが発生する可能性があります。
そのため、Hyperscale (Citus) ではコードと拡張機能のバージョンが一致することを検証し、一致しない場合はエラーが発生します。

この値は既定で true であり、コーディネーター上で有効です。 まれなケースとして、複雑なアップグレード プロセスではこのパラメーターを false に設定する必要があり、そのためこのチェックを無効にする場合があります。

### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus.log\_distributed\_deadlock\_detection (ブール値)

分散デッドロックの検出に関連した処理をサーバー ログに記録するかどうか。 この既定値は false です。

### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus.distributed\_deadlock\_detection\_factor (浮動小数点)

分散デッドロックを確認するまでの待ち時間を設定します。 特に、待ち時間は、この値に PostgreSQL の [deadlock\_timeout](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) 設定を掛けた値になります。 既定値は `2` です。 `-1` の値を指定すると、分散デッドロックの検出が無効になります。

### <a name="citusnode_connection_timeout-integer"></a>citus.node\_connection\_timeout (整数)

`citus.node_connection_timeout` GUC は、接続の確立を待つ最大期間 (ミリ秒単位) を設定します。 Hyperscale (Citus) では、少なくとも 1 つのワーカー接続が確立される前にタイムアウトが経過した場合にエラーが発生します。 この GUC は、コーディネーターからワーカーへの接続と、ワーカー相互の接続に影響を与えます。

-   既定値: 5 秒
-   最小:10 ミリ秒
-   最大: 1 時間

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

## <a name="query-statistics"></a>クエリ統計

### <a name="citusstat_statements_purge_interval-integer"></a>citus.stat\_statements\_purge\_interval (整数)

メンテナンス デーモンが `pg_stat_statements` で不一致のレコードを [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) から削除する頻度を設定します。 この構成値は、削除間の時間間隔 (秒単位) を設定し、既定値は 10 です。 0 の値を指定すると、削除が無効になります。

```psql
SET citus.stat_statements_purge_interval TO 5;
```

このパラメーターはコーディネーター上で有効であり、実行時に変更できます。

## <a name="data-loading"></a>データの読み込み

### <a name="citusmulti_shard_commit_protocol-enum"></a>citus.multi\_shard\_commit\_protocol (列挙型)

ハッシュ分散テーブルで COPY を実行するときに使用するコミット プロトコルを設定します。 個々のシャード配置では、COPY 中にエラーが発生した場合はデータが取り込まれないようにするために、この COPY はトランザクション ブロックで実行されます。 ただし、COPY はすべての配置で成功するが、すべてのトランザクションがコミットする前に (ハードウェア) 障害が発生する特定のエラー ケースが存在します。 このパラメーターを使用すると、次のコミット プロトコルから選択することによって、そのケースでのデータ損失を防止できます。

-   **2pc:** (既定) シャード配置で COPY が実行されるトランザクションが最初に PostgreSQL の [2 フェーズ コミット](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html)を使用して準備され、次にコミットされます。 失敗したコミットは、COMMIT PREPARED または ROLLBACK PREPARED を使用して、それぞれ手動で回復または中止できます。
    2pc を使用する場合は、すべてのワーカー上で [max\_prepared\_transactions](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) を (通常は max\_connections と同じ値に) 増やす必要があります。
-   **1pc:** シャード配置で COPY が実行されるトランザクションが 1 回でコミットされます。 COPY がすべての配置で成功した後にコミットが失敗した場合は、データが失われる可能性があります (まれなケースです)。

### <a name="citusshard_replication_factor-integer"></a>citus.shard\_replication\_factor (整数)

シャードのレプリケーション係数、つまり、シャードが配置されるノードの数を設定し、既定値は 1 です。 このパラメーターは実行時に設定でき、コーディネーター上で有効です。 このパラメーターの理想的な値は、クラスターのサイズやノード障害の頻度によって異なります。  たとえば、大規模なクラスターを実行し、ノード障害をより頻繁に観察する場合は、このレプリケーション係数を増やすこともできます。

### <a name="citusshard_count-integer"></a>citus.shard\_count (整数)

ハッシュ パーティション テーブルのシャード数を設定し、既定値は 32 です。  この値は、ハッシュ パーティション テーブルを作成するときに [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF によって使用されます。 このパラメーターは実行時に設定でき、コーディネーター上で有効です。

### <a name="citusshard_max_size-integer"></a>citus.shard\_max\_size (整数)

シャードが分割されるまでに達する最大サイズを設定し、既定値は 1 GB です。 1 つのシャードのソース ファイルのサイズ (これはステージングに使用されます) がこの構成値を超えた場合、データベースでは常に新しいシャードが作成されます。 このパラメーターは実行時に設定でき、コーディネーター上で有効です。

## <a name="planner-configuration"></a>プランナーの構成

### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus.limit\_clause\_row\_fetch\_count (整数)

LIMIT 句の最適化のためにタスクごとにフェッチする行数を設定します。
場合によっては、LIMIT 句を含む SELECT クエリが、結果を生成するために各タスクからすべての行をフェッチする必要があることがあります。 これらの場合で、かつ近似によって意味のある結果が生成されるとき、この構成値は各シャードからフェッチする行数を設定します。 制限の近似は既定では無効であり、このパラメーターは -1 に設定されています。 この値は実行時に設定でき、コーディネーター上で有効です。

### <a name="cituscount_distinct_error_rate-floating-point"></a>citus.count\_distinct\_error\_rate (浮動小数点)

Hyperscale (Citus) では、postgresql-hll 拡張機能を使用して count(distinct) の概数を計算できます。 この構成エントリは、count(distinct) を計算するときの望ましいエラー率を設定します。 既定値である 0.0 によって count(distinct) の近似が無効になり、1.0 では結果の精度に関する保証が得られません。 最適な結果を得るために、このパラメーターを 0.005 に設定することをお勧めします。 この値は実行時に設定でき、コーディネーター上で有効です。

### <a name="citustask_assignment_policy-enum"></a>citus.task\_assignment\_policy (列挙型)

> [!NOTE]
> この GUC は、[shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) が 1 を超えている場合か、または [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables) に対するクエリにのみ適用されます。

ワーカーにタスクを割り当てるときに使用するポリシーを設定します。 コーディネーターは、シャードの場所に基づいてワーカーにタスクを割り当てます。 この構成値は、これらの割り当てを行うときに使用するポリシーを指定します。
現在、使用できるタスク割り当てポリシーには次の 3 つがあります。

-   **greedy:** 最長一致ポリシーは既定値であり、ワーカーにまたがってタスクを均等に分散させることを目的にしています。
-   **round-robin:** ラウンドロビン ポリシーは、異なるレプリカを交互に使用するラウンドロビン方式でワーカーにタスクを割り当てます。 このポリシーでは、ワーカーの数に比べてテーブルのシャード数が少ない場合にクラスター使用率が向上します。
-   **first-replica:** 最初のレプリカ ポリシーは、シャードの配置 (レプリカ) の挿入順序に基づいてタスクを割り当てます。 つまり、シャードのフラグメント クエリは、そのシャードの最初のレプリカを含むワーカーに割り当てられます。
    この方法では、どのシャードがどのノード上で使用されるかに関する強力な保証 (つまり、より強力なメモリ常駐の保証) が得られます。

このパラメーターは実行時に設定でき、コーディネーター上で有効です。

## <a name="intermediate-data-transfer"></a>中間データの転送

### <a name="citusbinary_worker_copy_format-boolean"></a>citus.binary\_worker\_copy\_format (ブール値)

ワーカー間で中間データを転送するためにバイナリ コピー形式を使用します。
大規模なテーブルの結合中、Hyperscale (Citus) では動的に再パーティション分割し、異なるワーカー間でデータをシャッフルすることが必要になる場合があります。 既定では、このデータはテキスト形式で転送されます。 このパラメーターを有効にすると、データベースは PostgreSQL のバイナリ シリアル化形式を使用してこのデータを転送するよう指示されます。 このパラメーターはワーカー上で有効であり、postgresql.conf ファイルで変更する必要があります。 この構成ファイルを編集した後、ユーザーは SIGHUP シグナルを送信するか、またはサーバーを再起動してこの変更を有効にすることができます。

### <a name="citusbinary_master_copy_format-boolean"></a>citus.binary\_master\_copy\_format (ブール値)

コーディネーターとワーカーの間でデータを転送するためにバイナリ コピー形式を使用します。 分散クエリを実行している場合、ワーカーは、その中間結果を最終的な集計のためにコーディネーターに転送します。 既定では、このデータはテキスト形式で転送されます。 このパラメーターを有効にすると、データベースは PostgreSQL のバイナリ シリアル化形式を使用してこのデータを転送するよう指示されます。
このパラメーターは実行時に設定でき、コーディネーター上で有効です。

### <a name="citusmax_intermediate_result_size-integer"></a>citus.max\_intermediate\_result\_size (整数)

実行のためや、複雑なサブクエリのためにワーカー ノードにプッシュダウンできない CTE に対する中間結果の最大サイズ (KB 単位)。 既定値は 1 GB であり、-1 の値は制限がないことを示します。
制限を超えるクエリは取り消され、エラー メッセージが生成されます。

## <a name="ddl"></a>DDL (DDL)

### <a name="citusenable_ddl_propagation-boolean"></a>citus.enable\_ddl\_propagation (ブール値)

DDL の変更をコーディネーターからすべてのワーカーに自動的に伝達するかどうかを指定します。 既定値は、true です。 一部のスキーマ変更ではテーブルに対するアクセスの排他的ロックが必要であるため、また自動伝達はすべてのワーカーに順番に適用されるため、Hyperscale (Citus) クラスターの応答性が一時的に低下する場合があります。 この設定を無効にして、変更を手動で伝達することを選択できます。

## <a name="executor-configuration"></a>エグゼキューターの構成

### <a name="general"></a>全般

#### <a name="citusall_modifications_commutative"></a>citus.all\_modifications\_commutative

Hyperscale (Citus) では、動作の正確性を保証するために、可換法則が適用され、変更操作のための適切なロックが取得されます。 たとえば、INSERT ステートメントが別の INSERT ステートメントと可換であるが、UPDATE または DELETE ステートメントとは可換でないことを前提としています。 同様に、UPDATE または DELETE ステートメントが別の UPDATE または DELETE ステートメントとは可換でないことを前提としています。 この予防措置は、UPDATE や DELETE では Hyperscale (Citus) がより強力なロックを取得する必要があることを示しています。

INSERT や他の UPDATE と可換である UPDATE ステートメントがある場合は、このパラメーターを true に設定して、これらの可換性の前提を緩和できます。 このパラメーターが true に設定されている場合は、すべてのコマンドが可換であると見なされ、共有ロックを要求するため、全体的なスループットが向上する可能性があります。 このパラメーターは実行時に設定でき、コーディネーター上で有効です。

#### <a name="citusremote_task_check_interval-integer"></a>citus.remote\_task\_check\_interval (整数)

Hyperscale (Citus) が、タスク トラッカー エグゼキューターによって管理されるジョブの状態を確認する頻度を設定します。 この既定値は 10 ms です。 コーディネーターはワーカーにタスクを割り当てた後、各タスクの進行状況を定期的に確認します。 この構成値は、2 つの結果確認の間の時間間隔を設定します。 このパラメーターはコーディネーター上で有効であり、実行時に設定できます。

#### <a name="citustask_executor_type-enum"></a>citus.task\_executor\_type (列挙型)

Hyperscale (Citus) には、分散 SELECT クエリを実行するための 3 つの種類のエグゼキューターがあります。  この構成パラメーターを設定することによって、目的のエグゼキューターを選択できます。 このパラメーターで許容される値は次のとおりです。

-   **adaptive:** 既定値。 これは、複数のシャードにまたがる集計や併置結合が必要なクエリへの高速な応答のために最適です。
-   **task-tracker:** タスク トラッカー エグゼキューターは、ワーカー ノードにまたがるデータのシャッフルや効率的なリソース管理を必要とする実行時間の長い、複雑なクエリに最適です。
-   **real-time:** (非推奨) アダプティブ エグゼキューターと同様の目的に対応しますが、柔軟性が低く、ワーカー ノードへの接続の負荷は大きくなる可能性があります。

このパラメーターは実行時に設定でき、コーディネーター上で有効です。

#### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus.multi\_task\_query\_log\_level (列挙型) {#multi_task_logging}

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

#### <a name="citusenable_repartition_joins-boolean"></a>citus.enable\_repartition\_joins (ブール値)

通常、アダプティブ エグゼキューターで再パーティション分割の結合を実行しようとすると、エラー メッセージで失敗します。  ただし、`citus.enable_repartition_joins` を true に設定すると、Hyperscale (Citus) はその結合をタスク トラッカー エグゼキューターで実行するように一時的に切り替えることができます。  既定値は false です。

### <a name="task-tracker-executor-configuration"></a>タスク トラッカー エグゼキューターの構成

#### <a name="citustask_tracker_delay-integer"></a>citus.task\_tracker\_delay (整数)

このパラメーターは、タスク管理ラウンド間のタスク トラッカーのスリープ時間を設定し、既定値は 200 ms です。 タスク トラッカー プロセスは定期的にスリープ解除し、割り当てられているすべてのタスクを確認した後、これらのタスクをスケジュールして実行します。  その後、タスク トラッカーはこれらのタスクを再び確認する前に、一定の期間スリープ状態になります。
この構成値によって、そのスリープ期間の長さが決定されます。 このパラメーターはワーカー上で有効であり、postgresql.conf ファイルで変更する必要があります。 この構成ファイルを編集した後、ユーザーは SIGHUP シグナルを送信するか、またはサーバーを再起動してこの変更を有効にすることができます。

このパラメーターを小さくすると、管理ラウンド間の時間間隔が減るため、タスク トラッカー エグゼキューターによって発生する遅延を減少させることができます。
遅延の減少はシャード クエリが短い場合に有効であるため、それらの状態を定期的に更新します。

#### <a name="citusmax_assign_task_batch_size-integer"></a>citus.max\_assign\_task\_batch\_size (整数)

コーディネーター上のタスク トラッカー エグゼキューターは、タスクをバッチでワーカー上のデーモンに同期的に割り当てます。 このパラメーターは、1 つのバッチで割り当てるタスクの最大数を設定します。 より大きなバッチ サイズを選択すると、タスクの割り当てが高速になります。 ただし、ワーカーの数が多いと、すべてのワーカーにタスクを割り当てるまでの時間が長くなる可能性があります。  このパラメーターは実行時に設定でき、コーディネーター上で有効です。

#### <a name="citusmax_running_tasks_per_node-integer"></a>citus.max\_running\_task\_per\_node (整数)

タスク トラッカー プロセスは、必要に応じて、割り当てられているタスクをスケジュールして実行します。 この構成値は、特定の時点で 1 つのノード上で同時に実行するタスクの最大数を設定し、既定値は 8 です。

この制限により、多数のタスクが同時にディスクにアクセスすることはなくなるため、ディスク I/O の競合を回避するために役立ちます。 クエリがメモリまたは SSD から処理されている場合は、あまり気にせず max\_running\_task\_per\_node を増やすことができます。

#### <a name="cituspartition_buffer_size-integer"></a>citus.partition\_buffer\_size (整数)

パーティション操作に使用するバッファー サイズを設定し、既定値は 8 MB です。
Hyperscale (Citus) では、2 つの大規模なテーブルが結合される場合、テーブル データを複数のファイルに再パーティション分割できます。 このパーティション バッファーがいっぱいになると、再パーティション分割されたデータはディスク上のファイルにフラッシュされます。  この構成エントリは実行時に設定でき、ワーカー上で有効です。

### <a name="explain-output"></a>EXPLAIN 出力

#### <a name="citusexplain_all_tasks-boolean"></a>citus.explain\_all\_tasks (ブール値)

既定では、分散クエリで [EXPLAIN](http://www.postgresql.org/docs/current/static/sql-explain.html) を実行している場合、Hyperscale (Citus) には 1 つの任意のタスクの出力が表示されます。 ほとんどの場合、EXPLAIN 出力はタスク間で同様の内容になります。 場合によっては、一部のタスクが異なって計画されるか、または実行時間が大幅に長くなることがあります。 これらの場合は、このパラメーターを有効にすると役立つことがあります。その後、EXPLAIN 出力にはすべてのタスクが含まれます。 すべてのタスクを説明すると、EXPLAIN の時間が長くなる可能性があります。

## <a name="next-steps"></a>次の手順

* サーバー パラメーター以外の別の形式の構成として、Hyperscale (Citus) サーバー グループには[構成オプション](concepts-hyperscale-configuration-options.md)のリソースがあります。
* 基になる PostgreSQL データにも[構成パラメーター](http://www.postgresql.org/docs/current/static/runtime-config.html)があります。
