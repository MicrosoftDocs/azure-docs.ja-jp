---
title: Azure Database for MariaDB でサポートされているバージョン
description: Azure Database for MariaDB でサポートされているバージョンについて説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: e5d1dbc8c212d4cdefb12fb740a454324d3adfa1
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962917"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>サポートされている Azure Database for MariaDB サーバーのバージョン

Azure Database for MariaDB は、InnoDB エンジンを使用して、オープン ソースの [MariaDB Server](https://downloads.mariadb.org/) から開発されました。 

MariaDB では、X.Y.Z の名前付けスキームが使用されます。 X はメジャー バージョン、Y はマイナー バージョン、Z はパッチ バージョンです。

> [!NOTE]
> サービスでは、接続をサーバー インスタンスにリダイレクトするためにゲートウェイが使用されます。 接続が確立すると、MySQL クライアントには、MariaDB サーバー インスタンスで実行されている実際のバージョンではなく、ゲートウェイに設定されている MariaDB のバージョンが表示されます。 MariaDB サーバー インスタンスのバージョンを判断するには、`SELECT VERSION();` コマンドを使用します。

Azure Database for MariaDB では現在、次のバージョンがサポートされています。

## <a name="mariadb-version-102"></a>MariaDB バージョン 10.2

パッチ バージョン:10.2.25

このバージョンの機能強化と修正について詳しくは、[MariaDB のドキュメント](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/)を参照してください。

## <a name="mariadb-version-103"></a>MariaDB バージョン 10.3

パッチ バージョン:10.3.16

このバージョンの機能強化と修正について詳しくは、[MariaDB のドキュメント](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/)を参照してください。

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理
サービスはパッチの更新プログラムのアップグレードを自動的に管理します。 たとえば、10.2.21 から 10.2.23 などです。  

現在は、マイナー バージョンとメジャー バージョンのアップグレードはサポートされていません。 たとえば、MariaDB 10.2 から MariaDB 10.3 へのアップグレードはサポートされていません。 10.2 から 10.3 にアップグレードする場合は、新しいエンジンのバージョンで作成されたサーバーに[ダンプを復元](./howto-migrate-dump-restore.md)します。

## <a name="next-steps"></a>次の手順

- **サービス レベル**に基づく特定のリソース クォータと制限については、[サービス レベル](./concepts-pricing-tiers.md)に関するページをご覧ください。
