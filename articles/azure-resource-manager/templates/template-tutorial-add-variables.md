---
title: チュートリアル - テンプレートに変数を追加する
description: Azure Resource Manager テンプレート (ARM テンプレート) に変数を追加して構文を単純化します。
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 46ed1fc55a108bf80089d249abc58bc5d1a6479a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97106956"
---
# <a name="tutorial-add-variables-to-your-arm-template"></a>チュートリアル:ARM テンプレートに変数を追加する

このチュートリアルでは、Azure Resource Manager テンプレート (ARM テンプレート) に変数を追加する方法について説明します。 変数は、式を一度だけ記述すればテンプレート全体で再利用できるため、テンプレートが簡素化されます。 このチュートリアルの所要時間は **7 分** です。

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[関数に関するチュートリアル](template-tutorial-add-functions.md)を済ませておくことをお勧めします。

Visual Studio Code と Resource Manager Tools 拡張機能に加え、Azure PowerShell または Azure CLI を所有している必要があります。 詳細については、[テンプレートのツール](template-tutorial-create-first-template.md#get-tools)に関する記事を参照してください。

## <a name="review-template"></a>テンプレートを確認する

前のチュートリアルで完成したテンプレートには、次の JSON が含まれていました。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json":::

ストレージ アカウント名では、パラメーターは使い勝手がよくありません。自分で一意の名前を指定する必要があるためです。 このシリーズの先行するチュートリアルを終えている方は、一意の名前を考えることに煩わしさを感じているのではないでしょうか。 この問題は、ストレージ アカウント用に一意の名前を構築する変数を追加すれば解決します。

## <a name="use-variable"></a>変数を使用する

以下の例で強調表示した部分は、一意のストレージ アカウント名を作成するための変数をテンプレートに追加するにあたって変更した箇所を示しています。 ファイル全体をコピーして、既存のテンプレートの内容を置き換えてください。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json" range="1-47" highlight="5-9,29-31,36":::

`uniqueStorageName` という名前の変数があることに注目してください。 この変数には、文字列値を構築するための関数が 4 つ使用されています。

[parameters](template-functions-deployment.md#parameters) 関数については見覚えがあるかと思いますので、ここでは説明しません。

また、[resourceGroup](template-functions-resource.md#resourcegroup) 関数もよくご存じでしょう。 このケースでは、`location` プロパティ (前のチュートリアルを参照) の代わりに `id` プロパティを取得します。 `id` プロパティは、サブスクリプション ID とリソース グループ名を含む、リソース グループの完全な識別子を返します。

[uniqueString](template-functions-string.md#uniquestring) は、13 文字のハッシュ値を作成する関数です。 返される値は、指定したパラメーターによって決まります。 このチュートリアルでは、ハッシュ値の入力としてリソース グループ ID を使用します。 つまり、このテンプレートを異なるリソース グループにデプロイすれば、異なる一意の文字列値が得られることになります。 一方、同じリソース グループにデプロイした場合には同じ値が返されます。

[concat](template-functions-string.md#concat) は、値を受け取ってそれらを結合する関数です。 この変数用に、パラメーターからの文字列と `uniqueString` 関数からの文字列を受け取り、それらを結合して 1 つの文字列にします。

ストレージ アカウントを識別しやすくするためのプレフィックスを `storagePrefix` パラメーターで渡すことができます。 デプロイ後に、多数のリソースからストレージ アカウントが識別しやすくなるような独自の名前付け規則を作成できます。

最後に、ストレージ名がパラメーターではなく変数に設定されている点に注目してください。

## <a name="deploy-template"></a>テンプレートのデプロイ

では、テンプレートをデプロイしましょう。 このテンプレートは、ストレージ名のプレフィックスを指定するだけで済むため、前のテンプレートよりもデプロイが容易です。

まだリソース グループを作成していない場合は、「[リソース グループの作成](template-tutorial-create-first-template.md#create-resource-group)」を参照してください。 この例では、`templateFile` 変数にテンプレート ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](template-tutorial-create-first-template.md#deploy-template)を参照)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

```azurecli
az deployment group create \
  --name addnamevariable \
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
> [出力を追加する](template-tutorial-add-outputs.md)
