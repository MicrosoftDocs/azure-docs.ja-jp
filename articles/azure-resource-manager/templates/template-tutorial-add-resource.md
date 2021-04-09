---
title: チュートリアル - テンプレートにリソースを追加する
description: Azure Resource Manager テンプレート (ARM テンプレート) を初めて作成する際の手順について説明します。 テンプレート ファイルの構文とストレージ アカウントのデプロイ方法について説明します。
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 49cee5c98c4099e214a732371269e935db353152
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97106973"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>チュートリアル:ARM テンプレートにリソースを追加する

[前のチュートリアル](template-tutorial-create-first-template.md)では、空の Azure Resource Manager テンプレート (ARM テンプレート) を作成してデプロイする方法について説明しました。 これで、実際にリソースをデプロイすることができます。 このチュートリアルでは、ストレージ アカウントを追加します。 このチュートリアルを完了するには約 **9 分** かかります。

## <a name="prerequisites"></a>前提条件

必須ではありませんが、[テンプレートに関する入門チュートリアル](template-tutorial-create-first-template.md)を済ませておくことをお勧めします。

Visual Studio Code と Resource Manager Tools 拡張機能に加え、Azure PowerShell または Azure CLI を所有している必要があります。 詳細については、[テンプレートのツール](template-tutorial-create-first-template.md#get-tools)に関する記事を参照してください。

## <a name="add-resource"></a>リソースの追加

既存のテンプレートにストレージ アカウントの定義を追加するには、次の例で強調表示されている JSON をご覧ください。 テンプレートの一部をコピーするのではなく、ファイル全体をコピーして、既存のテンプレートの内容を置き換えてください。

`{provide-unique-name}` と中かっこ `{}` を一意のストレージ アカウント名に置き換えます。

> [!IMPORTANT]
> ストレージ アカウント名は Azure 内で一意である必要があります。 名前に使用できるのは、小文字と数字だけです。 24 文字以内にする必要があります。 名前付けパターンとして、プレフィックスに **store1** を使用し、自分のイニシャルと今日の日付を追加する形が考えられます。 たとえば、**store1abc09092019** のような名前を使用できます。

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

ストレージ アカウントの一意の名前を考えるのは容易ではなく、大規模なデプロイの自動化ではうまくいきません。 このチュートリアル シリーズでは、後ほど、一意の名前の作成を容易にするテンプレート機能を使用します。

## <a name="resource-properties"></a>リソースのプロパティ

リソースの種類ごとに使用するプロパティをどうやって見つければよいのか、悩む方もいるかもしれません。 デプロイするリソースの種類を見つけるには、[ARM テンプレート リファレンス](/azure/templates/)をご利用ください。

デプロイするすべてのリソースには、少なくとも次の 3 つのプロパティがあります。

- `type`: リソースの種類。 この値は、リソース プロバイダーの名前空間とリソースの種類を組み合わせたものです (例: `Microsoft.Storage/storageAccounts`)。
- `apiVersion`: リソースの作成に使用する REST API バージョン。 リソース プロバイダーからは、それぞれ独自の API バージョンが公開されているため、これはその種類に固有の値となります。
- `name`:リソースの名前。

ほとんどのリソースには、リソースのデプロイ先リージョンを設定する `location` プロパティがあります。

その他のプロパティは、リソースの種類と API バージョンにより異なります。 API バージョンと利用可能なプロパティの関係を理解することが大切ですので、もう少し踏み込んでみましょう。

このチュートリアルでは、テンプレートにストレージ アカウントを追加しました。 その API バージョンは、[storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts) で確認できます。 すべてのプロパティをテンプレートに追加したわけではないことに注目してください。 プロパティの多くは省略可能です。 `Microsoft.Storage` リソース プロバイダーから新しい API バージョンがリリースされる可能性もありますが、デプロイするバージョンを変更する必要はありません。 そのバージョンを使い続けることができ、またデプロイの結果に一貫性を確保できます。

以前の API バージョン ([storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts) など) を表示した場合、用意されているプロパティが少ないことがわかります。

リソースの API バージョンを変更することにした場合は、そのバージョンのプロパティを評価したうえで、適宜テンプレートを調整してください。

## <a name="deploy-template"></a>テンプレートのデプロイ

テンプレートをデプロイしてストレージ アカウントを作成できます。 履歴内で見つけやすいよう、実際のデプロイには別の名前を付けてください。

まだリソース グループを作成していない場合は、「[リソース グループの作成](template-tutorial-create-first-template.md#create-resource-group)」を参照してください。 この例では、`templateFile` 変数にテンプレート ファイルのパスが設定済みであることを想定しています ([1 つ目のチュートリアル](template-tutorial-create-first-template.md#deploy-template)を参照)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

> [!NOTE]
> デプロイに失敗した場合は、`verbose` スイッチを使用して、作成しているリソースに関する情報を取得します。 デバッグの詳細については、`debug` スイッチを使用してください。

発生する可能性のあるデプロイ エラーとして、次の 2 つが考えられます。

- `Error: Code=AccountNameInvalid; Message={provide-unique-name}` は、有効なストレージ アカウント名ではありません。 ストレージ アカウント名は、長さが 3 文字から 24 文字で、数字と小文字だけを使用できます。

    テンプレート内の `{provide-unique-name}` は、一意のストレージ アカウント名に置き換えてください。 「[リソースの追加](#add-resource)」を参照してください。

- `Error: Code=StorageAccountAlreadyTaken; Message=The storage account named store1abc09092019` は既に使用されています。

    テンプレート内で、別のストレージ アカウント名を試してみてください。

このデプロイは、ストレージ アカウントが作成されるため、空のテンプレートのデプロイより時間がかかります。 1 分ほどかかることもありますが、通常はもっと短時間で済みます。

## <a name="verify-deployment"></a>デプロイの確認

Azure portal からリソース グループを探すことでデプロイを確認できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左側のメニューから **[リソース グループ]** を選択します。
1. デプロイ先のリソース グループを選択します。
1. ストレージ アカウントがデプロイされていることがわかります。
1. デプロイ ラベルに "**Deployments: 2 Succeeded (デプロイ: 2 件成功)** " と表示されていることに注目してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに移動する場合は、リソース グループを削除する必要はありません。

ここで終了する場合は、リソース グループを削除して、デプロイ済みのリソースをクリーンアップしましょう。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

Azure ストレージ アカウントをデプロイする簡単なテンプレートを作成しました。 後続のチュートリアルでは、パラメーター、変数、リソース、出力をテンプレートに追加する方法について説明します。 それらの機能は、はるかに複雑なテンプレートの構成要素となります。

> [!div class="nextstepaction"]
> [パラメーターを追加する](template-tutorial-add-parameters.md)
