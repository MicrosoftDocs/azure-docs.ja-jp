---
title: クイック スタート:Azure DB for MariaDB を作成する - ARM テンプレート
description: このクイックスタートの記事では、Azure Resource Manager テンプレートを使用して、Azure Database for MariaDB サーバーを作成する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/14/2020
ms.openlocfilehash: de6df8349025c3e87e5b005196008053039fa49f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662075"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mariadb-server"></a>クイック スタート:ARM テンプレートを使用して Azure Database for MariaDB サーバーを作成する

Azure Database for MariaDB は、高可用性 MariaDB データベースをクラウドで実行、管理、スケーリングするために使用する管理サービスです。 このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure portal、PowerShell、または Azure CLI に Azure Database for MariaDB サーバーを作成します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

# <a name="portal"></a>[ポータル](#tab/azure-portal)

アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/)。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/)。
* [Azure PowerShell](/powershell/azure/) (コードをローカルで実行したい場合)。

# <a name="cli"></a>[CLI](#tab/CLI)

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/)。
* [Azure CLI](/cli/azure/) (コードをローカルで実行したい場合)。

---

## <a name="review-the-template"></a>テンプレートを確認する

Azure Database for MariaDB サーバーは、定義済みの一連のコンピューティングおよびストレージ リソースを使って作成します。 詳細については、「[Azure Database for MariaDB の価格レベル](concepts-pricing-tiers.md)」を参照してください。 サーバーは、[Azure リソース グループ](../azure-resource-manager/management/overview.md)内に作成します。

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-managed-mariadb-with-vnet/)からのものです。

:::code language="json" source="~/quickstart-templates/101-managed-mariadb-with-vnet/azuredeploy.json":::

このテンプレートには、次の 5 つの Azure リソースが定義されています。

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft.DBforMariaDB/servers**](/azure/templates/microsoft.dbformariadb/servers)
* [**Microsoft.DBforMariaDB/servers/virtualNetworkRules**](/azure/templates/microsoft.dbformariadb/servers/virtualnetworkrules)
* [**Microsoft.DBforMariaDB/servers/firewallRules**](/azure/templates/microsoft.dbformariadb/servers/firewallrules)

その他の Azure Database for MariaDB テンプレートのサンプルは、[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformariadb&pageNumber=1&sort=Popular)から入手できます。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal で Azure Database for MariaDB サーバーのテンプレートをデプロイするには、次のリンクを選択します。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

**[Deploy Azure Database for MariaDB with VNet]\(VNet を使用して Azure Database for MariaDB をデプロイする\)** ページで、次の作業を行います。

1. **[リソース グループ]** の **[新規作成]** を選択し、新しいリソース グループの名前を入力し、 **[OK]** を選択します。

2. 新しいリソース グループを作成した場合は、リソース グループと新しいサーバーの **場所** を選択します。

3. **サーバー名**、**管理者のログイン**、**管理者のログイン パスワード** を入力します。

    ![[Deploy Azure Database for MariaDB with VNet]\(VNet を使用して Azure Database for MariaDB をデプロイする\) ウィンドウ、Azure クイックスタート テンプレート、Azure portal](./media/quickstart-create-mariadb-server-database-arm-template/deploy-azure-database-mariadb-vnet.png)

