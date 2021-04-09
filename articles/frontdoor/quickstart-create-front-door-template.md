---
title: クイック スタート」を参照してください。Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure Front Door Service を作成する
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure Front Door Service を作成する方法を説明します。
services: front-door
documentationcenter: ''
author: duongau
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: 223006193219afe4179f3161d5e60e6439207b22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896057"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>クイック スタート」を参照してください。ARM テンプレートを使用して Front Door を作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Web エンドポイントの高可用性を設定するための Front Door を作成する方法を説明します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、**[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
* Web サイトまたはアプリケーションの IP または FQDN。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-front-door-create-basic)からのものです。

このクイックスタートでは、1 つのバックエンドと、`/*` に一致する 1 つの既定のパスを使用して、Front Door 構成を作成します。

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

テンプレートには、1 つの Azure リソースが定義されています。

* [**Microsoft.Network/frontDoors**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. 次のコード ブロックの **[使ってみる]** を選択して Azure Cloud Shell を開き、指示に従って Azure にサインインします。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    コンソールからプロンプトが表示されるまで待ちます。

1. 前のコード ブロックから **[コピー]** を選択して、PowerShell スクリプトをコピーします。

1. シェル コンソール ウィンドウを右クリックし、 **[貼り付け]** を選択します。

1. 値を入力します。

    テンプレートのデプロイにより、1 つのバックエンドを使用した Front Door が作成されます。 この例では、`microsoft.com` が **backendAddress** として使用されます。

    リソース グループの名前は、**rg** が付加されたプロジェクト名です。

    > [!NOTE]
    > テンプレートが正常にデプロイされるためには、**frontDoorName** がグローバルに一意の名前である必要があります。 デプロイが失敗した場合は、手順 1. からやり直します。

    テンプレートのデプロイには数分かかります。 完了すると、次のように出力されます。

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Front Door Resource Manager テンプレートの PowerShell デプロイ出力":::

テンプレートをデプロイするには Azure PowerShell を使用します。 Azure PowerShell だけでなく、Azure portal、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-portal.md)」を参照してください。

## <a name="validate-the-deployment"></a>デプロイの検証

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 左側のウィンドウから **[リソース グループ]** を選択します。

1. 前のセクションで作成したリソース グループを選択します 既定のリソース グループ名は、プロジェクト名に **rg** が追加されたものです。

1. 前に作成した Front Door を選択し、 **[フロントエンド ホスト]** リンクをクリックします。 このリンクにより、Web ブラウザーが開き、作成時に定義したバックエンド FQDN にリダイレクトされます。

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Front Door ポータルの概要":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Front Door サービスが必要なくなったら、リソース グループを削除します。 これにより、Front Door とすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Front Door を作成しました。

フロント ドアにカスタム ドメインを追加する方法を学習するには、Front Door のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Front Door のチュートリアル](front-door-custom-domain.md)
