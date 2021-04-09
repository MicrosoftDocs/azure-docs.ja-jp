---
title: チュートリアル - Azure portal からテンプレートをエクスポートする
description: エクスポートしたテンプレートを使用してテンプレート開発を行う方法について説明します。
author: mumian
ms.date: 09/09/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ba1797da5a78eeebd25f5df1b6e37eb92470f584
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97106922"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>チュートリアル:Azure portal からエクスポートしたテンプレートを使用する

このチュートリアル シリーズでは、Azure ストレージ アカウントをデプロイするためのテンプレートを作成しました。 次の 2 つのチュートリアルでは、"*App Service プラン*" と "*Web サイト*" を追加します。 テンプレートを最初から作成する代わりに、Azure portal からテンプレートをエクスポートする方法と、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/)」からサンプル テンプレートを使用する方法について説明します。 それらのテンプレートを自分用にカスタマイズします。 このチュートリアルでは、テンプレートのエクスポートと、テンプレートの結果のカスタマイズに焦点を当てます。 所要時間は約 **14 分** です。

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[出力に関するチュートリアル](template-tutorial-add-outputs.md)を済ませておくことをお勧めします。

Visual Studio Code と Resource Manager Tools 拡張機能に加え、Azure PowerShell または Azure CLI を所有している必要があります。 詳細については、[テンプレートのツール](template-tutorial-create-first-template.md#get-tools)に関する記事を参照してください。

## <a name="review-template"></a>テンプレートを確認する

前のチュートリアルで完成したテンプレートには、次の JSON が含まれていました。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

このテンプレートを使用すればストレージ アカウントをデプロイできますが、それにさらにリソースを追加したい場合があります。 既存のリソースからテンプレートをエクスポートして、そのリソースの JSON をすばやく取得できます。

## <a name="create-app-service-plan"></a>Create App Service plan

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[リソースの作成]** を選択します。
1. **[Marketplace を検索]** に「**App Service プラン**」と入力し、 **[App Service プラン]** を選択します。  **[App Service プラン (クラシック)]** は選択しないでください。
1. **［作成］** を選択します
1. 次のように入力します。

    - **サブスクリプション**: Azure サブスクリプションを選択します。
    - **リソース グループ**: **[新規作成]** を選択し、名前を指定します。 このチュートリアル シリーズで使用しているものとは別のリソース グループ名を指定してください。
    - **[名前]** : App Service プランの名前を入力します。
    - **[オペレーティング システム]** : **[Linux]** を選択します。
    - **[リージョン]** : Azure の場所を選択します。 たとえば **[米国中部]** です。
    - **[価格レベル]** : コストを節約するため、SKU を **[Basic B1]** (Dev/Test の下) に変更します。

    ![Resource Manager テンプレート、テンプレート エクスポート ポータル](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. **[確認と作成]** を選択します。
1. **［作成］** を選択します リソースが作成されるまでしばらくかかります。

## <a name="export-template"></a>テンプレートをエクスポートする

1. **[リソースに移動]** を選択します。

    ![リソースに移動](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. **[テンプレートのエクスポート]** を選択します。

    ![Resource Manager テンプレート、テンプレートのエクスポート](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   テンプレートのエクスポート機能では、リソースの現在の状態が取得されて、それをデプロイするためのテンプレートが生成されます。 テンプレートのエクスポートは、リソースをデプロイするために必要な JSON をすばやく取得する方法として便利な場合があります。

1. エクスポートされたテンプレートで `Microsoft.Web/serverfarms` 定義とパラメーター定義を確認します。 これらのセクションをコピーする必要はありません。 このエクスポートされたテンプレートは、このリソースをテンプレートに追加する方法の例としてのみ使用できます。

    ![Resource Manager テンプレート、テンプレートのエクスポート、エクスポートされたテンプレート](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> 通常、エクスポートされたテンプレートには、テンプレートの作成に必要なものより詳細な情報が含まれます。 たとえば、エクスポートされたテンプレートの SKU オブジェクトには、5 つのプロパティがあります。 このテンプレートでも機能しますが、`name` プロパティだけを使用することもできます。 エクスポートしたテンプレートから始めて、必要に応じて変更できます。

## <a name="revise-existing-template"></a>既存のテンプレートを修正する

エクスポートされたテンプレートでは、必要な JSON の大部分が提供されますが、カスタマイズして自分用のテンプレートにする必要があります。 独自のテンプレートとエクスポートされたテンプレートでの、パラメーターと変数の違いに特に注意してください。 もちろん、エクスポート プロセスでは、独自のテンプレートで既に定義したパラメーターと変数は認識されません。

次の例では、独自のテンプレートに対する追加部分が強調して示されています。 それには、エクスポートされたコードに加えていくつかの変更が含まれます。 最初に、独自の名前付け規則に合わせてパラメーターの名前が変更されています。 次に、App Service プランの場所に対する location パラメーターが使用されています。 3 つ目として、既定値で問題がないプロパティがいくつか削除されています。

ファイル全体をコピーして、既存のテンプレートの内容を置き換えてください。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json" range="1-77" highlight="28-31,50-69":::

## <a name="deploy-template"></a>テンプレートのデプロイ

テンプレートをデプロイするには、Azure CLI または Azure PowerShell を使用します。

まだリソース グループを作成していない場合は、「[リソース グループの作成](template-tutorial-create-first-template.md#create-resource-group)」を参照してください。 この例では、`templateFile` 変数にテンプレート ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](template-tutorial-create-first-template.md#deploy-template)を参照)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> デプロイに失敗した場合は、`verbose` スイッチを使用して、作成しているリソースに関する情報を取得します。 デバッグの詳細については、`debug` スイッチを使用してください。

## <a name="verify-deployment"></a>デプロイの確認

Azure portal からリソース グループを探すことでデプロイを確認できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のメニューから **[リソース グループ]** を選択します。
1. デプロイ先のリソース グループを選択します。
1. リソース グループには、ストレージ アカウントと App Service プランが含まれます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに移動する場合は、リソース グループを削除する必要はありません。

ここで終了する場合は、リソース グループを削除して、デプロイ済みのリソースをクリーンアップしましょう。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

Azure portal からテンプレートをエクスポートする方法と、テンプレートの開発にエクスポートしたテンプレートを使用する方法について学習しました。 また、Azure クイックスタート テンプレートを使用して、テンプレートを間単に開発することもできます。

> [!div class="nextstepaction"]
> [Azure クイック スタート テンプレートを使用する](template-tutorial-quickstart-template.md)
