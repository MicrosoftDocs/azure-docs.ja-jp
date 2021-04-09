---
title: DMS のインスタンスを作成する (Azure Resource Manager テンプレート)
description: Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Database Migration Service を作成する方法を学習します。
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: dms
ms.openlocfilehash: a9c68bca4d50af734a0a2cd8a91c7e46d9b56ff1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94963217"
---
# <a name="quickstart-create-instance-of-azure-database-migration-service-using-arm-template"></a>クイック スタート:ARM テンプレートを使用して Azure Database Migration Service のインスタンスを作成する

この Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure Database Migration Service のインスタンスをデプロイします。 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure Database Migration Service の ARM テンプレートには、次のものが必要です。 

- 最新バージョンの [Azure CLI](/cli/azure/install-azure-cli) または [PowerShell](/powershell/scripting/install/installing-powershell)。 
- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-azure-database-migration-simple-deploy/)からのものです。

:::code language="json" source="~/quickstart-templates/101-azure-database-migration-simple-deploy/azuredeploy.json":::

テンプレートには、次の 3 つの Azure リソースが定義されています。 

- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks): 仮想ネットワークを作成します。 
- [Microsoft.Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets): サブネットを作成します。 
- [Microsoft.DataMigration/services](/azure/templates/microsoft.datamigration/services): Azure Database Migration Service のインスタンスをデプロイします。 

その他の Azure Database Migration Service のテンプレートについては、[クイック スタート テンプレート ギャラリー](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datamigration&pageNumber=1&sort=Popular)を参照してください。


## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 このテンプレートでは、Azure Database Migration Service のインスタンスを作成します。 

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

2. 次の値を選択または入力します。

    * **サブスクリプション**:Azure サブスクリプションを選択します。
    * **[リソース グループ]** :ドロップ ダウンから既存のリソース グループを選ぶか、 **[新規作成]** を選択して新しいリソース グループを作成します。 
    * **[リージョン]** :リソースがデプロイされる場所。
    * **サービス名**: 新しい移行サービスの名前。
    * **[場所]** :リソース グループの場所。既定の `[resourceGroup().location]` のままにします。
    * **VNet 名**: 新しい仮想ネットワークの名前。
    * **サブネット名**: 仮想ネットワークに関する新しいサブネットの名前。



3. **[Review + create]\(レビュー + 作成\)** を選択します。 Azure Database Migration Service のインスタンスが正常にデプロイされた後、通知が表示されます。 


テンプレートをデプロイするには Azure portal を使用します。 Azure portal だけでなく、Azure PowerShell、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure CLI を使用して、デプロイされたリソースを確認できます。 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```


## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、Azure CLI または Azure PowerShell を使用してリソース グループを削除します。

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

テンプレートの作成手順について説明したチュートリアルについては、次のページを参照してください。

> [!div class="nextstepaction"]
> [ チュートリアル: 初めての ARM テンプレートを作成してデプロイする](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Azure Database Migration Service をデプロイするその他の方法については、以下を参照してください。 
- [Azure Portal](quickstart-create-data-migration-service-portal.md)

詳細については、[Azure Database Migration Service の概要](dms-overview.md)に関するページを参照してください