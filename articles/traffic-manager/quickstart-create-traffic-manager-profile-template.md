---
title: クイック スタート:Azure Resource Manager テンプレート (ARM テンプレート) を使用して Traffic Manager を作成する
description: このクイックスタート記事では、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure Traffic Manager プロファイルを作成する方法を説明します。
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: duau
ms.date: 09/01/2020
ms.openlocfilehash: ec569781a6318062810358c2c5e17ba71efc4f71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92676004"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Traffic Manager プロファイルを作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、パフォーマンスによるルーティング方法を使用する外部エンドポイントがある Traffic Manager プロファイルの作成方法を説明します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint)からのものです。

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

テンプレートには、1 つの Azure リソースが定義されています。

* [**Microsoft.Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

Azure Traffic Manager に関連するテンプレートをさらに探すには、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)」を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. 次のコード ブロックの **[使ってみる]** を選択して Azure Cloud Shell を開き、指示に従って Azure にサインインします。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    コンソールからプロンプトが表示されるまで待ちます。

1. 前のコード ブロックから **[コピー]** を選択して、PowerShell スクリプトをコピーします。

1. シェル コンソール ウィンドウを右クリックし、 **[貼り付け]** を選択します。

1. 値を入力します。

    テンプレートのデプロイによって、2 つの外部エンドポイントがあるプロファイルが作成されます。 **Endpoint1** は、`www.microsoft.com` のターゲット エンドポイントを使用し、その場所は **北ヨーロッパ** です。 **Endpoint2** は、`docs.microsoft.com` のターゲット エンドポイントを使用し、その場所は **米国中南部** です。

    リソース グループの名前は、**rg** が付加されたプロジェクト名です。

    > [!NOTE]
    > テンプレートが正常にデプロイされるためには、**uniqueDNSname** がグローバルに一意の名前である必要があります。 デプロイが失敗した場合は、手順 1. からやり直します。

    テンプレートのデプロイには数分かかります。 完了すると、次のように出力されます。

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Azure Traffic Manager の Resource Manager テンプレートによる PowerShell でのデプロイの出力":::

テンプレートをデプロイするには Azure PowerShell を使用します。 Azure PowerShell だけでなく、Azure portal、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-portal.md)」を参照してください。

## <a name="validate-the-deployment"></a>デプロイの検証

1. [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile) を使用して Traffic Manager プロファイルの DNS 名を決定します。

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    **RelativeDnsName** 値をコピーします。 Traffic Manager プロファイルの DNS 名は、`<relativednsname>.trafficmanager.net` です。

1. ローカル PowerShell から次のコマンドを実行します。その際に、 **{relativeDNSname}** 変数を `<relativednsname>.trafficmanager.net` に置き換えます。

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```

    どちらのリージョンが近いかに応じて、`www.microsoft.com` または `docs.microsoft.com` の NameHost を取得する必要があります。

1. もう一方のエンドポイントに解決できるかどうかを確認するには、最後の手順で取得したターゲットのエンドポイントを無効にします。 `www.microsoft.com` または `docs.microsoft.com` のターゲットを無効にするには、 **{endpointName}** をそれぞれ **endpoint1** または **endpoint2** に置き換えます。

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```

1. ローカル PowerShell で、手順 2. のコマンドをもう一度実行します。 今回は、もう一方のエンドポイントの NameHost を取得する必要があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Traffic Manager プロフィルが必要なくなったら、リソース グループを削除します。 これにより、Traffic Manager プロファイルとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Traffic Manager プロファイルを作成しました。

トラフィックのルーティングについて理解を深めるために、引き続き Traffic Manager のチュートリアルをご覧ください。

> [!div class="nextstepaction"]
> [Traffic Manager のチュートリアル](tutorial-traffic-manager-improve-website-response.md)
