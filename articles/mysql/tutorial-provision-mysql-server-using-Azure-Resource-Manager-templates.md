---
title: チュートリアル:Azure Database for MySQL を作成する - Azure Resource Manager テンプレート
description: このチュートリアルでは、Azure Resource Manager テンプレートを使用して Azure Database for MySQL サーバーのデプロイをプロビジョニングし、自動化する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fd5fa4e09dd3f73aa7a8f044ded04d504bd2f3dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705474"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>チュートリアル:Azure Resource Manager テンプレートを使用して Azure Database for MySQL サーバーをプロビジョニングする

[Azure Database for MySQL の REST API](/rest/api/mysql/) により、DevOps エンジニアは、Azure 内のマネージド MySQL サーバーおよびデータベースのプロビジョニング、構成、操作を自動化および統合できます。  API を使用して、Azure Database for MySQL サービス上の MySQL サーバーおよびデータベースを作成、列挙、管理、削除できます。

Azure Resource Manager では、基になる REST API を利用して、コード概念としてのインフラストラクチャに合わせて、大規模デプロイに必要な Azure リソースを宣言およびプログラミングします。 テンプレートでは、Azure リソース名、SKU、ネットワーク、ファイアウォール構成、設定をパラメーター化し、1 回の作成で複数回使用できるようにします。  Azure Resource Manager テンプレートは、[Azure portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) または [Visual Studio Code](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=CLI) を使用して簡単に作成できます。 これらにより、アプリケーションのパッケージ化、標準化、およびデプロイの自動化が可能になり、DevOps CI/CD パイプライン内で統合できます。  たとえば、Azure Database for MySQL バックエンドと共に Web アプリをすばやくデプロイしようとする場合は、GitHub ギャラリーにあるこの[クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/)を使用してエンドツーエンドのデプロイを実行できます。

このチュートリアルでは、Azure Resource Manager テンプレートとその他のユーティリティを使用して、次のことを行う方法を説明します。

> [!div class="checklist"]
> * Azure Resource Manager テンプレートを使用して、VNet サービス エンドポイントによって Azure Database for MySQL サーバーを作成する
> * [mysql コマンドライン ツール](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)を使用したデータベースの作成
> * サンプル データを読み込む
> * クエリ データ
> * データの更新

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して、VNet サービス エンドポイントによって Azure Database for MySQL サーバーを作成する

Azure Database for MySQL サーバー用の JSON テンプレート リファレンスを取得するには、[Microsoft.DBforMySQL サーバー](/azure/templates/microsoft.dbformysql/servers) テンプレート リファレンスをご覧ください。 VNet サービス エンドポイントを使用して Azure Database for MySQL を実行する新しいサーバーを作成するために使用できるサンプルの JSON テンプレートを次に示します。
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
この要求では、次の値をカスタマイズする必要があります。
+   `name` - (ドメイン名を含めずに) MySQL サーバーの名前を指定します。
+   `location` - MySQL サーバーに対して有効な Azure データ センター リージョンを指定します。 たとえば、westus2 です。
+   `properties/version` - デプロイする MySQL サーバーのバージョンを指定します。 たとえば、5.6 や 5.7 です。
+   `properties/administratorLogin` - サーバーに対する MySQL 管理者のログインを指定します。 管理者のサインイン名に azure_superuser、admin、administrator、root、guest、public は使用できません。
+   `properties/administratorLoginPassword` - 上記で指定した MySQL 管理者ユーザーのパスワードを指定します。
+   `properties/sslEnforcement` - 有効/無効を指定して、SslEnforcement を有効または無効にします。
+   `storageProfile/storageMB` - サーバーに必要なプロビジョニングされたストレージの最大サイズをメガバイト単位で指定します。 たとえば、5120 です。
+   `storageProfile/backupRetentionDays` - 必要なバックアップ保有期間の日数を指定します。 たとえば、7 です。 
+   `storageProfile/geoRedundantBackup` - Geo DR の要件に応じて有効/無効を指定します。
+   `sku/tier` -デプロイに対する Basic、GeneralPurpose、MemoryOptimized レベルを指定します。
+   `sku/capacity` - 仮想コア容量を指定します。 指定できる値には、2、4、8、16、32、64 が含まれます。
+   `sku/family` - Gen5 を指定して、サーバー デプロイ用のハードウェア世代を選択します。
+   `sku/name` - TierPrefix_family_capacity を指定します。 たとえば、B_Gen5_1、GP_Gen5_16、MO_Gen5_32 です。 リージョンごとおよびレベルごとの有効な値については、[価格レベル](./concepts-pricing-tiers.md)のドキュメントをご覧ください。
+   `resources/properties/virtualNetworkSubnetId` - Azure MySQL サーバーを配置する VNet 内のサブネットの Azure 識別子を指定します。 
+   `tags(optional)` - 省略可能なタグを指定します。これは、課金などの目的でリソースを分類するために使用するキーと値のペアです。

組織での Azure Database for MySQL のデプロイを自動化するために Azure Resource Manager テンプレートの作成を予定している場合、Azure クイック スタートの GitHub ギャラリーにあるサンプルの [Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet)から開始し、それに基づいて作成することをお勧めします。 

Azure Resource Manager テンプレートを使用したことがなく、これを試してみたい場合は、次の手順に従って開始できます。
+   サンプルの [Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet)を複製するか、Azure クイック スタート ギャラリーからダウンロードします。  
+   azuredeploy.parameters.json を変更して設定に基づいてパラメーターの値を更新し、ファイルを保存します。 
+   Azure CLI を使用し、次のコマンドを使用して Azure MySQL サーバーを作成します

このチュートリアルのコード ブロックを実行するには、ブラウザーで Azure Cloud Shell を使用するか、お使いのコンピューターに Azure CLI をインストールします。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l "West US2"
az deployment group create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>接続情報の取得
サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

結果は JSON 形式です。 **fullyQualifiedDomainName** と **administratorLogin** の値を書き留めておきます。
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>mysql を使用してサーバーに接続する
[mysql コマンドライン ツール](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)を使用して、Azure Database for MySQL サーバーへの接続を確立します。 この例では、コマンドは次のようになります。
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
Azure Database for MySQL データベースに接続する方法を説明したので、次はいくつかの基本的なタスクを実行します。

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

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、リソース グループを削除してください。リソース グループを削除すれば、リソース グループ内のリソースが削除されます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com) で、 **[リソース グループ]** を検索して選択します。

2. リソース グループの一覧で、リソース グループの名前を選択します。

3. リソース グループの **[概要]** ページで、 **[リソース グループの削除]** を選択します。

4. 確認のダイアログ ボックスでリソース グループの名前を入力し、 **[削除]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>次のステップ
このチュートリアルで学習した内容は次のとおりです。
> [!div class="checklist"]
> * Azure Resource Manager テンプレートを使用して、VNet サービス エンドポイントによって Azure Database for MySQL サーバーを作成する
> * mysql コマンドライン ツールを使用したデータベースの作成
> * サンプル データを読み込む
> * クエリ データ
> * データの更新

> [!div class="nextstepaction"]
> [Azure Database for MySQL にアプリケーションを接続する方法](./howto-connection-string.md)