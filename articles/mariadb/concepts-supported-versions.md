---
title: Azure Database for MariaDB でサポートされているバージョン
description: Azure Database for MariaDB でサポートされているバージョンについて説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 760cb39ea1f3980faba348c7aa1de68a66b20a8d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065720"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>サポートされている Azure Database for MariaDB サーバーのバージョン

Azure Database for MariaDB は、InnoDB エンジンを使用して、オープン ソースの [MariaDB Server](https://downloads.mariadb.org/) から開発されました。 Azure Database for MariaDB では現在、次のバージョンがサポートされています。

## <a name="mariadb-version-102"></a>MariaDB バージョン 10.2

MariaDB 10.2.23 の機能強化と修正については、[MariaDB のドキュメント](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/)を参照してください。

## <a name="mariadb-version-103"></a>MariaDB バージョン 10.3

MariaDB 10.3.14 の機能強化と修正については、[MariaDB のドキュメント](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/)を参照してください。

> [!NOTE]
> サービスでは、接続をサーバー インスタンスにリダイレクトするためにゲートウェイが使用されます。 接続が確立すると、MySQL クライアントには、MariaDB サーバー インスタンスで実行されている実際のバージョンではなく、ゲートウェイに設定されている MariaDB のバージョンが表示されます。 MariaDB サーバー インスタンスのバージョンを判断するには、MySQL プロンプトで `SELECT VERSION();` コマンドを使用します。

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理

このサービスでは、マイナー バージョンの更新プログラムの適用が自動管理されます。

## <a name="next-steps"></a>次の手順

- **サービス レベル**に基づく特定のリソース クォータと制限については、[サービス レベル](./concepts-pricing-tiers.md)に関するページをご覧ください。
