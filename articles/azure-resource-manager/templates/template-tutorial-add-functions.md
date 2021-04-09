---
title: チュートリアル - テンプレート関数を追加する
description: Azure Resource Manager テンプレート (ARM テンプレート) にテンプレート関数を追加して値を構築します。
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 52b5bd0650b3a069adc3ef7f101c48a4674deaab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97107109"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>チュートリアル:ARM テンプレートにテンプレート関数を追加する

このチュートリアルでは、Azure Resource Manager テンプレート (ARM テンプレート) に[テンプレート関数](template-functions.md)を追加する方法について説明します。 関数を使用して動的に値を構築します。 システムに備わっているテンプレート関数に加えて、[ユーザー定義関数](./template-user-defined-functions.md)を作成することもできます。 このチュートリアルの所要時間は **7 分** です。

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[パラメーターに関するチュートリアル](template-tutorial-add-parameters.md)を済ませておくことをお勧めします。

Visual Studio Code と Resource Manager Tools 拡張機能に加え、Azure PowerShell または Azure CLI を所有している必要があります。 詳細については、[テンプレートのツール](template-tutorial-create-first-template.md#get-tools)に関する記事を参照してください。

## <a name="review-template"></a>テンプレートを確認する

前のチュートリアルで完成したテンプレートには、次の JSON が含まれていました。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

ストレージ アカウントの場所が **米国東部** にハードコーディングされています。 しかし、ストレージ アカウントを他のリージョンにデプロイしなければならないこともあるかもしれません。 テンプレートの柔軟性の欠如という問題に再び向き合うことになります。 場所のパラメーターを追加することもできますが、単に値をハードコーディングするよりも、妥当な値を既定値にできれば便利だと思われます。

## <a name="use-function"></a>関数を使用する

このシリーズの先行するチュートリアルを終えている方は、既に関数を使用しています。 `"[parameters('storageName')]"` を追加したときに、[parameters](template-functions-deployment.md#parameters) 関数を使用しました。 ブラケットは、その内側の構文が[テンプレート式](template-expressions.md)であることを示します。 Resource Manager では、それらがリテラル値としては扱われずに、構文として解決されます。

関数を使うことでデプロイ中に動的に値が取得されるため、テンプレートの柔軟性が増します。 このチュートリアルでは、関数を使用して、デプロイに使用するリソース グループの場所を取得します。

次の例では、`location` というパラメーターを追加するための変更箇所が強調表示されています。 このパラメーターの既定値によって、[resourceGroup](template-functions-resource.md#resourcegroup) 関数が呼び出されます。 この関数は、デプロイに使用されたリソース グループについての情報を含んだオブジェクトを返します。 そのオブジェクトのプロパティの 1 つが location プロパティです。 既定値を使用すると、リソース グループと同じ場所がストレージ アカウントの場所に割り当てられます。 リソース グループ内のリソースで同じ場所を共有する必要はありません。 必要であれば異なる場所を指定することもできます。

ファイル全体をコピーして、既存のテンプレートの内容を置き換えてください。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>テンプレートのデプロイ

先行するチュートリアルでは、ストレージ アカウントを米国東部に作成しましたが、リソース グループは米国中部に作成されていました。 このチュートリアルでは、リソース グループと同じリージョンにストレージ アカウントを作成します。 location には既定値を使用するので、そのパラメーター値を指定する必要はありません。 ストレージ アカウントの作成場所が変わることになるので、ストレージ アカウントに新しい名前を指定する必要があります。 たとえば、プレフィックスとして **store1** の代わりに **store2** を使用します。

まだリソース グループを作成していない場合は、「[リソース グループの作成](template-tutorial-create-first-template.md#create-resource-group)」を参照してください。 この例では、`templateFile` 変数にテンプレート ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](template-tutorial-create-first-template.md#deploy-template)を参照)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
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

このチュートリアルでは、パラメーターの既定値を定義する際に関数を使用しました。 このチュートリアル シリーズでは、引き続き関数を使用していきます。 シリーズの最後には、テンプレートのあらゆる箇所に関数を追加することになります。

> [!div class="nextstepaction"]
> [変数を追加する](template-tutorial-add-variables.md)
