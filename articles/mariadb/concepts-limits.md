---
title: 制限事項 - Azure Database for MariaDB
description: この記事では、Azure Database for MariaDB の制限 (接続数やストレージ エンジンのオプションなど) について説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 18f227c1888e0565eebb640fa61ced56dc994865
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "80632334"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Azure Database for MariaDB の制限事項
以降のセクションでは、容量、ストレージ エンジンのサポート、権限のサポート、データ操作ステートメントのサポート、およびデータベース サービスの機能に関する制限事項について説明します。

## <a name="server-parameters"></a>サーバー パラメーター

いくつかの一般的なサーバー パラメーターの最小と最大の値は、価格レベルと仮想コアによって決まります。 制限については、以下の表を参照してください。

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
|メモリ最適化|2|600|10|800|
|メモリ最適化|4|1250|10|2500|
|メモリ最適化|8|2500|10|5000|
|メモリ最適化|16|5000|10|10000|
|メモリ最適化|32|10000|10|20000|

接続数が制限を超えると、次のエラーが表示される場合があります。
> ERROR 1040 (08004):Too many connections (接続が多すぎます)

> [!IMPORTANT]
> 最適なエクスペリエンスを得るために、ProxySQL のような接続プーラーを使用して、接続を効率的に管理することをお勧めします。

MariaDB への新しいクライアント接続を作成するには時間がかかり、一度確立されると、アイドル状態のときでも、これらの接続によってデータベース リソースが消費されます。 ほとんどのアプリケーションでは、短時間の接続を多数要求します。これにより、この状況が悪化します。 結果として、実際のワークロードに使用できるリソースが少なくなるため、パフォーマンスが低下します。 アイドル状態の接続を減らして既存の接続を再利用する接続プーラーは、これを回避するのに役立ちます。 ProxySQL の設定については、[ブログ投稿](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)を参照してください。

### <a name="query_cache_size"></a>query_cache_size

クエリ キャッシュは、既定では無効になっています。 クエリ キャッシュを有効にするには、`query_cache_type` パラメーターを構成します。 

このパラメーターの詳細については、[MariaDB のドキュメント](https://mariadb.com/kb/en/server-system-variables/#query_cache_size)を確認してください。

|**価格レベル**|**仮想コア数**|**既定値**|**最小値**|**最大値**|
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

|**価格レベル**|**仮想コア数**|**既定値**|**最小値**|**最大値**|
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

### <a name="join_buffer_size"></a>join_buffer_size

このパラメーターの詳細については、[MariaDB のドキュメント](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size)を確認してください。

|**価格レベル**|**仮想コア数**|**既定値**|**最小値**|**最大値**|
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

### <a name="max_heap_table_size"></a>max_heap_table_size

このパラメーターの詳細については、[MariaDB のドキュメント](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size)を確認してください。

|**価格レベル**|**仮想コア数**|**既定値**|**最小値**|**最大値**|
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

### <a name="tmp_table_size"></a>tmp_table_size

このパラメーターの詳細については、[MariaDB のドキュメント](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size)を確認してください。

|**価格レベル**|**仮想コア数**|**既定値**|**最小値**|**最大値**|
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

タイム ゾーン テーブルには、MySQL コマンド ラインや MySQL Workbench などのツールから `mysql.az_load_timezone` ストアド プロシージャを呼び出すことでデータを入力できます。 ストアド プロシージャを呼び出す方法とグローバル レベルまたはセッション レベルのタイム ゾーンを設定する方法については、[Azure portal](howto-server-parameters.md#working-with-the-time-zone-parameter) または [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) の記事を参照してください。

## <a name="storage-engine-support"></a>ストレージ エンジンのサポート

### <a name="supported"></a>サポートされています
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>サポートされていない
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHIVE](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>権限のサポート

### <a name="unsupported"></a>サポートされていない
- DBA ロール:多くのサーバー パラメーターおよび設定によって、誤ってサーバー パフォーマンスを低下させたり、DBMS の ACID プロパティを負数にしてしまったりする恐れがあります。 そのため、製品レベルのサービス整合性と SLA を維持するために、このサービスでは、DBA ロールを公開していません。 新しいデータベース インスタンスの作成時に構成される既定のユーザー アカウントによって、ユーザーは管理データベース インスタンスでほとんどの DDL および DML ステートメントを実行できます。
- SUPER 権限:同様に、[SUPER 権限](https://mariadb.com/kb/en/library/grant/#global-privileges)も制限されています。
- DEFINER: 作成するには SUPER 権限が必要であり、制限されています。 バックアップを使用してデータをインポートする場合、mysqldump の実行時に `CREATE DEFINER` コマンドを手動で、または `--skip-definer` コマンドを使用して削除します。

## <a name="data-manipulation-statement-support"></a>データ操作ステートメントのサポート

### <a name="supported"></a>サポートされています
- `LOAD DATA INFILE` はサポートされていますが、`[LOCAL]` パラメーターで UNC パス (SMB を介してマウントされた Azure ストレージ) を指定する必要があります。

### <a name="unsupported"></a>サポートされていない
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>機能制限

### <a name="scale-operations"></a>スケール操作
- Basic 価格レベルとの間の動的スケーリングは現在サポートされていません。
- サーバー ストレージを減らすことはできません。

### <a name="server-version-upgrades"></a>サーバー バージョンのアップグレード
- データベース エンジンのメジャー バージョン間での自動移行は現在サポートされていません。

### <a name="point-in-time-restore"></a>ポイントインタイム リストア
- PITR 機能を使うと、基になっているサーバーと同じ構成で新しいサーバーが作成されます。
- 削除されたサーバーへの復元はサポートされていません。

### <a name="subscription-management"></a>サブスクリプション管理
- サブスクリプションとリソース グループ間での事前作成されたサーバーの動的な移動は現在サポートされていません。

### <a name="vnet-service-endpoints"></a>VNet サービス エンドポイント
- VNet サービス エンドポイントは、汎用サーバーとメモリ最適化サーバーでのみサポートされています。

### <a name="storage-size"></a>ストレージ サイズ
- 価格レベルごとのストレージ サイズの上限については、[価格レベル](concepts-pricing-tiers.md)に関するページを参照してください。

## <a name="current-known-issues"></a>現時点での既知の問題
- MariaDB サーバー インスタンスでは、接続が確立された後に不正なサーバー バージョンが表示されます。 正確なサーバー インスタンス エンジンのバージョンを取得するには、`select version();` コマンドを使用します。

## <a name="next-steps"></a>次のステップ
- [各サービス レベルで使用できる内容について](concepts-pricing-tiers.md)
- [サポートされている MariaDB Database バージョン](concepts-supported-versions.md)
