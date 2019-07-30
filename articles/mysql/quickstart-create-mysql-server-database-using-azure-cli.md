---
title: クイック スタート:Azure Database for MySQL サーバーを作成する - Azure CLI
description: このクイック スタートでは、Azure CLI を使用して、Azure Database for MySQL サーバーを Azure リソース グループに作成する方法を説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/09/2019
ms.custom: mvc
ms.openlocfilehash: 10acb353e282508c838bee89b131d94dcd3fa7ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160400"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>クイック スタート:Azure CLI を使用した Azure Database for MySQL サーバーの作成

> [!TIP]
> よりシンプルな [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI コマンド (現在はプレビュー段階) の使用を検討してください。 こちらの[クイック スタート](./quickstart-create-server-up-azure-cli.md) をお試しください。

このクイック スタートでは、Azure CLI を使用して、約 5 分で Azure Database for MySQL サーバーを Azure リソース グループに作成する方法を説明します。 Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

複数のサブスクリプションをお持ちの場合は、リソースが存在するか、課金の対象となっている適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account#az-account-set) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>リソース グループの作成
[az group create](/cli/azure/group#az-group-create) コマンドで [Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

次の例では、`myresourcegroup` という名前のリソース グループを `westus` の場所に作成します。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成
**[az mysql server create](/cli/azure/mysql/server#az-mysql-server-create)** コマンドを使用して、Azure Database for MySQL サーバーを作成します。 1 つのサーバーで複数のデータベースを管理できます。 通常は、プロジェクトまたはユーザーごとに個別のデータベースを使用します。

**設定** | **値の例** | **説明**
---|---|---
name | mydemoserver | Azure Database for MySQL サーバーを識別する一意の名前を選択します。 サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、 3 ～ 63 文字にする必要があります。
resource-group | myresourcegroup | Azure リソース グループの名前を指定します。
sku-name | GP_Gen5_2 | SKU の名前。 省略表現の {価格レベル}\_{コンピューティング世代}\_{仮想コア} という規則に従います。 sku-name パラメーターの詳細については、この表の下方を参照してください。
backup-retention | 7 | バックアップを保持する必要のある時間。 単位は日数です。 範囲は 7 ～ 35 です。 
geo-redundant-backup | Disabled | このサーバーに対して geo 冗長バックアップを有効にする必要があるかどうかどうか。 使用できる値は以下の通りです。Enabled、Disabled
location | westus | サーバーの Azure の場所。
ssl-enforcement | Enabled | このサーバーに対して ssl を有効にする必要があるかどうかどうか。 使用できる値は以下の通りです。Enabled、Disabled
storage-size | 51200 | サーバーのストレージ容量 (単位はメガバイト)。 有効な storage-size は最小 5,120 MB で、1,024 MB ずつ増加します。 ストレージ サイズの制限の詳細については、[価格レベル](./concepts-pricing-tiers.md)についてのドキュメントを参照してください。 
version | 5.7 | MySQL のメジャー バージョン。
admin-user | myadmin | 管理者ログインのユーザー名。 これを **azure_superuser**、**admin**、**administrator**、**root**、**guest**、**public** にすることはできません。
admin-password | *セキュリティで保護されたパスワード* | 管理者ユーザーのパスワード。 8 ～ 128 文字にする必要があります。 パスワードには、英大文字、英小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。


sku-name パラメーターの値は、次の例のように、{価格レベル}\_{コンピューティング世代}\_{仮想コア数} という規約に従います。
+ `--sku-name B_Gen5_1` は、"Basic、Gen 5、および 1 個の仮想コア" にマップされます。 このオプションは、利用できる最小の SKU です。
+ `--sku-name GP_Gen5_32` は、"汎用、Gen 5、および 32 個の仮想コア" にマップされます。
+ `--sku-name MO_Gen5_2` は、"メモリ最適化、Gen 5、および 2 個の仮想コア" にマップされます。

リージョンごとおよびレベルごとに有効な値を理解するには、[価格レベル](./concepts-pricing-tiers.md)のドキュメントを参照してください。

次の例では、サーバー管理者ログイン `myadmin` を使用して、リソース グループ `myresourcegroup` の `mydemoserver` という名前の MySQL 5.7 サーバーを米国西部に作成します。 これは、2 つの**仮想コア**を備えた **Gen 4** **汎用**サーバーです。 `<server_admin_password>` は独自の値に置き換えます。

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

> [!NOTE]
> 低負荷なコンピューティングと I/O がワークロードに適している場合は、Basic 価格レベルの使用を検討してください。 Basic 価格レベルで作成されたサーバーは後で General Purpose またはメモリ最適化にスケーリングできないことに注意してください。 詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/mysql/)を参照してください。
> 

## <a name="configure-firewall-rule"></a>ファイアウォール規則の構成
**[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create)** コマンドで、Azure Database for MySQL サーバーレベルのファイアウォール規則を作成します。 サーバーレベルのファイアウォール規則により、**mysql.exe** コマンド ライン ツールや MySQL Workbench などの外部アプリケーションが、Azure MySQL service ファイアウォールを経由してサーバーに接続できるようになります。 

次の例では、特定の IP アドレス 192.168.0.1 からの接続を許可する、`AllowMyIP` と呼ばれるファイアウォール規則を作成しています。 実際の接続元となる場所に対応する IP アドレスまたは IP アドレスの範囲に置き換えてください。 

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Azure Database for MySQL との接続では、ポート 3306 が通信に使用されます。 企業ネットワーク内から接続を試みる場合、ポート 3306 での送信トラフィックが許可されていない場合があります。 その場合、会社の IT 部門によってポート 3306 が開放されない限り、サーバーに接続することはできません。
> 


## <a name="configure-ssl-settings"></a>SSL 設定の構成
既定では、サーバーとクライアント アプリケーション間で SSL 接続が適用されます。 この既定値では、インターネットでのデータ ストリームを暗号化することによって、"インモーション" データのセキュリティが確保されます。 このクイック スタートを簡略化するため、サーバーの SSL 接続を無効にします。 実稼働サーバーで SSL を無効にすることはお勧めしません。 詳細については、「[Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する](./howto-configure-ssl.md)」を参照してください。

次の例は、MySQL サーバー上で SSL の適用を無効にします。
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

結果は JSON 形式です。 **fullyQualifiedDomainName** と **administratorLogin** の値を書き留めておきます。
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>mysql.exe コマンドライン ツールを使用したサーバーへの接続
**mysql.exe** コマンドライン ツールを使用してサーバーに接続します。 MySQL を[ここ](https://dev.mysql.com/downloads/)からダウンロードして、コンピューターにインストールすることができます。 

次のコマンドを入力します。 

1. **mysql** コマンドライン ツールを使用してサーバーに接続します。
   ```bash
   mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
   ```

2. サーバーの状態を表示します。
   ```sql
   mysql> status
   ```
   すべてが問題ない場合は、コマンドライン ツールの出力は次のようになります。

```dos
C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
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
Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
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
1. クライアント コンピューターで MySQL Workbench アプリケーションを起動します。 MySQL Workbench は[ここ](https://dev.mysql.com/downloads/workbench/)からダウンロードしてインストールできます。

2. **[Setup New Connection] \(新しい接続のセットアップ)** ダイアログ ボックスで、次の情報を **[Parameters] \(パラメーター)** タブに入力します。

   ![新しい接続のセットアップ](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **設定** | **推奨値** | **説明** |
|---|---|---|
|   接続名 | My Connection | この接続のラベルを指定します (任意に指定できます) |
| 接続方法 | Standard (TCP/IP) を選択します | TCP/IP プロトコルを使用して Azure Database for MySQL に接続します |
| ホスト名 | mydemoserver.mysql.database.azure.com | 前にメモしておいたサーバー名です。 |
| Port | 3306 | MySQL の既定のポートが使用されます。 |
| ユーザー名 | myadmin@mydemoserver | 前にメモしておいたサーバーの管理者ログインです。 |
| パスワード | **** | 前に構成した管理者アカウントのパスワードを使用します。 |

**[Test Connection] \(接続のテスト)** をクリックして、すべてのパラメーターが正しく構成されているかをテストします。
これで、接続をクリックしてサーバーに正常に接続できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ
これらのリソースが別のクイック スタート/チュートリアルで不要である場合、次のコマンドで削除することができます。 

```azurecli-interactive
az group delete --name myresourcegroup
```

新しく作成した 1 つのサーバーを削除するだけの場合は、 **[az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete)** コマンドを実行してください。
```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure CLI での MySQL データベースの設計](./tutorial-design-database-using-cli.md)
