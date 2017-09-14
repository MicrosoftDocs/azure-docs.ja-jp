---
title: "Azure Database for PostgreSQL のサーバーの概念 | Microsoft Docs"
description: "このトピックでは、Azure Database for PostgreSQL サーバーを操作するための考慮事項とガイドラインを示します。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 07/06/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 43123486f21178a5ba657caa9d4b090922435913
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017

---
# <a name="azure-database-for-postgresql-servers"></a>Azure Database for PostgreSQL サーバー
このトピックでは、Azure Database for PostgreSQL サーバーを操作するための考慮事項とガイドラインを示します。

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーとは
Azure Database for PostgreSQL サーバーは、複数のデータベースの中央管理ポイントです。 これは、オンプレミスで一般的な PostgreSQL サーバー コンストラクトと同じです。 具体的には、PostgreSQL サービスが管理され、パフォーマンスが保証されるほか、アクセスと機能がサーバー レベルで公開されます。

Azure Database for PostgreSQL サーバーの特徴を次に示します。

- Azure サブスクリプション内で作成されます。
- データベースの親リソースです。
- データベースに名前空間を提供します。
- 強力な有効期間のセマンティクスが含まれるコンテナーです。サーバーを削除すると、包含データベースが削除されます。
- リージョンにリソースを併置します。
- サーバーおよびデータベース アクセスの接続エンドポイントを提供します (.postgresql.database.azure.com)。
- データベースに適用される管理ポリシーのスコープ (ログイン、ファイアウォール、ユーザー、ロール、構成など) を提供します。
- 複数のバージョンで使用できます。 詳細については、[サポートされる PostgreSQL データベース バージョン](concepts-supported-versions.md)に関するページをご覧ください。
- ユーザーが拡張できます。 詳細については、[PostgreSQL の拡張機能](concepts-extensions.md)に関するページをご覧ください。

Azure Database for PostgreSQL サーバー内では、1 つ以上のデータベースを作成できます。 サーバーごとに 1 つのデータベースを作成してすべてのリソースを利用するか、複数のデータベースを作成してリソースを共有することができます。 価格は、価格レベル、コンピューティング ユニット、ストレージ (GB) の構成に基づき、サービスごとに構造化されています。 詳細については、「[価格レベル](./concepts-service-tiers.md)」をご覧ください。

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーへの接続および認証方法
次の要素が、データベースへの安全なアクセスを確保するうえで役に立ちます。

|||
| :-- | :-- |
| **認証と権限承認** | Azure Database for PostgreSQL サーバーは、ネイティブ PostgreSQL 認証をサポートしています。 サーバーにはサーバーの管理者ログインで接続し、認証できます。 |
| **プロトコル** | サービスは、PostgreSQL で使用されるメッセージ ベースのプロトコルをサポートしています。 |
| **TCP/IP** | プロトコルは、TCP/IP および UNIX ドメイン ソケット経由でサポートされます。 |
| **ファイアウォール** | データを保護するため、ファイアウォール規則は、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーまたはそのデータベースへのすべてのアクセスを遮断します。 「[Azure Database for PostgreSQL サーバーのファイアウォール規則](concepts-firewall-rules.md)」を参照してください。 |
|||

## <a name="how-do-i-manage-a-server"></a>サーバーの管理方法
Azure Database for PostgreSQL サーバーを管理するには、Azure Portal または [Azure CLI](/cli/azure/postgres) を使用します。

## <a name="next-steps"></a>次のステップ
- サービスの概要については、[Azure Database for PostgreSQL の概要](overview.md)に関するページをご覧ください
- **サービス レベル**に基づく特定のリソース クォータと制限については、[サービス レベル](concepts-service-tiers.md)に関するページをご覧ください
- サービスへの接続については、「[Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)」を参照してください。

