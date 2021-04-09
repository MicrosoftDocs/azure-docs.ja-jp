---
title: ファイアウォール規則の管理 - Azure CLI - Azure Database for MariaDB
description: この記事では、Azure CLI コマンド ラインを使って Azure Database for MariaDB ファイアウォール規則を作成し、管理する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ef04e2b4c820c14ea7df6c35ecb0189ef31ef7dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665040"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Azure CLI を使用した Azure Database for MariaDB ファイアウォール規則の作成と管理
サーバーレベルのファイアウォール規則を使用して、特定の IP アドレスまたは IP アドレス範囲からの Azure Database for MariaDB サーバーへのアクセスを管理できます。 便利な Azure CLI コマンドを使用すると、サーバーを管理するためのファイアウォール規則の作成、更新、削除、一覧化、表示などができます。 Azure Database for MariaDB ファイアウォールの概要については、「[Azure Database for MariaDB サーバーのファイアウォール規則](./concepts-firewall-rules.md)」をご覧ください。

仮想ネットワーク (VNet) 規則を使用して、サーバーへのアクセスをセキュリティで保護することもできます。 [Azure CLI を使用した仮想ネットワーク サービス エンドポイントと規則の作成と管理](howto-manage-vnet-cli.md)の詳細を確認してください。

## <a name="prerequisites"></a>前提条件
* [Azure CLI のインストール](/cli/azure/install-azure-cli)。
* [Azure Database for MariaDB サーバーとデータベース](quickstart-create-mariadb-server-database-using-azure-cli.md)。

## <a name="firewall-rule-commands"></a>ファイアウォール規則のコマンド:
Azure CLI の **az mariadb server firewall-rule** コマンドで、ファイアウォール規則を作成、削除、一覧表示、表示、更新します。

コマンド:
- **create**: Azure MariaDB サーバーのファイアウォール規則を作成します。
- **delete**: Azure MariaDB サーバーのファイアウォール規則を削除します。
- **list**: Azure MariaDB サーバーのファイアウォール規則を一覧表示します。
- **show**: Azure MariaDB サーバーのファイアウォール規則の詳細を表示します。
- **update**: Azure MariaDB サーバーのファイアウォール規則を更新します。

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Azure にサインインして Azure Database for MariaDB サーバーを一覧表示する
**az login** コマンドを使用して、ご利用の Azure アカウントで Azure CLI に安全に接続します。

1. コマンド ラインから次のコマンドを実行します。
   ```azurecli
   az login
   ```
   このコマンドにより、次の手順で使用するコードが出力されます。

2. Web ブラウザーを使用して [https://aka.ms/devicelogin](https://aka.ms/devicelogin) ページを開いた後、コードを入力します。

3. プロンプトで、Azure 資格情報を使用してサインインします。

4. ログインの認証が完了すると、サブスクリプションの一覧がコンソールに出力されます。 目的のサブスクリプションの ID をコピーして、使用する現在のサブスクリプションを設定します。 [az account set](/cli/azure/account#az-account-set) コマンドを使用します。
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. サブスクリプションとリソース グループの名前がわからない場合は、Azure Databases for MariaDB サーバーを一覧表示します。 [az mariadb server list](/cli/azure/mariadb/server#az-mariadb-server-list) コマンドを使用します。

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   この一覧に表示される名前属性を確認します。これは、使用する MariaDB サーバーを指定するために必要です。 必要に応じて、そのサーバーの詳細を確認し、名前属性を使用して正しいかどうかを確認します。 [az mariadb server show](/cli/azure/mariadb/server#az-mariadb-server-show) コマンドを使用します。

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Azure Database for MariaDB サーバーのファイアウォール規則を一覧表示する 
サーバー名とリソース グループ名を使用して、そのサーバー上で既存のサーバー ファイアウォール規則を一覧表示します。 [az mariadb server firewall list](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list) コマンドを使用します。  サーバー名属性は、 **--name** スイッチではなく **--server** スイッチで指定されることに注意してください。 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
規則がある場合は、JSON 形式 (既定) で出力として一覧表示されます。 **--output table** スイッチを使用すると、結果をよりわかりやすい表形式で出力できます。
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Azure Database for MariaDB サーバーのファイアウォール規則を作成する
Azure MariaDB サーバー名とリソース グループ名を使用して、サーバーに新しいファイアウォール規則を作成します。 [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) コマンドを使用します。 規則の名前に加え、(IP アドレス範囲へのアクセスを提供するための) 開始 IP と終了 IP を指定します。
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

1 つの IP アドレスに対するアクセスを許可するには、次の例のように、開始 IP と終了 IP に同じ IP アドレスを指定します。
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Azure IP アドレスからの Azure Database for MariaDB サーバーへの接続を許可するには、次の例のように、開始 IP と終了 IP に IP アドレス 0.0.0.0 を指定します。
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> このオプションでは、他のお客様のサブスクリプションからの接続を含む、Azure からのすべての接続を許可するようにファイアウォールを構成します。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。
> 

正常に完了すると、各コマンドの出力として、作成したファイアウォール規則の詳細が JSON 形式 (既定) で一覧表示されます。 失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Azure Database for MariaDB サーバーのファイアウォール規則を更新する 
Azure MariaDB サーバー名とリソース グループ名を使用して、サーバーの既存のファイアウォール規則を更新します。 [az mariadb server firewall update](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) コマンドを使用します。 入力として既存のファイアウォール規則の名前に加え、更新する開始 IP と終了 IP 属性を指定します。
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
正常に完了すると、コマンドの出力として、更新したファイアウォール規則の詳細が JSON 形式 (既定) で一覧表示されます。 失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

> [!NOTE]
> ファイアウォール規則が存在しない場合は、更新コマンドによって規則が作成されます。

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Azure Database for MariaDB サーバーのファイアウォール規則の詳細を表示する
Azure MariaDB サーバー名とリソース グループ名を使用して、サーバーの既存のファイアウォール規則の詳細を表示します。 [az mariadb server firewall show](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) コマンドを使用します。 既存のファイアウォール規則の名前を入力します。
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
正常に完了すると、コマンドの出力として、指定したファイアウォール規則の詳細が JSON 形式 (既定) で一覧表示されます。 失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Azure Database for MariaDB サーバーのファイアウォール規則を削除する
Azure MariaDB サーバー名とリソース グループ名を使用して、サーバーの既存のファイアウォール規則を削除します。 [az mariadb server firewall delete](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) コマンドを使用します。 既存のファイアウォール規則の名前を入力します。
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
正常に完了すると、出力はありません。 失敗した場合は、エラー メッセージ テキストが表示されます。

## <a name="next-steps"></a>次のステップ
- 詳細については、「[Azure Database for MariaDB サーバーのファイアウォール規則](./concepts-firewall-rules.md)」を参照してください。
- [Azure portal を使用した Azure Database for MariaDB ファイアウォール規則の作成と管理](./howto-manage-firewall-portal.md)。
- [Azure CLI を使用して仮想ネットワーク サービス エンドポイントと規則を作成および管理](howto-manage-vnet-cli.md)して、サーバーへのアクセスのセキュリティ保護を強化します。