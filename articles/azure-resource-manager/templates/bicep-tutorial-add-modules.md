---
title: チュートリアル - Azure Resource Manager Bicep ファイルにモジュールを追加する
description: モジュールを使用して、生のリソース宣言の複雑な詳細をカプセル化します。
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6efd9c230df49c83adc17361082af85b0ef9edc5
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633140"
---
# <a name="tutorial-add-modules-to-azure-resource-manager-bicep-file"></a>チュートリアル: Azure Resource Manager Bicep ファイルにモジュールを追加する

[前のチュートリアル](bicep-tutorial-use-parameter-file.md)では、パラメーター ファイルを使用して Bicep ファイルをデプロイする方法について説明しました。 このチュートリアルでは、Bicep モジュールを使用して、生のリソース宣言の複雑な詳細をカプセル化する方法について説明します。 モジュールは、ソリューション内で共有および再利用できます。  所要時間は約 **12 分** です。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[パラメーター ファイルに関するチュートリアル](bicep-tutorial-use-parameter-file.md)を済ませておくことをお勧めします。

Visual Studio Code と Bicep 拡張機能に加え、Azure PowerShell または Azure CLI のいずれかが必要です。 詳細については、[Bicep ツール](bicep-tutorial-create-first-bicep.md#get-tools)に関するセクションを参照してください。

## <a name="review-bicep-file"></a>Bicep ファイルを確認する

前のチュートリアルが完了した時点で、Bicep ファイルには次の内容が含まれていました。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

この Bicep ファイルは正常に機能します。 ただし、大規模なソリューションの場合は、Bicep ファイルを多数の関連モジュールに分割して、これらのモジュールを共有および再利用できるようにする必要があります。 現在の Bicep ファイルは、ストレージ アカウント、App Service プラン、および Web サイトをデプロイします。  ストレージ アカウントをモジュールに分割してみましょう。

## <a name="create-bicep-module"></a>Bicep モジュールを作成する

すべての Bicep ファイルはモジュールとして使用できます。したがって、モジュールを定義するための特定の構文はありません。 次の内容で _storage.bicep_ ファイルを作成します。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/storage.bicep":::

このモジュールには、ストレージ アカウント リソースと、関連するパラメーターおよび変数が含まれています。 _location_ パラメーターと _resourceTags_ パラメーターの値が削除されています。 これらの値は、メインの Bicep ファイルから渡されます。

## <a name="consume-bicep-module"></a>Bicep モジュールを使用する

既存の _azuredeploy.bicep_ 内のストレージ アカウント リソース定義を、次の Bicep の内容に置き換えます。

```bicep
module stg './storage.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: storagePrefix
    location: location
    resourceTags: resourceTags
  }
}
```

- **モジュール**: キーワード。
- **シンボリック名** (stg): これは、モジュールの識別子です。
- **モジュール ファイル**: この例のモジュールへのパスは、相対パス (./storage.bicep) を使用して指定されています。 Bicep のすべてのパスは、クロスプラットフォームでの一貫したコンパイルを保証するために、スラッシュ (/) ディレクトリ区切り文字を使用して指定する必要があります。 Windows の円記号 (\) 文字はサポートされていません。

ストレージ エンドポイントを取得するには、_azuredeploy.bicep_ の出力を次の Bicep に更新します。

```bicep
output storageEndpoint object = stg.outputs.storageEndpoint
```

完成した azuredeploy.bicep の内容は次のとおりです。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/azuredeploy.bicep":::

## <a name="deploy-template"></a>テンプレートのデプロイ

テンプレートをデプロイするには、Azure CLI または Azure PowerShell を使用します。

まだリソース グループを作成していない場合は、「[リソース グループの作成](bicep-tutorial-create-first-bicep.md#create-resource-group)」を参照してください。 この例では、`bicepFile` 変数に Bicep ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)を参照)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

このデプロイ コマンドレットを実行するには、Azure PowerShell の[最新バージョン](/powershell/azure/install-az-ps)が必要です。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addmodule `
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
  --name addmodule \
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
1. このチュートリアルでデプロイした新しいリソース グループが 2 つ表示されます。
1. どちらかのリソース グループを選択して、デプロイ済みのリソースを確認します。 その環境用のパラメーター ファイルに指定した値と一致していることがわかります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。 このシリーズをすべて実施した場合、削除するリソース グループは **myResourceGroup**、**myResourceGroupDev**、**myResourceGroupProd** の 3 つです。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

お疲れさまでした。Azure に Bicep ファイルをデプロイするための基本的な方法は以上です。 ご意見やご提案がありましたら、フィードバック セクションからお寄せください。 よろしくお願いいたします。

次のチュートリアル シリーズでは、テンプレートのデプロイについてさらに詳しく取り上げます。

> [!div class="nextstepaction"]
> [モジュールを追加する](./bicep-tutorial-add-modules.md)
