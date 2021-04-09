---
title: 制限事項 - Azure Database for MySQL
description: この記事では、Azure Database for MySQL の制限 (接続数やストレージ エンジンのオプションなど) について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 9b18b24686908ac92f97ea0cae892369919ae4d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721022"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Azure Database for MySQL の制限事項
以降のセクションでは、容量、ストレージ エンジンのサポート、権限のサポート、データ操作ステートメントのサポート、およびデータベース サービスの機能に関する制限事項について説明します。 MySQL データベース エンジンに適用できる[一般的な制限事項](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html)も確認してください。

## <a name="server-parameters"></a>サーバー パラメーター

> [!NOTE]
> `max_connections` や `innodb_buffer_pool_size` など、サーバー パラメーターの最小値と最大値を求めている場合、この情報は **[サーバー パラメーター](./concepts-server-parameters.md)** に関する記事に移動しました。

Azure Database for MySQL では、サーバー パラメーターの値のチューニングがサポートされています。 一部のパラメーターの最小値と最大値 ( `max_connections`、`join_buffer_size`、`query_cache_size` など) は、サーバーの価格レベルと仮想コアによって決まります。 これらの制限の詳細については、[サーバー パラメーター](./concepts-server-parameters.md)に関する記事を参照してください。

初期デプロイの時点で、Azure for MySQL サーバーにはタイム ゾーン情報のシステム テーブルが含まれていますが、これらのテーブルには値が設定されていません。 タイム ゾーン テーブルには、MySQL コマンド ラインや MySQL Workbench などのツールから `mysql.az_load_timezone` ストアド プロシージャを呼び出すことでデータを入力できます。 ストアド プロシージャを呼び出す方法とグローバル レベルまたはセッション レベルのタイム ゾーンを設定する方法については、[Azure portal](howto-server-parameters.md#working-with-the-time-zone-parameter) または [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) の記事を参照してください。

"validate_password" や "caching_sha2_password" などのパスワード プラグインは、サービスではサポートされていません。

## <a name="storage-engines"></a>ストレージ エンジン

MySQL では多くのストレージ エンジンがサポートされています。 Azure Database for MySQL でサポートされている、およびサポートされていないストレージ エンジンは次のとおりです。

### <a name="supported"></a>サポートされています
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>サポートされていない
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>特権とデータ操作のサポート

多くのサーバー パラメーターおよび設定によって、誤ってサーバー パフォーマンスを低下させたり、MySQL サーバーの ACID プロパティを負数にしてしまったりする恐れがあります。 製品レベルでサービスの整合性と SLA を維持するため、このサービスでは複数のロールは公開されていません。 

MySQL サービスでは、基になるファイル システムに直接アクセスすることはできません。 一部のデータ操作コマンドはサポートされていません。 

### <a name="unsupported"></a>サポートされていない

次のものはサポートされていません。
- DBA ロール:制限付き。 または、管理者ユーザー (新しいサーバーの作成時に作成されます) を使用して、ほとんどの DDL ステートメントと DML ステートメントを実行できます。 
- SUPER 権限:同様に、[SUPER 権限](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super)は制限されています。
- DEFINER: 作成するには SUPER 権限が必要であり、制限されています。 バックアップを使用してデータをインポートする場合、mysqldump の実行時に `CREATE DEFINER` コマンドを手動で、または `--skip-definer` コマンドを使用して削除します。
- システム データベース:[mysql システム データベース](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)は読み取り専用であり、さまざまな PaaS 機能をサポートするために使用されます。 `mysql` システム データベースを変更することはできません。
- `SELECT ... INTO OUTFILE`:サービスではサポートされていません。
- `LOAD_FILE(file_name)`:サービスではサポートされていません。

### <a name="supported"></a>サポートされています
- `LOAD DATA INFILE` はサポートされていますが、`[LOCAL]` パラメーターで UNC パス (SMB を介してマウントされた Azure ストレージ) を指定する必要があります。

## <a name="functional-limitations"></a>機能制限

### <a name="scale-operations"></a>スケール操作
- Basic 価格レベルとの間の動的スケーリングは現在サポートされていません。
- サーバー ストレージを減らすことはできません。

### <a name="server-version-upgrades"></a>サーバー バージョンのアップグレード
- データベース エンジンのメジャー バージョン間での自動移行は現在サポートされていません。 次のメジャー バージョンにアップグレードする場合は、新しいエンジンのバージョンで作成されたサーバーに[ダンプを復元](./concepts-migrate-dump-restore.md)します。

### <a name="point-in-time-restore"></a>ポイントインタイム リストア
- PITR 機能を使うと、基になっているサーバーと同じ構成で新しいサーバーが作成されます。
- 削除されたサーバーへの復元はサポートされていません。

### <a name="vnet-service-endpoints"></a>VNet サービス エンドポイント
- VNet サービス エンドポイントは、汎用サーバーとメモリ最適化サーバーでのみサポートされています。

### <a name="storage-size"></a>ストレージ サイズ
- 価格レベルごとのストレージ サイズの上限については、[価格レベル](concepts-pricing-tiers.md)に関するページを参照してください。

## <a name="current-known-issues"></a>現時点での既知の問題
- MySQL サーバー インスタンスでは、接続が確立された後に不正確なサーバー バージョンが表示されます。 正確なサーバー インスタンス エンジンのバージョンを取得するには、`select version();` コマンドを使用します。

## <a name="next-steps"></a>次のステップ
- [各サービス レベルで使用できる内容について](concepts-pricing-tiers.md)
- [サポートされている MySQL Database バージョン](concepts-supported-versions.md)
