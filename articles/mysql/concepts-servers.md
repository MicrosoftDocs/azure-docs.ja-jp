---
title: Azure Database for MySQL のサーバーの概念
description: このトピックでは、Azure Database for MySQL サーバーを操作するための考慮事項とガイドラインを示します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 565e1bf7a4972e230b3cf56232ebd24519fcab5c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58007889"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Azure Database for MySQL のサーバーの概念

この記事では、Azure Database for MySQL サーバーを操作するための考慮事項とガイドラインを示します。

## <a name="what-is-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーとは

Azure Database for MySQL サーバーは、複数のデータベースの中央管理ポイントです。 これは、オンプレミスで一般的な MySQL サーバー コンストラクトと同じです。 具体的には、Azure Database for MySQL サービスは管理され、パフォーマンスが保証されるほか、アクセスと機能がサーバー レベルで公開されます。

Azure Database for MySQL サーバーの特徴を次に示します。

- Azure サブスクリプション内で作成されます。
- データベースの親リソースです。
- データベースに名前空間を提供します。
- 強力な有効期間のセマンティクスが含まれるコンテナーです。サーバーを削除すると、包含データベースが削除されます。
- リージョンにリソースを併置します。
- サーバーおよびデータベース アクセスの接続エンドポイントを提供します。
- データベースに適用される管理ポリシーのスコープ (ログイン、ファイアウォール、ユーザー、ロール、構成など) を提供します。
- 複数のバージョンで使用できます。 詳細については、[サポートされる Azure Database for MySQL データベース バージョン](./concepts-supported-versions.md)に関するページをご覧ください。

Azure Database for MySQL サーバー内には 1 つまたは複数のデータベースを作成できます。 サーバーごとに 1 つのデータベースを作成してすべてのリソースを使用するか、複数のデータベースを作成してリソースを共有することができます。 価格は、価格レベル、仮想コア、ストレージ (GB) の構成に基づき、サーバーごとに構造化されています。 詳細については、「[価格レベル](./concepts-service-tiers.md)」をご覧ください。

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーへの接続および認証方法

次の要素が、データベースへの安全なアクセスを確保するうえで役に立ちます。

|     |     |
| :-- | :-- |
| **認証と権限承認** | Azure Database for MySQL サーバーは、ネイティブ MySQL 認証をサポートしています。 サーバーにはサーバーの管理者ログインで接続し、認証できます。 |
| **プロトコル** | サービスは、MySQL で使用されるメッセージ ベースのプロトコルをサポートしています。 |
| **TCP/IP** | プロトコルは、TCP/IP および UNIX ドメイン ソケット経由でサポートされます。 |
| **ファイアウォール** | データを保護するため、ファイアウォール規則は、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。 「[Azure Database for MySQL サーバーのファイアウォール規則](./concepts-firewall-rules.md)」を参照してください。 |
| **SSL** | アプリケーションとデータベース サーバーの間に SSL 接続を適用できます。  「[Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する](./howto-configure-ssl.md)」を参照してください。 |

## <a name="how-do-i-manage-a-server"></a>サーバーの管理方法

Azure Database for MySQL サーバーを管理するには、Azure Portal または Azure CLI を使用します。

## <a name="next-steps"></a>次の手順

- サービスの概要については、[Azure Database for MySQL の概要](./overview.md)に関するページをご覧ください
- **サービス レベル**に基づく特定のリソース クォータと制限については、[サービス レベル](./concepts-service-tiers.md)に関するページをご覧ください
- サービスへの接続については、「[Azure Database for MySQL の接続ライブラリ](./concepts-connection-libraries.md)」を参照してください。
