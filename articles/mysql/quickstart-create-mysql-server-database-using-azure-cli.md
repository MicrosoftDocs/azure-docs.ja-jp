---
title: "クイック スタート: Azure Database for MySQL サーバーの作成 - Azure CLI | Microsoft Docs"
description: "このクイック スタートでは、Azure CLI を使用して、Azure Database for MySQL サーバーを Azure リソース グループに作成する方法を説明します。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: hero-article
ms.date: 06/13/2017
ms.custom: mvc
ms.openlocfilehash: 3d66efa6935150c665a3f568e60c35ddbd70e8be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI を使用した Azure Database for MySQL サーバーの作成
このクイック スタートでは、Azure CLI を使用して、約 5 分で Azure Database for MySQL サーバーを Azure リソース グループに作成する方法を説明します。 Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

複数のサブスクリプションをお持ちの場合は、リソースが存在するか、課金の対象となっている適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account#set) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>リソース グループの作成
[az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) コマンドで [Azure リソース グループ](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

次の例では、`myresourcegroup` という名前のリソース グループを `westus` の場所に作成します。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成
**az mysql server create** コマンドを使用して、Azure Database for MySQL サーバーを作成します。 1 つのサーバーで複数のデータベースを管理できます。 通常は、プロジェクトまたはユーザーごとに個別のデータベースを使用します。

次の例は、`westus` にあるリソース グループ `myresourcegroup` に、`myserver4demo` という名前の Azure Database for MySQL サーバーを作成します。 サーバーの管理者ログインの名前は `myadmin`、パスワードは `Password01!` です。 サーバーのパフォーマンス レベルは **Basic**、**50** のコンピューティング ユニットを同じサーバー内のすべてのデータベースで共有します。 アプリケーションのニーズに応じて、コンピューティング ユニットとストレージは自由にスケーリングできます。

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name myserver4demo --location westus --admin-user myadmin --admin-password Password01! --performance-tier Basic --compute-units 50
```

## <a name="configure-firewall-rule"></a>ファイアウォール規則の構成
**az mysql server firewall-rule create** コマンドで、Azure Database for MySQL サーバーレベルのファイアウォール規則を作成します。 サーバーレベルのファイアウォール規則により、**mysql.exe** コマンド ライン ツールや MySQL Workbench などの外部アプリケーションが、Azure MySQL service ファイアウォールを経由してサーバーに接続できるようになります。 

次の例では、定義済みのアドレス範囲に対するファイアウォール規則が作成されます。この例でのアドレス範囲は、IP アドレスの範囲として可能な全範囲です。

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server myserver4demo --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>SSL 設定の構成
既定では、サーバーとクライアント アプリケーション間で SSL 接続が適用されます。  これにより、インターネットでのデータ ストリームを暗号化することによって、「インモーション」データのセキュリティが確保されます。  このクイック スタートを簡略化するため、サーバーの SSL 接続を無効にします。  これは実稼働サーバーではお勧めしません。  詳細については、「[Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する](./howto-configure-ssl.md)」を参照してください。

次の例は、MySQL サーバー上で SSL の適用を無効にします。
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name myserver4demo -g -n --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name myserver4demo
```

結果は JSON 形式です。 **fullyQualifiedDomainName** と **administratorLogin** の値を書き留めておきます。
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "myserver4demo.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/myserver4demo",
  "location": "westus",
  "name": "myserver4demo",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>mysql.exe コマンドライン ツールを使用したサーバーへの接続
**mysql.exe** コマンドライン ツールを使用してサーバーに接続します。 MySQL を[ここ](https://dev.mysql.com/downloads/)からダウンロードして、コンピューターにインストールすることができます。 または、コード サンプルの **[試してみる]** ボタンをクリックするか、Azure Portal の右上のツールバーにある `>_` ボタンをクリックして、**Azure Cloud Shell** を起動することもできます。

次のコマンドを入力します。 

1. **mysql** コマンドライン ツールを使用してサーバーに接続します。
```azurecli-interactive
 mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

2. サーバーの状態を表示します。
```sql
 mysql> status
```
すべてが問題ない場合は、コマンドライン ツールの出力は次のようになります。

```dos
C:\Users\>mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             myserver4demo.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> その他のコマンドについては、「[MySQL 5.7 リファレンス マニュアル - 4.5.1 章](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)」を参照してください。

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>MySQL Workbench GUI ツールを使用したサーバーへの接続
1.  クライアント コンピューターで MySQL Workbench アプリケーションを起動します。 MySQL Workbench は[ここ](https://dev.mysql.com/downloads/workbench/)からダウンロードしてインストールできます。

2.  **[Setup New Connection] \(新しい接続のセットアップ)** ダイアログ ボックスで、次の情報を **[Parameters] \(パラメーター)** タブに入力します。

   ![新しい接続のセットアップ](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **設定** | **推奨値** | **説明** |
|---|---|---|
|   接続名 | My Connection | この接続のラベルを指定します (任意に指定できます) |
| 接続方法 | Standard (TCP/IP) を選択します | TCP/IP プロトコルを使用して Azure Database for MySQL に接続します |
| ホスト名 | myserver4demo.mysql.database.azure.com | 前にメモしておいたサーバー名です。 |
| Port | 3306 | MySQL の既定のポートが使用されます。 |
| ユーザー名 | myadmin@myserver4demo | 前にメモしておいたサーバーの管理者ログインです。 |
| パスワード | **** | 前に構成した管理者アカウントのパスワードを使用します。 |

**[Test Connection] \(接続のテスト)** をクリックして、すべてのパラメーターが正しく構成されているかをテストします。
これで、接続をクリックしてサーバーに正常に接続できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ
これらのリソースが別のクイック スタート/チュートリアルで不要である場合、次のコマンドで削除することができます。 

```azurecli-interactive
az group delete --name myresourcegroup
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure CLI での MySQL データベースの設計](./tutorial-design-database-using-cli.md)
