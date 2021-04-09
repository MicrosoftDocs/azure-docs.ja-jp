---
title: Azure Database for PostgreSQL - フレキシブル サーバーでパラメーターを構成する
description: この記事では、Azure CLI を使用して Azure Database for PostgreSQL - フレキシブル サーバーで Postgres パラメーターを構成する方法について説明します。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 43b14858cc99cac41e277b03171fd4cac4d6eafa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "90932587"
---
# <a name="customize-server-parameters-for-azure-database-for-postgresql---flexible-server-using-azure-cli"></a>Azure CLI を使用して Azure Database for PostgreSQL - フレキシブル サーバーのサーバー パラメーターをカスタマイズする

コマンド ライン インターフェイス (Azure CLI) を使用して、Azure PostgreSQL サーバーの構成パラメーターを一覧表示、表示、更新できます。 エンジン パラメーターのサブセットは、サーバーレベルで公開され、変更が可能です。 

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL の作成](quickstart-create-server-cli.md)に関する記事に従って、Azure Database for PostgreSQL サーバーおよびデータベースを作成します。
- コンピューターに [Azure CLI](/cli/azure/install-azure-cli) コマンド ライン インターフェイスをインストールするか、ブラウザーを使用して Azure portal の [Azure Cloud Shell](../../cloud-shell/overview.md) を使用します。

## <a name="list-server-parameters-for-a-flexible-server"></a>フレキシブル サーバーのサーバー パラメーターを一覧表示する

サーバー内の変更可能なすべてのパラメーターとその値を一覧表示するには、[az postgres flexible-server parameter list](/cli/azure/postgres/flexible-server/parameter) コマンドを実行します。

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.postgres.database.azure.com** のサーバー パラメーターを一覧表示できます。

```azurecli-interactive
az postgres flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="show-server-parameter-details"></a>サーバー パラメーターの詳細を表示する

サーバーの特定のパラメーターに関する詳細を表示するには、[az postgres flexible-server parameter show](/cli/azure/postgres/flexible-server/parameter) コマンドを実行します。

この例では、サーバー **mydemoserver.postgres.database.azure.com** のリソース グループ **myresourcegroup** の **log\_min\_messages** サーバー パラメーターの詳細を表示します。

```azurecli-interactive
az postgres flexible-server parameter show --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="modify-server-parameter-value"></a>サーバー パラメーター値を変更する

特定のサーバー パラメーターの値を変更することもできます。これによって PostgreSQL サーバー エンジンの基盤となる構成値が更新されます。 パラメーターを更新するには、[az postgres flexible-server parameter set](/cli/azure/postgres/flexible-server/parameter) コマンドを使用します。 

この例では、サーバー **mydemoserver.postgres.database.azure.com** のリソース グループ **myresourcegroup** の **log\_min\_messages** サーバー パラメーターを更新します。

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --value INFO --resource-group myresourcegroup --server-name mydemoserver
```

パラメーターの値をリセットする場合、省略可能な `--value` パラメーターを除外すると、既定値が適用されます。 上記の例の場合は、次のようになります。

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

このコマンドによって、**log\_min\_messages** パラメーターが既定値の **WARNING** にリセットされます。 サーバー パラメーターと許容値の詳細については、[パラメーターの設定](https://www.postgresql.org/docs/12/config-setting.html)に関する PostgreSQL のドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

- サーバー ログの構成およびアクセスについては、「[Server Logs in Azure Database for PostgreSQL (Azure Database for PostgreSQL のサーバー ログ)](concepts-logging.md)」を参照してください。
