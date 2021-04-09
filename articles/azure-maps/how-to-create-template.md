---
title: ARM テンプレートを使用して Azure Maps アカウントを作成する | Microsoft Azure Maps
description: Azure Resource Manager (ARM) テンプレートを使用して Azure Maps アカウントを作成する方法について説明します。
author: philmea
ms.author: philmea
ms.date: 10/20/2020
ms.topic: how-to
ms.service: azure-maps
ms.openlocfilehash: c715c0639e962f76f669515c1d2c826c8cf6cc9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92525009"
---
# <a name="create-your-azure-maps-account-using-an-arm-template"></a>ARM テンプレートを使用して Azure Maps アカウントを作成する

Azure Resource Manager (ARM) テンプレートを使用して Azure Maps アカウントを作成できます。 アカウントを取得したら、Web サイトまたはモバイル アプリケーションで API を実装できます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

この記事を完了するには:

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-maps-create/)からのものです。

:::code language="json" source="~/quickstart-templates/101-maps-create/azuredeploy.json":::

Azure Maps リソースはこのテンプレートで定義されています。

* [**Microsoft.Maps/accounts**](/azure/templates/microsoft.maps/accounts): Azure Maps アカウントを作成します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 このテンプレートによって Azure Maps アカウントが作成されます。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

2. 次の値を選択または入力します。

    ![ARM テンプレート デプロイのポータル](./media/how-to-create-template/create-account-using-template-portal.png)

    特に明記されていない場合は、既定値を使用して Azure Maps アカウントを作成してください。

    * **サブスクリプション**: Azure サブスクリプションを選択します。
    * **リソース グループ**: **[新規作成]** を選択し、リソース グループの一意の名前を入力し、 **[OK]** をクリックします。
    * **場所**: 場所を選択します。 たとえば、**米国西部 2** にします。
    * **アカウント名**: Azure Maps アカウントの名前を入力します。この名前はグローバルに一意である必要があります。
    * **価格レベル**: 適切な価格レベルを選択します。テンプレートの既定値は S0 です。

3. **[Review + create]\(レビュー + 作成\)** を選択します。 
4. [確認] ページで、設定を確認し、 **[作成]** をクリックします。 Azure Maps が正常にデプロイされると、次の通知が表示されます。

    ![ARM テンプレート デプロイのポータル通知](./media/how-to-create-template/resource-manager-template-portal-deployment-notification.png)

テンプレートをデプロイするには Azure portal を使用します。 Azure PowerShell、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal を使用して、Azure Maps アカウントを確認し、キーを表示できます。 次の Azure CLI スクリプトを使用して、アカウント キーを一覧表示することもできます。

```azurecli-interactive
az maps account keys list --name MyMapsAccount --resource-group MyResourceGroup
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったリソース グループは削除してください。リソース グループを削除すると、Azure Maps アカウントも削除されます。 Azure CLI を使用してリソース グループを削除するには、次を実行します。

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>次のステップ

Azure Maps と Azure Resource Manager の詳細については、引き続き以下の記事をご覧ください。

- Azure Maps の[デモ アプリケーション](quick-demo-map-app.md)を作成する
- [ARM テンプレート](../azure-resource-manager/templates/overview.md)の詳細を確認する