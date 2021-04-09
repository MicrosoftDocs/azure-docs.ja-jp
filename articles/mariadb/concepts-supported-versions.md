---
title: サポートされているバージョン - Azure Database for MariaDB
description: Azure Database for MariaDB サービスでサポートされている MariaDB サーバーのバージョンについて説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: dbf32626714200e6712c67b701ebc597c4a7ba7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662442"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>サポートされている Azure Database for MariaDB サーバーのバージョン

Azure Database for MariaDB は、InnoDB エンジンを使用して、オープン ソースの [MariaDB Server](https://downloads.mariadb.org/) から開発されました。

MariaDB では、X.Y.Z の名前付けスキームが使用されます。 X はメジャー バージョン、Y はマイナー バージョン、Z はパッチ バージョンです。

> [!NOTE]
> サービスでは、接続をサーバー インスタンスにリダイレクトするためにゲートウェイが使用されます。 接続が確立すると、MySQL クライアントには、MariaDB サーバー インスタンスで実行されている実際のバージョンではなく、ゲートウェイに設定されている MariaDB のバージョンが表示されます。 MariaDB サーバー インスタンスのバージョンを判断するには、`SELECT VERSION();` コマンドを使用します。

Azure Database for MariaDB では現在、次のバージョンがサポートされています。

## <a name="mariadb-version-102"></a>MariaDB バージョン 10.2

パッチ バージョン:10.2.32

このバージョンの機能強化と修正について詳しくは、[MariaDB のドキュメント](https://mariadb.com/kb/en/mariadb-10232-release-notes/)を参照してください。

## <a name="mariadb-version-103"></a>MariaDB バージョン 10.3

パッチ バージョン:10.3.23

このバージョンの機能強化と修正について詳しくは、[MariaDB のドキュメント](https://mariadb.com/kb/en/mariadb-10323-release-notes/)を参照してください。

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理
サービスはパッチの更新プログラムのアップグレードを自動的に管理します。 たとえば、10.2.21 から 10.2.23 などです。  

現在は、マイナー バージョンとメジャー バージョンのアップグレードはサポートされていません。 たとえば、MariaDB 10.2 から MariaDB 10.3 へのアップグレードはサポートされていません。 10.2 から 10.3 にアップグレードする場合は、新しいエンジンのバージョンで作成されたサーバーに[ダンプを復元](./howto-migrate-dump-restore.md)します。

## <a name="next-steps"></a>次のステップ

- **サービス レベル** に基づく特定のリソース クォータと制限については、[サービス レベル](./concepts-pricing-tiers.md)に関するページをご覧ください。
