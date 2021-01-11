---
title: ログを管理する - Azure CLI - Azure Database for PostgreSQL - Single Server
description: この記事では、Azure CLI を使用して Azure Database for PostgreSQL - Single Server でサーバー ログ (.log files) を構成してアクセスする方法について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 50875d0575da7287269bee55004a2acfa5396cd5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87493552"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Azure CLI を使用してサーバー ログを構成してアクセスする
コマンド ライン インターフェイス (Azure CLI) を使用して PostgreSQL サーバーのエラー ログをダウンロードできます。 ただし、トランザクション ログへのアクセスはサポートされていません。 

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) コマンド ライン ユーティリティまたはブラウザーでの Azure Cloud Shell

## <a name="configure-logging"></a>ログの構成
クエリ ログとエラー ログにアクセスするサーバーを構成できます。 エラー ログには、自動バキューム、接続、およびチェックポイント情報を含めることができます。
1. ログ記録を有効にします。
2. クエリのログ記録を有効にするには、**log\_statement** と **log\_min\_duration\_statement** を更新します。
3. 保有期間を更新します。

詳細については、[サーバー構成パラメーターのカスタマイズ](howto-configure-server-parameters-using-cli.md)に関するページを参照してください。

## <a name="list-logs"></a>ログの一覧表示
サーバーの利用可能なログ ファイルを一覧表示するには、[az postgres server-logs list](/cli/azure/postgres/server-logs) コマンドを実行します。

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.postgres.database.azure.com** のログ ファイルを一覧表示できます。 その後、ログ ファイルの一覧を **log\_files\_list.txt** という名前のテキスト ファイルに送信します。
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>サーバーからローカルにログをダウンロードする
[az postgres server-logs download](/cli/azure/postgres/server-logs) コマンドを使用して、サーバーの個別のログ ファイルをダウンロードできます。 

次の例を使用して、リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.postgres.database.azure.com** の特定のログ ファイルをローカル環境にダウンロードします。
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>次のステップ
- サーバー ログの詳細については、「[Azure Database for PostgreSQL のサーバー ログ](concepts-server-logs.md)」を参照してください。
- サーバー パラメーターの詳細については、[Azure CLI を使用したサーバー構成パラメーターのカスタマイズ](howto-configure-server-parameters-using-cli.md)に関するページを参照してください。
