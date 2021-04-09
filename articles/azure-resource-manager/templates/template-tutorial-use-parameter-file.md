---
title: チュートリアル - パラメーター ファイルを使用してテンプレートをデプロイする
description: Azure Resource Manager テンプレート (ARM テンプレート) のデプロイに使用する値を含んだパラメーター ファイルを使用します。
author: mumian
ms.date: 09/10/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: b6257161017afc9dab692c43fcc64e5d961a90ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368428"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>チュートリアル:パラメーター ファイルを使用して ARM テンプレートをデプロイする

このチュートリアルでは、デプロイ時に渡す値を[パラメーター ファイル](parameter-files.md)を使用して格納する方法について説明します。 先行する各チュートリアルでは、デプロイ コマンドでインライン パラメーターを使用しました。 このアプローチは、Azure Resource Manager テンプレート (ARM テンプレート) をテストする用途には役立ちますが、デプロイを自動化する場合には、ご利用の環境に使用する値をまとめて渡した方が簡単です。 パラメーター ファイルには、特定の環境に使用するパラメーターの値をまとめやすくする効果があります。 このチュートリアルでは、開発環境用と運用環境用のパラメーター ファイルを作成します。 所要時間は約 **12 分** です。

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[タグに関するチュートリアル](template-tutorial-add-tags.md)を済ませておくことをお勧めします。

Visual Studio Code と Resource Manager Tools 拡張機能に加え、Azure PowerShell または Azure CLI を所有している必要があります。 詳細については、[テンプレートのツール](template-tutorial-create-first-template.md#get-tools)に関する記事を参照してください。

## <a name="review-template"></a>テンプレートを確認する

これまでのテンプレートには、デプロイ時に指定できるパラメーターが多数存在します。 前のチュートリアルで完成したテンプレートの内容は次のとおりです。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

このテンプレートでも適切に機能しますが、テンプレートに渡すパラメーターはもっと管理しやすくしたいところです。

## <a name="add-parameter-files"></a>パラメーター ファイルを追加する

パラメーター ファイルは、テンプレートに似た構造の JSON ファイルです。 デプロイ時に渡したいパラメーターの値をこのファイルに指定します。

パラメーター ファイル内で、テンプレートのパラメーターの値を指定します。 パラメーター ファイル内の各パラメーターの名前は、テンプレート内のパラメーターの名前と一致している必要があります。 名前の大文字と小文字は区別されませんが、値が一致していることを簡単に確認するには、テンプレートとの間で大文字と小文字を一致させることをお勧めします。

すべてのパラメーターに値を指定する必要はありません。 未指定のパラメーターに既定値がある場合、その値がデプロイ時に使用されます。 パラメーターに既定値がなく、パラメーター ファイルにも値が指定されていない場合は、デプロイ時に値を指定するように求められます。

テンプレート内のパラメーター名と一致しないパラメーター名をパラメーター ファイルに指定することはできません。 不明なパラメーターが指定されていると、エラーが発生します。

Visual Studio Code で、次の内容を含む新しいファイルを作成します。 このファイルに _azuredeploy.parameters.dev.json_ という名前を付けて保存します。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

このファイルは、開発環境用のパラメーター ファイルです。 ストレージ アカウントに **Standard_LRS** が使用され、リソースに **dev** で始まる名前が付き、さらに `Environment` タグが **Dev** に設定されていることがわかります。

もう一度、次の内容を含んだ新しいファイルを作成します。 このファイルには _azuredeploy.parameters.prod.json_ という名前を付けて保存します。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

このファイルは、運用環境用のパラメーター ファイルです。 ストレージ アカウントに **Standard_GRS** が使用され、リソースに **contoso** で始まる名前が付き、さらに `Environment` タグが **Production** に設定されていることがわかります。 実際の運用環境では、無料以外の SKU の App Service を使用したい場合もあるかもしれませんが、このチュートリアルでは引き続きこの SKU を使用します。

## <a name="deploy-template"></a>テンプレートのデプロイ

テンプレートをデプロイするには、Azure CLI または Azure PowerShell を使用します。

テンプレートの最終テストとして、新しいリソース グループを 2 つ作成しましょう。 1 つは開発環境用で、もう 1 つは運用環境用です。

テンプレート変数とパラメーター変数については、`{path-to-the-template-file}`、`{path-to-azuredeploy.parameters.dev.json}`、`{path-to-azuredeploy.parameters.prod.json}` (中かっこ `{}` を含む) を、実際のテンプレート ファイルとパラメーター ファイルのパスに置き換えてください。

まず開発環境にデプロイします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters $devParameterFile
```

---

次に、運用環境にデプロイします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> デプロイに失敗した場合は、`verbose` スイッチを使用して、作成しているリソースに関する情報を取得します。 デバッグの詳細については、`debug` スイッチを使用してください。

## <a name="verify-deployment"></a>デプロイの確認

Azure portal からリソース グループを探すことでデプロイを確認できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のメニューから **[リソース グループ]** を選択します。
1. このチュートリアルでデプロイした新しいリソース グループが 2 つ表示されます。
1. どちらかのリソース グループを選択して、デプロイ済みのリソースを確認します。 その環境用のパラメーター ファイルに指定した値と一致していることがわかります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。 このシリーズをすべて実施した場合、削除するリソース グループは **myResourceGroup**、**myResourceGroupDev**、**myResourceGroupProd** の 3 つです。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

お疲れさまでした。Azure にテンプレートをデプロイするための基本的な方法は以上です。 ご意見やご提案がありましたら、フィードバック セクションからお寄せください。 よろしくお願いいたします。

次のチュートリアル シリーズでは、テンプレートのデプロイについてさらに詳しく取り上げます。

> [!div class="nextstepaction"]
> [ローカル テンプレートをデプロイする](./deployment-tutorial-local-template.md)
