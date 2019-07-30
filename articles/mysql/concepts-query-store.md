---
title: Azure Database for MySQL のクエリ ストア
description: この記事では、Azure Database for MySQL のクエリ ストア機能について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: a24bba0786201f4ea1d1be431107f7bfe26a2a8f
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461715"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>クエリ ストアを使用した Azure Database for MySQL のパフォーマンスの監視

**適用対象:**   Azure Database for MySQL 5.7

> [!NOTE]
> クエリ ストアはプレビュー段階にあります。

Azure Database for MySQL のクエリ ストア機能を使用すると、クエリ パフォーマンスを経時的に追跡できます。 クエリ ストアを使用すると、実行時間が最長のクエリおよびリソースを最も消費しているクエリを迅速に特定できるので、パフォーマンスのトラブルシューティングが簡単になります。 クエリ ストアでは、クエリおよびランタイム統計の履歴が自動的にキャプチャされて保持されるので、それらを確認できます。 データベースの使用パターンを確認できるように、データが時間枠で区切られます。 すべてのユーザー、データベース、クエリに関するデータが、Azure Database for MySQL インスタンス内の **mysql** スキーマ データベースに格納されます。

## <a name="common-scenarios-for-using-query-store"></a>クエリ ストアを使用する一般的なシナリオ

クエリ ストアは、次のようなさまざまなシナリオで使用できます。

- 後退したクエリを検出する
- 指定された時間枠内にクエリが実行された回数を確認する
- 大きなデルタを確認するために時間枠間でクエリの平均実行回数を比較する

## <a name="enabling-query-store"></a>クエリ ストアの有効化

クエリ ストアはオプトイン機能なので、既定ではサーバー上でアクティブになりません。 クエリ ストアは特定のサーバー上のすべてのデータベースに対してグローバルに有効または無効になり、データベースごとに有効または無効にすることはできません。

### <a name="enable-query-store-using-the-azure-portal"></a>Azure portal を使用してクエリ ストアを有効にする

1. Azure portal にサインインし、ご利用の Azure Database for MySQL サーバーを選択します。
1. メニューの  **[設定]**   セクションで、 **[サーバー パラメーター]**   を選択します。
1. query_store_capture_mode パラメーターを検索します。
1. 値を "ALL" に設定し、 **保存**します。

クエリ ストアでの待機統計を有効にするには、次の手順に従います。

1. query_store_wait_sampling_capture_mode パラメーターを検索します。
1. 値を "ALL" に設定し、 **保存**します。

mysql データベースにデータの最初のバッチが保持されるまで最大 20 分かかります。

## <a name="information-in-query-store"></a>クエリ ストア内の情報

クエリ ストアには、次の 2 つのストアがあります。

- クエリ実行の統計情報を保持するためのランタイム統計ストア。
- 待機統計情報を保持するための待機統計ストア。

使用領域を最小限に抑えるために、ランタイム統計ストア内のランタイム実行統計は、構成可能な固定時間枠で集計されます。 これらのストア内の情報は、クエリ ストアのビューに対してクエリを実行することで表示できます。

次のクエリでは、クエリ ストア内のクエリに関する情報が返されます。

```sql
SELECT * FROM mysql.query_store;
```

次のクエリでは、待機統計情報が返されます。

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>待機クエリの検索

待機イベントの種類では、類似性によってさまざまな待機イベントがバケットに結合されます。 クエリ ストアでは、待機イベントの種類、特定の待機イベント名、対象のクエリが提供されます。 この待機情報をクエリのランタイム統計に関連付けられることは、クエリのパフォーマンス特性に何が寄与しているかをより深く理解できることを意味します。

クエリ ストア内の待機統計を使用してワークロードの詳細な分析情報を得る方法の例を次にいくつか示します。

