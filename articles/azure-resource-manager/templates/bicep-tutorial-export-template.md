---
title: チュートリアル - Bicep 開発用に Azure portal から JSON テンプレートをエクスポートする
description: エクスポートした JSON テンプレートを使用して Bicep 開発を行う方法について説明します。
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3bc7ed4ada4f7810e9864778c7f76a0573c9dc89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632551"
---
# <a name="tutorial-use-exported-json-template-from-the-azure-portal"></a>チュートリアル:Azure portal からエクスポートした JSON テンプレートを使用する

このチュートリアル シリーズでは、Azure ストレージ アカウントをデプロイするための Bicep ファイルを作成しました。 次の 2 つのチュートリアルでは、"*App Service プラン*" と "*Web サイト*" を追加します。 テンプレートを最初から作成する代わりに、Azure portal から JSON テンプレートをエクスポートする方法と、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)」からサンプル テンプレートを使用する方法について説明します。 それらのテンプレートを自分用にカスタマイズします。 このチュートリアルでは、テンプレートのエクスポートと、Bicep ファイル用の結果のカスタマイズに焦点を当てます。 所要時間は約 **14 分** です。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[出力に関するチュートリアル](bicep-tutorial-add-outputs.md)を済ませておくことをお勧めします。

Visual Studio Code と Bicep 拡張機能に加え、Azure PowerShell または Azure CLI のいずれかが必要です。 詳細については、[Bicep ツール](bicep-tutorial-create-first-bicep.md#get-tools)に関するセクションを参照してください。

## <a name="review-bicep-file"></a>Bicep ファイルを確認する

前のチュートリアルが完了した時点で、Bicep ファイルには次の内容が含まれていました。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep":::

この Bicep ファイルを使用すればストレージ アカウントをうまくデプロイできますが、それにさらにリソースを追加してみましょう。 既存のリソースから JSON テンプレートをエクスポートして、そのリソースの JSON をすばやく取得できます。 その後、JSON を Bicep ファイルに追加する前に Bicep に変換します。

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

    ![Resource Manager テンプレート、テンプレート エクスポート ポータル](./media/bicep-tutorial-export-template/resource-manager-template-export.png)
1. **[確認と作成]** を選択します。
1. **［作成］** を選択します リソースが作成されるまでしばらくかかります。

## <a name="export-template"></a>テンプレートをエクスポートする

現時点で、Azure portal では JSON テンプレートのエクスポートのみがサポートされています。 JSON テンプレートを Bicep ファイルに逆コンパイルするために使用できるツールがあります。

1. **[リソースに移動]** を選択します。

    ![リソースに移動](./media/bicep-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. **[テンプレートのエクスポート]** を選択します。

    ![Resource Manager テンプレート、テンプレートのエクスポート](./media/bicep-tutorial-export-template/resource-manager-template-export-template.png)

   テンプレートのエクスポート機能では、リソースの現在の状態が取得されて、それをデプロイするためのテンプレートが生成されます。 テンプレートのエクスポートは、リソースをデプロイするために必要な JSON をすばやく取得する方法として便利な場合があります。

1. 必要な部分は、`Microsoft.Web/serverfarms` 定義とパラメーター定義です。

    ![Resource Manager テンプレート、テンプレートのエクスポート、エクスポートされたテンプレート](./media/bicep-tutorial-export-template/resource-manager-template-exported-template.png)

    > [!IMPORTANT]
    > 通常、エクスポートされたテンプレートには、テンプレートの作成に必要なものより詳細な情報が含まれます。 たとえば、エクスポートされたテンプレートの SKU オブジェクトには、5 つのプロパティがあります。 このテンプレートでも機能しますが、`name` プロパティだけを使用することもできます。 エクスポートしたテンプレートから始めて、必要に応じて変更できます。

1. **[ダウンロード]** を選択します。  ダウンロードした ZIP ファイルには、**template.json** と **parameters.json** が含まれています。 .zip ファイルを解凍します
1. **https://bicepdemo.z22.web.core.windows.net/** を参照します。 **[Decompile]\(逆コンパイル\)** を選択し、**template.json** を開きます。 Bicep のテンプレートを取得できます。

## <a name="revise-existing-bicep-file"></a>既存の Bicep ファイルを修正する

逆コンパイルされてエクスポートされたテンプレートは、必要な Bicep の大部分を含みますが、カスタマイズして独自の Bicep ファイルにする必要があります。 独自の Bicep ファイルとエクスポートされた Bicep ファイルとの間の、パラメーターと変数の違いに特に注意してください。 もちろん、エクスポート プロセスでは、独自の Bicep ファイルで既に定義したパラメーターと変数は認識されません。

次の例では、独自の Bicep ファイルに対する追加箇所が示されています。 それには、エクスポートされたコードに加えていくつかの変更が含まれます。 最初に、独自の名前付け規則に合わせてパラメーターの名前が変更されています。 次に、App Service プランの場所に対する location パラメーターが使用されています。 3 つ目として、既定値で問題がないプロパティがいくつか削除されています。

ファイル全体をコピーし、その内容で Bicep ファイルを置き換えます。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep" range="1-53" highlight="18,34-51":::

## <a name="deploy-bicep-file"></a>Bicep ファイルをデプロイする

Bicep ファイルをデプロイするには、Azure CLI または Azure PowerShell を使用します。

まだリソース グループを作成していない場合は、「[リソース グループの作成](bicep-tutorial-create-first-bicep.md#create-resource-group)」を参照してください。 この例では、`bicepFile` 変数に Bicep ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)を参照)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

このデプロイ コマンドレットを実行するには、Azure PowerShell の[最新バージョン](/powershell/azure/install-az-ps)が必要です。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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

Azure portal から JSON テンプレートをエクスポートする方法、JSON テンプレートを Bicep ファイルに変換する方法、およびエクスポートしたテンプレートを Bicep 開発に使用する方法について説明しました。 また、Azure クイックスタート テンプレートを使用して、Bicep 開発を簡略化することができます。

> [!div class="nextstepaction"]
> [Azure クイック スタート テンプレートを使用する](bicep-tutorial-quickstart-template.md)
