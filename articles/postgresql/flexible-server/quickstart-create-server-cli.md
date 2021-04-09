---
title: クイック スタート:サーバーを作成する - Azure CLI - Azure Database for PostgreSQL - フレキシブル サーバー
description: このクイック スタートでは、Azure CLI を使用して、Azure Database for PostgreSQL フレキシブル サーバーを Azure リソース グループ内に作成する方法を説明します。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d43fab43b05ccfd88308390f58ba48ff9111b295
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94842823"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure Database for PostgreSQL フレキシブル サーバーを作成する

このクイック スタートでは、[Azure Cloud Shell](https://shell.azure.com) で [Azure CLI](/cli/azure/get-started-with-azure-cli) コマンドを使用して、Azure Database for PostgreSQL フレキシブル サーバーを 5 分で作成する方法について説明します。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

> [!IMPORTANT] 
> Azure Database for PostgreSQL フレキシブル サーバーは現在プレビュー段階です。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

[Azure Cloud Shell](../../cloud-shell/overview.md) は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を開くこともできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、 **[入力]** を選択して実行します。

CLI をローカルにインストールして使用する場合、このクイックスタートでは、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

[az login](/cli/azure/reference-index#az-login) コマンドを使用して、アカウントにログインする必要があります。 **id** プロパティに注意してください。これは、お使いの Azure アカウントの **サブスクリプション ID** を参照します。

```azurecli-interactive
az login
```

[az account set](/cli/azure/account#az-account-set) コマンドを使用して、アカウントの特定のサブスクリプションを選択します。 コマンドの **subscription** 引数の値として使用する、**az login** 出力の **id** 値をメモしておきます。 複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 すべてのサブスクリプションを取得するには、[az account list](/cli/azure/account#az-account-list) を使用します。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>フレキシブル サーバーを作成する

`az group create` コマンドを使用して、[Azure リソース グループ](../../azure-resource-manager/management/overview.md)を作成し、このリソース グループ内に PostgreSQL フレキシブル サーバーを作成します。 一意の名前を指定する必要があります。 次の例では、`westus` の場所に `myresourcegroup` という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

`az postgres flexible-server create` コマンドを使用して、フレキシブル サーバーを作成します。 1 つのサーバーに複数のデータベースを含めることができます。 次のコマンドでは、サービスの既定値と Azure CLI の[ローカル コンテキスト](/cli/azure/local-context)からの値を使用してサーバーを作成します。 

```azurecli
az postgres flexible-server create
```

作成されたサーバーには、次の属性があります。 
- 自動生成されたサーバー名、管理者のユーザー名、管理者のパスワード、リソース グループ名 (ローカル コンテキストでまだ指定されていない場合)、およびリソース グループと同じ場所 
- 残りのサーバー構成のサービスの既定値: コンピューティング レベル (汎用)、コンピューティング サイズ/SKU (D2s_v3 - 2 vCore、8 GB RAM)、バックアップの保持期間 (7 日間)、および PostgreSQL のバージョン (12)
- 既定の接続方法は、自動生成された仮想ネットワークとサブネットを使用するプライベート アクセス (VNet 統合) です。

> [!NOTE] 
> サーバーの作成後に接続方法を変更することはできません。 たとえば、作成時に "*プライベート アクセス (VNet 統合)* " を選択した場合、作成後に *パブリック アクセス (使用できる IP アドレス)* に変更することはできません。 VNet 統合を使用してサーバーに安全にアクセスするには、プライベート アクセスを指定してサーバーを作成することを強くお勧めします。 プライベート アクセスの詳細については、[概念に関する記事](./concepts-networking.md)を参照してください。

既定値を変更したい場合は、Azure CLI リファレンス ドキュメントで、 <!--FIXME --> 構成可能な CLI パラメーターの完全な一覧を参照してください。 

> [!NOTE]
> Azure Database for PostgreSQL との接続では、ポート 5432 が通信に使用されます。 企業ネットワーク内から接続を試みる場合、ポート 5432 でのアウトバウンド トラフィックが許可されていない場合があります。 その場合、会社の IT 部門によってポート 5432 が開放されない限り、サーバーに接続することはできません。

## <a name="get-the-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。

```azurecli-interactive
az postgres flexible-server show --resource-group myresourcegroup --name mydemoserver
```

結果は JSON 形式です。 **fullyQualifiedDomainName** と **administratorLogin** の値を書き留めておきます。

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "name": "Standard_D2s_v3",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 131072
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "12"
}
```

## <a name="connect-using-postgresql-command-line-client"></a>PostgreSQL コマンドライン クライアントを使用して接続する

"*プライベート アクセス (VNet 統合)* " を指定してフレキシブル サーバーを作成したので、サーバーと同じ VNet 内のリソースからサーバーに接続する必要があります。 仮想マシンを作成して、作成された仮想ネットワークに追加できます。 

VM が作成されたら、コンピューターに SSH 接続して、 **[psql](https://www.postgresql.org/download/)** コマンドライン ツールをインストールできます。

psql では、次のコマンドを使用して接続します。 値は実際のサーバー名とパスワードに置き換えてください。 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

これらのリソースが別のクイック スタート/チュートリアルで不要である場合、次のコマンドで削除することができます。

```azurecli-interactive
az group delete --name myresourcegroup
```

新しく作成した 1 つのサーバーを削除するだけの場合は、`az postgres flexible-server delete` コマンドを実行できます。

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[App Service と PostgreSQL を使用して Django アプリをデプロイする](tutorial-django-app-service-postgres.md)
