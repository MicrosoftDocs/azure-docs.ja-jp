---
title: クイックスタート - Azure Resource Manager テンプレートを使用して Azure Cosmos DB とコンテナーを作成する
description: Azure Resource Manager テンプレートを使用して Azure Cosmos データベースとコンテナーを作成する方法についてのクイックスタート
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: quickstart
ms.date: 06/01/2020
ms.custom: subject-armqs
ms.openlocfilehash: b97702bb84eb38d185fe97ffcc596e374f28fbe2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339667"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Azure Cosmos DB とコンテナーを作成する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB は、あらゆる規模に対応する、オープン API を備えた Microsoft の高速 NoSQL データベースです。 Azure Cosmos DB を使用すると、キーと値のデータベース、ドキュメント データベース、グラフ データベースをすばやく作成し、クエリを実行できます。 このクイックスタートでは、Azure Cosmos データベースを作成してその中にコンテナーを作成する Azure Resource Manager テンプレート (ARM テンプレート) のデプロイ プロセスについて重点的に取り上げます。 その後、そのコンテナーにデータを格納することができます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションまたは Azure Cosmos DB の無料試用版アカウント

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/)からのものです。

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

テンプレートには、次の 3 つの Azure リソースが定義されています。

* [Microsoft.DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): Azure Cosmos アカウントを作成します。

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): Azure Cosmos データベースを作成します。

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): Azure Cosmos コンテナーを作成します。

その他の Azure Cosmos DB テンプレートのサンプルについては、[クイックスタート テンプレート ギャラリー](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb)をご覧ください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 テンプレートによって Azure Cosmos アカウント、データベース、コンテナーが作成されます。

   [:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure へのデプロイ":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

2. 次の値を選択または入力します。

   :::image type="content" source="./media/quick-create-template/create-cosmosdb-using-template-portal.png" alt-text="ARM テンプレート、Azure Cosmos DB 統合、デプロイ ポータル":::

    特に明記されていない場合は、既定値を使用して Azure Cosmos リソースを作成してください。

    * **サブスクリプション**: Azure サブスクリプションを選択します。
    * **リソース グループ**: **[新規作成]** を選択し、リソース グループの一意の名前を入力し、 **[OK]** をクリックします。
    * **場所**: 場所を選択します。  たとえば **[米国中部]** です。
    * **アカウント名**: Azure Cosmos アカウントの名前を入力します。 名前はグローバルに一意である必要があります。
    * **場所**: Azure Cosmos アカウントの作成先となる場所を入力します。 Azure Cosmos アカウントは、リソース グループと同じ場所でかまいません。
    * **プライマリ リージョン**: Azure Cosmos アカウントのプライマリ レプリカのリージョン。
    * **セカンダリ リージョン**: Azure Cosmos アカウントのセカンダリ レプリカのリージョン。
    * **既定の整合性レベル**: Azure Cosmos アカウントの既定の一貫性レベル。
    * **最大整合性制約プレフィックス**: 整合性制約要求の最大数。 BoundedStaleness の場合に必須。
    * **最大間隔秒数**: 最大ラグ時間。 BoundedStaleness の場合に必須。
    * **データベース名**: Azure Cosmos データベースの名前。
    * **コンテナー名**: Azure Cosmos コンテナーの名前。
    * **スループット**:コンテナーのスループット。最小スループット値は 400 RU/秒です。
    * **上記の使用条件に同意する**: 選択。

3. **[購入]** を選択します。 Azure Cosmos アカウントが正常にデプロイされると、次の通知が表示されます。

   :::image type="content" source="./media/quick-create-template/resource-manager-template-portal-deployment-notification.png" alt-text="ARM テンプレート、Cosmos DB 統合、デプロイ ポータル通知":::

テンプレートをデプロイするには Azure portal を使用します。 Azure portal だけでなく、Azure PowerShell、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

## <a name="validate-the-deployment"></a>デプロイの検証

Azure portal を使用し、Azure Cosmos アカウント、データベース、コンテナーを確認するか、次の Azure CLI または Azure PowerShell スクリプトを使用し、作成されたシークレットを一覧表示できます。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことができます。
不要になったら、リソース グループを削除します。これにより、Azure Cosmos アカウントおよび関連リソースが削除されます。 Azure CLI または Azure PowerShell を使用してリソース グループを削除するには、次を実行します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ARM テンプレートを使用して Azure Cosmos アカウント、データベース、コンテナーを作成し、デプロイを検証しました。 Azure Cosmos DB と Azure Resource Manager の詳細については、引き続き以下の記事を参照してください。

- [Azure Cosmos DB の概要](introduction.md)を確認する
- [Azure Resource Manager](../azure-resource-manager/management/overview.md) の詳細を確認する
- 他の [Azure Cosmos DB Resource Manager テンプレート](./templates-samples-sql.md)を入手する