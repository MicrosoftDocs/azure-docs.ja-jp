---
title: 制限事項 - Azure Database for MariaDB
description: この記事では、Azure Database for MariaDB の制限 (接続数やストレージ エンジンのオプションなど) について説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 51aff856aa5bdeb042493d47f100be0ca32dfbbb
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88032681"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Azure Database for MariaDB の制限事項
以降のセクションでは、容量、ストレージ エンジンのサポート、権限のサポート、データ操作ステートメントのサポート、およびデータベース サービスの機能に関する制限事項について説明します。

## <a name="server-parameters"></a>サーバー パラメーター

> [!NOTE]
> `max_connections` や `innodb_buffer_pool_size` など、サーバー パラメーターの最小値と最大値を求めている場合、この情報は **[サーバー パラメーター](./concepts-server-parameters.md)** に関する記事に移動しました。

Azure Database for MariaDB では、サーバー パラメーターの値のチューニングがサポートされています。 一部のパラメーターの最小値と最大値 ( `max_connections`、`join_buffer_size`、`query_cache_size` など) は、サーバーの価格レベルと仮想コアによって決まります。 これらの制限の詳細については、[サーバー パラメーター](./concepts-server-parameters.md)に関する記事を参照してください。

初期デプロイの時点で、Azure for MariaDB サーバーにはタイム ゾーン情報のシステム テーブルが含まれていますが、これらのテーブルには値が設定されていません。 タイム ゾーン テーブルには、MySQL コマンド ラインや MySQL Workbench などのツールから `mysql.az_load_timezone` ストアド プロシージャを呼び出すことでデータを入力できます。 ストアド プロシージャを呼び出す方法とグローバル レベルまたはセッション レベルのタイム ゾーンを設定する方法については、[Azure portal](howto-server-parameters.md#working-with-the-time-zone-parameter) または [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) の記事を参照してください。

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
- システム データベース:Azure Database for MariaDB では、[mysql システム データベース](https://mariadb.com/kb/en/the-mysql-database-tables/)はさまざまな PaaS サービス機能をサポートするために使用されるので読み取り専用です。 `mysql` システム データベースの内容は変更できないことに注意してください。

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
