---
title: チュートリアル - Azure Resource Manager Bicep ファイルに出力を追加する
description: 構文を簡略化するために、Bicep ファイルに出力を追加します。
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 4b7d7a1414091c516dba2c474e1681ba357b55a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594310"
---
# <a name="tutorial-add-outputs-to-azure-resource-manager-bicep-file"></a>チュートリアル: Azure Resource Manager Bicep ファイルに出力を追加する

このチュートリアルでは、デプロイから値を返す方法について説明します。 デプロイされたリソースから得られる値が必要なときは出力を使用します。 このチュートリアルの所要時間は **7 分** です。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[変数に関するチュートリアル](bicep-tutorial-add-variables.md)を済ませておくことをお勧めします。

Visual Studio Code と Bicep 拡張機能に加え、Azure PowerShell または Azure CLI のいずれかが必要です。 詳細については、[Bicep ツール](bicep-tutorial-create-first-bicep.md#get-tools)に関するセクションを参照してください。

## <a name="review-bicep-file"></a>Bicep ファイルを確認する

前のチュートリアルが完了した時点で、Bicep ファイルには次の内容が含まれていました。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep":::

ストレージ アカウントはデプロイされますが、そのストレージ アカウントに関する情報は返されません。 新しいリソースのプロパティを後で参照できるようキャプチャしなければならない場合があります。

## <a name="add-outputs"></a>出力を追加する

出力を使用すると、デプロイから値を返すことができます。 たとえば、新しいストレージ アカウントのエンドポイントを取得して利用することが考えられます。

次の例では、Bicep ファイルに出力値を追加するために行った変更が示されています。 ファイル全体をコピーし、その内容で Bicep ファイルを置き換えます。

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep" range="1-33" highlight="33":::

追加した出力値については、注目すべき重要な事柄がいくつかあります。

戻り値の型は `object` に設定されています。これはテンプレート オブジェクトが返されることを意味します。

ストレージ アカウントから `primaryEndpoints` プロパティを取得するには、ストレージ アカウントのシンボリック名を使用します。 Visual Studio Code のオートコンプリート機能を使用すると、プロパティの完全な一覧が表示されます。

   ![Visual Studio Code の Bicep シンボリック名オブジェクト プロパティ](./media/bicep-tutorial-add-outputs/visual-studio-code-bicep-output-properties.png)

## <a name="deploy-bicep-file"></a>Bicep ファイルをデプロイする

それでは Bicep ファイルをデプロイして、戻り値を見てみましょう。

まだリソース グループを作成していない場合は、「[リソース グループの作成](bicep-tutorial-create-first-bicep.md#create-resource-group)」を参照してください。 この例では、`bicepFile` 変数に Bicep ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)を参照)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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

ここまでの 6 つのチュートリアルでは、多くのことを行いました。 少し作業内容を振り返って見ましょう。 簡単に指定できるパラメーターを使って Bicep ファイルを作成しました。 Bicep ファイルはカスタマイズできるほか、必要な値が動的に作成されるため、異なる環境で再利用することができます。 そこからストレージ アカウントに関する情報を取得し、スクリプトで使用することもできます。

それでは、リソース グループとデプロイ履歴を見てみましょう。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のメニューから **[リソース グループ]** を選択します。
1. デプロイ先のリソース グループを選択します。
1. 実施した手順によっては、リソース グループに少なくとも 1 つ (場合によっては複数) のストレージ アカウントが存在するはずです。
1. また履歴には、いくつかの成功したデプロイが表示されます。 そのリンクを選択します。

   ![デプロイを選択します。](./media/bicep-tutorial-add-outputs/select-deployments.png)

1. 実行したすべてのデプロイが履歴に表示されます。 **addoutputs** というデプロイを選択します。

   ![デプロイ履歴を表示する](./media/bicep-tutorial-add-outputs/show-history.png)

1. 入力内容を確認します。

   ![入力内容を表示する](./media/bicep-tutorial-add-outputs/show-inputs.png)

1. 出力内容を確認します。

   ![出力内容を表示する](./media/bicep-tutorial-add-outputs/show-outputs.png)

1. JSON テンプレートを確認します。

   ![テンプレートの表示](./media/bicep-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに移動する場合は、リソース グループを削除する必要はありません。

ここで終了する場合は、リソース グループを削除して、デプロイ済みのリソースをクリーンアップしましょう。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Bicep ファイルに戻り値を追加しました。 次のチュートリアルでは、JSON テンプレートをエクスポートして、エクスポートしたテンプレートの一部を自分の Bicep ファイルに使用する方法を説明します。

> [!div class="nextstepaction"]
> [エクスポートしたテンプレートを使用する](bicep-tutorial-export-template.md)
