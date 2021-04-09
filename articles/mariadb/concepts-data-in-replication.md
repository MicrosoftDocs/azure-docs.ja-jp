---
title: データイン レプリケーション - Azure Database for MariaDB
description: データイン レプリケーションを使用して、外部のサーバーから Azure Database for MariaDB サービスに同期する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 331e064bcf11af31a778cb8dd06c463712421b7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662629"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>データを Azure Database for MariaDB にレプリケートする

データイン レプリケーションでは、オンプレミス、仮想マシン、または他のクラウド プロバイダーによってホストされるデータベース サービスで実行中の MariaDB サーバーから Azure Database for MariaDB サービスにデータを同期することができます。 データイン レプリケーションは、MariaDB のネイティブなバイナリ ログ (binlog) ファイルの位置ベースのレプリケーションに基づいています。 binlog レプリケーションの詳細については、[binlog レプリケーションの概要](https://mariadb.com/kb/en/library/replication-overview/)に関する記事を参照してください。

## <a name="when-to-use-data-in-replication"></a>いつデータイン レプリケーションを使用するか
データイン レプリケーションの使用を検討する主なシナリオは次のとおりです。

- **ハイブリッド データ同期:** データイン レプリケーションを使用して、オンプレミス サーバーと Azure Database for MariaDB の間でデータの同期を維持できます。 この同期は、ハイブリッド アプリケーションを作成するのに役立ちます。 この方法は、既存のローカル データベース サーバーがあるが、エンドユーザーに近いリージョンにデータを移動する場合に魅力的です。
- **複数のクラウドの同期:** 複雑なクラウド ソリューションでは、データイン レプリケーションを使用して、Azure Database for MariaDB と各種のクラウド プロバイダーとの間でデータを同期します (これらのクラウドでホストされている仮想マシンとデータベース サービスが含まれます)。

## <a name="limitations-and-considerations"></a>制限と考慮事項

### <a name="data-not-replicated"></a>レプリケートされないデータ
ソース サーバー上の "[*mysql システム データベース*](https://mariadb.com/kb/en/library/the-mysql-database-tables/)" はレプリケートされません。 ソース サーバーでのアカウントとアクセス許可の変更はレプリケートされません。 ソース サーバーでアカウントを作成し、そのアカウントでレプリカ サーバーにアクセスする必要がある場合は、レプリカ サーバー側で同じアカウントを手動で作成します。 システム データベースに含まれているテーブルの詳細については、[MariaDB のドキュメント](https://mariadb.com/kb/en/library/the-mysql-database-tables/)を参照してください。

### <a name="requirements"></a>必要条件
- ソース サーバーのバージョンは、MariaDB バージョン 10.2 以上である必要があります。
- ソース サーバーとレプリカ サーバーのバージョンは同じである必要があります。 たとえば、どちらも MariaDB バージョン 10.2 にする必要があります。
- 各テーブルには主キーが必要です。
- ソース サーバーでは、InnoDB エンジンを使用する必要があります。
- ユーザーは、バイナリ ログの構成とソース サーバーでの新しいユーザーの作成を実行できるアクセス許可を持っている必要があります。
- ソース サーバーで SSL が有効になっている場合は、ドメインに対して指定されている SSL CA 証明書が `mariadb.az_replication_change_master` ストアド プロシージャに含まれていることを確認します。 次の[例](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication)と `master_ssl_ca` パラメーターを参照してください。
- ソース サーバーの IP アドレスが Azure Database for MariaDB レプリカ サーバーのファイアウォール規則に確実に追加されているようにします。 [Azure portal](howto-manage-firewall-portal.md) または [Azure CLI](howto-manage-firewall-cli.md) を使用してファイアウォール規則を更新します。
- ソース サーバーをホストしているコンピューターで、ポート 3306 の受信トラフィックと送信トラフィックの両方が確実に許可されているようにします。
- ソース サーバーに **パブリック IP アドレス** があるか、DNS がパブリックにアクセスできるか、または完全修飾ドメイン名 (FQDN) を持っているようにしてください。

### <a name="other"></a>その他
- データイン レプリケーションは、General Purpose 価格レベルとメモリ最適化価格レベルでのみサポートされます。

## <a name="next-steps"></a>次の手順
- [データイン レプリケーションを設定する](howto-data-in-replication.md)方法を確認する。
