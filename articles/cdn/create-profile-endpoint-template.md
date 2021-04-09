---
title: クイック スタート:プロファイルとエンドポイントを作成する - Resource Manager テンプレート
titleSuffix: Azure Content Delivery Network
description: このクイックスタートでは、Resource Manager テンプレートを使用して Azure Content Delivery Network のプロファイルとエンドポイントを作成する方法について説明します
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: a36484bea3a2bc9caadc5cf4482aae3bea84eafa
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560465"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>クイック スタート:Azure CDN のプロファイルとエンドポイントの作成 - ARM テンプレート

Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure Content Delivery Network (CDN) を開始します。 テンプレートによってプロファイルとエンドポイントがデプロイされます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-cdn-with-custom-origin/)からのものです。

このテンプレートは、以下のリソースを作成するように構成されています。

* プロファイル
* エンドポイント

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json":::

テンプレートには、1 つの Azure リソースが定義されています。

* **[Microsoft.Cdn/profiles](/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>テンプレートのデプロイ

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

### <a name="portal"></a>ポータル

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 左側のウィンドウから **[リソース グループ]** を選択します。

3. 前のセクションで作成したリソース グループを選択します 既定のリソース グループ名は **myResourceGroupCDN** です

4. 次のリソースがリソース グループ内に作成されていることを確認します。

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Azure CDN リソース グループ" border="true":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

### <a name="azure-cli"></a>Azure CLI

リソース グループとそこに含まれているすべてのリソースは、不要になったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して削除できます。

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

必要がなくなったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループと、その内部に含まれているすべてのリソースを削除できます。

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>ポータル

必要がなくなったら、リソース グループ、CDN プロファイル、およびすべての関連リソースを削除します。 CDN のプロファイルとエンドポイントを含むリソース グループ **[myResourceGroupCDN]** を選択し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、次のものを作成しました。

* CDN プロファイル
* エンドポイント

Azure CDN と Azure Resource Manager の詳細については、引き続き以下の記事を参照してください。

> [!div class="nextstepaction"]
> [チュートリアル: サーバーの静的コンテンツへの CDN を Web アプリから使用する](cdn-add-to-web-app.md)
