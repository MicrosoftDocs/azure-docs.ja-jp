---
title: チュートリアル - パラメーター ファイルを使用して Azure Resource Manager テンプレートをデプロイしやすくする
description: Azure Resource Manager テンプレートのデプロイに使用する値を含んだパラメーター ファイルを使用します。
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fa29ea3d2f6edbbb016ce5c0c74415a5e765e85a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177545"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-resource-manager-template"></a>チュートリアル:パラメーター ファイルを使用して Resource Manager テンプレートをデプロイする

このチュートリアルでは、デプロイ時に渡す値を[パラメーター ファイル](resource-manager-parameter-files.md)を使用して格納する方法について説明します。 先行する各チュートリアルでは、デプロイ コマンドでインライン パラメーターを使用しました。 このアプローチは、テンプレートをテストする用途には役立ちますが、デプロイを自動化する場合には、ご利用の環境に使用する値をまとめて渡した方が簡単です。 パラメーター ファイルには、特定の環境に使用するパラメーターの値をまとめやすくする効果があります。 このチュートリアルでは、開発環境用と運用環境用のパラメーター ファイルを作成します。 所要時間は約 **12 分**です。

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[タグに関するチュートリアル](template-tutorial-add-tags.md)を済ませておくことをお勧めします。

Visual Studio Code と Resource Manager Tools 拡張機能に加え、Azure PowerShell または Azure CLI を所有している必要があります。 詳細については、[テンプレートのツール](template-tutorial-create-first-template.md#get-tools)に関するセクションを参照してください。

## <a name="review-your-template"></a>テンプレートを確認する

これまでのテンプレートには、デプロイ時に指定できるパラメーターが多数存在します。 前のチュートリアルで完成したテンプレートの内容は次のとおりです。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json)]

このテンプレートでも適切に機能しますが、テンプレートに渡すパラメーターはもっと管理しやすくしたいところです。

## <a name="add-parameter-files"></a>パラメーター ファイルを追加する

パラメーター ファイルは、テンプレートに似た構造の JSON ファイルです。 デプロイ時に渡したいパラメーターの値をこのファイルに指定します。

VS Code で、次の内容を含む新しいファイルを作成します。 このファイルに **azuredeploy.parameters.dev.json** という名前を付けて保存します。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json)]

このファイルは、開発環境用のパラメーター ファイルです。 ストレージ アカウントに Standard_LRS が使用され、リソースに **dev** で始まる名前が付き、さらに **Environment** タグが **Dev** に設定されていることがわかります。

もう一度、次の内容を含んだ新しいファイルを作成します。 このファイルには **azuredeploy.parameters.prod.json** という名前を付けて保存します。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json)]

このファイルは、運用環境用のパラメーター ファイルです。 ストレージ アカウントに Standard_GRS が使用され、リソースに **contoso** で始まる名前が付き、さらに **Environment** タグが **Production** に設定されていることがわかります。 実際の運用環境では、無料以外の SKU の App Service を使用したい場合もあるかもしれませんが、このチュートリアルでは引き続きこの SKU を使用します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートをデプロイするには、Azure CLI または Azure PowerShell を使用します。

テンプレートの最終テストとして、新しいリソース グループを 2 つ作成しましょう。 1 つは開発環境用で、もう 1 つは運用環境用です。

まず開発環境にデプロイします。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az group deployment create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.dev.json
```

---

次に、運用環境にデプロイします。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroupProd \
  --location "West US"
az group deployment create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.prod.json
```

---

## <a name="verify-the-deployment"></a>デプロイを検証する

Azure portal からリソース グループを探すことでデプロイを確認できます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側のメニューから **[リソース グループ]** を選択します。
1. このチュートリアルでデプロイした新しいリソース グループが 2 つ表示されます。
1. どちらかのリソース グループを選択して、デプロイ済みのリソースを確認します。 その環境用のパラメーター ファイルに指定した値と一致していることがわかります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。 このシリーズをすべて実施した場合、削除するリソース グループは myResourceGroup、myResourceGroupDev、myResourceGroupProd の 3 つです。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

お疲れさまでした。Azure にテンプレートをデプロイするための基本的な方法は以上です。 ご意見やご提案がありましたら、フィードバック セクションからお寄せください。 よろしくお願いいたします。

テンプレートに関するより高度な概念を学ぶ準備ができました。 次のチュートリアルでは、デプロイするリソースを効率よく定義できるテンプレート リファレンス ドキュメントの利用について詳しく取り上げます。

> [!div class="nextstepaction"]
> [テンプレート リファレンスの利用](resource-manager-tutorial-create-encrypted-storage-accounts.md)
