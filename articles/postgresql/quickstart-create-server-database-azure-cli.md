---
title: "Azure CLI を使用した Azure Database for PostgreSQL の作成 | Microsoft Docs"
description: "Azure CLI (コマンド ライン インターフェイス) を使用して Azure Database for PostgreSQL サーバーを作成して管理するクイック スタート ガイド"
services: postgresql
author: sanagama
ms.author: sanagama
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: b2be9e265075c58ed53a0a49c01a08e05db35a06
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017

---
# <a name="create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Azure CLI を使用した Azure Database for PostgreSQL の作成
Azure Database for PostgreSQL は、高可用性 PostgreSQL データベースをクラウドで実行、管理、および拡張することができる、管理されたサービスです。 Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このクイック スタートでは、Azure CLI を使用して、Azure Database for PostgreSQL サーバーを [Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)に作成する方法を説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となっている適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account#set) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#create) コマンドで [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 次の例では、`myresourcegroup` という名前のリソース グループを `westus` の場所に作成します。
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成

[az postgres server create](/cli/azure/postgres/server#create) コマンドを使用して、[Azure Database for PostgreSQL サーバー](overview.md)を作成します。 サーバーには、ひとまとめにして管理される一連のデータベースが含まれています。 

次の例では、サーバー管理者ログイン `mylogin` を使用して、リソース グループ `myresourcegroup` に `mypgserver-20170401` という名前のサーバーを作成します。 サーバーの名前は DNS 名にマップするため、Azure でグローバルに一意である必要があります。 `<server_admin_password>` は独自の値に置き換えます。
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401  --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> ここで指定するサーバー管理者ログインとパスワードは、このクイック スタートの後の方でサーバーとそのデータベースにログインするために必要です。 後で使用するために、この情報を覚えておくか、記録しておきます。

既定では、**postgres** データベースがサーバーに作成されます。 [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) は既定のデータベースで、ユーザー、ユーティリティ、およびサード パーティ製のアプリケーションが使用するためのものです。 


## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成

[az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create) コマンドで、Azure PostgreSQL サーバーレベルのファイアウォール規則を作成します。 サーバーレベルのファイアウォール規則により、[psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) や [PgAdmin](https://www.pgadmin.org/) などの外部アプリケーションが、Azure PostgreSQL サービスのファイアウォールを経由してサーバーに接続できるようになります。 

ネットワークからの接続が可能な IP 範囲を指定するファイアウォール規則を設定することができます。 次の例では、[az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#create) を使用して、IP アドレスの範囲を指定するファイアウォール規則 `AllowAllIps` を作成します。 すべての IP アドレスを開放するには、開始 IP アドレスとして 0.0.0.0 を、終了アドレスとして 255.255.255.255 を使用します。
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL サーバーはポート 5432 を介して通信します。 企業ネットワーク内から接続しようとしても、ポート 5432 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 Azure SQL Database サーバーに接続するには、会社の IT 部門にポート 5432 を開放してもらう必要があります。

## <a name="get-the-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

結果は JSON 形式です。 **administratorLogin** と **fullyQualifiedDomainName** の値を書き留めておきます。
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-postgresql-database-using-psql"></a>psql を使用した PostgreSQL データベースへの接続

クライアント コンピューターに PostgreSQL がインストールされている場合は、[psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) のローカル インスタンスを使用して Azure PostgreSQL サーバーに接続できます。 ここでは psql コマンド ライン ユーティリティを使用して、Azure PostgreSQL サーバーに接続しましょう。

1. 次の psql コマンドを実行して Azure Database for PostgreSQL に接続します。
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  たとえば次のコマンドは、アクセス資格情報を使用して、PostgreSQL サーバー **mypgserver 20170401.postgres.database.azure.com** にある既定のデータベース **postgres** に接続します。 パスワードの入力を求められたら、選択した `<server_admin_password>` を入力します。
  
  ```azurecli-interactive
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
```

2.  サーバーに接続したら、プロンプトで空のデータベースを作成します。
```sql
CREATE DATABASE mypgsqldb;
```

3.  プロンプトで次のコマンドを実行し、新しく作成したデータベース **mypgsqldb** に接続を切り替えます。
```sql
\c mypgsqldb
```

## <a name="connect-to-postgresql-database-using-pgadmin"></a>pgAdmin を使用した PostgreSQL データベースへの接続

GUI ツール _pgAdmin_ を使用して Azure PostgreSQL に接続する手順は次のとおりです。
1.  クライアント コンピューターで _pgAdmin_ アプリケーションを起動します。 _pgAdmin_ は http://www.pgadmin.org/ からインストールできます。
2.  **[Quick Links (クイック リンク)]** メニューの **[Add New Server (新しいサーバーの追加)]** を選択します。
3.  **[Create - Server]** ダイアログ ボックスの **[General]** タブに、サーバーの一意のフレンドリ名を入力します。 たとえば、「**Azure PostgreSQL Server**」です。
 ![pgAdmin ツール - 作成 - サーバー](./media/quickstart-create-server-database-azure-cli/1-pgadmin-create-server.png)
4.  **[Create - Server]** ダイアログ ボックスの **[Connection]** タブで次のように設定します。
    - 完全修飾サーバー名 (たとえば **mypgserver-20170401.postgres.database.azure.com**) を **[Host Name/ Address]** ボックスに入力します。 
    - ポート 5432 を **[Port]** ボックスに入力します。 
    - このクイック スタートで既に入手した**サーバー管理者ログイン (user@mypgserver)** を **[Username]** ボックスに、サーバーの作成時に入力したパスワードを **[Password]** ボックスにそれぞれ入力します。
    - **[SSL Mode]** で **[Require]** を選択します。 既定では、すべての Azure PostgreSQL サーバーは SSL 適用が有効化された状態で作成されます。 SSL 適用を無効にするには、[SSL の適用](./concepts-ssl-connection-security.md)に関する記事を参照してください。

    ![pgAdmin - 作成 - サーバー](./media/quickstart-create-server-database-azure-cli/2-pgadmin-create-server.png)
5.  [ **Save**] をクリックします。
6.  左側の [Browser (ブラウザー)] ウィンドウで **[Servers Groups (サーバー グループ)]** を展開します。 サーバー **Azure PostgreSQL Server** を選択します。
7.  接続した**サーバー**を選択してから、その下の **[Databases (データベース)]** を選択します。 
8.  **[Databases (データベース)]** を右クリックしてデータベースを作成します。
9.  データベース名 **mypgsqldb** と、その所有者としてサーバー管理者ログイン **mylogin** を選択します。
10. **[Save (保存)]** をクリックして、空のデータベースを作成します。
11. **[Browser]** で **[Servers]** グループを展開します。 作成したサーバーを展開すると、その中にデータベース **mypgsqldb** が表示されます。
 ![pgAdmin - 作成 - データベース](./media/quickstart-create-server-database-azure-cli/3-pgadmin-database.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ

[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を削除して、クイックスタートで作成したすべてのリソースをクリーンアップします。

> [!TIP]
> このコレクションの他のクイック スタートは、このクイック スタートに基づいています。 引き続きクイック スタートの作業を行う場合は、このクイック スタートで作成したリソースをクリーンアップしないでください。 これ以上作業を行わない場合は、以下の手順に従い、このクイック スタートで作成したすべてのリソースを Azure CLI で削除してください。

```azurecli-interactive
az group delete --name myresourcegroup
```

新しく作成したサーバーを 1 つだけ削除する場合は、[az postgres server delete](/cli/azure/postgres/server#delete) コマンドを実行してください。
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [エクスポートとインポートを使用したデータベースの移行](./howto-migrate-using-export-and-import.md)

