---
title: サーバー パラメーター - Azure Database for MySQL
description: このトピックでは、Azure Database for MySQL でのサーバー パラメーターの構成に関するガイドラインを示します。
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/26/2021
ms.openlocfilehash: 756337ce20c827d0c6549181c20fd843fa60c020
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720955"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>Azure Database for MySQL でのサーバー パラメーター

この記事では、Azure Database for MySQL でサーバー パラメーターを構成するための考慮事項とガイドラインを示します。

## <a name="what-are-server-parameters"></a>サーバー パラメーターとは 

MySQL エンジンには、エンジンの動作を構成および調整するために使用できるさまざまなサーバー変数やパラメーターが用意されています。 実行時に動的に設定できるパラメーターもあれば、適用するためにサーバーを再起動する必要がある "静的" なものもあります。

Azure Database for MySQL では、さまざまな MySQL サーバー パラメーターの値を、[Azure portal](./howto-server-parameters.md)、[Azure CLI](./howto-configure-server-parameters-using-cli.md)、[PowerShell](./howto-configure-server-parameters-using-powershell.md) を使ってワークロードのニーズに合わせて変更できる機能が公開されています。

## <a name="configurable-server-parameters"></a>構成可能なサーバー パラメーター

サポートされるサーバー パラメーターの一覧は、拡大を続けています。 Azure portal の [サーバー パラメーター] タブを使用して完全な一覧を表示し、サーバー パラメーターの値を構成できます。

よく更新されるいくつかのサーバー パラメーターに関する制限の詳細については、以下のセクションを参照してください。 制限は、サーバーの価格レベルと仮想コアによって決まります。

### <a name="thread-pools"></a>スレッド プール

MySQL では、従来からすべてのクライアント接続にスレッドが割り当てられます。 同時実行ユーザーの数が増えると、それに応じてパフォーマンスが低下します。 アクティブなスレッドが多いと、コンテキスト切り替えの増加、スレッドの競合、CPU キャッシュの不適切な局所性によって、パフォーマンスが大幅に低下する可能性があります。

サーバー側の機能であり、接続プールとは別のものであるスレッド プールを使用すると、ワーカー スレッドの動的プール (サーバーで実行されているアクティブなスレッドの数を制限してスレッド チャーンを最小限に抑えるために使用できる) が導入されることによって、パフォーマンスが最大化されます。 これにより、接続のバーストによってサーバーのリソースが不足したり、メモリ不足エラーが発生してクラッシュしたりするのを防ぐことができます。 スレッド プールは、短いクエリと CPU 集中型のワークロード、たとえば OLTP ワークロードに対して最も効率的です。

スレッド プールの詳細については、「[Azure Database for MySQL でのスレッド プールの概要](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-thread-pools-in-azure-database-for-mysql-service/ba-p/1504173)」を参照してください。

> [!NOTE]
> スレッド プール機能は、MySQL 5.6 バージョンではサポートされていません。 

### <a name="configuring-the-thread-pool"></a>スレッド プールの構成
スレッド プールを有効にするには、`thread_handling` サーバー パラメーターを "pool-of-threads" に更新します。 既定では、このパラメーターは `one-thread-per-connection` に設定されています。つまり、MySQL で新しい接続ごとに新しいスレッドが作成されます。 これは静的パラメーターであるため、適用するにはサーバーを再起動する必要があることに注意してください。

次のサーバー パラメーターを設定して、プール内のスレッドの最大数と最小数を構成することもできます。 
- `thread_pool_max_threads`:この値により、プール内のスレッド数がこの数を超えないようにすることができます。
- `thread_pool_min_threads`:この値は、接続が閉じられた後も予約されるスレッドの数を設定します。

スレッド プールでの短いクエリのパフォーマンスの問題を改善するために、Azure Database for MySQL では、クエリの実行直後にスレッド プールに戻るのではなく、バッチ実行を有効にすることができます。そうするとスレッドは、この接続を介して次のクエリを待機するために短時間アクティブなままになります。 その後、スレッドでクエリを迅速に実行し、完了したら、このプロセス全体の消費時間がしきい値を超えるまで、次の処理を待機します。 バッチ実行の動作は、次のサーバー パラメーターを使用して決定されます。  

