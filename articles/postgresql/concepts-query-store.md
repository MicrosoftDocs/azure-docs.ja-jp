---
title: Azure Database for PostgreSQL - Single Server でのクエリ ストア
description: この記事では、Azure Database for PostgreSQL - Single Server でのクエリ ストア機能について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: deab527d44713bffed1f430ec283592d0e4232ee
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764411"
---
# <a name="monitor-performance-with-the-query-store"></a>クエリ ストアによるパフォーマンスの監視

**適用対象:** Azure Database for PostgreSQL - 単一サーバー バージョン 9.6、10、11

Azure Database for PostgreSQL のクエリ ストア機能では、一定期間にわたってクエリ パフォーマンスを追跡する手段が提供されます。 クエリ ストアを使用すると、実行時間が最長のクエリおよびリソースを最も消費しているクエリを迅速に特定できるので、パフォーマンスのトラブルシューティングが簡単になります。 クエリ ストアでは、クエリおよびランタイム統計の履歴が自動的にキャプチャされて保持されるので、それらを確認できます。 データベースの使用パターンを確認できるように、データが時間枠で区切られます。 すべてのユーザー、データベース、クエリに関するデータが Azure Database for PostgreSQL インスタンス内の **azure_sys** という名前のデータベースに格納されます。

> [!IMPORTANT]
> **azure_sys** データベースまたはそのスキーマを変更しないでください。 そうすると、クエリ ストアおよび関連するパフォーマンス機能が正しく機能しません。

## <a name="enabling-query-store"></a>クエリ ストアの有効化
クエリ ストアはオプトイン機能なので、既定ではサーバー上でアクティブになりません。 ストアは特定のサーバー上のすべてのデータベースに対してグローバルで有効または無効になり、データベースごとにオンまたはオフにすることはできません。

### <a name="enable-query-store-using-the-azure-portal"></a>Azure portal を使用してクエリ ストアを有効にする
1. Azure portal にサインインし、ご利用の Azure Database for PostgreSQL サーバーを選択します。
2. メニューの **[設定]** セクションで、 **[サーバー パラメーター]** を選択します。
3. `pg_qs.query_capture_mode` パラメーターを検索します。
4. 値を `TOP` に設定して**保存**します。

クエリ ストアでの待機統計を有効にするには、次の手順に従います。 
1. `pgms_wait_sampling.query_capture_mode` パラメーターを検索します。
1. 値を `ALL` に設定して**保存**します。


または Azure CLI を使用して、これらのパラメーターを設定することもできます。
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

azure_sys データベースに保持するデータの最初のバッチには、最大で 20 分ほどかかります。

## <a name="information-in-query-store"></a>クエリ ストア内の情報
クエリ ストアには、次の 2 つのストアがあります。
- クエリ実行の統計情報を保持するためのランタイム統計ストア。
- 待機統計情報を保持するための待機統計ストア。

クエリ ストアを使用するための一般的なシナリオは次のとおりです。
- 指定された時間枠内にクエリが実行された回数を確認する
- 大きなデルタを確認するために時間枠間でクエリの平均実行回数を比較する
- 過去 X 時間に実行時間が最も長かったクエリを識別する
- リソースを待機している上位 N 件のクエリを特定する
- 特定のクエリの待機の性質を理解する

領域の使用量を最小限に抑えるために、ランタイム統計ストア内のランタイム実行統計は、固定の構成可能な時間枠で集計されます。 これらのストア内の情報は、クエリ ストアのビューに対してクエリを実行することで表示できます。

次のクエリでは、クエリ ストア内のクエリに関する情報が返されます。
```sql
SELECT * FROM query_store.qs_view; 
``` 

また、次のクエリは待機統計に関するものです。
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>待機クエリの検索
待機イベントの種類では、類似性によってさまざまな待機イベントがバケットに結合されます。 クエリ ストアでは、待機イベントの種類、特定の待機イベント名、対象のクエリが提供されます。 この待機情報をクエリのランタイム統計に関連付けられることは、クエリのパフォーマンス特性に何が寄与しているかをより深く理解できることを意味します。

クエリ ストア内の待機統計を使用してワークロードの詳細な分析情報を得る方法の例を次にいくつか示します。

