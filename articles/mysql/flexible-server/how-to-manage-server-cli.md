---
title: サーバーを管理する - Azure CLI - Azure Database for MySQL - フレキシブル サーバー
description: Azure CLI から Azure Database for MySQL フレキシブル サーバーを管理する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b58a9dd7901f85c59b09bc4ccb197d012bce2200
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92545057"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Azure CLI を使用して Azure Database for MySQL - フレキシブル サーバー (プレビュー) を管理する

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

この記事では、Azure でデプロイされたフレキシブル サーバー (プレビュー) を管理する方法を示します。 管理タスクには、コンピューティングとストレージのスケーリング、管理者パスワードのリセット、サーバーの詳細の表示が含まれます。

## <a name="prerequisites"></a>前提条件
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。 この記事では、Azure CLI バージョン 2.0 以降をローカルで実行している必要があります。 インストールされているバージョンを確認するには、`az --version` コマンドを実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

[az login](/cli/azure/reference-index#az-login) コマンドを使用して、アカウントにログインする必要があります。 **id** プロパティに注意してください。これは、お使いの Azure アカウントの **サブスクリプション ID** を参照します。

```azurecli-interactive
az login
```

[az account set](/cli/azure/account) コマンドを使用して、アカウントの特定のサブスクリプションを選択します。 コマンドの **subscription** 引数の値として使用する、**az login** 出力の **id** 値をメモしておきます。 複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 すべてのサブスクリプションを取得するには、[az account list](/cli/azure/account#az-account-list) を使用します。

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> フレキシブル サーバーをまだ作成していない場合は、1 つ作成して、こちらのハウツー ガイドから始めてください。

## <a name="scale-compute-and-storage"></a>コンピューティングとストレージのスケーリング

次のコマンドを使用して、コンピューティング レベル、仮想コア、およびストレージを簡単にスケールアップできます。 [az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) を実行して、すべてのサーバー操作を表示できます

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

上記の引数の詳細を次に示します。

**設定** | **値の例** | **説明**
---|---|---
name | mydemoserver | Azure Database for MySQL サーバーの一意の名前を入力します。 サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、 3 ～ 63 文字にする必要があります。
resource-group | myresourcegroup | Azure リソース グループの名前を指定します。
sku-name|Standard_D4ds_v4|コンピューティング レベルの名前とサイズを入力します。 省略表現の Standard_{VM サイズ} という規則に従います。 詳細については、[価格レベル](../concepts-pricing-tiers.md)に関するページを参照してください。
storage-size | 6144 | サーバーのストレージ容量 (単位はメガバイト)。 最小値は 5120 で、1024 ずつ増加します。

> [!Important]
> - ストレージをスケールアップすることはできますが、ストレージをスケールダウンすることはできません


## <a name="manage-mysql-databases-on-a-server"></a>サーバー上の MySQL データベースを管理します。
これらのコマンドのいずれかを使用して、サーバー上のデータベースのデータベース プロパティの作成、削除、一覧表示、表示を行うことができます

| コマンドレット | 使用法| 説明 |
| --- | ---| --- |
|[az mysql flexible-server db create](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |データベースを作成します。|
|[az mysql flexible-server db delete](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|サーバーからデータベースを削除します。 このコマンドでは、サーバーは削除されません。 |
|[az mysql flexible-server db list](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|サーバー上のすべてのデータベースの一覧を表示します|
|[az mysql flexible-server db show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|データベースの詳細を表示します|

## <a name="update-admin-password"></a>管理パスワードの更新
このコマンドで、管理者ロールのパスワードを変更できます
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  パスワードは、8 文字以上 128 文字以下にしてください。
> パスワードには、英大文字、英小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。

## <a name="delete-a-server"></a>サーバーの削除
MySQL フレキシブル サーバーを削除するだけの場合は、[az mysql flexible-server server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete) コマンドを実行してください。

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>次のステップ
- [サーバーを起動または停止する方法を確認する](how-to-stop-start-server-portal.md)
- [仮想ネットワークを管理する方法を確認する](how-to-manage-virtual-network-cli.md)
- [接続の問題のトラブルシューティング](how-to-troubleshoot-common-connection-issues.md)
- [ファイアウォールの作成および管理](how-to-manage-firewall-cli.md)