-  `thread_pool_batch_wait_timeout`:この値は、スレッドで別のクエリの処理を待機する時間を指定します。
- `thread_pool_batch_max_time`:この値は、スレッドでクエリ実行のサイクルを繰り返して次のクエリを待機する最大時間を決定します。

> [!IMPORTANT]
> 運用環境で有効にする前に、スレッド プールをテストしてください。 

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

Azure Database for MySQL の場合、バイナリ ログは常に有効になっています (つまり、`log_bin` が ON に設定されています)。 トリガーを使用したい場合、"*SUPER 特権を持っておらず、バイナリ ログが有効になっています (より安全度の低い `log_bin_trust_function_creators` 変数を使用することもできます)* " のようなエラーが表示されます。 

バイナリ ログ形式は常に **行** であり、サーバーへのすべての接続では **常に** 行ベースのバイナリ ログが使用されます。 行ベースのバイナリ ログでは、セキュリティ上の問題が存在せず、バイナリ ログを中断できないため、[`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) を安全に **TRUE** に設定できます。

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

このパラメーターの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size)を確認してください。

#### <a name="servers-supporting-up-to-4-tb-storage"></a>最大 4 TB のストレージをサポートするサーバー

|**価格レベル**|**仮想コア数**|**既定値 (バイト)**|**最小値 (バイト)**|**最大値 (バイト)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|General Purpose|2|3758096384|134217728|3758096384|
|General Purpose|4|8053063680|134217728|8053063680|
|General Purpose|8|16106127360|134217728|16106127360|
|General Purpose|16|32749125632|134217728|32749125632|
|General Purpose|32|66035122176|134217728|66035122176|
|General Purpose|64|132070244352|134217728|132070244352|
|メモリ最適化|2|7516192768|134217728|7516192768|
|メモリ最適化|4|16106127360|134217728|16106127360|
|メモリ最適化|8|32212254720|134217728|32212254720|
|メモリ最適化|16|65498251264|134217728|65498251264|
|メモリ最適化|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>サーバーは最大 16 TB のストレージをサポート

|**価格レベル**|**仮想コア数**|**既定値 (バイト)**|**最小値 (バイト)**|**最大値 (バイト)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|General Purpose|2|7516192768|134217728|7516192768|
|General Purpose|4|16106127360|134217728|16106127360|
|General Purpose|8|32212254720|134217728|32212254720|
|General Purpose|16|65498251264|134217728|65498251264|
|General Purpose|32|132070244352|134217728|132070244352|
|General Purpose|64|264140488704|134217728|264140488704|
|メモリ最適化|2|15032385536|134217728|15032385536|
|メモリ最適化|4|32212254720|134217728|32212254720|
|メモリ最適化|8|64424509440|134217728|64424509440|
|メモリ最適化|16|130996502528|134217728|130996502528|
|メモリ最適化|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table` は、価格レベルのうち、General Purpose と Memory Optimized でのみ更新できます。

MySQL では、テーブルの作成時に指定した構成に基づいて、InnoDB テーブルが異なるテーブルスペースに格納されます。 [システム テーブルスペース](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html)は、InnoDB データ辞書のストレージ領域です。 [file-per-table テーブルスペース](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html)は、1 つの InnoDB テーブルに対するデータとインデックスを含み、固有のデータ ファイル内のファイル システムに格納されています。 この動作は、`innodb_file_per_table` サーバー パラメーターによって制御されています。 `innodb_file_per_table` を `OFF` に設定すると、InnoDB ではシステム テーブルスペースにテーブルが作成されます。 それ以外の場合、InnoDB では file-per-table テーブルスペースにテーブルが作成されます。

Azure Database for MySQL は、1 つのデータ ファイルで、最大 **4 TB** までサポートします。 データベースのサイズが 4 TB を超える場合は、[innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) テーブルスペースにテーブルを作成する必要があります。 1 つのテーブル サイズが 4 TB を超える場合は、パーティション テーブルを使用する必要があります。

### <a name="join_buffer_size"></a>join_buffer_size

このパラメーターの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size)を確認してください。

