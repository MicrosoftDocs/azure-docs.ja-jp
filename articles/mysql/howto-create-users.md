---
title: Azure Database for MySQL サーバーでユーザーを作成する
description: この記事では、Azure Database for MySQL サーバーと対話する新しいユーザー アカウントを作成する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 4cd2305ed3d7f88f6c3825d8f7cdb5d81f9a0f6b
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351254"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーでユーザーを作成する 
この記事では、Azure Database for MySQL サーバーでユーザーを作成する方法について説明します。

Azure Database for MySQL を初めて作成したときに、サーバー管理者のログイン ユーザー名とパスワードを指定しています。 詳細については、[クイックスタート](quickstart-create-mysql-server-database-using-azure-portal.md)に関するページを参照してください。 サーバー管理者のログイン ユーザー名は、Azure Portal で確認できます。

サーバー管理者のユーザーがサーバーに関して付与される特権は、SELECT、INSERT、UPDATE、DELETE、CREATE、DROP、RELOAD、PROCESS、REFERENCES、INDEX、ALTER、SHOW DATABASES、CREATE TEMPORARY TABLES、LOCK TABLES、EXECUTE、REPLICATION SLAVE、REPLICATION CLIENT、CREATE VIEW、SHOW VIEW、CREATE ROUTINE、ALTER ROUTINE、CREATE USER、EVENT、TRIGGER です

Azure Database for MySQL サーバーが作成されると、最初のサーバー管理者のユーザー アカウントを使用して、追加のユーザーを作成し、それらのユーザーに管理者アクセス権を付与できます。 また、サーバー管理者アカウントを使用して、個々のデータベース スキーマに対するアクセス権を持つ特権の少ないユーザーを作成することもできます。

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Azure Database for MySQL で追加の管理者のユーザーを作成する方法
1. 接続情報と管理者のユーザー名を取得します。
   データベース サーバーに接続するには、サーバーの完全な名前と管理者サインイン資格情報が必要となります。 Azure Portal で、サーバーの **[概要]** ページまたは **[プロパティ]** ページからサーバー名とサインイン情報を簡単に確認できます。 

2. 管理者のアカウントとパスワードを使用して、データベース サーバーに接続します。 MySQL Workbench、mysql.exe、HeidiSQL など、好みのクライアント ツールを使用します。 
   接続方法がわからない場合は、[MySQL Workbench を使用して接続してデータのクエリを実行する方法](./connect-workbench.md)に関するページを参照してください。

3. 次の SQL コードを編集して実行します。 プレースホルダー値 `new_master_user` を新しいユーザー名に置き換えます。 この構文では、ユーザー名 (この例では new_master_user) に対して、すべてのデータベース スキーマ (*.*) の列挙した特権を付与します。 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. 特権の付与を確認します 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Azure Database for MySQL でデータベース ユーザーを作成する方法

1. 接続情報と管理者のユーザー名を取得します。
   データベース サーバーに接続するには、サーバーの完全な名前と管理者サインイン資格情報が必要となります。 Azure Portal で、サーバーの **[概要]** ページまたは **[プロパティ]** ページからサーバー名とサインイン情報を簡単に確認できます。 

2. 管理者のアカウントとパスワードを使用して、データベース サーバーに接続します。 MySQL Workbench、mysql.exe、HeidiSQL など、好みのクライアント ツールを使用します。 
   接続方法がわからない場合は、[MySQL Workbench を使用して接続してデータのクエリを実行する方法](./connect-workbench.md)に関するページを参照してください。

3. 次の SQL コードを編集して実行します。 プレースホルダー値 `db_user` を目的の新しいユーザー名で置き換え、プレースホルダー値 `testdb` をデータベース名で置き換えます。

   この SQL コード構文では、例のために testdb という新しいデータベースを作成します。 次に、MySQL サービスに新しいユーザーを作成し、そのユーザーの新しいデータベース スキーマ (testdb.\*) にすべての特権を付与します。 

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

5. 指定したデータベース、新しいユーザー名とパスワードを使用して、サーバーにログインします。 この例は、mysql のコマンド ラインです。 このコマンドでは、ユーザー名のパスワード入力が求められます。 実際のサーバー名、データベース名、およびユーザー名に置き換えます。

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>次の手順
新しいユーザーのマシンの IP アドレスについてファイアウォールを開き、接続できるようにします。[Azure portal](howto-manage-firewall-using-portal.md) または [Azure CLI](howto-manage-firewall-using-cli.md) を使用して、Azure Database for MySQL ファイアウォール規則の作成と管理を行います。

ユーザー アカウント管理の詳細については、[ユーザー アカウント管理](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html)、[GRANT の構文](https://dev.mysql.com/doc/refman/5.7/en/grant.html)、および[特権](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html)に関する MySQL 製品ドキュメントを参照してください。
