---
title: チュートリアル - Azure Resource Manager テンプレートにパラメーターを追加する
description: Azure Resource Manager テンプレートにパラメーターを追加して、そのテンプレートを再利用できるようにします。
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: f5e631994223d6362512ed0ddc89d1d3c884fbd4
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001503"
---
# <a name="tutorial-add-parameters-to-your-resource-manager-template"></a>チュートリアル:Resource Manager テンプレートにパラメーターを追加する

[前のチュートリアル](template-tutorial-add-resource.md)では、ストレージ アカウントをテンプレートに追加し、そのテンプレートをデプロイする方法について説明しました。 このチュートリアルでは、パラメーターを追加することによってテンプレートを改良する方法について説明します。 このチュートリアルの所要時間は約 **14 分**です。

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[リソースに関するチュートリアル](template-tutorial-add-resource.md)を済ませておくことをお勧めします。

Visual Studio Code と Resource Manager Tools 拡張機能に加え、Azure PowerShell または Azure CLI を所有している必要があります。 詳細については、[テンプレートのツール](template-tutorial-create-first-template.md#get-tools)に関するセクションを参照してください。

## <a name="review-your-template"></a>テンプレートを確認する

前のチュートリアルで完成したテンプレートには、次の JSON が含まれていました。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json)]

お気付きかもしれませんが、このテンプレートには問題があります。 ストレージ アカウント名がハードコーディングされています。 このテンプレートは、同じストレージ アカウントを毎回デプロイする目的でしか使用できません。 別の名前でストレージ アカウントをデプロイするためには、新しいテンプレートを作成する必要があり、デプロイを自動化する方法としては明らかに実用的ではありません。

## <a name="make-your-template-reusable"></a>テンプレートを再利用可能にする

テンプレートを再利用可能にするために、ストレージ アカウント名を渡すために使用できるパラメーターを追加しましょう。 次の例で強調表示されている JSON は、テンプレートの変更箇所を示しています。 **storageName** パラメーターは、文字列として指定されています。 長すぎる名前を防止するために最大長は 24 文字に設定されています。

ファイル全体をコピーして、既存のテンプレートの内容を置き換えてください。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json?range=1-26&highlight=4-10,15)]

## <a name="deploy-the-template"></a>テンプレートのデプロイ

では、テンプレートをデプロイしましょう。 次の例では、Azure CLI または PowerShell を使用してテンプレートをデプロイします。 デプロイ コマンドの値の 1 つとしてストレージ アカウント名を指定していることがわかります。 ストレージ アカウント名には、前のチュートリアルで使用したものと同じ名前を指定してください。

まだリソース グループを作成していない場合は、「[リソース グループの作成](template-tutorial-create-first-template.md#create-resource-group)」を参照してください。 この例では、**templateFile** 変数にテンプレート ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](template-tutorial-create-first-template.md#deploy-template)を参照)。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>リソースの更新について

前のセクションでは、以前作成したものと同じ名前を使用してストレージ アカウントをデプロイしました。 再デプロイによってリソースにはどのような影響が生じるのでしょうか。

リソースが既に存在している場合、プロパティに変更が検出されなければ、何も処理は実行されません。 リソースが既に存在していてプロパティが変更されている場合、リソースが更新されます。 リソースが存在しなければ作成されます。

このように更新が処理されるため、テンプレートには、Azure ソリューションに必要なすべてのリソースを含めることができます。 テンプレートを安心して再デプロイすることができ、またリソースは必要なときにだけ変更または作成されるという確信が得られます。 たとえば、自分のストレージ アカウントにファイルが追加済みであるときに、そのストレージ アカウントを再デプロイしてもそれらのファイルは失われません。

## <a name="customize-by-environment"></a>環境ごとにカスタマイズする

特定の環境に合った値をパラメーターに渡すことで、デプロイをカスタマイズすることができます。 たとえば、デプロイ先が開発環境であるか、テスト環境であるか、運用環境であるかに応じて異なる値を渡すことができます。

前のテンプレートでは常に Standard_LRS ストレージ アカウントがデプロイされていました。 環境に応じて異なる SKU をデプロイする柔軟性が必要になることもあるでしょう。 次の例は、SKU のパラメーターを追加するための変更点を示しています。 ファイル全体をコピーして、既存のテンプレートに貼り付けてください。

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json?range=1-40&highlight=10-23,32)]

**storageSKU** パラメーターには既定値があります。 デプロイ中に値が指定されなければ、この値が使用されます。 また、指定できる一連の値も存在します。 これらの値は、ストレージ アカウントの作成に必要な値と一致します。 機能しない SKU がテンプレートのユーザーによって渡されるのを防ぐ必要があります。

## <a name="redeploy-the-template"></a>テンプレートを再デプロイする

これで再デプロイする準備は整いました。 既定の SKU は **Standard_LRS** に設定されているため、そのパラメーターの値を指定する必要はありません。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

テンプレートの柔軟性を確認するために、再度デプロイしてみましょう。 今回は、SKU パラメーターを **Standard_GRS** に設定します。 新しい名前を渡して異なるストレージ アカウントを作成することも、同じ名前を使用して既存のストレージ アカウントを更新することもできます。 どちらの方法でもうまくいきます。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usedefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name usedefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

最後にもう 1 つテストを実施します。指定できる値のいずれにも該当しない SKU を渡したらどうなるかを見てみましょう。 ここでは、**basic** が SKU の 1 つであるとテンプレートのユーザーが思い込んでいる状況を想定してテストします。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

許可されていない値であることを示すエラー メッセージが表示されて、コマンドはすぐに失敗します。 このエラーは、デプロイが開始される前に Resource Manager によって特定されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

次のチュートリアルに移動する場合は、リソース グループを削除する必要はありません。

ここで終了する場合は、リソース グループを削除して、デプロイ済みのリソースをクリーンアップしましょう。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

パラメーターを追加することで、[1 つ目のチュートリアル](template-tutorial-create-first-template.md)で作成したテンプレートを改良しました。 次のチュートリアルでは、テンプレート関数について説明します。

> [!div class="nextstepaction"]
> [テンプレート関数を追加する](template-tutorial-add-functions.md)
