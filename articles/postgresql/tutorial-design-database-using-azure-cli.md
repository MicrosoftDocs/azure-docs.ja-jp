---
title: チュートリアル:Azure Database for PostgreSQL - Single Server を設計する - Azure CLI
description: このチュートリアルでは、Azure CLI を使用して、初めての Azure Database for PostgreSQL - 単一サーバーを作成、構成、および照会する方法を示します。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 019e6e738ea312b7e6a16c44354c7dcd54e24f2f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93331898"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-azure-cli"></a>チュートリアル:Azure CLI を使用して Azure Database for PostgreSQL - 単一サーバーを設計する 
このチュートリアルでは、Azure CLI (コマンド ライン インターフェイス) とその他のユーティリティを使用して、次のことを行う方法を説明します。
> [!div class="checklist"]
> * Azure Database for PostgreSQL サーバーの作成
> * サーバーのファイアウォールの構成
> * [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) ユーティリティを使用したデータベースの作成
> * サンプル データを読み込む
> * クエリ データ
> * データの更新
> * データの復元

このチュートリアルのコマンドを実行するには、ブラウザーで Azure Cloud Shell を使用するか、お使いのコンピューターに [Azure CLI をインストール]( /cli/azure/install-azure-cli)します。

## <a name="prerequisites"></a>前提条件
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

