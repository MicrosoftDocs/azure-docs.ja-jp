---
title: Azure Database for MariaDB でサポートされているバージョン
description: Azure Database for MariaDB でサポートされているバージョンについて説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 69330e9d5a05fbcc892889f70a04f5eb4a4a2fb9
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53541423"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>サポートされている Azure Database for MariaDB サーバーのバージョン
Azure Database for MariaDB は、InnoDB エンジンを使用して、オープン ソースの [MariaDB Server](https://downloads.mariadb.org/) から開発されました。 Azure Database for MariaDB では現在、次のバージョンがサポートされています。

## <a name="mariadb-version-10217"></a>MariaDB Version 10.2.17
MariaDB 10.2.17 の機能強化と修正については、[MariaDB のドキュメント](https://downloads.mariadb.org/mariadb/10.2.17/)を参照してください。

> [!NOTE]
> サービスでは、接続をサーバー インスタンスにリダイレクトするためにゲートウェイが使用されます。 接続が確立すると、MySQL クライアントには、MariaDB サーバー インスタンスで実行されている実際のバージョンではなく、ゲートウェイに設定されている MariaDB のバージョンが表示されます。 MariaDB サーバー インスタンスのバージョンを判断するには、MySQL プロンプトで `SELECT VERSION();` コマンドを使用します。

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理
このサービスでは、マイナー バージョンの更新プログラムの適用が自動管理されます。

## <a name="next-steps"></a>次の手順
**サービス レベル**に基づく特定のリソース クォータと制限については、[サービス レベル](./concepts-pricing-tiers.md)に関するページをご覧ください
