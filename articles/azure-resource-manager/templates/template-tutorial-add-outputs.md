---
title: チュートリアル - テンプレートに出力を追加する
description: Azure Resource Manager テンプレート (ARM テンプレート) に出力を追加して構文を単純化します。
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 7e022822f1306a5351cef43498f00a0ac0f2ef63
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707287"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>チュートリアル:ARM テンプレートに出力を追加する

このチュートリアルでは、Azure Resource Manager テンプレート (ARM テンプレート) から値を返す方法について説明します。 デプロイされたリソースから得られる値が必要なときは出力を使用します。 このチュートリアルの所要時間は **7 分** です。

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[変数に関するチュートリアル](template-tutorial-add-variables.md)を済ませておくことをお勧めします。

Visual Studio Code と Resource Manager Tools 拡張機能に加え、Azure PowerShell または Azure CLI を所有している必要があります。 詳細については、[テンプレートのツール](template-tutorial-create-first-template.md#get-tools)に関する記事を参照してください。

## <a name="review-template"></a>テンプレートを確認する

前のチュートリアルで完成したテンプレートには、次の JSON が含まれていました。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

ストレージ アカウントはデプロイされますが、そのストレージ アカウントに関する情報は返されません。 新しいリソースのプロパティを後で参照できるようキャプチャしなければならない場合があります。

## <a name="add-outputs"></a>出力を追加する

テンプレートから値を返すには出力を使用します。 たとえば、新しいストレージ アカウントのエンドポイントを取得して利用することが考えられます。

以下の例では、テンプレートに出力値を追加するために行った変更箇所が強調表示されています。 ファイル全体をコピーして、既存のテンプレートの内容を置き換えてください。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

追加した出力値については、注目すべき重要な事柄がいくつかあります。

返される値の型は `object` に設定されています。これは JSON オブジェクトが返されることを意味します。

ストレージ アカウントの実行時の状態を取得するために、[reference](template-functions-resource.md#reference) 関数が使用されています。 リソースの実行時の状態を取得するには、リソースの名前または ID を渡します。 このケースでは、ストレージ アカウントの名前を作成する際に使用した変数を使用します。

最後に、ストレージ アカウントの `primaryEndpoints` プロパティを返します。

## <a name="deploy-template"></a>テンプレートのデプロイ

それではテンプレートをデプロイして、返される値を見てみましょう。

まだリソース グループを作成していない場合は、「[リソース グループの作成](template-tutorial-create-first-template.md#create-resource-group)」を参照してください。 この例では、`templateFile` 変数にテンプレート ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](template-tutorial-create-first-template.md#deploy-template)を参照)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

デプロイ コマンドの出力では、出力が JSON 形式の場合にのみ、次の例のようなオブジェクトが表示されます。

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> デプロイに失敗した場合は、`verbose` スイッチを使用して、作成しているリソースに関する情報を取得します。 デバッグの詳細については、`debug` スイッチを使用してください。

## <a name="review-your-work"></a>作業を振り返る

ここまでの 6 つのチュートリアルでは、多くのことを行いました。 少し作業内容を振り返って見ましょう。 簡単に渡すことのできるパラメーターを使ってテンプレートを作成しました。 テンプレートはカスタマイズできるほか、必要な値が動的に作成されるため、異なる環境で再利用することができます。 そこからストレージ アカウントに関する情報を取得し、スクリプトで使用することもできます。

それでは、リソース グループとデプロイ履歴を見てみましょう。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のメニューから **[リソース グループ]** を選択します。
1. デプロイ先のリソース グループを選択します。
1. 実施した手順によっては、リソース グループに少なくとも 1 つ (場合によっては複数) のストレージ アカウントが存在するはずです。
1. また履歴には、いくつかの成功したデプロイが表示されます。 そのリンクを選択します。

   ![デプロイを選択します。](./media/template-tutorial-add-outputs/select-deployments.png)

1. 実行したすべてのデプロイが履歴に表示されます。 **addoutputs** というデプロイを選択します。

   ![デプロイ履歴を表示する](./media/template-tutorial-add-outputs/show-history.png)

1. 入力内容を確認します。

   ![入力内容を表示する](./media/template-tutorial-add-outputs/show-inputs.png)

1. 出力内容を確認します。

   ![出力内容を表示する](./media/template-tutorial-add-outputs/show-outputs.png)

1. テンプレートを確認します。

   ![テンプレートの表示](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに移動する場合は、リソース グループを削除する必要はありません。

ここで終了する場合は、リソース グループを削除して、デプロイ済みのリソースをクリーンアップしましょう。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、テンプレートに戻り値を追加しました。 次のチュートリアルでは、テンプレートをエクスポートして、その一部を自分のテンプレートに使用する方法を説明します。

> [!div class="nextstepaction"]
> [エクスポートしたテンプレートを使用する](template-tutorial-export-template.md)
