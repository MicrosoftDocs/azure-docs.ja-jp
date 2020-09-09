---
title: クイック スタート:ARM テンプレートを使用して Azure Cognitive Services リソースを作成する | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して Cognitive Services リソースのデプロイを開始します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 07/27/2020
ms.author: aahi
ms.custom: subject-armqs
ms.openlocfilehash: 2aa9325fc01fff683e2604063408281e66d65804
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641927"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Cognitive Services リソースを作成する

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Cognitive Services リソースを作成およびデプロイする方法について説明します。 このマルチサービス リソースにより、次のことが可能になります。
* 1 つのキーとエンドポイントを使用して複数の Azure Cognitive Services にアクセスします。
* ご利用の複数のサービスの課金を統合します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、[無料でアカウントを作成](https://azure.microsoft.com/free/cognitive-services)してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-cognitive-services-universalkey/)からのものです。

:::code language="json" source="~/quickstart-templates/101-cognitive-services-universalkey/azuredeploy.json":::

テンプレートには、1 つの Azure リソースが定義されています。
* [Microsoft.CognitiveServices/accounts](https://docs.microsoft.com/azure/templates/microsoft.cognitiveservices/accounts): Cognitive Services リソースを作成します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. **[Deploy to Azure]** (Azure にデプロイ) ボタンをクリックします。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

2. 次の値を入力します。
    
    |値  |説明  |
    |---------|---------|
    | **サブスクリプション** | Azure サブスクリプションを選択します。 |
    | **リソース グループ** | **[新規作成]** を選択し、リソース グループの一意の名前を入力し、 **[OK]** をクリックします。 |
    | **リージョン** | リージョンを選択します。  たとえば、**East US** などとします。 |
    | **Cognitive Service 名** | リソースの一意の名前に置き換えます。 次のセクションでデプロイを検証するときに、その名前が必要です。 |
    | **場所** | 上で使用したリージョンに置き換えます。 |
    | **SKU** | リソースの[価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/)。 |
    
    :::image type="content" source="media/arm-template/universal-key-portal-template.png" alt-text="リソース作成画面。":::

3. **[確認および作成]** 、 **[作成]** の順に選択します。 リソースのデプロイが正常に完了すると、 **[リソースに移動]** ボタンが強調表示されます。

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

> [!NOTE]
> `az deployment group` の作成には、Azure CLI バージョン 2.6 以降が必要です。 バージョンを表示するには、`az --version` と入力します。 詳細については、[ドキュメント](https://docs.microsoft.com/cli/azure/deployment/group)を参照してください。

[ローカル コンピューターで](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure コマンド ライン インターフェイス (CLI) を使用するか、ブラウザーで **[使ってみる]** ボタンを使用して、次のスクリプトを実行します。 新しいリソース グループの名前と場所 (`centralus` など) を入力します。ARM テンプレートを使用して、その中に Cognitive Services リソースをデプロイします。 使用した名前を忘れないでください。 後でデプロイを検証するときに使用します。


```azurecli-interactive
read -p "Enter a name for your new resource group:" resourceGroupName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cognitive-services-universalkey/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]


## <a name="validate-the-deployment"></a>デプロイの検証

# <a name="portal"></a>[ポータル](#tab/portal)

デプロイが完了すると、 **[リソースに移動]** ボタンをクリックして新しいリソースを表示できます。 リソース グループは、次の方法でも見つけることができます。

1. 左側のナビゲーション メニューから、 **[リソース グループ]** を選択する。
2. リソース グループ名を選択する。

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

Azure CLI を使用して次のスクリプトを実行し、前の手順で作成したリソース グループの名前を入力します。

```azurecli-interactive
echo "Enter the resource group where the Cognitive Services resource exists:" &&
read resourceGroupName &&
az cognitiveservices account list -g $resourceGroupName
```

---


## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、そのグループに含まれている他のリソースも削除されます。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Azure Portal で左側のメニューを展開してサービスのメニューを開き、 **[リソース グループ]** を選択して、リソース グループの一覧を表示します。
2. 削除するリソースが含まれているリソース グループを見つけます
3. リソース グループの一覧を右クリックします。 **[リソース グループの削除]** を選択し、確認します。

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

Azure CLI を使用して次のスクリプトを実行し、前の手順で作成したリソース グループの名前を入力します。 

```azurecli-interactive
echo "Enter the resource group name, for deletion:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>次のステップ

* [Azure Cognitive Services に対する要求の認証](authentication.md)
* [Azure Cognitive Services とは](Welcome.md)
* [自然言語のサポート](language-support.md)
* [Docker コンテナーのサポート](cognitive-services-container-support.md)
