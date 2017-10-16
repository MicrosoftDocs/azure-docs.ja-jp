---
title: "Azure CLI を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理 | Microsoft Docs"
description: "この記事では、Azure CLI コマンド ラインを使って Azure Database for PostgreSQL ファイアウォール規則を作成し、管理する方法について説明します。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.openlocfilehash: 6f081416dd7d78f0153b3fda21a340a8c1a70c5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Azure CLI を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理
サーバーレベルのファイアウォール規則を使用すると、管理者は特定の IP アドレスまたは IP アドレス範囲からの Azure Database for PostgreSQL サーバーへのアクセスを管理できます。 便利な Azure CLI コマンドを使用すると、サーバーを管理するためのファイアウォール規則の作成、更新、削除、一覧化、表示などができます。 Azure Database for PostgreSQL ファイアウォールの概要については、「[Azure Database for PostgreSQL サーバーのファイアウォール規則](concepts-firewall-rules.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL サーバーとデータベース](quickstart-create-server-database-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) コマンド ライン ユーティリティをインストールするか、ブラウザーで Azure Cloud Shell を使用します。

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のファイアウォール規則の構成
ファイアウォール規則を構成するには、[az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) コマンドを使用します。

## <a name="list-firewall-rules"></a>ファイアウォール規則の一覧表示 
サーバー上の既存のサーバー ファイアウォール規則の一覧を表示するには、[az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule#list) コマンドを実行します。
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
規則がある場合は、既定では JSON 形式で、出力として一覧表示されます。 出力をもっとわかりやすい表形式にする場合は、`--output table` スイッチを使用することもできます。
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>ファイアウォール規則の作成
サーバーに新しいファイアウォール規則を作成するには、[az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create) コマンドを実行します。 

この例では、すべての IP アドレス範囲からサーバー **mypgserver-20170401.postgres.database.azure.com** へのアクセスが許可されます。
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
単一の IP アドレスにだけアクセスを許可するには、次の例のように、開始 IP と終了 IP に同じアドレスを入力します。
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "AllowSingleIpAddress" --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
正常に完了すると、作成したファイアウォール規則の詳細が、既定では JSON 形式で、コマンドの出力として一覧表示されます。 失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

## <a name="update-firewall-rule"></a>ファイアウォール規則の更新 
サーバー上の既存のファイアウォール規則を更新するには、[az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule#update) コマンドを使用します。 更新する既存のファイアウォール規則の名前、開始 IP、終了 IP 属性を入力します。
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
正常に完了すると、更新したファイアウォール規則の詳細が、既定では JSON 形式で、コマンドの出力として一覧表示されます。 失敗した場合は、代わりにエラー メッセージ テキストが出力されます。
> [!NOTE]
> ファイアウォール規則が存在しない場合は、更新コマンドによって新しい規則が作成されます。

## <a name="show-firewall-rule-details"></a>ファイアウォール規則の詳細の表示
サーバーの既存のファイアウォール規則の詳細を表示するには、[az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule#show) コマンドを実行することもできます。
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
正常に完了すると、指定したファイアウォール規則の詳細が、既定では JSON 形式で、コマンドの出力として一覧表示されます。 失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

## <a name="delete-firewall-rule"></a>ファイアウォール規則の削除
特定の IP 範囲からのアクセスをサーバーから取り消すには、[az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule#delete) コマンドを実行して既存のファイアウォール規則を削除します。 既存のファイアウォール規則の名前を入力します。
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
正常に完了すると、出力はありません。 失敗した場合は、エラー メッセージ テキストが返されます。

## <a name="next-steps"></a>次のステップ
- 同様に、Web ブラウザー上で [Azure Portal を使用して Azure Database for PostgreSQL ファイアウォール規則の作成と管理](howto-manage-firewall-using-portal.md)を行うことができます。
- 詳細については、「[Azure Database for MySQL サーバーのファイアウォール規則](concepts-firewall-rules.md)」をご覧ください。
- Azure Database for PostgreSQL サーバーに接続する方法のヘルプについては、「[Azure Database for PostgreSQL の接続ライブラリ](concepts-connection-libraries.md)」をご覧ください。
