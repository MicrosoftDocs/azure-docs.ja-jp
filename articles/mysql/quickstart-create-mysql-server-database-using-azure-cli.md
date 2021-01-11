---
title: クイック スタート:サーバーを作成する - Azure CLI - Azure Database for MySQL
description: このクイック スタートでは、Azure CLI を使用して、Azure Database for MySQL サーバーを Azure リソース グループに作成する方法を説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a7d69ae15e72133e08b3e66de607aa06fefdbd32
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495388"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>クイック スタート:Azure CLI を使用した Azure Database for MySQL サーバーの作成

> [!TIP]
> よりシンプルな [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI コマンド (現在はプレビュー段階) の使用を検討してください。 こちらの[クイック スタート](./quickstart-create-server-up-azure-cli.md) をお試しください。

このクイック スタートでは、[Azure Cloud Shell](https://shell.azure.com) の [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) コマンドを使用して、Azure Database for MySQL サーバーを 5 分で作成する方法について説明します。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

> [!NOTE]
> CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件
この記事では、Azure CLI バージョン 2.0 以降をローカルで実行している必要があります。 インストールされているバージョンを確認するには、`az --version` コマンドを実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

[az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) コマンドを使用して、アカウントにログインする必要があります。 **id** プロパティに注意してください。これは、お使いの Azure アカウントの**サブスクリプション ID** を参照します。 

```azurecli-interactive
az login
```

[az account set](/cli/azure/account) コマンドを使用して、アカウントの特定のサブスクリプションを選択します。 コマンドの **subscription** 引数の値として使用する、**az login** 出力の **id** 値をメモしておきます。 複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 すべてのサブスクリプションを取得するには、[az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) を使用します。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成
[az group create](/cli/azure/group) コマンドを使用して、[Azure リソース グループ](../azure-resource-manager/management/overview.md)を作成し、このリソース グループ内に MySQL サーバーを作成します。 一意の名前を指定する必要があります。 次の例では、`westus` の場所に `myresourcegroup` という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

[az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) コマンドを使用して、Azure Database for MySQL サーバーを作成します。 1 つのサーバーに複数のデータベースを含めることができます。

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

上記の引数の詳細を次に示します。 

**設定** | **値の例** | **説明**
---|---|---
name | mydemoserver | Azure Database for MySQL サーバーの一意の名前を入力します。 サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、 3 ～ 63 文字にする必要があります。
resource-group | myresourcegroup | Azure リソース グループの名前を指定します。
location | westus | サーバーの Azure の場所。
admin-user | myadmin | 管理者ログインのユーザー名。 これを **azure_superuser**、**admin**、**administrator**、**root**、**guest**、**public** にすることはできません。
admin-password | *セキュリティで保護されたパスワード* | 管理者ユーザーのパスワード。 8 ～ 128 文字にする必要があります。 パスワードには、英大文字、英小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。
sku-name|GP_Gen5_2|価格レベルとコンピューティング構成の名前を入力します。 省略表現の {価格レベル} _{コンピューティング世代}_ {仮想コア} という規則に従います。 詳細については、[価格レベル](./concepts-pricing-tiers.md)に関するページを参照してください。

>[!IMPORTANT] 
>- サーバーの既定の MySQL バージョンは 5.7 です。 現在、5.6 と 8.0 のバージョンも使用できます。
>- **az mysql server create** コマンドのすべての引数を確認するには、この[リファレンス ドキュメント](/cli/azure/mysql/server#az-mysql-server-create)を参照してください。
>- サーバーでは、既定で SSL が有効になっています。 SSL の詳細については、[SSL 接続の構成](howto-configure-ssl.md)に関するページを参照してください

## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成 
既定では、作成された新しいサーバーはファイアウォール規則で保護され、パブリックにアクセスすることはできません。 [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule) コマンドを使用して、サーバーでファイアウォール規則を構成できます。 これにより、サーバーにローカルで接続できるようになります。

次の例では、特定の IP アドレス 192.168.0.1 からの接続を許可する、`AllowMyIP` と呼ばれるファイアウォール規則を作成しています。 接続元となる IP アドレスを置き換えてください。 必要に応じて、IP アドレスの範囲を使用できます。 IP アドレスの検索方法がわからない場合は、[https://whatismyipaddress.com/](https://whatismyipaddress.com/) にアクセスして IP アドレスを取得してください。

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Azure Database for MySQL との接続では、ポート 3306 が通信に使用されます。 企業ネットワーク内から接続を試みる場合、ポート 3306 での送信トラフィックが許可されていない場合があります。 その場合、会社の IT 部門によってポート 3306 が開放されない限り、サーバーに接続することはできません。

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

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>mysql コマンドライン クライアントを使用して Azure Database for MySQL サーバーに接続する
[Azure Cloud Shell](../cloud-shell/overview.md) で、一般的なクライアント ツールの **[mysql.exe](https://dev.mysql.com/downloads/)** コマンドライン ツールを使用して、サーバーに接続できます。 あるいは、ローカル環境で mysql コマンド ラインを使用することもできます。
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする
これらのリソースが別のクイック スタート/チュートリアルで不要である場合、次のコマンドで削除することができます。 

```azurecli-interactive
az group delete --name myresourcegroup
```

新しく作成した 1 つのサーバーを削除するだけの場合は、[az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete) コマンドを実行してください。

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[Windows 上で PHP アプリと MySQL を構築する](../app-service/app-service-web-tutorial-php-mysql.md)
>[Linux 上で PHP アプリと MySQL を構築する](../app-service/containers/tutorial-php-mysql-app.md)
>[MySQL を使用して Java ベースの Spring アプリを構築する](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)
