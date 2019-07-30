---
title: Azure CLI を使用して Azure Database for MySQL の低速クエリ ログにアクセスする
description: この記事では、Azure CLI を使用して Azure Database for MySQL の低速クエリ ログにアクセスする方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: e6d25a4d8b470580626cab4a84f9d912a3f79f75
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612652"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Azure CLI を使用して低速クエリ ログを構成してアクセスする
Azure CLI (Azure のコマンドライン ユーティリティ) を使用して Azure Database for MySQL の低速クエリ ログをダウンロードできます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-cli.md)
- ブラウザーでの [Azure CLI](/cli/azure/install-azure-cli) または Azure Cloud Shell

## <a name="configure-logging"></a>ログの構成
以下の手順に従って、MySQL 低速クエリ ログにアクセスするサーバーを構成できます。
1. **slow\_query\_log** パラメーターをオンに設定して低速クエリ ログ記録を有効にします。
2. **long\_query\_time** や **log\_slow\_admin\_statements** などのパラメーターを調整します。

これらのパラメーターの値を Azure CLI で設定する方法については、[サーバーのパラメーターを構成する方法](howto-configure-server-parameters-using-cli.md)に関する記事をご覧ください。

たとえば、次の CLI コマンドは低速クエリ ログをオンにし、長時間クエリを 10 秒に設定してから、低速管理ステートメントのログ記録をオフにします。 最後に、確認のために構成のオプションが一覧表示されます。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのログの一覧表示
サーバーの利用可能な低速クエリ ログ ファイルを一覧表示するには、[az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) コマンドを実行します。

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.mysql.database.azure.com** のログ ファイルを一覧表示できます。 その後、ログ ファイルの一覧を **log\_files\_list.txt** という名前のテキスト ファイルに送信します。
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>サーバーからログをダウンロードする
[az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) コマンドで、サーバーの個々のログ ファイルをダウンロードできます。 

次の例を使用して、リソース グループ **myresourcegroup** のサーバー **mydemoserver.mysql.database.azure.com** の特定のログ ファイルを、ローカル環境にダウンロードします。
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>次の手順
- [Azure Database for MySQL の低速クエリ ログ](concepts-server-logs.md)について学習します。
