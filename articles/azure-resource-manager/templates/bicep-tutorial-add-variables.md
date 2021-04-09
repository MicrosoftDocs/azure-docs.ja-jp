---
title: チュートリアル - Azure Resource Manager Bicep ファイルに変数を追加する
description: 構文を簡略化するために、Bicep ファイルに変数を追加します。
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: da2755c1f2c0f9fa891fe1a99b1fed21f64492c8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632477"
---
# <a name="tutorial-add-variables-to-azure-resource-manager-bicep-file"></a>チュートリアル: Azure Resource Manager Bicep ファイルに変数を追加する

このチュートリアルでは、Bicep ファイルに変数を追加する方法について説明します。 変数を使用すると、式を一度だけ記述すれば Bicep ファイル全体で再利用できるため、Bicep ファイルが簡素化されます。 このチュートリアルの所要時間は **7 分** です。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[関数に関するチュートリアル](bicep-tutorial-add-functions.md)を済ませておくことをお勧めします。

Visual Studio Code と Bicep 拡張機能に加え、Azure PowerShell または Azure CLI のいずれかが必要です。 詳細については、[Bicep ツール](bicep-tutorial-create-first-bicep.md#get-tools)に関するセクションを参照してください。

## <a name="review-bicep-file"></a>Bicep ファイルを確認する

前のチュートリアルが完了した時点で、Bicep ファイルには次の内容が含まれていました。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep":::

ストレージ アカウント名では、パラメーターは使い勝手がよくありません。自分で一意の名前を指定する必要があるためです。 このシリーズの先行するチュートリアルを終えている方は、一意の名前を考えることに煩わしさを感じているのではないでしょうか。 この問題は、ストレージ アカウント用に一意の名前を構築する変数を追加すれば解決します。

## <a name="use-variable"></a>変数を使用する

次の例では、一意のストレージ アカウント名を作成するための変数を Bicep ファイルに追加するにあたって変更した箇所を示しています。 ファイル全体をコピーし、その内容で Bicep ファイルを置き換えます。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep" range="1-31" highlight="1-3,19,22":::

`uniqueStorageName` という名前の変数があることに注目してください。 この変数には、文字列値を構築するための関数が 3 つ使用されています。

[resourceGroup](template-functions-resource.md#resourcegroup) 関数についてはよくご存じでしょう。 このケースでは、`location` プロパティ (前のチュートリアルを参照) の代わりに `id` プロパティを取得します。 `id` プロパティは、サブスクリプション ID とリソース グループ名を含む、リソース グループの完全な識別子を返します。

[uniqueString](template-functions-string.md#uniquestring) は、13 文字のハッシュ値を作成する関数です。 返される値は、指定したパラメーターによって決まります。 このチュートリアルでは、ハッシュ値の入力としてリソース グループ ID を使用します。 つまり、この Bicep ファイルを異なるリソース グループにデプロイすれば、異なる一意の文字列値が得られることになります。 一方、同じリソース グループにデプロイした場合には同じ値が返されます。

Bicep では、[文字列補間](https://en.wikipedia.org/wiki/String_interpolation#)の構文がサポートされています。 この変数用に、パラメーターからの文字列と `uniqueString` 関数からの文字列を受け取り、それらを結合して 1 つの文字列にします。

ストレージ アカウントを識別しやすくするためのプレフィックスを `storagePrefix` パラメーターで渡すことができます。 デプロイ後に、多数のリソースからストレージ アカウントが識別しやすくなるような独自の名前付け規則を作成できます。

最後に、ストレージ名がパラメーターではなく変数に設定されている点に注目してください。

## <a name="deploy-bicep-file"></a>Bicep ファイルをデプロイする

では、Bicep ファイルをデプロイしましょう。 この Bicep ファイルは、ストレージ名のプレフィックスを指定するだけで済むため、前の Bicep ファイルよりもデプロイが容易です。

まだリソース グループを作成していない場合は、「[リソース グループの作成](bicep-tutorial-create-first-bicep.md#create-resource-group)」を参照してください。 この例では、`bicepFile` 変数に Bicep ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)を参照)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

このデプロイ コマンドレットを実行するには、Azure PowerShell の[最新バージョン](/powershell/azure/install-az-ps)が必要です。

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

```azurecli
az deployment group create \
  --name addnamevariable \
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
1. ストレージ アカウント リソースがデプロイされていることがわかります。 ストレージ アカウントの名前は、**store** にランダムな文字列を結合したものになります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに移動する場合は、リソース グループを削除する必要はありません。

ここで終了する場合は、リソース グループを削除して、デプロイ済みのリソースをクリーンアップしましょう。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ストレージ アカウントの一意の名前を作成する変数を追加しました。 次のチュートリアルでは、デプロイ済みのストレージ アカウントから値を返します。

> [!div class="nextstepaction"]
> [出力を追加する](bicep-tutorial-add-outputs.md)
