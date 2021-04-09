---
title: ユーザーの作成 - Azure Database for MariaDB
description: この記事では、Azure Database for MariaDB サーバーとやりとりする新しいユーザー アカウントを作成する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 01/18/2021
ms.openlocfilehash: 28ec060e95d09cb150fc699919dde6cc0e1eaf23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663708"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Azure Database for MariaDB でユーザーを作成する

この記事では、Azure Database for MariaDB でユーザーを作成する方法について説明します。

Azure Database for MariaDB を初めて作成したときに、サーバー管理者のログイン ユーザー名とパスワードを指定しています。 詳細については、[クイックスタート](quickstart-create-mariadb-server-database-using-azure-portal.md)に関するページを参照してください。 サーバー管理者のログイン ユーザー名は、Azure Portal で確認できます。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "_スレーブ_" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

サーバー管理者のユーザーがサーバーに関して付与される特権は、SELECT、INSERT、UPDATE、DELETE、CREATE、DROP、RELOAD、PROCESS、REFERENCES、INDEX、ALTER、SHOW DATABASES、CREATE TEMPORARY TABLES、LOCK TABLES、EXECUTE、REPLICATION SLAVE、REPLICATION CLIENT、CREATE VIEW、SHOW VIEW、CREATE ROUTINE、ALTER ROUTINE、CREATE USER、EVENT、TRIGGER です

Azure Database for MariaDB サーバーが作成されたら、最初のサーバー管理者のユーザー アカウントを使用して、追加のユーザーを作成し、それらのユーザーに管理者アクセス権を付与できます。 また、サーバー管理者アカウントを使用して、個々のデータベース スキーマに対するアクセス権を持つ特権の少ないユーザーを作成することもできます。

> [!NOTE]
> SUPER 権限と DBA ロールはサポートされていません。 制限事項に関する記事に記載されている[権限](concepts-limits.md#privileges--data-manipulation-support)を確認して、サービスでサポートされていない権限を理解してください。<br><br>
> "validate_password" や "caching_sha2_password" などのパスワード プラグインは、サービスではサポートされていません。

## <a name="create-more-admin-users"></a>追加の管理者ユーザーを作成する

1. 接続情報と管理者のユーザー名を取得します。
   データベース サーバーに接続するには、サーバーの完全な名前と管理者サインイン資格情報が必要となります。 Azure Portal で、サーバーの **[概要]** ページまたは **[プロパティ]** ページからサーバー名とサインイン情報を簡単に確認できます。

2. 管理者のアカウントとパスワードを使用して、データベース サーバーに接続します。 MySQL Workbench、mysql.exe、HeidiSQL など、好みのクライアント ツールを使用します。
   接続方法がわからない場合は、[MySQL Workbench を使用して接続してデータのクエリを実行する方法](./connect-workbench.md)に関するページを参照してください

3. 次の SQL コードを編集して実行します。 プレースホルダー値 `new_master_user` を新しいユーザー名に置き換えます。 この構文では、ユーザー名 (この例では new_master_user) に対して、すべてのデータベース スキーマ ( *.* ) の列挙した特権を付与します。 

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

## <a name="create-database-users"></a>データベース ユーザーを作成する

1. 接続情報と管理者のユーザー名を取得します。
   データベース サーバーに接続するには、サーバーの完全な名前と管理者サインイン資格情報が必要となります。 Azure Portal で、サーバーの **[概要]** ページまたは **[プロパティ]** ページからサーバー名とサインイン情報を簡単に確認できます。 

2. 管理者のアカウントとパスワードを使用して、データベース サーバーに接続します。 MySQL Workbench、mysql.exe、HeidiSQL など、好みのクライアント ツールを使用します。
   接続方法がわからない場合は、[MySQL Workbench を使用して接続してデータのクエリを実行する方法](./connect-workbench.md)に関するページを参照してください。

3. 次の SQL コードを編集して実行します。 プレースホルダー値 `db_user` を目的の新しいユーザー名で置き換え、プレースホルダー値 `testdb` をデータベース名で置き換えます。

   この SQL コード構文では、例のために testdb という新しいデータベースを作成します。 次に、Azure Database for MariaDB サービスに新しいユーザーを作成し、そのユーザーの新しいデータベース スキーマ (testdb.\*) にすべての特権を付与します。 

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

5. 新しいユーザー名とパスワードを使用し、対象のデータベースを指定して、サーバーにログインします。 この例は、mysql のコマンド ラインです。 このコマンドでは、ユーザー名のパスワード入力が求められます。 実際のサーバー名、データベース名、およびユーザー名に置き換えます。

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

   ユーザー アカウント管理の詳細については、[ユーザー アカウント管理](https://mariadb.com/kb/en/library/user-account-management/)、[GRANT の構文](https://mariadb.com/kb/en/library/grant/)、および[特権](https://mariadb.com/kb/en/library/grant/#privilege-levels)に関する MariaDB ドキュメントを参照してください。

## <a name="azure_superuser"></a>azure_superuser

すべての Azure Database for MySQL サーバーは、"azure_superuser" というユーザーを使用して作成されます。 これは、監視、バックアップ、その他の定期的なメンテナンスを行うサーバーを管理するために Microsoft によって作成されるシステム アカウントです。 また、オンコール エンジニアも、証明書の認証に関するインシデントの間にサーバーにアクセスするためにこのアカウントを使用する場合があり、Just-In-Time (JIT) プロセスを使用してアクセスを要求する必要があります。

## <a name="next-steps"></a>次のステップ

新しいユーザーのマシンの IP アドレスについてファイアウォールを開き、接続できるようにします。[Azure portal を使用した Azure Database for MariaDB ファイアウォール規則の作成と管理](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
