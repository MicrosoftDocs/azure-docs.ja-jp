---
title: チュートリアル - Azure Resource Manager テンプレートのリソースにタグを追加する
description: Azure Resource Manager テンプレートでデプロイするリソースにタグを追加します。 タグを使用すると、リソースを論理的に整理できます。
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8b6ff50f7254a51bcdf37ecb0afd8f0041a2c5da
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177565"
---
# <a name="tutorial-add-tags-in-your-resource-manager-template"></a>チュートリアル:Resource Manager テンプレートにタグを追加する

このチュートリアルでは、テンプレートのリソースにタグを追加する方法について説明します。 [タグ](resource-group-using-tags.md)は、リソースを論理的に整理するために役立ちます。 タグ値はコスト レポートに表示されます。 このチュートリアルの所要時間は **8 分**です。

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[クイックスタート テンプレートに関するチュートリアル](template-tutorial-quickstart-template.md)を済ませておくことをお勧めします。

Visual Studio Code と Resource Manager ツール拡張機能に加え、Azure PowerShell または Azure CLI を所有している必要があります。 詳細については、[テンプレートのツール](template-tutorial-create-first-template.md#get-tools)に関するセクションを参照してください。

## <a name="review-your-template"></a>テンプレートを確認する

以前のテンプレートでは、ストレージ アカウント、App Service プラン、Web アプリをデプロイしました。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json)]

これらのリソースをデプロイした後、コストを追跡し、あるカテゴリに属するリソースを見つけることが必要になる場合があります。 このような問題の解決にタグを追加することができます。

## <a name="add-tags"></a>タグを追加する

リソースにタグを付けて、使用法を識別するために役立つ値を追加します。 たとえば、環境とプロジェクトを一覧表示するタグを追加できます。 コスト センターまたはリソースを所有するチームを識別するタグを追加できます。 組織にとって意味のある値を追加してください。

次の例では、テンプレートの変更を強調表示します。 ファイル全体をコピーして、既存のテンプレートの内容を置き換えてください。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json?range=1-118&highlight=46-52,64,78,100)]

## <a name="deploy-the-template"></a>テンプレートのデプロイ

次はテンプレートをデプロイし、結果を確認します。

まだリソース グループを作成していない場合は、「[リソース グループの作成](template-tutorial-create-first-template.md#create-resource-group)」を参照してください。 この例では、**templateFile** 変数にテンプレート ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](template-tutorial-create-first-template.md#deploy-template)を参照)。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-the-deployment"></a>デプロイを検証する

Azure portal からリソース グループを探すことでデプロイを確認できます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 左側のメニューから **[リソース グループ]** を選択します。
1. デプロイ先のリソース グループを選択します。
1. ストレージ アカウント リソースなど、リソースのいずれかを選択します。 タグが付けられていることがわかります。

   ![タグの表示](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

次のチュートリアルに移動する場合は、リソース グループを削除する必要はありません。

ここで終了する場合は、リソース グループを削除して、デプロイ済みのリソースをクリーンアップしましょう。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、リソースにタグを追加しました。 次のチュートリアルでは、パラメーター ファイルを使用して、テンプレートへの値の受け渡しを簡略化する方法について説明します。

> [!div class="nextstepaction"]
> [パラメーター ファイルを使用する](template-tutorial-use-parameter-file.md)
