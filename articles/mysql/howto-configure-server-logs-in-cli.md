---
title: 低速クエリ ログにアクセスする - Azure CLI - Azure Database for MySQL
description: この記事では、Azure CLI を使用して Azure Database for MySQL の低速クエリ ログにアクセスする方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 945a67f81010a61adf814f6f6f422eba5001b48d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998551"
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
2. **log\_output** を使用して、ログの出力先を選択します。 ログをローカル ストレージと Azure Monitor 診断ログの両方に送信するには、 **[ファイル]** を選択します。 ログを Azure Monitor ログにのみ送信するには、 **[なし]** を選択します。
3. **long\_query\_time** や **log\_slow\_admin\_statements** などのパラメーターを調整します。

これらのパラメーターの値を Azure CLI で設定する方法については、[サーバーのパラメーターを構成する方法](howto-configure-server-parameters-using-cli.md)に関する記事をご覧ください。

たとえば、次の CLI コマンドは低速クエリ ログをオンにし、長時間クエリを 10 秒に設定してから、低速管理ステートメントのログ記録をオフにします。 最後に、確認のために構成のオプションが一覧表示されます。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのログの一覧表示
**log_output** が [ファイル] に構成されている場合は、サーバーのローカル ストレージから直接ログにアクセスできます。 サーバーの利用可能な低速クエリ ログ ファイルを一覧表示するには、[az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) コマンドを実行します。

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.mysql.database.azure.com** のログ ファイルを一覧表示できます。 その後、ログ ファイルの一覧を **log\_files\_list.txt** という名前のテキスト ファイルに送信します。
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>サーバーからログをダウンロードする
**log_output** が [ファイル] に構成されている場合は、[az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) コマンドを使用してサーバーから個々のログ ファイルをダウンロードできます。

次の例を使用して、リソース グループ **myresourcegroup** のサーバー **mydemoserver.mysql.database.azure.com** の特定のログ ファイルを、ローカル環境にダウンロードします。
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>次のステップ
- [Azure Database for MySQL の低速クエリ ログ](concepts-server-logs.md)について学習します。
