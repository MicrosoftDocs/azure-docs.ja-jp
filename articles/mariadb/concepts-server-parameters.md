---
title: サーバー パラメーター - Azure Database for MariaDB
description: このトピックでは、Azure Database for MariaDB でのサーバー パラメーターの構成に関するガイドラインを示します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 7797ee9d20b33a25c1b51289036651c7ad9f22a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664148"
---
# <a name="server-parameters-in-azure-database-for-mariadb"></a>Azure Database for MariaDB でのサーバー パラメーター

この記事では、Azure Database for MariaDB でサーバー パラメーターを構成するための考慮事項とガイドラインを示します。

## <a name="what-are-server-parameters"></a>サーバー パラメーターとは 

MariaDB エンジンには、エンジンの動作を構成および調整するために使用できるさまざまなサーバー変数やパラメーターが用意されています。 実行時に動的に設定できるパラメーターもあれば、適用するためにサーバーを再起動する必要がある "静的" なものもあります。

Azure Database for MariaDB では、さまざまな MariaDB サーバー パラメーターの値を、[Azure portal](./howto-server-parameters.md)、[Azure CLI](./howto-configure-server-parameters-cli.md)、[PowerShell](./howto-configure-server-parameters-using-powershell.md) を使ってワークロードのニーズに合わせて変更できる機能が公開されています。

## <a name="configurable-server-parameters"></a>構成可能なサーバー パラメーター

サポートされるサーバー パラメーターの一覧は、拡大を続けています。 Azure portal の [サーバー パラメーター] タブを使用して完全な一覧を表示し、サーバー パラメーターの値を構成できます。

よく更新されるいくつかのサーバー パラメーターに関する制限の詳細については、以下のセクションを参照してください。 制限は、サーバーの価格レベルと仮想コアによって決まります。

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

Azure Database for MariaDB では、バイナリ ログは常に有効になっています (つまり、`log_bin` が ON に設定されています)。 トリガーを使用したい場合、"*SUPER 特権を持っておらず、バイナリ ログが有効になっています (より安全度の低い `log_bin_trust_function_creators` 変数を使用することもできます)"* のようなエラーが表示されます。

バイナリ ログ形式は常に **行** であり、サーバーへのすべての接続では **常に** 行ベースのバイナリ ログが使用されます。 行ベースのバイナリ ログでは、セキュリティ上の問題が存在せず、バイナリ ログを中断できないため、[`log_bin_trust_function_creators`](https://mariadb.com/docs/reference/mdb/system-variables/log_bin_trust_function_creators/) を安全に **TRUE** に設定できます。

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

このパラメーターの詳細については、[MariaDB のドキュメント](https://mariadb.com/kb/en/innodb-system-variables/#innodb_buffer_pool_size)を確認してください。

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

MariaDB では、テーブルの作成時に指定した構成に基づいて、InnoDB テーブルが異なるテーブルスペースに格納されます。 [システム テーブルスペース](https://mariadb.com/kb/en/innodb-system-tablespaces/)は、InnoDB データ辞書のストレージ領域です。 [file-per-table テーブルスペース](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/)は、1 つの InnoDB テーブルに対するデータとインデックスを含み、固有のデータ ファイル内のファイル システムに格納されています。 この動作は、`innodb_file_per_table` サーバー パラメーターによって制御されています。 `innodb_file_per_table` を `OFF` に設定すると、InnoDB ではシステム テーブルスペースにテーブルが作成されます。 それ以外の場合、InnoDB では file-per-table テーブルスペースにテーブルが作成されます。

Azure Database for MariaDB は 1 つのデータ ファイル内で、最大 **1 TB** までサポートされています。 データベースのサイズが 1 TB を超える場合は、[innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table) テーブルスペースにテーブルを作成する必要があります。 1 つのテーブル サイズが 1 TB を超える場合は、パーティション テーブルを使用する必要があります。

### <a name="join_buffer_size"></a>join_buffer_size

このパラメーターの詳細については、[MariaDB のドキュメント](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size)を確認してください。

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

MariaDB への新しいクライアント接続を作成するには時間がかかり、一度確立されると、アイドル状態のときでも、これらの接続によってデータベース リソースが消費されます。 ほとんどのアプリケーションでは、短時間の接続を多数要求します。これにより、この状況が悪化します。 結果として、実際のワークロードに使用できるリソースが少なくなるため、パフォーマンスが低下します。 アイドル状態の接続を減らして既存の接続を再利用する接続プーラーは、これを回避するのに役立ちます。 ProxySQL の設定については、[ブログ投稿](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)を参照してください。

>[!Note]
>ProxySQL はオープンソースのコミュニティ ツールです。 Microsoft によってベスト エフォート方式でサポートされています。 信頼のあるガイダンスでの運用サポートを受けるには、[ProxySQL 製品サポート](https://proxysql.com/services/support/)を確認して問い合わせることができます。

### <a name="max_heap_table_size"></a>max_heap_table_size

このパラメーターの詳細については、[MariaDB のドキュメント](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size)を確認してください。

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

クエリ キャッシュは、MariaDB では、`have_query_cache` パラメーターを使用して既定で有効になっています。 

このパラメーターの詳細については、[MariaDB のドキュメント](https://mariadb.com/kb/en/server-system-variables/#query_cache_size)を確認してください。

|**価格レベル**|**仮想コア数**|**既定値 (バイト)**|**最小値 (バイト)**|**最大値 (バイト)**|
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

### <a name="sort_buffer_size"></a>sort_buffer_size

このパラメーターの詳細については、[MariaDB のドキュメント](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size)を確認してください。

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

このパラメーターの詳細については、[MariaDB のドキュメント](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size)を確認してください。

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

### <a name="time_zone"></a>time_zone

初期デプロイの時点で、Azure for MariaDB サーバーにはタイム ゾーン情報のシステム テーブルが含まれていますが、これらのテーブルには値が設定されていません。 タイム ゾーン テーブルには、MySQL コマンド ラインや MySQL Workbench などのツールから `mysql.az_load_timezone` ストアド プロシージャを呼び出すことでデータを入力できます。 ストアド プロシージャを呼び出す方法とグローバル レベルまたはセッション レベルのタイム ゾーンを設定する方法については、[Azure portal](howto-server-parameters.md#working-with-the-time-zone-parameter) または [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) の記事を参照してください。

## <a name="non-configurable-server-parameters"></a>構成不可能なサーバー パラメーター

次に示すサーバー パラメーターは、システム内で構成できません。

|**パラメーター**|**固定値**|
| :------------------------ | :-------- |
|Basic レベルの innodb_file_per_table|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

ここに記載されていないその他のサーバー パラメーターはすべて、[MariaDB](https://mariadb.com/kb/en/server-system-variables/) に対する MariaDB の初期既定値に設定されます。

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用してサーバー パラメーターを構成する](./howto-server-parameters.md)方法を学習する
- [Azure CLI を使用してサーバー パラメーターを構成する](./howto-configure-server-parameters-cli.md)方法を学習する
- [PowerShell を使用してサーバー パラメーターを構成する](./howto-configure-server-parameters-using-powershell.md)方法を学習する