|**価格レベル**|**仮想コア数**|**既定値 (バイト)**|**最小値 (バイト)**|**最大値 (バイト)**|
|---|---|---|---|---|
|Basic|1|Basic レベルでは構成できません|該当なし|該当なし|
|Basic|2|Basic レベルでは構成できません|該当なし|該当なし|
|General Purpose|2|262144|128|268435455|
|General Purpose|4|262144|128|536870912|
|General Purpose|8|262144|128|1073741824|
|General Purpose|16|262144|128|2147483648|
|General Purpose|32|262144|128|4294967295|
|General Purpose|64|262144|128|4294967295|
|メモリ最適化|2|262144|128|536870912|
|メモリ最適化|4|262144|128|1073741824|
|メモリ最適化|8|262144|128|2147483648|
|メモリ最適化|16|262144|128|4294967295|
|メモリ最適化|32|262144|128|4294967295|

### <a name="max_connections"></a>max_connections

|**価格レベル**|**仮想コア数**|**既定値**|**最小値**|**最大値**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|General Purpose|2|300|10|600|
|General Purpose|4|625|10|1250|
|General Purpose|8|1250|10|2500|
|General Purpose|16|2500|10|5000|
|General Purpose|32|5000|10|10000|
|General Purpose|64|10000|10|20000|
|メモリ最適化|2|625|10|1250|
|メモリ最適化|4|1250|10|2500|
|メモリ最適化|8|2500|10|5000|
|メモリ最適化|16|5000|10|10000|
|メモリ最適化|32|10000|10|20000|

接続数が制限を超えると、次のエラーが表示される場合があります。
> ERROR 1040 (08004):Too many connections (接続が多すぎます)

> [!IMPORTANT]
> 最適なエクスペリエンスを得るために、ProxySQL のような接続プーラーを使用して、接続を効率的に管理することをお勧めします。

MySQL への新しいクライアント接続を作成するには時間がかかり、一度確立されると、アイドル状態のときでも、これらの接続によってデータベース リソースが消費されます。 ほとんどのアプリケーションでは、短時間の接続を多数要求します。これにより、この状況が悪化します。 結果として、実際のワークロードに使用できるリソースが少なくなるため、パフォーマンスが低下します。 アイドル状態の接続を減らして既存の接続を再利用する接続プーラーは、これを回避するのに役立ちます。 ProxySQL の設定については、[ブログ投稿](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)を参照してください。

>[!Note]
>ProxySQL はオープンソースのコミュニティ ツールです。 Microsoft によってベスト エフォート方式でサポートされています。 信頼のあるガイダンスでの運用サポートを受けるには、[ProxySQL 製品サポート](https://proxysql.com/services/support/)を確認して問い合わせることができます。

### <a name="max_heap_table_size"></a>max_heap_table_size

このパラメーターの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size)を確認してください。

|**価格レベル**|**仮想コア数**|**既定値 (バイト)**|**最小値 (バイト)**|**最大値 (バイト)**|
|---|---|---|---|---|
|Basic|1|Basic レベルでは構成できません|該当なし|該当なし|
|Basic|2|Basic レベルでは構成できません|該当なし|該当なし|
|General Purpose|2|16777216|16384|268435455|
|General Purpose|4|16777216|16384|536870912|
|General Purpose|8|16777216|16384|1073741824|
|General Purpose|16|16777216|16384|2147483648|
|General Purpose|32|16777216|16384|4294967295|
|General Purpose|64|16777216|16384|4294967295|
|メモリ最適化|2|16777216|16384|536870912|
|メモリ最適化|4|16777216|16384|1073741824|
|メモリ最適化|8|16777216|16384|2147483648|
|メモリ最適化|16|16777216|16384|4294967295|
|メモリ最適化|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

クエリ キャッシュは、既定では無効になっています。 クエリ キャッシュを有効にするには、`query_cache_type` パラメーターを構成します。 

このパラメーターの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size)を確認してください。

> [!NOTE]
> クエリ キャッシュは MySQL 5.7.20 以降で非推奨とされ、MySQL 8.0 で削除されました

