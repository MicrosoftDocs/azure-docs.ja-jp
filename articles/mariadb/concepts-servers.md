---
title: Azure Database for MariaDB のサーバーの概念
description: このトピックでは、Azure Database for MariaDB サーバーを操作するための考慮事項とガイドラインを示します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f61f8740c9514f6276afb2ee84bcdccdc54c0710
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57997903"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Azure Database for MariaDB のサーバーの概念
この記事では、Azure Database for MariaDB サーバーを操作するための考慮事項とガイドラインを示します。

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB サーバーとは

Azure Database for MariaDB サーバーは、複数のデータベースの中央管理ポイントです。 これは、オンプレミスで一般的な MariaDB サーバー コンストラクトと同じです。 具体的には、Azure Database for MariaDB サービスは管理され、パフォーマンスが保証されるほか、アクセスと機能がサーバー レベルで公開されます。

Azure Database for MariaDB サーバー:

- Azure サブスクリプション内で作成されます。
- データベースの親リソースです。
- データベースに名前空間を提供します。
- 強力な有効期間のセマンティクスが含まれるコンテナーです。サーバーを削除すると、包含データベースが削除されます。
- リージョンにリソースを併置します。
- サーバーおよびデータベース アクセスの接続エンドポイントを提供します。
- データベースに適用される管理ポリシーのスコープ (ログイン、ファイアウォール、ユーザー、ロール、構成など) を提供します。
- MariaDB エンジン バージョン 10.2 で使用できます。 詳細については、[サポートされる Azure Database for MariaDB データベース バージョン](./concepts-supported-versions.md)に関するページをご覧ください。

Azure Database for MariaDB サーバー内には 1 つまたは複数のデータベースを作成できます。 サーバーごとに 1 つのデータベースを作成してすべてのリソースを使用するか、複数のデータベースを作成してリソースを共有することができます。 価格は、価格レベル、仮想コア、ストレージ (GB) の構成に基づき、サーバーごとに構造化されています。 詳細については、「[価格レベル](./concepts-pricing-tiers.md)」をご覧ください。

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB サーバーをセキュリティ保護する方法

次の要素が、データベースへの安全なアクセスを確保するうえで役に立ちます。

|||
| :--| :--|
| **認証と権限承認** | Azure Database for MariaDB サーバーは、ネイティブ MySQL 認証をサポートしています。 サーバーにはサーバーの管理者ログインで接続し、認証できます。 |
| **プロトコル** | サービスは、MySQL で使用されるメッセージ ベースのプロトコルをサポートしています。 |
| **TCP/IP** | プロトコルは、TCP/IP および UNIX ドメイン ソケット経由でサポートされます。 |
| **ファイアウォール** | データを保護するため、ファイアウォール規則は、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。 「[Azure Database for MariaDB サーバーのファイアウォール規則](./concepts-firewall-rules.md)」をご覧ください。 |
| **SSL** | アプリケーションとデータベース サーバーの間に SSL 接続を適用できます。 「[Azure Database for MariaDB に安全に接続するためにご利用のアプリケーション内で SSL 接続を構成する](./howto-configure-ssl.md)」をご覧ください。 |

## <a name="how-do-i-manage-a-server"></a>サーバーの管理方法
Azure Database for MariaDB サーバーを管理するには、Azure portal または Azure CLI を使用します。

## <a name="next-steps"></a>次の手順
- サービスの概要については、[Azure Database for MariaDB の概要](./overview.md)に関するページをご覧ください。
- **サービス レベル**に基づく特定のリソース クォータと制限については、[サービス レベル](./concepts-pricing-tiers.md)に関するページをご覧ください

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
