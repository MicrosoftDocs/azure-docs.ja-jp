---
title: Azure Database for PostgreSQL でのサービスのパラメーターの構成
description: この記事では、Azure CLI コマンド ラインを使って Azure Database for PostgreSQL のサービス パラメーターを構成する方法について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: c88518749129abed1cf43a70b9165035626a780f
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731565"
---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Azure CLI を使用したサーバー構成パラメーターのカスタマイズ
コマンド ライン インターフェイス (Azure CLI) を使用して、Azure PostgreSQL サーバーの構成パラメーターを一覧表示、表示、更新できます。 エンジン構成のサブセットは、サーバーレベルで公開され、変更が可能です。 

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL の作成](quickstart-create-server-database-azure-cli.md)に関する記事に従って、Azure Database for PostgreSQL サーバーおよびデータベースを作成します。
- コンピューターに [Azure CLI](/cli/azure/install-azure-cli) コマンド ライン インターフェイスをインストールするか、ブラウザーを使用して Azure portal の [Azure Cloud Shell](../cloud-shell/overview.md) を使用します。

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーのサーバー構成パラメーターを一覧表示する
サーバー内の変更可能なすべてのパラメーターとその値を一覧表示するには、[az postgres server configuration list](/cli/azure/postgres/server/configuration) コマンドを実行します。

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.postgres.database.azure.com** のサーバー構成パラメーターを一覧表示できます。
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>サーバー構成パラメーター詳細を表示する
サーバーの特定の構成パラメーターに関する詳細を表示するには、[az postgres server configuration show](/cli/azure/postgres/server/configuration) コマンドを実行します。

この例では、サーバー **mydemoserver.postgres.database.azure.com** のリソース グループ **myresourcegroup** の **log\_min\_messages** サーバー構成パラメーターの詳細を表示します。
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>サーバー構成パラメーターの値を変更する
特定のサーバー構成パラメーターの値を変更することもできます。これによって PostgreSQL サーバー エンジンの基盤となる構成値が更新されます。 構成を更新するには、[az postgres server configuration set](/cli/azure/postgres/server/configuration) コマンドを使用します。 

この例では、サーバー **mydemoserver.postgres.database.azure.com** のリソース グループ **myresourcegroup** の **log\_min\_messages** サーバー構成パラメーターを更新します。
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
構成パラメーターの値をリセットする場合、省略可能な `--value` パラメーターを除外すると、既定値が適用されます。 上記の例の場合は、次のようになります。
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
このコマンドによって、**log\_min\_messages** 構成が既定値の **WARNING** にリセットされます。 サーバーの構成と許容値の詳細については、[サーバー構成](https://www.postgresql.org/docs/9.6/static/runtime-config.html)に関する PostgreSQL のドキュメントを参照してください。

## <a name="next-steps"></a>次の手順
- サーバー ログの構成およびアクセスについては、「[Server Logs in Azure Database for PostgreSQL (Azure Database for PostgreSQL のサーバー ログ)](concepts-server-logs.md)」を参照してください。
