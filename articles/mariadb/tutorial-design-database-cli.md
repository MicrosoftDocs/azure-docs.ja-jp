---
title: チュートリアル:Azure Database for MariaDB を設計する - Azure CLI
description: このチュートリアルでは、コマンド ラインから Azure CLI を使用して、Azure Database for MariaDB サーバーとデータベースを作成および管理する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8f6f8d5a2cc9dc17d08486125fc2e44307c1be46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664487"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-azure-cli"></a>チュートリアル:Azure CLI を使用して Azure Database for MariaDB を設計する

Azure Database for MariaDB は、Microsoft クラウドにおける、MariaDB コミュニティ エディションのデータベース エンジンを基にしたリレーショナル データベース サービスです。 このチュートリアルでは、Azure CLI (コマンド ライン インターフェイス) とその他のユーティリティを使用して、次のことを行う方法を説明します。

> [!div class="checklist"]
> * Azure Database for MariaDB を作成する
> * サーバーのファイアウォールの構成
> * [mysql コマンドライン ツール](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)を使用したデータベースの作成
> * サンプル データを読み込む
> * クエリ データ
> * データの更新
> * データの復元

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

複数のサブスクリプションをお持ちの場合は、リソースが存在するか、課金の対象となっている適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account#az-account-set) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>リソース グループを作成する
[az group create](/cli/azure/group#az-group-create) コマンドで [Azure リソース グループ](../azure-resource-manager/management/overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

次の例では、`westus` の場所に `myresourcegroup` という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB サーバーを作成する
`az mariadb server create` コマンドを使用して Azure Database for MariaDB サーバーを作成します。 1 つのサーバーで複数のデータベースを管理できます。 通常は、プロジェクトまたはユーザーごとに個別のデータベースを使用します。

次の例は、`westus` にあるリソース グループ `myresourcegroup` に、`mydemoserver` という名前の Azure Database for MariaDB サーバーを作成します。 サーバーの管理者ログインの名前は `myadmin` です。 これは、2 個の仮想コアを備えた General Purpose Gen 5 サーバーです。 `<server_admin_password>` は独自の値に置き換えます。

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```
sku-name パラメーターの値は、次の例のように、{価格レベル}\_{コンピューティング世代}\_{仮想コア数} という規約に従います。
+ `--sku-name B_Gen5_4` は、"Basic、Gen 5、および 4 個の仮想コア" にマップされます。
+ `--sku-name GP_Gen5_32` は、"汎用、Gen 5、および 32 個の仮想コア" にマップされます。
+ `--sku-name MO_Gen5_2` は、"メモリ最適化、Gen 5、および 2 個の仮想コア" にマップされます。

リージョンごとおよびレベルごとに有効な値を理解するには、[価格レベル](./concepts-pricing-tiers.md)のドキュメントを参照してください。

> [!IMPORTANT]
> ここで指定するサーバー管理者のログイン名とパスワードは、このクイックスタートの後半でサーバーとそのデータベースにログインするために必要です。 後で使用するために、この情報を覚えておくか、記録しておきます。


## <a name="configure-firewall-rule"></a>ファイアウォール規則の構成
`az mariadb server firewall-rule create` コマンドを使用して Azure Database for MariaDB サーバーレベルのファイアウォール規則を作成します。 サーバーレベルのファイアウォール規則により、**mysql** コマンド ライン ツールや MySQL Workbench などの外部アプリケーションが、Azure MariaDB サービスのファイアウォールを経由してサーバーに接続できるようになります。

次の例では、特定の IP アドレス 192.168.0.1 からの接続を許可する、`AllowMyIP` と呼ばれるファイアウォール規則を作成しています。 実際の接続元となる場所に対応する IP アドレスまたは IP アドレスの範囲に置き換えてください。

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

## <a name="get-the-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。
```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

結果は JSON 形式です。 **fullyQualifiedDomainName** と **administratorLogin** の値を書き留めておきます。
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-using-mysql"></a>mysql を使用してサーバーに接続する
[mysql コマンド ライン ツール](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)を使用して、Azure Database for MariaDB サーバーへの接続を確立します。 この例では、コマンドは次のようになります。
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
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
Azure Database for MariaDB データベースに接続する方法を説明したので、次はいくつかの基本的なタスクを実行します。

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
テーブルを誤って削除した場合を想定してください。 データの復元は容易なことではありません。 Azure Database for MariaDB では、過去最長 35 日間における任意の時点に戻り、新しいデータベースに過去のデータを復元することができます。 この新しいサーバーを使用して、削除されたデータを復元することができます。 次の手順を実行して、テーブルを追加する前の状態にサンプル データベースを復元します。

復元するには、次の情報が必要です。

- 復元ポイント: サーバーが変更される前の日時を選択します。 ソース データベースの最も古いバックアップと同じか、それよりも前の値にする必要があります。
- 対象サーバー: 復元先の新しいサーバー名を指定します。
- ソース サーバー: 復元するサーバーの名前を指定します。
- 場所: リージョンを選択することはできません。既定では、ソース サーバーと同じ場所になります。

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

`az mariadb server restore` コマンドには、次のパラメーターが必要です。

| 設定 | 推奨値 | 説明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  ソース サーバーが存在するリソース グループ。  |
| name | mydemoserver-restored | 復元コマンドで作成される新しいサーバーの名前。 |
| restore-point-in-time | 2017-04-13T13:59:00Z | 復元する特定の時点を選びます。 この日付と時刻は、ソース サーバーのバックアップ保有期間内でなければなりません。 ISO8601 の日時形式を使います。 たとえば、ローカルなタイムゾーン (例: `2017-04-13T05:59:00-08:00`) または UTC Zulu 形式 (例: `2017-04-13T13:59:00Z`) を使うことができます。 |
| source-server | mydemoserver | 復元元のソース サーバーの名前または ID。 |

特定の時点にサーバーを復元すると、新しいサーバーが作成され、指定した時点の元のサーバーとしてコピーされます。 復元されたサーバーの場所と価格レベルの値は、元のサーバーと同じです。

コマンドは同期的であり、サーバーが復元された後に戻ります。 復元が終了した後、作成された新しいサーバーを調べます。 データが期待どおりに復元されたことを確認します。

## <a name="next-steps"></a>次のステップ
このチュートリアルで学習した内容は次のとおりです。
> [!div class="checklist"]
> * Azure Database for MariaDB サーバーを作成する
> * サーバーのファイアウォールの構成
> * [mysql コマンドライン ツール](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)を使用したデータベースの作成
> * サンプル データを読み込む
> * クエリ データ
> * データの更新
> * データの復元