| **観測** | **アクション** |
|---|---|
|ロック待機が長い | 影響を受けているクエリのクエリ テキストを確認し、ターゲット エンティティを識別します。 同じエンティティを変更する他のクエリのクエリ ストアで、頻繁に実行されているクエリ、実行時間が長いクエリ、あるいはその両方を探します。 これらのクエリを特定した後で、コンカレンシーを向上させるためにアプリケーション ロジックを変更するか、より制限の低い分離レベルを使用します。|
| バッファー IO 待機が長い | クエリ ストア内で物理読み取り回数が多いクエリを検索します。 それらと IO 待機が長いクエリが一致する場合は、スキャンではなくシークを実行するために、基になるエンティティへのインデックスの導入を検討します。 これにより、クエリの IO オーバーヘッドが最小限に抑えられます。 ポータル上でサーバーの **[パフォーマンスの推奨事項]** を調べて、このサーバーに対してクエリを最適化するインデックスの推奨事項があるかどうかを確認します。|
| メモリ待機が多い | クエリ ストア内で、メモリを最も消費しているクエリを探します。 おそらくこれらのクエリによって、影響を受けているクエリの進行がさらに遅れています。 ポータル上でサーバーの **[パフォーマンスの推奨事項]** を調べて、これらのクエリを最適化するインデックスの推奨事項があるかどうかを確認します。|

## <a name="configuration-options"></a>構成オプション
クエリ ストアが有効になっている場合、データは 15 分間の集計ウィンドウで保存され、ウィンドウあたり最大 500 件の個別のクエリが保存されます。 

次のオプションは、クエリ ストア パラメーターを構成するために使用できます。

| **パラメーター** | **説明** | **既定値** | **Range**|
|---|---|---|---|
| pg_qs.query_capture_mode | 追跡対象のステートメントを設定します。 | なし | none、top、all |
| pg_qs.max_query_text_length | 保存できるクエリの最大長を設定します。 これより長いクエリは切り詰められます。 | 6000 | 100 - 10K |
| pg_qs.retention_period_in_days | 保有期間を設定します。 | 7 | 1 - 30 |
| pg_qs.track_utility | ユーティリティ コマンドを追跡するかどうかを設定します | on | on、off |

待機統計には次のオプションが適用されます。

| **パラメーター** | **説明** | **既定値** | **Range**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | 待機統計の追跡対象のステートメントを設定します。 | なし | none、all|
| Pgms_wait_sampling.history_period | 待機イベントをサンプリングする頻度をミリ秒単位で設定します。 | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** は **pgms_wait_sampling.query_capture_mode** よりも優先されます。 pg_qs.query_capture_mode が NONE の場合、pgms_wait_sampling.query_capture_mode の設定は効果がありません。


パラメーターに対して別の値を取得または設定するには [Azure portal](howto-configure-server-parameters-using-portal.md) または [Azure CLI](howto-configure-server-parameters-using-cli.md) を使用します。

## <a name="views-and-functions"></a>ビューと関数
次のビューと関数を使用してクエリ ストアを表示および管理します。 PostgreSQL の public ロールに属するユーザーは、これらのビューを使用してクエリ ストア内のデータを表示できます。 これらのビューは、**azure_sys** データベース内でのみ使用できます。

クエリは、リテラルと定数を削除した後、その構造を調べることで正規化されます。 2 つのクエリがリテラル値を除いて同一の場合、それらは同じハッシュを持ちます。

### <a name="query_storeqs_view"></a>query_store.qs_view
このビューでは、クエリ ストア内のすべてのデータが返されます。 個別のデータベース ID、ユーザー ID、クエリ ID ごとに 1 つの行があります。 

