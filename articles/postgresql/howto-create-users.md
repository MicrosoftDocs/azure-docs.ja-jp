---
title: ユーザーの作成 - Azure Database for PostgreSQL - Single Server
description: この記事では、Azure Database for PostgreSQL - Single Server と対話する新しいユーザー アカウントを作成する方法について説明します。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2019
ms.openlocfilehash: 1dbbdde03d1c24882be298d8c81362744debeecf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91704941"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server でユーザーを作成する

この記事では、Azure Database for PostgreSQL サーバー内にユーザーを作成する方法について説明します。

Azure サブスクリプションのユーザーとその特権を作成して管理する方法について確認する場合は、[Azure ロールベースのアクセス制御 (Azure RBAC) に関する記事](../role-based-access-control/built-in-roles.md)を参照するか、[ロールのカスタマイズ方法](../role-based-access-control/custom-roles.md)について確認してください。

## <a name="the-server-admin-account"></a>サーバー管理者アカウント

Azure Database for PostgreSQL を初めて作成したときに、サーバー管理者のユーザー名とパスワードを指定しています。 手順について詳しくは、[クイック スタート](quickstart-create-server-database-portal.md)に関するページをご覧ください。 サーバー管理者のユーザー名はカスタム名なので、選んだサーバー管理者ユーザー名は Azure Portal で確認できます。

Azure Database for PostgreSQL サーバーを作成すると、3 つの既定のロールが定義されます。 これらのロールは、`SELECT rolname FROM pg_roles;` コマンドを実行して確認できます。

- azure_pg_admin
- azure_superuser
- サーバーの管理者ユーザー

サーバーの管理者ユーザーは、azure_pg_admin ロールのメンバーです。 ただし、サーバー管理者アカウントは azure_superuser ロールの一部ではありません。 このサービスは管理対象の PaaS サービスなので、Microsoft だけがスーパー ユーザー ロールの一部になります。

[PostgreSQL 製品のドキュメント](https://www.postgresql.org/docs/current/static/sql-createrole.html)で説明されているように、PostgreSQL エンジンは特権を使ってデータベース オブジェクトへのアクセスを制御します。 Azure Database for PostgreSQL でサーバー管理者ユーザーが付与される特権は、LOGIN、NOSUPERUSER、INHERIT、CREATEDB、CREATEROLE、NOREPLICATION です

サーバー管理者ユーザー アカウントを使うと、他のユーザーを作成して、azure_pg_admin ロールをそれらのユーザーに付与できます。 また、サーバー管理者アカウントを使って、個々のデータベースとスキーマに対するアクセス権を持つ特権の少ないユーザーとロールを作成することもできます。

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL で追加の管理者ユーザーを作成する方法

1. 接続情報と管理者のユーザー名を取得します。
   データベース サーバーに接続するには、サーバーの完全な名前と管理者サインイン資格情報が必要となります。 Azure Portal で、サーバーの **[概要]** ページまたは **[プロパティ]** ページからサーバー名とサインイン情報を簡単に確認できます。

2. 管理者のアカウントとパスワードを使用して、データベース サーバーに接続します。 pgAdmin や psql など、好みのクライアント ツールを使ってください。
   接続方法がわからない場合は、[クイック スタート](./quickstart-create-server-database-portal.md)を参照してください

3. 次の SQL コードを編集して実行します。 プレースホルダー値 <new_user> は新しいユーザー名に置き換え、パスワードのプレースホルダーは自分の強力なパスワードに置き換えます。 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL でデータベース ユーザーを作成する方法

1. 接続情報と管理者のユーザー名を取得します。
   データベース サーバーに接続するには、サーバーの完全な名前と管理者サインイン資格情報が必要となります。 Azure Portal で、サーバーの **[概要]** ページまたは **[プロパティ]** ページからサーバー名とサインイン情報を簡単に確認できます。

2. 管理者のアカウントとパスワードを使用して、データベース サーバーに接続します。 pgAdmin や psql など、好みのクライアント ツールを使ってください。

3. 次の SQL コードを編集して実行します。 プレースホルダー値 `<db_user>` を目的の新しいユーザー名で置き換え、プレースホルダー値 `<newdb>` をデータベース名で置き換えます。 パスワードのプレースホルダーは自分の強力なパスワードに置き換えます。

   この SQL コード構文では、例のために testdb という新しいデータベースを作成します。 次に、PostgreSQL サービスに新しいユーザーを作成し、そのユーザーに新しいデータベースへの接続特権を付与します。

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. 管理者アカウントを使うと、データベース内のオブジェクトをセキュリティで保護するために追加の特権を付与することが必要な場合があります。 データベースのロールと特権について詳しくは、[PostgreSQL のドキュメント](https://www.postgresql.org/docs/current/static/ddl-priv.html)をご覧ください。 次に例を示します。

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. 指定したデータベースを指定し、新しいユーザー名とパスワードを使って、サーバーにログインします。 この例は、psql のコマンド ラインです。 このコマンドでは、ユーザー名のパスワード入力が求められます。 実際のサーバー名、データベース名、およびユーザー名に置き換えます。

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>次のステップ

新しいユーザーのマシンの IP アドレスについてファイアウォールを開き、接続できるようにします。[Azure portal](howto-manage-firewall-using-portal.md) または [Azure CLI](howto-manage-firewall-using-cli.md) を使用して、Azure Database for PostgreSQL ファイアウォール規則の作成と管理を行います。

ユーザー アカウントの管理について詳しくは、[データベース ロールと特権](https://www.postgresql.org/docs/current/static/user-manag.html)、[GRANT の構文](https://www.postgresql.org/docs/current/static/sql-grant.html)、および[特権](https://www.postgresql.org/docs/current/static/ddl-priv.html)に関する PostgreSQL 製品ドキュメントをご覧ください。
