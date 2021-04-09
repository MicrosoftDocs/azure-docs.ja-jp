---
title: チュートリアル - Azure Resource Manager Bicep ファイルのリソースにタグを追加する
description: Bicep ファイルでデプロイするリソースにタグを追加します。 タグを使用すると、リソースを論理的に整理できます。
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ea5e078eb692d002b3f86cd43663dd042d692611
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632606"
---
# <a name="tutorial-add-tags-in-azure-resource-manager-bicep-files"></a>チュートリアル - Azure Resource Manager Bicep ファイルにタグを追加する

このチュートリアルでは、Bicep ファイルのリソースにタグを追加する方法について説明します。 [タグ](../management/tag-resources.md)は、リソースを論理的に整理するために役立ちます。 タグ値はコスト レポートに表示されます。 このチュートリアルの所要時間は **8 分** です。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[クイックスタート テンプレートに関するチュートリアル](bicep-tutorial-quickstart-template.md)を済ませておくことをお勧めします。

Visual Studio Code と Bicep 拡張機能に加え、Azure PowerShell または Azure CLI のいずれかが必要です。 詳細については、[Bicep ツール](bicep-tutorial-create-first-bicep.md#get-tools)に関するセクションを参照してください。

## <a name="review-bicep-file"></a>Bicep ファイルを確認する

以前の Bicep ファイルでは、ストレージ アカウント、App Service プラン、Web アプリをデプロイしました。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep":::

これらのリソースをデプロイした後、コストを追跡し、あるカテゴリに属するリソースを見つけることが必要になる場合があります。 このような問題の解決にタグを追加することができます。

## <a name="add-tags"></a>タグを追加する

リソースにタグを付けて、使用法を識別するために役立つ値を追加します。 たとえば、環境とプロジェクトを一覧表示するタグを追加できます。 コスト センターまたはリソースを所有するチームを識別するタグを追加できます。 組織にとって意味のある値を追加してください。

次の例では、Bicep ファイルに対する変更箇所が示されています。 ファイル全体をコピーし、その内容で Bicep ファイルを置き換えます。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep" range="1-81" highlight="27-30,38,51,71":::

## <a name="deploy-bicep-file"></a>Bicep ファイルをデプロイする

次は、Bicep ファイルをデプロイし、結果を確認します。

まだリソース グループを作成していない場合は、「[リソース グループの作成](bicep-tutorial-create-first-bicep.md#create-resource-group)」を参照してください。 この例では、`bicepFile` 変数に Bicep ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)を参照)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

このデプロイ コマンドレットを実行するには、Azure PowerShell の[最新バージョン](/powershell/azure/install-az-ps)が必要です。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
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
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> デプロイに失敗した場合は、`verbose` スイッチを使用して、作成しているリソースに関する情報を取得します。 デバッグの詳細については、`debug` スイッチを使用してください。

## <a name="verify-deployment"></a>デプロイの確認

Azure portal からリソース グループを探すことでデプロイを確認できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のメニューから **[リソース グループ]** を選択します。
1. デプロイ先のリソース グループを選択します。
1. ストレージ アカウント リソースなど、リソースのいずれかを選択します。 タグが付けられていることがわかります。

   ![タグの表示](./media/bicep-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに移動する場合は、リソース グループを削除する必要はありません。

ここで終了する場合は、リソース グループを削除して、デプロイ済みのリソースをクリーンアップしましょう。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、リソースにタグを追加しました。 次のチュートリアルでは、パラメーター ファイルを使用して、デプロイへの値の受け渡しを簡略化する方法について説明します。

> [!div class="nextstepaction"]
> [パラメーター ファイルを使用する](bicep-tutorial-use-parameter-file.md)
