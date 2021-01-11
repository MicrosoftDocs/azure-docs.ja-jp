---
title: Azure Resource Manager テンプレートを使用して SQL プールを作成する
description: Azure Resource Manager テンプレートを使用して Azure Synapse Analytics SQL プールを作成する方法を学習します。
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 29d4e4d696b34aa493714c870ebb466f491c47fe
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641876"
---
# <a name="quickstart-create-an-azure-synapse-analytics-sql-pool-by-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Azure Synapse Analytics SQL プールを作成する

この Azure Resource Manager テンプレート (ARM テンプレート) を使用すると、Transparent Data Encryption を有効にした Azure Synapse Analytics SQL プールを作成できます。 Synapse SQL プールは、Azure Synapse で一般提供されているエンタープライズ データ ウェアハウス機能を指します。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/)からのものです。

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

このテンプレートには、次の 1 つのリソースが定義されています。

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次の画像を選択します。 このテンプレートを使用すると、Synapse SQL プールを作成できます。
   
   [![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. 次の値を入力または更新します。

   * **サブスクリプション**:Azure サブスクリプションを選択します。
   * **[リソース グループ]** : **[新規作成]** を選択して、リソース グループの一意の名前を入力し、 **[OK]** を選択します。 新しいリソース グループを作成すると、リソースのクリーンアップが容易になります。
   * **[リージョン]** :リージョンを選択します。  たとえば **[米国中部]** です。
   * **SQL Server 名**: 既定の名前を受け入れるか、SQL Server の名前を入力します。
   * **[SQL Administrator login]\(SQL 管理者のログイン\)** : SQL Server の管理者のユーザー名を入力します。
   * **[SQL Administrator password]\(SQL 管理者のパスワード\)** : SQL Server の管理者のパスワードを入力します。
   * **[データ ウェアハウス名]** : SQL プール名を入力します。
   * **[Transparent Data Encryption]** : 既定値の [有効] を受け入れます。 
   * **[サービス レベル目標]** : 既定値の [DW400c] を受け入れます。
   * **[場所]** :リソース グループの既定の場所を受け入れます。
   * **[確認と作成]** : 選択。
   * **作成**:選択。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal を使用してデプロイされたリソースを確認するか、Azure CLI または Azure PowerShell スクリプトを使用してデプロイされたリソースを一覧表示することができます。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your Synapse SQL pool exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your SQL pool account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

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

このクイックスタートでは、ARM テンプレートを使用して Azure Synapse Analytics SQL プールを作成し、デプロイを検証しました。 Azure Synapse Analytics と Azure Resource Manager の詳細については、以下の記事に進んでください。

- [Azure Synapse Analytics の概要](sql-data-warehouse-overview-what-is.md)を読む
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md) の詳細を確認する
- [初めての ARM テンプレートを作成してデプロイする](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
