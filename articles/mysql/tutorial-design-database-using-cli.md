---
title: "最初の Azure Database for MySQL データベースを設計する - Azure CLI | Microsoft Docs"
description: "このチュートリアルでは、コマンド ラインから Azure CLI 2.0 を使用して、Azure Database for MySQL サーバーとデータベースを作成および管理する方法について説明します。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
ms.service: mysql
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 06/13/2017
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 590cba6cb58d0c0eaedb9f122ac048c33988004d
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017

---

# <a name="design-your-first-azure-database-for-mysql-database"></a>最初の Azure Database for MySQL データベースを設計する

Azure Database for MySQL は、Microsoft クラウドにおける、MySQL Community Edition のデータベース エンジンをベースとしたリレーショナル データベース サービスです。 このチュートリアルでは、Azure CLI (コマンド ライン インターフェイス) とその他のユーティリティを使用して、次のことを行う方法を説明します。

> [!div class="checklist"]
> * Azure Database for MySQL の作成
> * サーバーのファイアウォールの構成
> * [mysql コマンド ライン ツール](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)を使用したデータベースの作成
> * サンプル データの読み込み
> * データのクエリを実行する
> * データの更新
> * データの復元

このチュートリアルのコード ブロックを実行するには、ブラウザーで Azure Cloud Shell を使用するか、お使いのコンピューターに [Azure CLI 2.0 をインストール]( /cli/azure/install-azure-cli)します。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

複数のサブスクリプションをお持ちの場合は、リソースが存在するか、課金の対象となっている適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account#set) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>リソース グループの作成
[az group create](https://docs.microsoft.com/cli/azure/group#create) コマンドで [Azure リソース グループ](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

次の例では、`mycliresource` という名前のリソース グループを `westus` の場所に作成します。

```azurecli-interactive
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成
az mysql server create コマンドを使用して、Azure Database for MySQL サーバーを作成します。 1 つのサーバーで複数のデータベースを管理できます。 通常は、プロジェクトまたはユーザーごとに個別のデータベースを使用します。

次の例は、`westus` にあるリソース グループ `mycliresource` に、`mycliserver` という名前の Azure Database for MySQL サーバーを作成します。 サーバーの管理者ログインの名前は `myadmin`、パスワードは `Password01!` です。 サーバーのパフォーマンス レベルは **Basic**、**50** のコンピューティング ユニットを同じサーバー内のすべてのデータベースで共有します。 アプリケーションのニーズに応じて、コンピューティング ユニットとストレージは自由にスケーリングできます。

```azurecli-interactive
az mysql server create --resource-group mycliresource --name mycliserver
--location westus --user myadmin --password Password01!
--performance-tier Basic --compute-units 50
```

## <a name="configure-firewall-rule"></a>ファイアウォール規則の構成
az mysql server firewall-rule create コマンドで、Azure Database for MySQL サーバーレベルのファイアウォール規則を作成します。 サーバーレベルのファイアウォール規則により、**mysql** コマンド ライン ツールや MySQL Workbench などの外部アプリケーションが、Azure MySQL service ファイアウォールを経由してサーバーに接続できるようになります。 

次の例は、定義済みのアドレス範囲を指定するファイアウォール規則を作成します。 ここでは、指定可能なすべての範囲の IP アドレスを示しています。

```azurecli-interactive
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="get-the-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。
```azurecli-interactive
az mysql server show --resource-group mycliresource --name mycliserver
```

結果は JSON 形式です。 **fullyQualifiedDomainName** と **administratorLogin** の値を書き留めておきます。
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-mysql"></a>mysql を使用してサーバーに接続する
[mysql コマンド ライン ツール](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)を使用して、Azure Database for MySQL サーバーへの接続を確立します。 この例では、コマンドは次のようになります。
```cmd
mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
```

## <a name="create-a-blank-database"></a>空のデータベースの作成
サーバーに接続したら、空のデータベースを作成します。
```sql
mysql> CREATE DATABASE mysampledb;
```

プロンプトで次のコマンドを実行し、新しく作成したデータベースに接続を切り替えます。
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>データベースのテーブルを作成する
Azure Database for MySQL データベースに接続する方法を説明したので、次は基本的なタスクを行う方法をいくつか説明します。

最初に、テーブルを作成してデータを読み込みます。 インベントリ情報を格納するテーブルを作成しましょう。
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>テーブルにデータを読み込む
テーブルを作成したので、次はデータを挿入します。 開いているコマンド プロンプト ウィンドウで、次のクエリを実行してデータ行を挿入します。
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

これで、先ほど作成したテーブルにサンプル データが 2 行挿入されました。

## <a name="query-and-update-the-data-in-the-tables"></a>クエリを実行し、テーブル内のデータを更新する
次のクエリを実行して、データベース テーブルから情報を取得します。
```sql
SELECT * FROM inventory;
```

さらに、テーブル内のデータを更新することもできます。
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

データを取得するとき、それに応じてデータ行が更新されます。
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>データベースを以前の状態に復元する
テーブルを誤って削除した場合を想定してください。 データの復元は容易なことではありません。 Azure Database for MySQL では、過去最長 35 日間における任意の時点に戻り、新しいデータベースに過去のデータを復元することができます。 この新しいサーバーを使用して、削除されたデータを復元することができます。 次の手順を実行して、テーブルを追加する前の状態にサンプル データベースを復元します。

復元するには、次の情報が必要です。

- 復元ポイント: サーバーが変更される前の日時を選択します。 ソース データベースの最も古いバックアップと同じか、それよりも前の値にする必要があります。
- 対象サーバー: 復元先の新しいサーバー名を指定します。
- ソース サーバー: 復元するサーバーの名前を指定します。
- 場所: リージョンを選択することはできません。既定では、ソース サーバーと同じ場所になります。

```azurecli-interactive
az mysql server restore --resource-group mycliresource --name mycliserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mycliserver
```

[テーブルが削除される前の状態にサーバーを復元](./howto-restore-server-portal.md)します。 異なる時点にサーバーを復元すると、[サービス レベル](./concepts-service-tiers.md)の保有期間内であれば、指定した時点の元サーバーと同じサーバー内に、新しいサーバーが複製されます。

## <a name="next-steps"></a>次のステップ
このチュートリアルで学習した内容は次のとおりです。
> [!div class="checklist"]
> * Azure Database for MySQL の作成
> * サーバーのファイアウォールの構成
> * [mysql コマンド ライン ツール](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)を使用したデータベースの作成
> * サンプル データの読み込み
> * データのクエリを実行する
> * データの更新
> * データの復元

> [!div class="nextstepaction"]
> [Azure Database for MySQL - Azure CLI サンプル](./sample-scripts-azure-cli.md)

