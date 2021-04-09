---
title: チュートリアル - Azure Resource Manager Bicep ファイルに関数を追加する
description: Bicep ファイルに関数を追加して、値を作成します。
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: b909beb0cce9ad04ba00068ee25247520dcff47d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102633157"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>チュートリアル: Azure Resource Manager Bicep ファイルに関数を追加する

このチュートリアルでは、Bicep ファイルに[テンプレート関数](template-functions.md)を追加する方法について説明します。 関数を使用して動的に値を構築します。 システムに備わっているテンプレート関数に加えて、[ユーザー定義関数](./template-user-defined-functions.md)を作成することもできます。 このチュートリアルの所要時間は **7 分** です。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[パラメーターに関するチュートリアル](bicep-tutorial-add-parameters.md)を済ませておくことをお勧めします。

Visual Studio Code と Bicep 拡張機能に加え、Azure PowerShell または Azure CLI のいずれかが必要です。 詳細については、[Bicep ツール](bicep-tutorial-create-first-bicep.md#get-tools)に関するセクションを参照してください。

## <a name="review-bicep-file"></a>Bicep ファイルを確認する

前のチュートリアルが完了した時点で、Bicep ファイルには次の内容が含まれていました。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

ストレージ アカウントの場所が **米国東部** にハードコーディングされています。 しかし、ストレージ アカウントを他のリージョンにデプロイしなければならないこともあるかもしれません。 Bicep ファイルの柔軟性の欠如という問題に再び向き合うことになります。 場所のパラメーターを追加することもできますが、単に値をハードコーディングするよりも、妥当な値を既定値にできれば便利だと思われます。

## <a name="use-function"></a>関数を使用する

関数を使うことでデプロイ中に動的に値が取得されるため、Bicep ファイルに柔軟性が追加されます。 このチュートリアルでは、関数を使用して、デプロイに使用するリソース グループの場所を取得します。

次の例は、`location` というパラメーターを追加するための変更点を示しています。 このパラメーターの既定値によって、[resourceGroup](template-functions-resource.md#resourcegroup) 関数が呼び出されます。 この関数は、デプロイに使用されたリソース グループについての情報を含んだオブジェクトを返します。 そのオブジェクトのプロパティの 1 つが location プロパティです。 既定値を使用すると、リソース グループと同じ場所がストレージ アカウントの場所に割り当てられます。 リソース グループ内のリソースで同じ場所を共有する必要はありません。 必要であれば異なる場所を指定することもできます。

ファイル全体をコピーし、その内容で Bicep ファイルを置き換えます。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>Bicep ファイルをデプロイする

先行するチュートリアルでは、ストレージ アカウントを米国東部に作成しましたが、リソース グループは米国中部に作成されていました。 このチュートリアルでは、リソース グループと同じリージョンにストレージ アカウントを作成します。 location には既定値を使用するので、そのパラメーター値を指定する必要はありません。 ストレージ アカウントの作成場所が変わることになるので、ストレージ アカウントに新しい名前を指定する必要があります。 たとえば、プレフィックスとして **store1** の代わりに **store2** を使用します。

まだリソース グループを作成していない場合は、「[リソース グループの作成](bicep-tutorial-create-first-bicep.md#create-resource-group)」を参照してください。 この例では、`bicepFile` 変数に Bicep ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)を参照)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

このデプロイ コマンドレットを実行するには、Azure PowerShell の[最新バージョン](/powershell/azure/install-az-ps)が必要です。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> デプロイに失敗した場合は、`verbose` スイッチを使用して、作成しているリソースに関する情報を取得します。 デバッグの詳細については、`debug` スイッチを使用してください。

## <a name="verify-deployment"></a>デプロイの確認

Azure portal からリソース グループを探すことでデプロイを確認できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のメニューから **[リソース グループ]** を選択します。
1. デプロイ先のリソース グループを選択します。
1. ストレージ アカウント リソースがデプロイされ、リソース グループと同じ場所を割り当てられていることがわかります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに移動する場合は、リソース グループを削除する必要はありません。

ここで終了する場合は、リソース グループを削除して、デプロイ済みのリソースをクリーンアップしましょう。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、パラメーターの既定値を定義する際に関数を使用しました。 このチュートリアル シリーズでは、引き続き関数を使用していきます。 シリーズの終わりまでに、Bicep ファイルのすべてのセクションに関数を追加します。

> [!div class="nextstepaction"]
> [変数を追加する](bicep-tutorial-add-variables.md)
