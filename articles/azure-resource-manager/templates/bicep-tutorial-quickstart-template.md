---
title: チュートリアル - Azure Resource Manager Bicep 開発にクイックスタート テンプレートを使用する
description: Azure クイックスタート テンプレートを使用して Bicep 開発を行う方法について説明します。
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: cf655885e01fe6bca99a82c82d6bbbc4c1a080b3
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632436"
---
# <a name="tutorial-use-azure-quickstart-templates-for-azure-resource-manager-bicep-development"></a>チュートリアル: Azure Resource Manager Bicep 開発にクイックスタート テンプレートを使用する

[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)は、コミュニティ提供の JSON テンプレートのリポジトリです。 Bicep 開発にサンプル テンプレートを使用できます。 このチュートリアルでは、Web サイトのリソース定義を見つけ、それを Bicep に逆コンパイルして、Bicep ファイルに追加します。 所要時間は約 **12 分** です。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[エクスポートしたテンプレートに関するチュートリアル](bicep-tutorial-export-template.md)を済ませておくことをお勧めします。

Visual Studio Code と Bicep 拡張機能に加え、Azure PowerShell または Azure CLI のいずれかが必要です。 詳細については、[Bicep ツール](bicep-tutorial-create-first-bicep.md#get-tools)に関するセクションを参照してください。

## <a name="review-bicep-file"></a>Bicep ファイルを確認する

前のチュートリアルが完了した時点で、Bicep ファイルには次の内容が含まれていました。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep":::

この Bicep ファイルを使用すれば、ストレージ アカウントと App Service プランをデプロイできますが、さらに Web サイトを追加してみましょう。 作成済みのテンプレートを使用すれば、リソースのデプロイに必要な JSON をすばやく見つけることができます。 Azure クイックスタート テンプレートで Bicep サンプルを提供する前に、変換ツールを使用して JSON テンプレートを Bicep ファイルに変換できます。

## <a name="find-template"></a>テンプレートを検索する

現時点で、Azure クイックスタート テンプレートでは JSON テンプレートのみが提供されます。 JSON テンプレートを Bicep テンプレートに逆コンパイルするために使用できるツールがあります。

1. [[Azure クイックスタート テンプレート]](https://azure.microsoft.com/resources/templates/) を開きます。
1. **[検索]** に「_deploy linux web app_」と入力します。
1. "**Deploy a basic Linux web app**" というタイトルのタイルを選択します。 見つからない場合は、[直接リンク](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)からアクセスしてください。
1. **[GitHub で参照する]** を選択します。
1. _azuredeploy.json_ を選択します。 これが、使用できるテンプレートです。
1. **[Raw]\(生\)** を選択し、URL をコピーします。
1. **https://bicepdemo.z22.web.core.windows.net/** を参照します。 **[Decompile]\(逆コンパイル\)** を選択し、生のテンプレートの URL を入力します。
1. Bicep テンプレートを確認します。 特に、`Microsoft.Web/sites` リソースを探します。

    ![Resource Manager テンプレート クイックスタート Web サイト](./media/bicep-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

    JSON テンプレートで作業したことがある場合は、この新しいリソースで注意すべき重要な Bicep 機能がいくつかあります。

    ARM JSON テンプレートでは、_dependsOn_ プロパティを使用してリソースの依存関係を手動で指定する必要があります。 Web サイトのリソースは、App Service プランのリソースに依存します。 Bicep では、シンボリック名を使用してリソースのプロパティを参照すると、dependsOn プロパティが自動的に追加されます。

    リソース ID は、App Service プランのシンボリック名から簡単に参照できます (appServicePlanName.id)。これは、コンパイルされた JSON テンプレートの resourceId(...) 関数に変換されます。

## <a name="revise-existing-bicep-file"></a>既存の Bicep ファイルを修正する

逆コンパイルされたクイックスタート テンプレートを既存の Bicep ファイルとマージします。 前のチュートリアルで行ったように、リソースのシンボリック名とリソース名を名前付け規則に合わせて更新します。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep" range="1-73" highlight="20-25,28,61-71":::

Web アプリ名は、Azure 全体で一意であることが必要です。 名前の重複を防ぐために、`webAppPortalName` 変数は `var webAppPortalName_var = '${webAppName}-webapp'` から `var webAppPortalName = '${webAppName}${uniqueString(resourceGroup().id)}'` に更新しました。

## <a name="deploy-bicep-file"></a>Bicep ファイルをデプロイする

Bicep テンプレートをデプロイするには、Azure CLI または Azure PowerShell を使用します。

まだリソース グループを作成していない場合は、「[リソース グループの作成](bicep-tutorial-create-first-bicep.md#create-resource-group)」を参照してください。 この例では、**bicepFile** 変数に Bicep ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)を参照)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

このデプロイ コマンドレットを実行するには、Azure PowerShell の[最新バージョン](/powershell/azure/install-az-ps)が必要です。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
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
  --template-file $bicepFile \
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

Bicep 開発にクイックスタート テンプレートを使用する方法について説明しました。 次のチュートリアルでは、リソースにタグを追加します。

> [!div class="nextstepaction"]
> [タグを追加する](bicep-tutorial-add-tags.md)