| **観測** | **アクション** |
|---|---|
|ロック待機が長い | 影響を受けているクエリのクエリ テキストを確認し、ターゲット エンティティを識別します。 同じエンティティを変更する他のクエリのクエリ ストアで、頻繁に実行されているクエリ、実行時間が長いクエリ、あるいはその両方を探します。 これらのクエリを特定した後で、コンカレンシーを向上させるためにアプリケーション ロジックを変更するか、より制限の低い分離レベルを使用します。 |
|バッファー IO 待機が長い | クエリ ストア内で物理読み取り回数が多いクエリを検索します。 それらと IO 待機が長いクエリが一致する場合は、スキャンではなくシークを実行するために、基になるエンティティへのインデックスの導入を検討します。 これにより、クエリの IO オーバーヘッドが最小限に抑えられます。 ポータルでサーバーの  **[パフォーマンスの推奨事項]**   を調べて、このサーバーに対してクエリを最適化するインデックスの推奨事項があるかどうかを確認します。 |
|メモリ待機が多い | クエリ ストア内で、メモリを最も消費しているクエリを探します。 おそらくこれらのクエリによって、影響を受けているクエリの進行がさらに遅れています。 ポータルでサーバーの  **[パフォーマンスの推奨事項]**   を調べて、これらのクエリを最適化するインデックスの推奨事項があるかどうかを確認します。|

## <a name="configuration-options"></a>構成オプション

クエリ ストアが有効になっている場合、データは 15 分間の集計ウィンドウで保存され、ウィンドウあたり最大 500 件の個別のクエリが保存されます。

次のオプションは、クエリ ストア パラメーターを構成するために使用できます。

| **パラメーター** | **説明** | **既定値** | **Range** |
|---|---|---|---|
| query_store_capture_mode | この値に基づいて、クエリ ストア機能をオン/オフにします。 注:performance_schema がオフの場合、query_store_capture_mode をオンにすると、この機能に必要な performance_schema とパフォーマンス スキーマ インストルメントのサブセットが有効になります。 | ALL | NONE、ALL |
| query_store_capture_interval | クエリ ストアのキャプチャ間隔 (分単位)。 クエリ メトリックを集計する間隔を指定できます。 | 15 | 5 から 60 |
| query_store_capture_utility_queries | システムで実行されているすべてのユーティリティ クエリのキャプチャをオンまたはオフにします。 | NO | YES、NO |
| query_store_retention_period_in_days | クエリ ストアにデータを保持する時間枠 (日数)。 | 7 | 1 - 30 |

待機統計には次のオプションが適用されます。

| **パラメーター** | **説明** | **既定値** | **Range** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | 待機統計をオン/オフにすることができます。 | なし | NONE、ALL |
| query_store_wait_sampling_frequency | 待機サンプリングの頻度を秒単位で変更します。 5 から 300 秒です。 | 30 | 5 から 300 |

> [!NOTE]
> 現時点では、**query_store_capture_mode** がこの構成よりも優先されます。つまり、待機統計を機能させるには、**query_store_capture_mode** と **query_store_wait_sampling_capture_mode** の両方を ALL に設定して有効にする必要があります。 待機統計では、有効になっている performance_schema と、クエリ ストアによってキャプチャされた query_text を利用するため、**query_store_capture_mode** がオフになっている場合、待機統計もオフになります。

パラメーターの別の値を取得または設定するには、 [Azure portal](howto-server-parameters.md)  または  [Azure CLI](howto-configure-server-parameters-using-cli.md)  を使用します。

## <a name="views-and-functions"></a>ビューと関数

次のビューと関数を使用してクエリ ストアを表示および管理します。 [select privilege public ロール](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql)に属するユーザーは、これらのビューを使用してクエリ ストア内のデータを表示できます。 これらのビューは、**mysql**  データベース内でのみ使用できます。

クエリは、リテラルと定数を削除した後、その構造を調べることで正規化されます。 2 つのクエリがリテラル値を除いて同一の場合、それらは同じハッシュを持ちます。

### <a name="mysqlquerystore"></a>mysql.query_store

このビューでは、クエリ ストア内のすべてのデータが返されます。 個別のデータベース ID、ユーザー ID、クエリ ID ごとに 1 つの行があります。

