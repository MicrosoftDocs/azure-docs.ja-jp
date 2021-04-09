---
title: チュートリアル - Azure Resource Manager Bicep ファイルにパラメーターを追加する
description: Bicep ファイルにパラメーターを追加し、これを再利用できるようにします。
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 00df2ffc6272011127c5a1eb0c1e302011f8de5f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632783"
---
# <a name="tutorial-add-parameters-to-azure-resource-manager-bicep-file"></a>チュートリアル: Azure Resource Manager Bicep ファイルにパラメーターを追加する

[前のチュートリアル](bicep-tutorial-create-first-bicep.md)では、ストレージ アカウントをデプロイする方法について説明しました。 このチュートリアルでは、パラメーターを追加することによって Bicep ファイルを改良する方法について説明します。 このチュートリアルの所要時間は約 **14 分** です。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>前提条件

[最初の Bicep ファイルの作成](bicep-tutorial-create-first-bicep.md)を完了することをお勧めしますが、必須ではありません。

Visual Studio Code と Bicep 拡張機能に加え、Azure PowerShell または Azure CLI のいずれかが必要です。 詳細については、[Bicep ツール](bicep-tutorial-create-first-bicep.md#get-tools)に関するセクションを参照してください。

## <a name="review-bicep-file"></a>Bicep ファイルを確認する

前のチュートリアルが完了した時点で、Bicep ファイルは次のようになっています。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.bicep":::

お気付きかもしれませんが、この Bicep ファイルには問題があります。 ストレージ アカウント名がハードコーディングされています。 この Bicep ファイルは、毎回同じストレージ アカウントをデプロイする目的でしか使用できません。 別の名前でストレージ アカウントをデプロイするためには、新しい Bicep ファイルを作成する必要があり、デプロイを自動化する方法としては明らかに実用的ではありません。

## <a name="make-bicep-file-reusable"></a>Bicep ファイルを再利用可能にする

Bicep ファイルを再利用可能にするために、ストレージ アカウント名を渡すために使用できるパラメーターを追加しましょう。 次の Bicep ファイルは、ファイルの変更内容を示しています。 `storageName` パラメーターは、文字列として指定されています。 長すぎる名前を防ぐために、最大長は 24 文字に設定されています。

ファイル全体をコピーし、次の内容に置き換えます。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.bicep" range="1-15" highlight="1-3,6":::

ARM JSON テンプレートで [parameters('storageName')] が必要になるのに対し、Bicep ではパラメーターをその名前を使用して直接参照できることに注意してください。

## <a name="deploy-bicep-file"></a>Bicep ファイルをデプロイする

では、Bicep ファイルをデプロイしましょう。 次の例では、Azure CLI または PowerShell を使用して Bicep ファイルをデプロイします。 デプロイ コマンドの値の 1 つとしてストレージ アカウント名を指定していることがわかります。 ストレージ アカウント名には、前のチュートリアルで使用したものと同じ名前を指定してください。

まだリソース グループを作成していない場合は、「[リソース グループの作成](bicep-tutorial-create-first-bicep.md#create-resource-group)」を参照してください。 この例では、`bicepFile` 変数に Bicep ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)を参照)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

このデプロイ コマンドレットを実行するには、Azure PowerShell の[最新バージョン](/powershell/azure/install-az-ps)が必要です。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>リソースの更新について

前のセクションでは、以前作成したものと同じ名前を使用してストレージ アカウントをデプロイしました。 再デプロイによってリソースにはどのような影響が生じるのでしょうか。

リソースが既に存在している場合、プロパティに変更が検出されなければ、何も処理は実行されません。 リソースが既に存在していてプロパティが変更されている場合、リソースが更新されます。 リソースが存在しない場合は、作成されます。

このように更新が処理されるため、Bicep ファイルには、Azure ソリューションに必要なすべてのリソースを含めることができます。 Bicep ファイルを安全に再デプロイして、必要なときにのみリソースが変更または作成されることを確認できます。 たとえば、自分のストレージ アカウントにファイルが追加済みであるときに、そのストレージ アカウントを再デプロイしてもそれらのファイルは失われません。

## <a name="customize-by-environment"></a>環境ごとにカスタマイズする

特定の環境に合った値をパラメーターに渡すことで、デプロイをカスタマイズすることができます。 たとえば、デプロイ先が開発環境であるか、テスト環境であるか、運用環境であるかに応じて異なる値を渡すことができます。

前の Bicep ファイルでは、常に **Standard_LRS** ストレージ アカウントがデプロイされていました。 環境に応じて異なる SKU をデプロイする柔軟性が必要になることもあるでしょう。 次の例は、SKU のパラメーターを追加するための変更点を示しています。 ファイル全体をコピーして、Bicep ファイルに貼り付けます。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep" range="1-27" highlight="5-15,21":::

`storageSKU` パラメーターには既定値があります。 デプロイ中に値が指定されなければ、この値が使用されます。 また、指定できる一連の値も存在します。 これらの値は、ストレージ アカウントの作成に必要な値と一致します。 機能しない SKU が Bicep ファイルのユーザーによって渡されるのを防ぐ必要があります。

## <a name="redeploy-bicep-file"></a>Bicep ファイルを再デプロイする

これで再デプロイする準備は整いました。 既定の SKU は **Standard_LRS** に設定されているため、そのパラメーターの値を指定する必要はありません。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> デプロイに失敗した場合は、`verbose` スイッチを使用して、作成しているリソースに関する情報を取得します。 デバッグの詳細については、`debug` スイッチを使用してください。

Bicep ファイルの柔軟性を確認するために、再度デプロイしてみましょう。 今回は、SKU パラメーターを **Standard_GRS** に設定します。 新しい名前を渡して異なるストレージ アカウントを作成することも、同じ名前を使用して既存のストレージ アカウントを更新することもできます。 どちらの方法でもうまくいきます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

最後にもう 1 つテストを実施します。指定できる値のいずれにも該当しない SKU を渡したらどうなるかを見てみましょう。 ここでは、**basic** が SKU の 1 つであると Bicep ファイルのユーザーが思い込んでいる状況を想定してテストします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

許可されていない値であることを示すエラー メッセージが表示されて、コマンドはすぐに失敗します。 このエラーは、デプロイが開始される前に Resource Manager によって特定されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに移動する場合は、リソース グループを削除する必要はありません。

ここで終了する場合は、リソース グループを削除して、デプロイ済みのリソースをクリーンアップしましょう。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

パラメーターを追加することで、[1 つ目のチュートリアル](bicep-tutorial-create-first-bicep.md)で作成した Bicep ファイルを改良しました。 次のチュートリアルでは、Bicep 関数について説明します。

> [!div class="nextstepaction"]
> [関数を追加する](bicep-tutorial-add-functions.md)
