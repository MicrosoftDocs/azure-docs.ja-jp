---
title: "Azure Database for PostgreSQL でのサービスのパラメーターの構成 | Microsoft Docs"
description: "この記事では、Azure CLI コマンド ラインを使って Azure Database for PostgreSQL のサービス パラメーターを構成する方法について説明します。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 9a575148a05843bef7524eff61407b377292ca3b
ms.contentlocale: ja-jp
ms.lasthandoff: 06/16/2017

---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Azure CLI を使用したサーバー構成パラメーターのカスタマイズ
コマンド ライン インターフェイス (Azure CLI) を使用して、Azure PostgreSQL サーバーの構成パラメーターを一覧表示、表示、および更新できます。 ただし、サーバーレベルで公開され、変更できるのは、エンジンの構成のサブセットのみです。 

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md) を作成するサーバーとデータベース
- [Azure CLI 2.0](/cli/azure/install-azure-cli) コマンド ライン ユーティリティをインストールするか、ブラウザーで Azure Cloud Shell を使用します。

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーのサーバー構成パラメーターを一覧表示する
サーバー内の変更可能なすべてのパラメーターとその値を一覧表示するには、[az postgres server configuration list](/cli/azure/postgres/server/configuration#list) コマンドを実行します。

サーバー **mypgserver-20170401.postgres.database.azure.com** のリソース グループ **myresourcegroup** のサーバー構成パラメーターを一覧表示できます。
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="show-server-configuration-parameter-details"></a>サーバー構成パラメーター詳細を表示する
サーバーの特定の構成パラメーターに関する詳細を表示するには、[az postgres server configuration show](/cli/azure/postgres/server/configuration#show) コマンドを実行します。

この例では、サーバー **mypgserver-20170401.postgres.database.azure.com** のリソース グループ **myresourcegroup** の **log\_min\_messages** サーバー構成パラメーターの詳細を表示します。
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="modify-server-configuration-parameter-value"></a>サーバー構成パラメーターの値を変更する
特定のサーバー構成パラメーターの値を変更することもでき、これによって PostgreSQL サーバー エンジンの基盤となる構成値が更新されます。 構成を更新するには、[az postgres server configuration set](/cli/azure/postgres/server/configuration#set) コマンドを使用します。 

この例では、サーバー **mypgserver-20170401.postgres.database.azure.com** のリソース グループ **myresourcegroup** の **log\_min\_messages** サーバー構成パラメーターを更新します。
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401 --value INFO
```
構成パラメーターの値をリセットする場合、省略可能な `--value` パラメーターを除外すると、既定値が適用されます。 上記の例の場合は、次のようになります。
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mypgserver-20170401
```
これによって、**log\_min\_messages** 構成が規定値の **WARNING** にリセットされます。 サーバーの構成と指定できる値の詳細については、[サーバー構成](https://www.postgresql.org/docs/9.6/static/runtime-config.html)に関する PostgreSQL のドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ
- サーバー ログの構成およびアクセスについては、「[Server Logs in Azure Database for PostgreSQL (Azure Database for PostgreSQL のサーバー ログ)](concepts-server-logs.md)」を参照してください。