| **Name** | **データ型** | **IS_NULLABLE** | **説明** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | スキーマの名前 |
| `query_id`| bigint(20) | NO| 特定のクエリに対して生成される一意の ID。同じクエリが異なるスキーマで実行されると、新しい ID が生成されます。 |
| `timestamp_id` | timestamp| NO| クエリが実行されたタイムスタンプ。 これは、query_store_interval の構成に基づきます。|
| `query_digest_text`| longtext| NO| すべてのリテラルを削除した後の正規化されたクエリ テキスト|
| `query_sample_text` | longtext| NO| リテラルを含む実際のクエリの最初の出現|
| `query_digest_truncated` | bit| はい| クエリ テキストが切り詰められたかどうか。 クエリが 1 KB を超えると、値は Yes になります。|
| `execution_count` | bigint(20)| NO| このタイムスタンプ ID で、または構成済みの間隔中にクエリが実行された回数|
| `warning_count` | bigint(20)| NO| この間隔中に、このクエリによって生成された警告の数|
| `error_count` | bigint(20)| NO| この間隔中に、このクエリによって生成されたエラーの数|
| `sum_timer_wait` | double| はい| この間隔中のこのクエリの合計実行時間|
| `avg_timer_wait` | double| はい| この間隔中のこのクエリの平均実行時間|
| `min_timer_wait` | double| はい| このクエリの最小実行時間|
| `max_timer_wait` | double| はい| 最大実行時間|
| `sum_lock_time` | bigint(20)| NO| この時間枠内に、このクエリ実行のすべてのロックに費やされた合計時間|
| `sum_rows_affected` | bigint(20)| NO| 影響を受けた行の数|
| `sum_rows_sent` | bigint(20)| NO| クライアントに送信された行の数|
| `sum_rows_examined` | bigint(20)| NO| 検査された行の数|
| `sum_select_full_join` | bigint(20)| NO| 完全結合の数|
| `sum_select_scan` | bigint(20)| NO| 選択スキャンの数 |
| `sum_sort_rows` | bigint(20)| NO| 並べ替えられた行の数|
| `sum_no_index_used` | bigint(20)| NO| クエリでインデックスが使用されなかった回数|
| `sum_no_good_index_used` | bigint(20)| NO| クエリ実行エンジンが有効なインデックスを使用しなかった回数|
| `sum_created_tmp_tables` | bigint(20)| NO| 作成された一時テーブルの総数|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| ディスクに作成された一時テーブルの総数 (I/O を生成)|
| `first_seen` | timestamp| NO| 集計の時間帯でのクエリの最初の発生 (UTC)|
| `last_seen` | timestamp| NO| 集計の時間帯でのクエリの最後の発生 (UTC)|

### <a name="mysqlquerystorewaitstats"></a>mysql.query_store_wait_stats

このビューでは、クエリ ストア内の待機イベント データが返されます。 個別のデータベース ID、ユーザー ID、クエリ ID、イベントごとに 1 つの行があります。

| **Name**| **データ型** | **IS_NULLABLE** | **説明** |
|---|---|---|---|
| `interval_start` | timestamp | NO| 間隔の開始 (15 分単位)|
| `interval_end` | timestamp | NO| 間隔の終了 (15 分単位)|
| `query_id` | bigint(20) | NO| 正規化されたクエリで生成された一意のID (クエリ ストアから)|
| `query_digest_id` | varchar(32) | NO| すべてのリテラルを削除した後の正規化されたクエリ テキスト (クエリ ストアから) |
| `query_digest_text` | longtext | NO| リテラルを含む実際のクエリの最初の出現 (クエリ ストアから) |
| `event_type` | varchar(32) | NO| 待機イベントのカテゴリ |
| `event_name` | varchar(128) | NO| 待機イベントの名前 |
| `count_star` | bigint(20) | NO| クエリの間隔中にサンプリングされた待機イベントの数 |
| `sum_timer_wait_ms` | double | NO| この間隔中のこのクエリの合計待機時間 (ミリ秒単位) |

### <a name="functions"></a>Functions

| **Name**| **説明** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | 指定されたタイム スタンプより前のクエリ ストア データをすべて消去します |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | 指定されたタイム スタンプより前の待機イベント データをすべて消去します |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | 有効期限が指定されたタイム スタンプより前である推奨事項を消去します |

## <a name="limitations-and-known-issues"></a>制限事項と既知の問題

- MySQL サーバーの `default_transaction_read_only` パラメーターがオンの場合、クエリ ストアはデータをキャプチャできません。
- 時間がかかる Unicode クエリ (\>= 6000 バイト) が発生した場合、クエリ ストア機能が中断されることがあります。
- 待機統計の保持期間は 24 時間です。
- 待機統計では、サンプリングを使用してイベントの一部をキャプチャします。 `query_store_wait_sampling_frequency` パラメーターを使用して頻度を変更できます。

## <a name="next-steps"></a>次の手順

- [Query Performance Insight](concepts-query-performance-insight.md) の詳細を確認する