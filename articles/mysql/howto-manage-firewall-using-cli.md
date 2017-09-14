---
title: "Azure CLI を使用した Azure Database for MySQL ファイアウォール規則の作成と管理 | Microsoft Docs"
description: "この記事では、Azure CLI コマンド ラインを使って Azure Database for MySQL ファイアウォール規則を作成し、管理する方法について説明します。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 9a03722e9f71be307bdbf0b846a4cbf7b34cd7ff
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017

---

# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-using-azure-cli"></a>Azure CLI を使用した Azure Database for MySQL ファイアウォール規則の作成と管理
サーバーレベルのファイアウォール規則を使用すると、管理者は特定の IP アドレスまたは IP アドレス範囲からの Azure Database for MySQL サーバーへのアクセスを管理できます。 便利な Azure CLI コマンドを使用すると、サーバーを管理するためのファイアウォール規則の作成、更新、削除、一覧化、表示などができます。 Azure Database for PostgreSQL ファイアウォールの概要については、「[Azure Database for MySQL サーバーのファイアウォール規則](./concepts-firewall-rules.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件
* [Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)
* PostgreSQL および MySQL サービス用の Azure Python SDK のインストール
* PostgreSQL および MySQL サービス用の Azure CLI コンポーネントのインストール
* Azure Database for MySQL サーバーの作成

## <a name="firewall-rule-commands"></a>ファイアウォール規則コマンド:
Azure CLI の **az mysql server firewall-rule** コマンドで、ファイアウォール規則を作成、削除、一覧化、表示、更新します。

コマンド:
- **create**: Azure MySQL サーバーのファイアウォール規則を作成します。
- **delete**: Azure MySQL サーバーのファイアウォール規則を削除します。
- **list**: Azure MySQL サーバーのファイアウォール規則を一覧表示します。
- **show**: Azure MySQL サーバーのファイアウォール規則の詳細を表示します。
- **update**: Azure MySQL サーバーのファイアウォール規則を更新します。

## <a name="login-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Azure へのログインと MySQL サーバーの Azure Database の一覧表示
Azure アカウントで、Azure CLI に安全に接続します。 そのためには、**az login** コマンドを使用します。

1. コマンド ラインから次のコマンドを実行します。
```azurecli
az login
```
このコマンドは、次の手順で使用するコードを出力します。

2. Web ブラウザーを使用してページ [https://aka.ms/devicelogin](https://aka.ms/devicelogin) を開き、コードを入力します。

3. プロンプトで、Azure 資格情報を使用してログインします。

4. ログインの認証が完了すると、サブスクリプションの一覧がコンソールに表示されます。 目的のサブスクリプションの ID をコピーして、このあと使用するために、その ID を現在のサブスクリプションに設定します。
   ```azurecli-interactive
   az account set --subscription {your subscription id}
   ```

5. サブスクリプションとリソース グループの名前がわからない場合は、Azure Databases for MySQL サーバーを一覧表示します。

   ```azurecli-interactive
   az mysql server list --resource-group myResourceGroup
   ```

   この一覧に表示される名前属性を確認します。これらの名前は、使用する MySQL サーバーを指定するために使用します。 必要な場合は、名前属性を使用するサーバーの詳細を確認して、名前が正しいかどうかを確認します。

   ```azurecli-interactive
   az mysql server show --resource-group myResourceGroup --name mysqlserver4demo
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのファイアウォール規則の一覧表示 
サーバー名とリソース グループ名を使用して、そのサーバー上で既存のサーバー ファイアウォール規則を一覧表示します。 サーバー名属性は、**--name** スイッチではなく **--server** スイッチで指定されることに注意してください。
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo
```
JSON 形式の既定では、規則がある場合は出力として規則が一覧表示されます。 出力をもっとわかりやすい表形式にする場合は、**--output table** スイッチを使用することもできます。
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo --output table
```
## <a name="create-firewall-rule-on-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのファイアウォール規則の作成
Azure MySQL サーバー名とリソース グループ名を使用して、サーバーに新しいファイアウォール規則を作成します。 規則の名前、開始 IP、終了 IP を入力して、アクセスを許可する IP アドレスの範囲を設定します。
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
単一の IP アドレスにだけアクセスを許可する場合は、この例のように、開始 IP と終了 IP に同じアドレスを入力します。
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  
--server mysql --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
正常に完了すると、JSON 形式の既定では、作成したファイアウォール規則の詳細がコマンドの出力として一覧表示されます。 設定に失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

## <a name="update-firewall-rule-on-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのファイアウォール規則の更新 
Azure MySQL サーバー名とリソース グループ名を使用して、サーバーの既存のファイアウォール規則を更新します。 更新する既存のファイアウォール規則の名前、開始 IP、終了 IP 属性を入力します。
```azurecli-interactive
az mysql server firewall-rule update --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
正常に完了すると、JSON 形式の既定では、更新したファイアウォール規則の詳細がコマンドの出力として一覧表示されます。 失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

> [!NOTE]
> ファイアウォール規則が存在しない場合は、更新コマンドによって新しい規則が作成されます。

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのファイアウォール規則の詳細の表示
Azure MySQL サーバー名とリソース グループ名を使用して、サーバーの既存のファイアウォール規則の詳細を表示します。 既存のファイアウォール規則の名前を入力します。
```azurecli-interactive
az mysql server firewall-rule show --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
正常に完了すると、JSON 形式の既定では、指定したファイアウォール規則の詳細がコマンドの出力として一覧表示されます。 設定に失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

## <a name="delete-firewall-rule-on-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのファイアウォール規則の削除
Azure MySQL サーバー名とリソース グループ名を使用して、サーバーの既存のファイアウォール規則を削除します。 既存のファイアウォール規則の名前を入力します。
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
正常に完了すると、出力はありません。 失敗した場合は、エラー メッセージ テキストが返されます。

## <a name="next-steps"></a>次のステップ
- 詳細については、「[Azure Database for MySQL サーバーのファイアウォール規則](./concepts-firewall-rules.md)」をご覧ください。
- [Azure Portal を使用した Azure Database for MySQL ファイアウォール規則の作成と管理](./howto-manage-firewall-using-portal.md)