複数のサブスクリプションをお持ちの場合は、リソースが存在するか、課金の対象となっている適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>リソース グループを作成する
[az group create](/cli/azure/group) コマンドで [Azure リソース グループ](../azure-resource-manager/management/overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 次の例では、`westus` の場所に `myresourcegroup` という名前のリソース グループを作成します。
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成
[az postgres server create](/cli/azure/postgres/server) コマンドを使用して、[Azure Database for PostgreSQL サーバー](overview.md)を作成します。 サーバーには、ひとまとめにして管理される一連のデータベースが含まれています。 

次の例では、サーバー管理者ログイン `myadmin` を使用して、リソース グループ `myresourcegroup` に `mydemoserver` という名前のサーバーを作成します。 サーバーの名前は DNS 名にマップするため、Azure でグローバルに一意である必要があります。 `<server_admin_password>` は独自の値に置き換えます。 これは、2 個の仮想コアを備えた General Purpose Gen 5 サーバーです。
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```
sku-name パラメーターの値は、次の例のように、{価格レベル}\_{コンピューティング世代}\_{仮想コア数} という規約に従います。
+ `--sku-name B_Gen5_2` は、"Basic、Gen 5、および 2 個の仮想コア" にマップされます。
+ `--sku-name GP_Gen5_32` は、"汎用、Gen 5、および 32 個の仮想コア" にマップされます。
+ `--sku-name MO_Gen5_2` は、"メモリ最適化、Gen 5、および 2 個の仮想コア" にマップされます。

リージョンごとおよびレベルごとに有効な値を理解するには、[価格レベル](./concepts-pricing-tiers.md)のドキュメントを参照してください。

> [!IMPORTANT]
> ここで指定するサーバー管理者のログイン名とパスワードは、このクイックスタートの後半でサーバーとそのデータベースにログインするために必要です。 後で使用するために、この情報を覚えておくか、記録しておきます。

既定では、**postgres** データベースがサーバーに作成されます。 [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) は既定のデータベースで、ユーザー、ユーティリティ、およびサード パーティ製のアプリケーションが使用するためのものです。 


## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成

[az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) コマンドで、Azure PostgreSQL サーバーレベルのファイアウォール規則を作成します。 サーバーレベルのファイアウォール規則により、[psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) や [PgAdmin](https://www.pgadmin.org/) などの外部アプリケーションが、Azure PostgreSQL サービスのファイアウォールを経由してサーバーに接続できるようになります。 

ネットワークからの接続が可能な IP 範囲を指定するファイアウォール規則を設定することができます。 次の例では、[az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) を使用して、1 つの IP アドレスからの接続を許可するファイアウォール規則 `AllowMyIP` を作成します。

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

Azure PostgreSQL サーバーへのアクセスをお使いのネットワークだけに制限する場合は、ファイアウォール規則を企業ネットワークの IP アドレスの範囲だけを指定するように設定します。

> [!NOTE]
> Azure PostgreSQL サーバーはポート 5432 を介して通信します。 企業ネットワーク内から接続しようとしても、ポート 5432 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 Azure SQL Database サーバーに接続するには、会社の IT 部門にポート 5432 を開放してもらう必要があります。
>

## <a name="get-the-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

結果は JSON 形式です。 **administratorLogin** と **fullyQualifiedDomainName** の値を書き留めておきます。
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"

}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>psql を使用して Azure Database for PostgreSQL に接続する
クライアント コンピューターに PostgreSQL がインストールされている場合は、[psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) のローカル インスタンスまたは Azure Cloud Console を使用して Azure PostgreSQL サーバーに接続できます。 ここでは psql コマンド ライン ユーティリティを使用して、Azure Database for PostgreSQL サーバーに接続しましょう。

1. 次の psql コマンドを実行して Azure Database for PostgreSQL データベースに接続します。
   ```
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   たとえば次のコマンドは、アクセス資格情報を使用して、PostgreSQL サーバー **mydemoserver.postgres.database.azure.com** にある既定のデータベース **postgres** に接続します。 パスワードの入力を求められたら、選択した `<server_admin_password>` を入力します。
  
   ```
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Postgres への接続に URL パスを使用する場合は、`%40` を使用してユーザー名の @ 記号を URL エンコードします。 たとえば、psql の接続文字列は次のようになります。
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

2. サーバーに接続したら、プロンプトで空のデータベースを作成します。
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. プロンプトで次のコマンドを実行し、新しく作成したデータベース **mypgsqldb** に接続を切り替えます。
   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>データベースのテーブルを作成する
Azure Database for PostgreSQL に接続する方法を説明したので、次はいくつかの基本的なタスクを実行します。

最初に、テーブルを作成してデータを読み込みます。 たとえば、インベントリ情報を追跡するテーブルを作成します。
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

次のように入力すると、新しく作成されたテーブルが一覧に表示されます。
```sql
\dt
```

## <a name="load-data-into-the-table"></a>テーブルにデータを読み込む
テーブルを作成したので、次はデータを挿入します。 開いているコマンド プロンプト ウィンドウで、次のクエリを実行してデータ行を挿入します。
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

これで、先ほど作成したテーブルにサンプル データが 2 行挿入されました。

## <a name="query-and-update-the-data-in-the-tables"></a>クエリを実行し、テーブル内のデータを更新する
次のクエリを実行して、インベントリ テーブルから情報を取得します。 
```sql
SELECT * FROM inventory;
```

さらに、インベントリ テーブル内のデータを更新することもできます。
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

データを取得すると、更新された値を見ることができます。
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>データベースを以前の状態に復元する
テーブルを誤って削除した場合を想定してください。 データの復元は容易なことではありません。 Azure Database for PostgreSQL では、サーバーのバックアップがある任意の時点 (設定したバックアップ リテンション期間によって決まります) に遡って、新しいデータベースに過去のデータを復元できます。 この新しいサーバーを使用して、削除されたデータを復元することができます。 

次のコマンドを実行して、テーブルを追加する前の状態にサンプル サーバーを復元します。
```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

`az postgres server restore` コマンドには、次のパラメーターが必要です。

| 設定 | 推奨値 | 説明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  ソース サーバーが存在するリソース グループ。  |
| name | mydemoserver-restored | 復元コマンドで作成される新しいサーバーの名前。 |
| restore-point-in-time | 2017-04-13T13:59:00Z | 復元する特定の時点を選びます。 この日付と時刻は、ソース サーバーのバックアップ保有期間内でなければなりません。 ISO8601 の日時形式を使います。 たとえば、ローカルなタイムゾーン (例: `2017-04-13T05:59:00-08:00`) または UTC Zulu 形式 (例: `2017-04-13T13:59:00Z`) を使うことができます。 |
| source-server | mydemoserver | 復元元のソース サーバーの名前または ID。 |

特定の時点にサーバーを復元すると、新しいサーバーが作成され、指定した時点の元のサーバーとしてコピーされます。 復元されたサーバーの場所と価格レベルの値は、元のサーバーと同じです。

コマンドは同期的であり、サーバーが復元された後に戻ります。 復元が終了した後、作成された新しいサーバーを調べます。 データが期待どおりに復元されたことを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

前の手順では、サーバー グループ内に Azure リソースを作成しました。 これらのリソースが将来不要であると思われる場合は、サーバー グループを削除します。 サーバー グループの *[概要]* ページで、 *[削除]* ボタンを押します。 ポップアップ ページでメッセージが表示されたら、サーバー グループの名前を確認し、最後の *[削除]* ボタンをクリックします。


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure CLI (コマンド ライン インターフェイス) とその他のユーティリティを使用して、次のことを行う方法を説明しました。
> [!div class="checklist"]
> * Azure Database for PostgreSQL サーバーの作成
> * サーバーのファイアウォールの構成
> * **psql** ユーティリティを使用したデータベースの作成
> * サンプル データを読み込む
> * クエリ データ
> * データの更新
> * データの復元

> [!div class="nextstepaction"]
> [Azure portal を使用して最初の Azure Database for PostgreSQL を設計する方法](tutorial-design-database-using-azure-portal.md)に関するチュートリアルを参照してください。
