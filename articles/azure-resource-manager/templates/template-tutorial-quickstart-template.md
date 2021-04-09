---
title: チュートリアル - クイックスタート テンプレートを使用する
description: Azure クイックスタート テンプレートを使用してテンプレート開発を行う方法について説明します。
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 46b32ae7aeb971c9391a69e3ca3d01f669774248
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106905"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>チュートリアル:Azure クイックスタート テンプレートを使用する

[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)は、コミュニティ提供のテンプレートのリポジトリです。 テンプレート開発にサンプル テンプレートを使用できます。 このチュートリアルでは、Web サイト リソースの定義を見つけて、自分のテンプレートに追加します。 所要時間は約 **12 分** です。

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[エクスポートしたテンプレートに関するチュートリアル](template-tutorial-export-template.md)を済ませておくことをお勧めします。

Visual Studio Code と Resource Manager Tools 拡張機能に加え、Azure PowerShell または Azure CLI を所有している必要があります。 詳細については、[テンプレートのツール](template-tutorial-create-first-template.md#get-tools)に関する記事を参照してください。

## <a name="review-template"></a>テンプレートを確認する

前のチュートリアルで完成したテンプレートには、次の JSON が含まれていました。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

このテンプレートを使用すれば、ストレージ アカウントと App Service プランをデプロイできますが、さらに Web サイトを追加してみましょう。 作成済みのテンプレートを使用すれば、リソースのデプロイに必要な JSON をすばやく見つけることができます。

## <a name="find-template"></a>テンプレートを検索する

1. [[Azure クイックスタート テンプレート]](https://azure.microsoft.com/resources/templates/) を開きます。
1. **[検索]** に「_deploy linux web app_」と入力します。
1. "**Deploy a basic Linux web app**" というタイトルのタイルを選択します。 見つからない場合は、[直接リンク](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)からアクセスしてください。
1. **[GitHub で参照する]** を選択します。
1. _azuredeploy.json_ を選択します。
1. テンプレートを確認します。 特に、`Microsoft.Web/sites` リソースを探します。

    ![Resource Manager テンプレート クイックスタート Web サイト](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>既存のテンプレートを修正する

クイックスタート テンプレートを既存のテンプレートにマージします。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

Web アプリ名は、Azure 全体で一意であることが必要です。 名前の重複を防ぐために、`webAppPortalName` 変数は `"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"` から `"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"` に更新しました。

`Microsoft.Web/serverfarms` 定義の最後にはコンマを追加します。そうすることで、リソース定義が `Microsoft.Web/sites` 定義と区別されます。

この新しいリソースには、注目すべき重要な機能が 2 つあります。

App Service プランに設定された、`dependsOn` という名前の要素があることがわかります。 この設定が必要な理由は、Web アプリを作成するためには、あらかじめ App Service プランが存在していなければならないためです。 `dependsOn` 要素によって、デプロイするリソースの順序が Resource Manager に伝えられます。

`serverFarmId` プロパティには、[resourceId](template-functions-resource.md#resourceid) 関数が使用されています。 この関数によって、リソースの一意識別子が取得されます。 このケースでは、App Service プランの一意識別子を取得しています。 Web アプリは、ある決まった 1 つの App Service プランに関連付けられます。

## <a name="deploy-template"></a>テンプレートのデプロイ

テンプレートをデプロイするには、Azure CLI または Azure PowerShell を使用します。

まだリソース グループを作成していない場合は、「[リソース グループの作成](template-tutorial-create-first-template.md#create-resource-group)」を参照してください。 この例では、**templateFile** 変数にテンプレート ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](template-tutorial-create-first-template.md#deploy-template)を参照)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> デプロイに失敗した場合は、`verbose` スイッチを使用して、作成しているリソースに関する情報を取得します。 デバッグの詳細については、`debug` スイッチを使用してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに移動する場合は、リソース グループを削除する必要はありません。

ここで終了する場合は、リソース グループを削除して、デプロイ済みのリソースをクリーンアップしましょう。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

テンプレート開発にクイックスタート テンプレートを使用する方法について説明しました。 次のチュートリアルでは、リソースにタグを追加します。

> [!div class="nextstepaction"]
> [タグを追加する](template-tutorial-add-tags.md)
