---
title: 制限 - Azure Database for MySQL - フレキシブル サーバー
description: この記事では、接続数やストレージ エンジンのオプションなど、Azure Database for MySQL - フレキシブル サーバーでの制限について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: a3abde9092519be057dcd73ec63318f970fd5c74
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94543087"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL - フレキシブル サーバー (プレビュー) での制限

> [!IMPORTANT] 
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

この記事では、Azure Database for MySQL フレキシブル サーバー サービスで制限事項について説明します。 MySQL データベース エンジンでの[一般的な制限事項](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html)も適用されます。 リソース (コンピューティング、メモリ、ストレージ) レベルの詳細については、[コンピューティングとストレージ](concepts-compute-storage.md)の記事を参照してください。

## <a name="server-parameters"></a>サーバー パラメーター

> [!NOTE]
> `max_connections` や `innodb_buffer_pool_size` など、サーバー パラメーターの最小/最大値を求めている場合、この情報はサーバー パラメーターの概念 ([サーバー パラメーター](./concepts-server-parameters.md)に関する記事) に移動しました。

Azure Database for MySQL では、サーバー パラメーターの値のチューニングがサポートされています。 一部のパラメーターの最小値と最大値 ( `max_connections`、`join_buffer_size`、`query_cache_size` など) は、サーバーのコンピューティング レベルとコンピューティング サイズによって決まります。 これらの制限の詳細については、[サーバー パラメーター](./concepts-server-parameters.md)に関する記事を参照してください。

"validate_password" や "caching_sha2_password" などのパスワード プラグインは、サービスではサポートされていません。

## <a name="storage-engines"></a>ストレージ エンジン

MySQL では多くのストレージ エンジンがサポートされています。 Azure Database for MySQL フレキシブル サーバーでサポートされている、およびサポートされていないストレージ エンジンは次のとおりです。

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

### <a name="supported"></a>サポートされています
- `LOAD DATA INFILE` はサポートされていますが、`[LOCAL]` パラメーターで UNC パス (SMB を介してマウントされた Azure ストレージ) を指定する必要があります。

## <a name="functional-limitations"></a>機能制限

### <a name="zone-redundant-ha"></a>ゾーン冗長 HA
- この構成は、サーバーの作成時にのみ設定できます。
- バースト可能なコンピューティング レベルではサポートされていません。

### <a name="networking"></a>ネットワーキング
- サーバーの作成後に接続方法を変更することはできません。 サーバーが "*プライベート アクセス (VNet 統合)* " で作成されている場合、作成後に "*パブリック アクセス (許可された IP アドレス)* " に変更することはできません。逆も同様です。
- TLS/SSL は既定で有効になっており、無効にすることはできません。
- サーバーでサポートされている TLS の最小バージョンは TLS 1.2 です。 詳細については、[TLS/SSL を使用した接続](./how-to-connect-tls-ssl.md)に関するページを参照してください。

### <a name="stopstart-operation"></a>停止/開始操作
- ゾーン冗長 HA 構成 (プライマリとスタンバイの両方) ではサポートされていません。
- 読み取りレプリカ構成 (ソースとレプリカの両方) ではサポートされていません。

### <a name="scale-operations"></a>スケール操作
- プロビジョニング済みのサーバー ストレージを減らすことはできません。

### <a name="read-replicas"></a>読み取りレプリカ
- ゾーン冗長 HA 構成 (プライマリとスタンバイの両方) ではサポートされていません。

### <a name="server-version-upgrades"></a>サーバー バージョンのアップグレード
- データベース エンジンのメジャー バージョン間での自動移行はサポートされていません。 メジャー バージョンをアップグレードする場合は、新しいエンジンのバージョンで作成されたサーバーに[ダンプを復元](../concepts-migrate-dump-restore.md)します。

### <a name="restoring-a-server"></a>サーバーの復元
- ポイントインタイム リストアでは、基になるソース サーバーと同じコンピューティング構成とストレージ構成で、新しいサーバーが作成されます。 サーバーの作成後に、新しく復元されたサーバーのコンピューティングをスケールダウンできます。
- 削除されたサーバーの復元はサポートされていません。

## <a name="next-steps"></a>次のステップ

- [使用できるコンピューティング オプションとストレージ オプション](concepts-compute-storage.md)について理解する
- [サポートされている MySQL のバージョンについて](concepts-supported-versions.md)学習する
- [Azure portal を使用してサーバーのバックアップと復元を行う方法](how-to-restore-server-portal.md)を確認する