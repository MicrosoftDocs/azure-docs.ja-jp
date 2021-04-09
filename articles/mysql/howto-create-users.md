---
title: データベースとユーザーを作成する - Azure Database for MySQL
description: この記事では、Azure Database for MySQL サーバーと対話する新しいユーザー アカウントを作成する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: 9a6346a2b62c81dd74cf0ebe9a85df12d3488679
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251252"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>Azure Database for MySQL でデータベースとユーザーを作成する

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

この記事では、Azure Database for MySQL でユーザーを作成する方法について説明します。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "_スレーブ_" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。
>

Azure Database for MySQL サーバーを初めて作成したときに、サーバー管理者のユーザー名とパスワードを指定しています。 詳細については、こちらの[クイックスタート](quickstart-create-mysql-server-database-using-azure-portal.md)をご覧ください。 サーバー管理者のユーザー名は、Azure portal で確認できます。

サーバー管理者のユーザーには、次の特権があります。

   SELECT、INSERT、UPDATE、DELETE、CREATE、DROP、RELOAD、PROCESS、REFERENCES、INDEX、ALTER、SHOW DATABASES、CREATE TEMPORARY TABLES、LOCK TABLES、EXECUTE、REPLICATION SLAVE、REPLICATION CLIENT、CREATE VIEW、SHOW VIEW、CREATE ROUTINE、ALTER ROUTINE、CREATE USER、EVENT、TRIGGER です

Azure Database for MySQL サーバーを作成すると、最初のサーバー管理者のアカウントを使用して、追加のユーザーを作成し、それらのユーザーに管理者アクセス権を付与できます。 また、サーバー管理者アカウントを使用して、個々のデータベース スキーマに対するアクセス権を持つ特権の少ないユーザーを作成することもできます。

> [!NOTE]
> SUPER 特権と DBA ロールはサポートされていません。 制限事項に関する記事に記載されている[権限](concepts-limits.md#privileges--data-manipulation-support)を確認して、サービスでサポートされていない権限を理解してください。
>
> `validate_password` や `caching_sha2_password` などのパスワード プラグインはサービスでサポートされていません。

## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>Azure Database for MySQL で管理者以外のユーザーを使用してデータベース ユーザーを作成するには

1. 接続情報と管理者のユーザー名を取得します。
   データベース サーバーに接続するには、サーバーの完全な名前と管理者サインイン資格情報が必要となります。 Azure portal で、サーバーの **[概要]** ページまたは **[プロパティ]** ページでサーバー名とサインイン情報を簡単に確認できます。

2. 管理者のアカウントとパスワードを使用して、データベース サーバーに接続します。 MySQL Workbench、mysql.exe、HeidiSQL など、好みのクライアント ツールを使用します。

   接続方法がわからない場合は、[単一サーバーで接続してデータのクエリを実行する](./connect-workbench.md)方法または[フレキシブル サーバーで接続してデータのクエリを実行する](./flexible-server/connect-workbench.md)方法を参照してください。

3. 次の SQL コードを編集して実行します。 プレースホルダー値 `db_user` を目的の新しいユーザー名に置き換えます。 プレースホルダー値 `testdb` をデータベース名に置き換えます。

   この SQL コードでは、testdb という名前の新しいデータベースを作成します。 次に、MySQL サービスに新しいユーザーを作成し、そのユーザーに新しいデータベース スキーマ (testdb.\*) のすべての特権を付与します。

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. データベース内の特権の付与を確認します。

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. 指定したデータベースを指定し、新しいユーザー名とパスワードを使用して、サーバーにサインインします。 この例は、mysql のコマンド ラインです。 このコマンドを使用すると、ユーザーのパスワードの入力を求められます。 実際のサーバー名、データベース名、およびユーザー名を使用します。

   ### <a name="single-server"></a>[単一サーバー](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

   ### <a name="flexible-server"></a>[フレキシブル サーバー](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="to-create-more-admin-users-in-azure-database-for-mysql"></a>Azure Database for MySQL で追加の管理者のユーザーを作成するには

1. 接続情報と管理者のユーザー名を取得します。
   データベース サーバーに接続するには、サーバーの完全な名前と管理者サインイン資格情報が必要となります。 Azure portal で、サーバーの **[概要]** ページまたは **[プロパティ]** ページでサーバー名とサインイン情報を簡単に確認できます。

2. 管理者のアカウントとパスワードを使用して、データベース サーバーに接続します。 MySQL Workbench、mysql.exe、HeidiSQL など、好みのクライアント ツールを使用します。

   接続方法がわからない場合は、[MySQL Workbench を使用して接続してデータのクエリを実行する方法](./connect-workbench.md)に関するページを参照してください。

3. 次の SQL コードを編集して実行します。 プレースホルダー値 `new_master_user` を新しいユーザー名に置き換えます。 この構文では、ユーザー (この例では `new_master_user`) に対して、すべてのデータベース スキーマ ( *.* ) の列挙した特権を付与します。

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. 特権の付与を確認します。

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

すべての Azure Database for MySQL サーバーは、"azure_superuser" というユーザーを使用して作成されます。 これは、監視、バックアップ、その他の定期的なメンテナンスを行うサーバーを管理するために Microsoft によって作成されるシステム アカウントです。 また、オンコール エンジニアも、証明書の認証に関するインシデントの間にサーバーにアクセスするためにこのアカウントを使用する場合があり、Just-In-Time (JIT) プロセスを使用してアクセスを要求する必要があります。

## <a name="next-steps"></a>次のステップ

新しいユーザーのマシンの IP アドレスについてファイアウォールを開き、接続できるようにします。

* [単一サーバーでファイアウォール規則を作成して管理する](howto-manage-firewall-using-portal.md)
* [フレキシブル サーバーでファイアウォール規則を作成して管理する](flexible-server/how-to-connect-tls-ssl.md)

ユーザー アカウント管理の詳細については、[ユーザー アカウント管理](https://dev.mysql.com/doc/refman/5.7/en/access-control.html)、[GRANT の構文](https://dev.mysql.com/doc/refman/5.7/en/grant.html)、および[特権](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html)に関する MySQL 製品ドキュメントを参照してください。