4. 必要に応じて、他の既定の設定を変更します。

    * **[サブスクリプション]** : サーバーに使用する Azure サブスクリプション。
    * **[SKU 容量]** : 仮想コア容量。*2* (既定)、*4*、*8*、*16*、*32*、*64* のいずれかを指定できます。
    * **[SKU 名]** : SKU レベル プレフィックス、SKU ファミリー、SKU 容量をアンダースコアで結合したもの (例: *B_Gen5_1*、*GP_Gen5_2* (既定)、*MO_Gen5_32*)。
    * **[Sku Size MB]\(SKU サイズ (MB)\)** : Azure Database for MariaDB サーバーのメガバイト単位のストレージ サイズ (既定値: *51200*)。
    * **[SKU レベル]** : デプロイ レベル (例: *Basic*、*GeneralPurpose* (既定)、*MemoryOptimized*)。
    * **[SKU ファミリ]** : *Gen4* または *Gen5* (既定)。サーバーのデプロイに使用するハードウェアの世代を指定します。
    * **[Mriadb バージョン]** : デプロイする MariaDB サーバーのバージョン (例: *10.2*、または *10.3* (既定値))。
    * **[Backup Retention Days]\(バックアップ保持期間の日数\)** : geo 冗長バックアップの保持期間の日数を指定します (既定値: *7*)。
    * **[Geo Redundant Backup]\(geo 冗長バックアップ\)** : geo ディザスター リカバリー (Geo-DR) の要件に応じて "*有効*" または "*無効*" (既定) を選択します。
    * **[仮想ネットワーク名]** : 仮想ネットワークの名前 (既定値: *azure_mariadb_vnet*)。
    * **[サブネット名]** : サブネットの名前 (既定値: *azure_mariadb_subnet*)。
    * **[Virtual Network Rule Name]\(仮想ネットワーク規則名\)** : サブネットを許可する仮想ネットワーク規則の名前 (既定値: *AllowSubnet*)。
    * **[Vnet Address Prefix]\(VNet のアドレス プレフィックス\)** : 仮想ネットワークのアドレス プレフィックス (既定値: *10.0.0.0/16*)。
    * **[Subnet Prefix]\(サブネット プレフィックス\)** : サブネットのアドレス プレフィックス (既定値: *10.0.0.0/16*)。

5. 使用条件を読み、 **[上記の使用条件に同意する]** をオンにします。

6. **[購入]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

テンプレートを使用して新しい Azure Database for MariaDB サーバーを作成するには、次の対話型コードを使用します。 このコードを実行すると、新しいサーバー名、新しいリソース グループの名前と場所、管理者のアカウント名とパスワードを入力するよう求められます。

Azure Cloud Shell でコードを実行するには、コード ブロックの上部にある **[使ってみる]** を選択してください。

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MariaDB server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MariaDB server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

テンプレートを使用して新しい Azure Database for MariaDB サーバーを作成するには、次の対話型コードを使用します。 このコードを実行すると、新しいサーバー名、新しいリソース グループの名前と場所、管理者のアカウント名とパスワードを入力するよう求められます。

Azure Cloud Shell でコードを実行するには、コード ブロックの上部にある **[使ってみる]** を選択してください。

```azurecli-interactive
read -p "Enter a name for the new Azure Database for MariaDB server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for MariaDB server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

新しい Azure Database for MariaDB サーバーの概要を確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、 **[Azure Database for MariaDB サーバー]** を検索して選択します。

2. データベース一覧から新しいサーバーを選択します。 新しい Azure Database for MariaDB サーバーの **[概要]** ページが表示されます。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Azure Database for MariaDB サーバーの詳細を確認するには、次の対話型コードを実行します。 新しいサーバーの名前を入力する必要があります。

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MariaDB server"
Get-AzResource -ResourceType "Microsoft.DbForMariaDB/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

Azure Database for MariaDB サーバーの詳細を確認するには、次の対話型コードを実行します。 新しいサーバーの名前とリソース グループを入力する必要があります。

```azurecli-interactive
read -p "Enter your Azure Database for MariaDB server name: " serverName &&
read -p "Enter the resource group where the Azure Database for MariaDB server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMariaDB/servers" &&
read -p "Press [ENTER] to continue: "
```

---

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
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the Resource Group name: " resourceGroupName &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>次のステップ

Resource Manager テンプレートの作成手順について説明したチュートリアルについては、次のページを参照してください。

> [!div class="nextstepaction"]
> [ チュートリアル: 初めての ARM テンプレートを作成してデプロイする](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