|**価格レベル**|**仮想コア数**|**既定値 (バイト)**|**最小値 (バイト)**|**最大値 **|
|---|---|---|---|---|
|Basic|1|Basic レベルでは構成できません|該当なし|該当なし|
|Basic|2|Basic レベルでは構成できません|該当なし|該当なし|
|General Purpose|2|0|0|16777216|
|General Purpose|4|0|0|33554432|
|General Purpose|8|0|0|67108864|
|General Purpose|16|0|0|134217728|
|General Purpose|32|0|0|134217728|
|General Purpose|64|0|0|134217728|
|メモリ最適化|2|0|0|33554432|
|メモリ最適化|4|0|0|67108864|
|メモリ最適化|8|0|0|134217728|
|メモリ最適化|16|0|0|134217728|
|メモリ最適化|32|0|0|134217728|

### <a name="lower_case_table_names"></a>lower_case_table_names

Lower_case_table_name は既定で 1 に設定されます。MySQL 5.6 および MySQL 5.7 ではこのパラメーターを更新できます

このパラメーターの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_lower_case_table_names)を確認してください。

> [!NOTE]
> MySQL 8.0 では、lower_case_table_name は既定で 1 に設定され、変更することはできません。

### <a name="innodb_strict_mode"></a>innodb_strict_mode

"Row size too large (> 8126) (行のサイズが大きすぎます (> 8126))" などのエラーが表示された場合は、**innodb_strict_mode** パラメーターをオフにすることができます。 サーバー パラメーター **innodb_strict_mode** をサーバー レベルでグローバルに変更することはできません。行データのサイズが 8 kb を超える場合、エラーが表示されずにデータが切り捨てられ、データが失われる可能性があるためです。 ページ サイズの制限に合うようにスキーマを変更することをお勧めします。 

このパラメーターは、`init_connect` を使用してセッション レベルで設定できます。 セッション レベルで **innodb_strict_mode** を設定するには、「[設定パラメーターが一覧に含まれていない](./howto-server-parameters.md#setting-parameters-not-listed)」を参照してください。

> [!NOTE]
> 読み取りレプリカ サーバーがある場合は、ソース サーバーのセッション レベルで **innodb_strict_mode** をオフに設定すると、レプリケーションが中断されます。 読み取りレプリカがある場合は、このパラメーターをオフに設定されたままにすることをお勧めします。

### <a name="sort_buffer_size"></a>sort_buffer_size

このパラメーターの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size)を確認してください。

|**価格レベル**|**仮想コア数**|**既定値 (バイト)**|**最小値 (バイト)**|**最大値 (バイト)**|
|---|---|---|---|---|
|Basic|1|Basic レベルでは構成できません|該当なし|該当なし|
|Basic|2|Basic レベルでは構成できません|該当なし|該当なし|
|General Purpose|2|524288|32768|4194304|
|General Purpose|4|524288|32768|8388608|
|General Purpose|8|524288|32768|16777216|
|General Purpose|16|524288|32768|33554432|
|General Purpose|32|524288|32768|33554432|
|General Purpose|64|524288|32768|33554432|
|メモリ最適化|2|524288|32768|8388608|
|メモリ最適化|4|524288|32768|16777216|
|メモリ最適化|8|524288|32768|33554432|
|メモリ最適化|16|524288|32768|33554432|
|メモリ最適化|32|524288|32768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

このパラメーターの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size)を確認してください。

|**価格レベル**|**仮想コア数**|**既定値 (バイト)**|**最小値 (バイト)**|**最大値 (バイト)**|
|---|---|---|---|---|
|Basic|1|Basic レベルでは構成できません|該当なし|該当なし|
|Basic|2|Basic レベルでは構成できません|該当なし|該当なし|
|General Purpose|2|16777216|1024|67108864|
|General Purpose|4|16777216|1024|134217728|
|General Purpose|8|16777216|1024|268435456|
|General Purpose|16|16777216|1024|536870912|
|General Purpose|32|16777216|1024|1073741824|
|General Purpose|64|16777216|1024|1073741824|
|メモリ最適化|2|16777216|1024|134217728|
|メモリ最適化|4|16777216|1024|268435456|
|メモリ最適化|8|16777216|1024|536870912|
|メモリ最適化|16|16777216|1024|1073741824|
|メモリ最適化|32|16777216|1024|1073741824|

