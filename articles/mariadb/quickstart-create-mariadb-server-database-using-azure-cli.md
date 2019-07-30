---
title: クイック スタート:Azure Database for MariaDB サーバーを作成する - Azure CLI
description: このクイック スタートでは、Azure CLI を使用して、Azure Database for MariaDB サーバーを Azure リソース グループに作成する方法を説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/09/2019
ms.custom: mvc
ms.openlocfilehash: 7d94834523e331ff048f787760561739765e7023
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171412"
---
# <a name="create-an-azure-database-for-mariadb-server-by-using-the-azure-cli"></a>Azure CLI を使用して Azure Database for MariaDB サーバーを作成する

Azure CLI を使用すると、コマンドラインまたはスクリプトで Azure リソースを作成したり管理したりすることができます。 このクイック スタートでは、Azure CLI を使用して、約 5 分で Azure Database for MariaDB サーバーを Azure リソース グループに作成する方法を説明します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

このクイック スタートで、CLI をローカルにインストールして使用する場合、Azure CLI バージョン 2.0 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 CLI をインストールまたはアップグレードする必要がある場合は、「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)]( /cli/azure/install-azure-cli)」を参照してください。 

複数のサブスクリプションをお持ちの場合は、リソースが含まれているサブスクリプションまたはお客様が課金の対象となっているサブスクリプションを選択してください。 アカウントの特定のサブスクリプション ID を選択するには、[az account set](/cli/azure/account#az-account-set) コマンドを使用します。

```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az-group-create) コマンドを使用して [Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

次の例では、`myresourcegroup` という名前のリソース グループを `westus` の場所に作成します。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB サーバーを作成する

[az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) コマンドを使用して、Azure Database for MariaDB サーバーを作成します。 1 つのサーバーで複数のデータベースを管理できます。 通常は、プロジェクトまたはユーザーごとに個別のデータベースを使用します。

Setting | 値の例 | 説明
---|---|---
name | **mydemoserver** | Azure Database for MariaDB サーバーを識別する一意の名前を入力します。 サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、 3 から 63 文字にする必要があります。
resource-group | **myresourcegroup** | Azure リソース グループの名前を入力します。
sku-name | **GP_Gen5_2** | SKU の名前。 省略表現の *<価格レベル>* \_ *<コンピューティング世代>* \_ *<仮想コア>* という規則に従います。 **sku-name** パラメーターの詳細については、この表の後続のセクションを参照してください。
backup-retention | **7** | バックアップを保持する必要のある時間。 単位は日数です。 範囲は 7 から 35 です。 
geo-redundant-backup | **Disabled** | このサーバーに対して geo 冗長バックアップを有効にする必要があるかどうかどうか。 使用できる値は以下の通りです。**Enabled**、**Disabled**
location | **westus** | サーバーの Azure の場所。
ssl-enforcement | **有効** | このサーバーに対して SSL を有効にする必要があるかどうかどうか。 使用できる値は以下の通りです。**Enabled**、**Disabled**
storage-size | **51200** | サーバーのストレージ容量 (単位はメガバイト)。 有効なストレージ サイズは 5,120 MB 以上です (増分単位は 1,024 MB)。 ストレージ サイズの制限の詳細については、[価格レベル](./concepts-pricing-tiers.md)に関するページを参照してください。 
version | **10.2** | MariaDB メジャー エンジンのバージョン。
admin-user | **myadmin** | 管理者ログインのユーザー名。 **admin-user** パラメーターに **azure_superuser**、**admin**、**administrator**、**root**、**guest**、**public** は使用できません。
admin-password | *<お使いのパスワード>* | 管理者ユーザーのパスワード。 パスワードは 8 から 128 文字にする必要があります。 パスワードには、英大文字、英小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。

sku-name パラメーターの値は、次の例のように、{価格レベル}\_{コンピューティング世代}\_{仮想コア数} という規約に従います。
+ `--sku-name B_Gen5_1` は、"Basic、Gen 5、および 1 個の仮想コア" にマップされます。 このオプションは、利用できる最小の SKU です。
+ `--sku-name GP_Gen5_32` は、"汎用、Gen 5、および 32 個の仮想コア" にマップされます。
+ `--sku-name MO_Gen5_2` は、"メモリ最適化、Gen 5、および 2 個の仮想コア" にマップされます。

リージョンおよびレベルごとの有効な値については、[価格レベル](./concepts-pricing-tiers.md)に関するページを参照してください。

次の例では、米国西部リージョンに **mydemoserver** という名前のサーバーを作成します。 このサーバーは、リソース グループ **myresourcegroup** に存在し、サーバー管理者ログイン **myadmin** を含んでいます。 汎用価格レベルの Gen 5 サーバーで、仮想コア数は 2 です。 サーバー名は DNS 名に対応しており、Azure 内でグローバルに一意であることが必要です。 `<server_admin_password>` は、実際のサーバー管理者パスワードに置き換えてください。

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

> [!NOTE]
> 低負荷なコンピューティングと I/O がワークロードに適している場合は、Basic 価格レベルの使用を検討してください。 Basic 価格レベルで作成されたサーバーは後で General Purpose またはメモリ最適化にスケーリングできないことに注意してください。 詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/mariadb/)を参照してください。
> 

## <a name="configure-a-firewall-rule"></a>ファイアウォール規則を構成する

[az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) コマンドを使用して、Azure Database for MariaDB サーバーレベルのファイアウォール規則を作成します。 サーバーレベルのファイアウォール規則により、mysql コマンドライン ツールや MySQL Workbench などの外部アプリケーションが、Azure Database for MariaDB サービスのファイアウォールを経由してサーバーに接続できるようになります。 

次の例では、特定の IP アドレス 192.168.0.1 からの接続を許可する、`AllowMyIP` と呼ばれるファイアウォール規則を作成しています。 お客様の接続元となる場所に対応する IP アドレスまたは IP アドレスの範囲に置き換えてください。 

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Azure Database for MariaDB との接続では、ポート 3306 が通信に使用されます。 企業ネットワーク内から接続しようとしても、ポート 3306 でのアウトバウンド トラフィックが許可されていない場合があります。 このようなケースでサーバーに接続できるのは、IT 部門によってポート 3306 が開放されている場合だけです。
> 

## <a name="configure-ssl-settings"></a>SSL 設定の構成

既定では、サーバーとクライアント アプリケーション間で SSL 接続が適用されます。 この既定の設定では、インターネット経由のデータ ストリームを暗号化することによって、"移動中" のデータのセキュリティが確保されます。 このクイック スタートでは、サーバーの SSL 接続を無効にします。 実稼働サーバーで SSL を無効にすることはお勧めしません。 詳細については、「[Azure Database for MariaDB に安全に接続するためにご利用のアプリケーション内で SSL 接続を構成する](./howto-configure-ssl.md)」を参照してください。

次の例では、Azure Database for MariaDB サーバー上で SSL の適用が無効になります。
 
```azurecli-interactive
az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
```

## <a name="get-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。 接続情報を取得するには、次のコマンドを実行します。

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

結果は JSON 形式です。 **fullyQualifiedDomainName** と **administratorLogin** の値を書き留めておきます。

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-by-using-the-mysql-command-line-tool"></a>mysql コマンドライン ツールを使用してサーバーに接続する

mysql コマンドライン ツールを使用してサーバーに接続します。 このコマンドライン ツールは、[ダウンロード](https://dev.mysql.com/downloads/)してコンピューターにインストールできます。 また、この記事のコード サンプルにある **[使ってみる]** ボタンを選択することで、コマンドライン ツールにアクセスすることもできます。 さらに、Azure portal の右上のツール バーにある **[>_]** ボタンを選択し、**Azure Cloud Shell** を開くことによって、コマンドライン ツールにアクセスすることもできます。

mysql コマンドライン ツールを使用してサーバーに接続するには:

1. サーバーに接続します。

   ```azurecli-interactive
   mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   ```

2. `mysql>` プロンプトでサーバーの状態を表示します。

   ```sql
   status
   ```
   次のようなテキストが表示されます。

   ```bash
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: ***********
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 65512
   Server version: 5.6.39.0 MariaDB Server

   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

   Connection id:          64681
   Current database:
   Current user:           myadmin@40.118.201.21
   SSL:                    Cipher in use is AES256-SHA
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.39.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 1 day 3 hours 28 min 50 sec

   Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
   --------------

   mysql>
   ```

> [!TIP]
> その他のコマンドについては、「[MySQL 5.7 リファレンス マニュアル - 4.5.1 章](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)」を参照してください。

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>MySQL Workbench を使用したサーバーへの接続

1. お使いのクライアント コンピューターで MySQL Workbench を開きます。 これをまだインストールしていない場合は、[ダウンロード](https://dev.mysql.com/downloads/workbench/)してインストールしてください。

2. **[Setup New Connection]\(新しい接続の設定\)** ダイアログ ボックスの **[Parameters]\(パラメーター\)** タブに次の情報を入力します。

   ![新しい接続の設定](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

   | Setting | 推奨値 | 説明 |
   |---|---|---|
   | 接続名 | **Demo connection** | この接続のラベル (任意の接続名) を入力します |
   | 接続方法 | **Standard (TCP/IP)** | TCP/IP プロトコルを使用して Azure Database for MariaDB に接続します |
   | ホスト名 | **mydemoserver.mariadb.database.azure.com** | 先ほど書き留めたサーバー名。 |
   | Port | **3306** | Azure Database for MariaDB 用の既定のポート。 |
   | ユーザー名 | **myadmin\@mydemoserver** | 先ほど書き留めたサーバー管理者ログイン。 |
   | パスワード | *<お使いのパスワード>* | 先ほど設定した管理者アカウントのパスワードを使用します。 |

3. すべてのパラメーターが正しく構成されているかどうかを確認するために、 **[Test Connection]\(接続のテスト\)** を選択します。

4. 接続を選択するとサーバーに正常に接続できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートで使用したリソースが別のクイック スタートまたはチュートリアルに必要なければ、次のコマンドを実行して削除できます。 

```azurecli-interactive
az group delete --name myresourcegroup
```

このクイック スタートで作成したサーバーだけを削除したい場合は、[az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete) コマンドを実行します。

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure CLI での MariaDB データベースの設計](./tutorial-design-database-cli.md)