|**Name**   |**Type** | **参照**  | **説明**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | runtime_stats_entries テーブルからの ID|
|user_id    |oid    |pg_authid.oid  |ステートメントを実行したユーザーの OID|
|db_id  |oid    |pg_database.oid    |ステートメントが実行されたデータベースの OID|
|query_id   |bigint  || ステートメントの解析ツリーから計算される内部ハッシュ コード|
|query_sql_text |Varchar(10000)  || 代表的なステートメントのテキスト。 同じ構造を持つ複数の異なるクエリがまとめてクラスター化されます。このテキストは、クラスター内の最初のクエリのテキストです。|
|plan_id    |bigint |   |まだ使用できない、このクエリに対応するプランの ID|
|start_time |timestamp  ||  クエリは、タイム バケットによって集計されます｡バケットの期間は既定で 15 分です。 これは、このエントリのタイム バケットに対応する開始時刻です。|
|end_time   |timestamp  ||  このエントリのタイム バケットに対応する終了時刻。|
|calls  |bigint  || クエリの実行回数|
|total_time |double precision   ||  クエリの合計実行時間 (ミリ秒)|
|min_time   |double precision   ||  クエリの最小実行時間 (ミリ秒)|
|max_time   |double precision   ||  クエリの最大実行時間 (ミリ秒)|
|mean_time  |double precision   ||  クエリの平均実行時間 (ミリ秒)|
|stddev_time|   double precision    ||  クエリ実行時間の標準偏差 (ミリ秒) |
|rows   |bigint ||  ステートメントによって取得または影響された行の合計数|
|shared_blks_hit|   bigint  ||  ステートメントによる共有ブロック キャッシュ ヒットの合計数|
|shared_blks_read|  bigint  ||  ステートメントによって読み取られた共有ブロックの合計数|
|shared_blks_dirtied|   bigint   || ステートメントによって使用された共有ブロックの合計数 |
|shared_blks_written|   bigint  ||  ステートメントによって書き込まれた共有ブロックの合計数|
|local_blks_hit|    bigint ||   ステートメントによるローカル ブロック キャッシュ ヒットの合計数|
|local_blks_read|   bigint   || ステートメントによって読み取られたローカル ブロックの合計数|
|local_blks_dirtied|    bigint  ||  ステートメンによって使用されたローカル ブロックの合計数|
|local_blks_written|    bigint  ||  ステートメントによって書き込まれたローカル ブロックの合計数|
|temp_blks_read |bigint  || ステートメントによって読み取られた一時ブロックの合計数|
|temp_blks_written| bigint   || ステートメントによって書き込まれた一時ブロックの合計数|
|blk_read_time  |double precision    || ステートメントによってブロックの読み取りに費やされた時間の合計 (ミリ秒単位) (track_io_timing が有効になっている場合。それ以外の場合は 0)|
|blk_write_time |double precision    || ステートメントによってブロックの書き込みに費やされた時間の合計 (ミリ秒単位) (track_io_timing が有効になっている場合。それ以外の場合は 0)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
このビューでは、クエリ ストア内のクエリ テキスト データが返されます。 個別の query_text ごとに 1 つの行があります。

|**Name**|  **種類**|   **説明**|
|---|---|---|
|query_text_id  |bigint     |query_texts テーブルの ID|
|query_sql_text |Varchar(10000)     |代表的なステートメントのテキスト。 同じ構造を持つ複数の異なるクエリがまとめてクラスター化されます。このテキストは、クラスター内の最初のクエリのテキストです。|

### <a name="query_storepgms_wait_sampling_view"></a>query_store.pgms_wait_sampling_view
このビューでは、クエリ ストア内の待機イベント データが返されます。 個別のデータベース ID、ユーザー ID、クエリ ID、イベントごとに 1 つの行があります。

|**Name**|  **Type**|   **参照**| **説明**|
|---|---|---|---|
|user_id    |oid    |pg_authid.oid  |ステートメントを実行したユーザーの OID|
|db_id  |oid    |pg_database.oid    |ステートメントが実行されたデータベースの OID|
|query_id   |bigint     ||ステートメントの解析ツリーから計算される内部ハッシュ コード|
|event_type |text       ||バックエンドによって待機されているイベントの種類|
|event  |text       ||バックエンドによって現在待機されている場合に、待機イベントの名前|
|calls  |整数        ||同じイベントがキャプチャされた回数|


### <a name="functions"></a>Functions
Query_store.qs_reset() returns void

`qs_reset`  では、クエリ ストアによってこれまでに収集されたすべての統計が破棄されます。 この関数は、サーバー管理者ロールによってのみ実行できます。

Query_store.staging_data_reset() returns void

`staging_data_reset`  では、クエリ ストアによってメモリ内で収集されたすべての統計 (つまり、データベースにまだフラッシュされていないメモリ内のデータ) が破棄されます。 この関数は、サーバー管理者ロールによってのみ実行できます。

## <a name="limitations-and-known-issues"></a>制限事項と既知の問題
- PostgreSQL サーバーのパラメーター default_transaction_read_only がオンの場合、クエリ ストアはデータをキャプチャできません。
- 時間がかかる Unicode クエリ (> = 6000 バイト) が発生した場合は、クエリ ストア機能の実行が中断されることがあります。
- [読み取りレプリカ](concepts-read-replicas.md)には、マスター サーバーからクエリ ストア データがレプリケートされます。 つまり、読み取りレプリカのクエリ ストアでは、読み取りレプリカで実行されるクエリに関する統計情報は提供されません。


## <a name="next-steps"></a>次の手順
- 詳細については、[クエリ ストアが特に役に立つシナリオ](concepts-query-store-scenarios.md)に関するページをご覧ください。
- 詳細については、[クエリ ストアの使用のベスト プラクティス](concepts-query-store-best-practices.md)に関するページをご覧ください。