### <a name="innodb-buffer-pool-warmup"></a>InnoDB バッファー プールのウォームアップ
Azure Database for MySQL サーバーを再起動した後、テーブルに対してクエリを実行すると、ディスクに存在するデータ ページが読み込まれます。 これにより、クエリを最初に実行したときの待機時間が長くなり、パフォーマンスが低下します。 これは、待機時間の影響を受けやすいワークロードでは許容できない場合があります。 InnoDB バッファー プールのウォームアップを利用すると、DML または SELECT の操作が対応する行にアクセスするのを待機するのではなく、再起動の前にバッファー プール内にあったディスク ページを再読み込みするため、ウォームアップ期間を短縮できます。

Azure Database for MySQL サーバーを再起動した後のウォームアップ期間を短縮できます。これは、[InnoDB バッファー プール サーバー パラメーター](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html)を構成すると得られるパフォーマンス上の利点です。 InnoDB は、サーバーのシャットダウン時に各バッファー プールで最近使用されたページを一定の割合で保存し、サーバーの起動時にこれらのページを復元します。

また、パフォーマンスの向上は、サーバーの起動時間が長くなるという代償を伴うことに注意してください。 このパラメーターを有効にすると、サーバーにプロビジョニングされている IOPS に応じて、サーバーの起動と再起動にかかる時間が長くなることが予想されます。 その間はサーバーを利用できないため、再起動時間をテストし監視して、起動/再起動のパフォーマンスが許容可能なものであることを確認することをお勧めします。 プロビジョニングされた IOPS が 1000 未満の場合 (つまり、プロビジョニングされたストレージが 335 GB 未満の場合) は、このパラメーターを使用しないことをお勧めします。

サーバーのシャットダウン時にバッファー プールの状態を保存するには、サーバー パラメーター `innodb_buffer_pool_dump_at_shutdown` を `ON` に設定します。 同様に、サーバー起動時にバッファー プールの状態を復元するには、サーバー パラメーター `innodb_buffer_pool_load_at_startup` を `ON` に設定します。 サーバー パラメーター `innodb_buffer_pool_dump_pct` の値を下げて微調整すると、起動/再起動時間への影響を制御できます。既定では、このパラメーターは `25` に設定されています。

> [!Note]
> InnoDB バッファー プールのウォームアップ パラメーターは、最大 16 TB のストレージを持つ汎用ストレージ サーバーでのみサポートされています。 Azure Database for MySQL のストレージ オプションの詳細については、[こちら](./concepts-pricing-tiers.md#storage)を参照してください。

### <a name="time_zone"></a>time_zone

初期デプロイの時点で、Azure for MySQL サーバーにはタイム ゾーン情報のシステム テーブルが含まれていますが、これらのテーブルには値が設定されていません。 タイム ゾーン テーブルには、MySQL コマンド ラインや MySQL Workbench などのツールから `mysql.az_load_timezone` ストアド プロシージャを呼び出すことでデータを入力できます。 ストアド プロシージャを呼び出す方法とグローバル レベルまたはセッション レベルのタイム ゾーンを設定する方法については、[Azure portal](howto-server-parameters.md#working-with-the-time-zone-parameter) または [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) の記事を参照してください。

## <a name="non-configurable-server-parameters"></a>構成不可能なサーバー パラメーター

次に示すサーバー パラメーターは、システム内で構成できません。

|**パラメーター**|**固定値**|
| :------------------------ | :-------- |
|Basic レベルの innodb_file_per_table|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

ここに記載されていないその他の変数は、既定の MySQL の標準値に設定されています。 既定値については、バージョン [8.0](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html)、[5.7](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)、[5.6](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html) の MySQL ドキュメントを参照してください。 

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用してサーバー パラメーターを構成する](./howto-server-parameters.md)方法を学習する
- [Azure CLI を使用してサーバー パラメーターを構成する](./howto-configure-server-parameters-using-cli.md)方法を学習する
- [PowerShell を使用してサーバー パラメーターを構成する](./howto-configure-server-parameters-using-powershell.md)方法を学習する