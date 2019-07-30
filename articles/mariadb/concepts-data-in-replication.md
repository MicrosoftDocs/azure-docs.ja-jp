---
title: データを Azure Database for MariaDB にレプリケートする
description: この記事では、Azure Database for MariaDB のデータイン レプリケーションについて説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: c19ec06ce353d653086fa693dde975a55f51f823
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839249"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>データを Azure Database for MariaDB にレプリケートする

データイン レプリケーションでは、オンプレミス、仮想マシン、または他のクラウド プロバイダーによってホストされるデータベース サービスで実行中の MariaDB サーバーから Azure Database for MariaDB サービスにデータを同期することができます。 データイン レプリケーションは、MariaDB のネイティブなバイナリ ログ (binlog) ファイルの位置ベースのレプリケーションに基づいています。 binlog レプリケーションの詳細については、[binlog レプリケーションの概要](https://mariadb.com/kb/en/library/replication-overview/)に関する記事を参照してください。

## <a name="when-to-use-data-in-replication"></a>いつデータイン レプリケーションを使用するか
データイン レプリケーションの使用を検討する主なシナリオは次のとおりです。

- **ハイブリッド データ同期:** データイン レプリケーションを使用して、オンプレミス サーバーと Azure Database for MariaDB の間でデータの同期を維持できます。 この同期は、ハイブリッド アプリケーションを作成するのに役立ちます。 この方法は、既存のローカル データベース サーバーがあるが、エンドユーザーに近いリージョンにデータを移動する場合に魅力的です。
- **複数のクラウドの同期:** 複雑なクラウド ソリューションでは、データイン レプリケーションを使用して、Azure Database for MariaDB と各種のクラウド プロバイダーとの間でデータを同期します (これらのクラウドでホストされている仮想マシンとデータベース サービスが含まれます)。

## <a name="limitations-and-considerations"></a>制限と考慮事項

### <a name="data-not-replicated"></a>レプリケートされないデータ
マスター サーバー上の "[*mysql システム データベース*](https://mariadb.com/kb/en/library/the-mysql-database-tables/)" はレプリケートされません。 プライマリ サーバーでのアカウントとアクセス許可の変更はレプリケートされません。 マスター サーバーでアカウントを作成し、そのアカウントでレプリカ サーバーにアクセスする必要がある場合は、レプリカ サーバー側で同じアカウントを手動で作成します。 システム データベースに含まれているテーブルの詳細については、[MariaDB のドキュメント](https://mariadb.com/kb/en/library/the-mysql-database-tables/)を参照してください。

### <a name="requirements"></a>必要条件
- マスター サーバーのバージョンは、MariaDB バージョン 10.2 以上である必要があります。
- マスター サーバーとレプリカ サーバーのバージョンは同じである必要があります。 たとえば、どちらも MariaDB バージョン 10.2 にする必要があります。
- 各テーブルには主キーが必要です。
- マスター サーバーでは、InnoDB エンジンを使用する必要があります。
- ユーザーは、バイナリ ログの構成とマスター サーバーでの新しいユーザーの作成を実行できるアクセス許可を持っている必要があります。

### <a name="other"></a>その他
- データイン レプリケーションは、General Purpose 価格レベルとメモリ最適化価格レベルでのみサポートされます。

## <a name="next-steps"></a>次の手順
- [データイン レプリケーションを設定する](howto-data-in-replication.md)方法を確認する。
