---
title: サーバーを管理する - Azure CLI - Azure Database for PostgreSQL
description: Azure CLI から Azure Database for PostgreSQL サーバーを管理する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 2ea07e2bc12e6fc0d62abd462b8537c6a93689f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935803"
---
# <a name="manage-an-azure-database-for-postgresql-single-server-using-the-azure-cli"></a>Azure CLI を使用して Azure Database for PostgreSQL 単一サーバーを管理する

この記事では、Azure でデプロイされた単一サーバーを管理する方法を示します。 管理タスクには、コンピューティングとストレージのスケーリング、管理者パスワードのリセット、サーバーの詳細の表示が含まれます。

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

まだサーバーを作成していない場合は、この[クイックスタート](quickstart-create-server-database-azure-cli.md)を参照して作成してください。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="scale-compute-and-storage"></a>コンピューティングとストレージのスケーリング

次のコマンドを使用して、価格レベル、コンピューティング、ストレージを簡単にスケールアップできます。 [az postgres server overview](/cli/azure/mysql/server)で、実行できるすべてのサーバー操作を表示できます。

```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

上記の引数の詳細を次に示します。

**設定** | **値の例** | **説明**
---|---|---
name | mydemoserver | Azure Database for PostgreSQL サーバー名として一意の名前を入力します。 サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、 3 ～ 63 文字にする必要があります。
resource-group | myresourcegroup | Azure リソース グループの名前を指定します。
sku-name|GP_Gen5_2|価格レベルとコンピューティング構成の名前を入力します。 省略表現の {価格レベル} _{コンピューティング世代}_ {仮想コア} という規則に従います。 詳細については、[価格レベル](./concepts-pricing-tiers.md)に関するページを参照してください。
storage-size | 6144 | サーバーのストレージ容量 (単位はメガバイト)。 最小値は 5120 で、1024 ずつ増加します。

> [!Important]
> - ストレージをスケールアップすることはできますが、ストレージをスケールダウンすることはできません
> - Basic から汎用またはメモリ最適化への価格レベルのスケールアップはサポートされていません。 [bash スクリプトを使用](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)するか、 [PostgreSQL Workbench を使用](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)すると、手動でスケールアップできます。


## <a name="manage-postgresql-databases-on-a-server"></a>サーバー上の PostgreSQL データベースを管理します。
これらのコマンドのいずれかを使用して、サーバー上のデータベースのデータベース プロパティの作成、削除、一覧表示、表示を行うことができます

| コマンドレット | 使用法| 説明 |
| --- | ---| --- |
|[az postgres db create](/cli/azure/sql/db#az-mysql-db-create)|```az postgres db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |データベースを作成します。|
|[az postgres db delete](/cli/azure/sql/db#az-mysql-db-delete)|```az postgres db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|サーバーからデータベースを削除します。 このコマンドでは、サーバーは削除されません。 |
|[az postgres db list](/cli/azure/sql/db#az-mysql-db-list)|```az postgres db list -g myresourcegroup -s mydemoserver```|サーバー上のすべてのデータベースの一覧を表示します|
|[az postgres db show](/cli/azure/sql/db#az-mysql-db-show)|```az postgres db show -g myresourcegroup -s mydemoserver -n mydatabasename```|データベースの詳細を表示します|

## <a name="update-admin-password"></a>管理パスワードの更新
このコマンドで、管理者ロールのパスワードを変更できます
```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  パスワードは、8 文字以上 128 文字以下にしてください。
> パスワードには、英大文字、英小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。

## <a name="delete-a-server"></a>サーバーの削除
PostgreSQL 単一サーバーを削除するだけの場合は、[az postgres server delete](/cli/azure/mysql/server#az-mysql-server-delete) コマンドを実行してください。

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>次のステップ
- [サーバーを再起動する](howto-restart-server-cli.md)
- [正常な状態でないサーバーを復元する](howto-restore-server-cli.md)
- [サーバーを監視して調整する](concepts-monitoring.md)
