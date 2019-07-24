---
title: Azure Database for PostgreSQL のサーバーの概念
description: この記事では、Azure Database for PostgreSQL サーバーを構成および管理するための考慮事項とガイドラインを示します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: a5b6db9f4bfbe9a11119270e9236580b41fae8c7
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540864"
---
# <a name="azure-database-for-postgresql-servers"></a>Azure Database for PostgreSQL サーバー
この記事では、Azure Database for PostgreSQL サーバーを操作するための考慮事項とガイドラインを示します。

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーとは
Azure Database for PostgreSQL サーバーは、複数のデータベースの中央管理ポイントです。 これは、オンプレミスで一般的な PostgreSQL サーバー コンストラクトと同じです。 具体的には、PostgreSQL サービスが管理され、パフォーマンスが保証されるほか、アクセスと機能がサーバー レベルで公開されます。

Azure Database for PostgreSQL サーバーの特徴を次に示します。

- Azure サブスクリプション内で作成されます。
- データベースの親リソースです。
- データベースに名前空間を提供します。
- 強力な有効期間のセマンティクスが含まれるコンテナーです。サーバーを削除すると、包含データベースが削除されます。
- リージョンにリソースを併置します。
- サーバーおよびデータベース アクセス用の接続エンドポイントを提供します 
- データベースに適用される管理ポリシーのスコープ (ログイン、ファイアウォール、ユーザー、ロール、構成など) を提供します。
- 複数のバージョンで使用できます。 詳しくは、「[サポートされている PostgreSQL Database バージョン](concepts-supported-versions.md)」をご覧ください。
- ユーザーが拡張できます。 詳細については、[PostgreSQL の拡張機能](concepts-extensions.md)に関するページをご覧ください。

Azure Database for PostgreSQL サーバー内では、1 つ以上のデータベースを作成できます。 サーバーごとに 1 つのデータベースを作成してすべてのリソースを利用するか、複数のデータベースを作成してリソースを共有することができます。 価格は、価格レベル、仮想コア、ストレージ (GB) の構成に基づき、サーバーごとに構造化されています。 詳細については、「[価格レベル](./concepts-pricing-tiers.md)」をご覧ください。

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーへの接続および認証方法
次の要素が、データベースへの安全なアクセスを確保するうえで役に立ちます。

|||
|:--|:--|
| **認証と権限承認** | Azure Database for PostgreSQL サーバーは、ネイティブ PostgreSQL 認証をサポートしています。 サーバーにはサーバーの管理者ログインで接続し、認証できます。 |
| **プロトコル** | サービスは、PostgreSQL で使用されるメッセージ ベースのプロトコルをサポートしています。 |
| **TCP/IP** | プロトコルは、TCP/IP および UNIX ドメイン ソケット経由でサポートされます。 |
| **ファイアウォール** | データを保護するため、ファイアウォール規則は、どのコンピューターに権限を持たせるかを指定するまで、使用中のサーバーおよびそのデータベースへのすべてのアクセスを遮断します。 「 [Azure Database for PostgreSQL サーバーのファイアウォール規則](concepts-firewall-rules.md)」を参照してください。 |

## <a name="managing-your-server"></a>サーバーの管理
Azure Database for PostgreSQL サーバーを管理するには、[Azure Portal](https://portal.azure.com) または [Azure CLI](/cli/azure/postgres) を使用します。

サーバーを作成しているときに、管理者ユーザーの資格情報を設定します。 管理者ユーザーは、サーバー上の最上位の特権ユーザーです。 これは、ロール azure_pg_admin に属します。 このロールには、完全なスーパー ユーザーのアクセス許可がありません。 

PostgreSQL スーパー ユーザー属性は、管理対象サービスに属している azure_superuser に割り当てられます。 このロールへのアクセス権がありません。

Azure Database for PostgreSQL サーバーには既定のデータベースがあります。 
- **postgres** - サーバーが作成された後で接続できる既定のデータベース。
- **azure_maintenance** - このデータベースは、ユーザーの操作から管理対象サービスを提供するプロセスを分離するために使用します。 このデータベースへのアクセス権がありません。
- **azure_sys** - クエリ ストアのデータベース。 クエリ ストアがオフの場合は、このデータベースがデータを蓄積することはありません。これは、既定の設定です。 詳細は、[クエリ ストアの概要](concepts-query-store.md)を参照してください｡


## <a name="server-parameters"></a>サーバー パラメーター
PostgreSQL サーバー パラメーターは、サーバーの構成を決定します。 Azure Database for PostgreSQL では、Azure Portal または Azure CLI を使用してパラメーターの一覧を表示および編集できます。 

Postgres の管理対象サービスとして、Azure Database for PostgreSQL 内の構成可能なパラメーターはローカル Postgres インスタンス内のパラメーターのサブセットです (Postgres パラメーターについて詳しくは、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/9.6/static/runtime-config.html)をご覧ください)。 Azure Database for PostgreSQL サーバーは、作成時に各パラメーターに対して既定値で有効化されます。 変更を反映するためにサーバーの再起動やスーパー ユーザーのアクセス権が必要な一部のパラメーターは、ユーザーが構成することはできません。


## <a name="next-steps"></a>次の手順
- サービスの概要については、 [Azure Database for PostgreSQL の概要](overview.md)に関するページをご覧ください。
-  **サービス レベル**に基づく特定のリソース クォータと制限については、 [サービス レベル](concepts-pricing-tiers.md)に関するページをご覧ください。
- サービスへの接続については、「 [Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)」を参照してください。
- [Azure Portal](howto-configure-server-parameters-using-portal.md) または [Azure CLI](howto-configure-server-parameters-using-cli.md) でサーバー パラメーターを表示および編集します。
