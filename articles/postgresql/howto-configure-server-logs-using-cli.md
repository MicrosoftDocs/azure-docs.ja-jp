---
title: "Azure CLI を使用した PostgreSQL のサーバー ログの構成とアクセス | Microsoft Docs"
description: "この記事では、Azure CLI コマンド ラインを使用した Azure Database for PostgreSQL のサーバー ログの構成方法とアクセス方法について説明します。"
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
ms.openlocfilehash: 920656aabe21191470f2611279977a763ac14c36
ms.contentlocale: ja-jp
ms.lasthandoff: 06/16/2017

---
# <a name="configure-and-access-server-logs-using-azure-cli"></a>Azure CLI を使用した PostgreSQL のサーバー ログの構成とアクセス
PostgreSQL サーバーのエラー ログは、コマンド ライン インターフェイス (Azure CLI) を使用してダウンロードできます。 ただし、トランザクション ログへのアクセスはサポートされていません。 

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) コマンド ライン ユーティリティをインストールするか、ブラウザーで Azure Cloud Shell を使用します。

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のログ記録の構成
クエリ ログとエラー ログにアクセスするサーバーを構成できます。 エラー ログには、自動バキューム、接続、チェックポイントなどの情報を含めることができます。
1. ログ記録を有効にする
2. log\_statement と log\_min\_duration\_statement を更新し、クエリのログ記録を有効にする
3. 保持期間を更新する

詳細については、[サーバー構成パラメーターのカスタマイズ](howto-configure-server-parameters-using-cli.md)に関するページをご覧ください。

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーのログの一覧表示
サーバーの利用可能なログ ファイルを一覧表示するには、[az postgres server-logs list](/cli/azure/postgres/server-logs#list) コマンドを実行します。

リソース グループ **myresourcegroup** のサーバー **mypgserver-20170401.postgres.database.azure.com** のログ ファイルを一覧表示し、それを **log\_files\_list.txt** と呼ばれるテキスト ファイルに出力できます。
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mypgserver-20170401 > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>サーバーからローカルにログをダウンロードする
[az postgres server-logs download](/cli/azure/postgres/server-logs#download) コマンドを使用すると、サーバーの個別のログ ファイルをダウンロードすることができます。 

この例では、リソース グループ **myresourcegroup** のサーバー **mypgserver-20170401.postgres.database.azure.com** の特定のログ ファイルをローカル環境にダウンロードします。
```azurecli-interactive
az postgres server-logs download --name 20170414-mypgserver-20170401-postgresql.log --resource-group myresourcegroup --server mypgserver-20170401
```
## <a name="next-steps"></a>次のステップ
- サーバー ログの詳細については、「[Azure Database for PostgreSQL のサーバー ログ](concepts-server-logs.md)」をご覧ください。
- サーバー パラメーターの詳細については、「[サーバー構成パラメーターのカスタマイズ](howto-configure-server-parameters-using-cli.md)」をご覧ください。

