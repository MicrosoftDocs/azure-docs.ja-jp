---
title: 'クイックスタート: Azure Resource Manager テンプレート (ARM テンプレート) を使用して ExpressRoute 回線を作成する'
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して ExpressRoute 回線を作成する方法について説明します。
services: expressroute
author: duongau
mnager: kumud
ms.service: expressroute
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: 7521344a2bb6aae67724c8bfbb9131e2ff1e6b94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92789728"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用してプライベート ピアリングによる ExpressRoute 回線を作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、プライベート ピアリングによる ExpressRoute 回線を作成する方法を説明します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet)からのものです。

このクイックスタートでは、サービス プロバイダーに *Equinix* を使用して ExpressRoute 回線を作成します。 この回線には、帯域幅 *50 Mbps*、ピアリングの場所を "*ワシントン DC*" とする *Premium SKU* が使用されます。 プライベート ピアリングは、プライマリ サブネット *192.168.10.16/30* とセカンダリ サブネット *192.168.10.20/30* を使用して有効化されます。 また、"*HighPerformance ExpressRoute ゲートウェイ*" と共に仮想ネットワークが作成されます。

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json":::

テンプレートでは、複数の Azure リソースが定義されています。

* [**Microsoft.Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft.Network/expressRouteCircuits/peerings**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (回線でプライベート ピアリングを有効にするために使用されます)
* [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (仮想ネットワーク内のサブネットにはネットワーク セキュリティ グループが適用されます)
* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicIPAddresses) (ExpressRoute ゲートウェイによってパブリック IP が使用されます)
* [**Microsoft.Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways) (ExpressRoute ゲートウェイを使用して VNet と回線がリンクされます)

ExpressRoute に関連するテンプレートをさらに探すには、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)」を参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. 次のコード ブロックの **[使ってみる]** を選択して Azure Cloud Shell を開き、指示に従って Azure にサインインします。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    コンソールからプロンプトが表示されるまで待ちます。

1. 前のコード ブロックから **[コピー]** を選択して、PowerShell スクリプトをコピーします。

1. シェル コンソール ウィンドウを右クリックし、 **[貼り付け]** を選択します。

1. 値を入力します。

    リソース グループの名前は、**rg** が付加されたプロジェクト名です。

    テンプレートのデプロイには約 20 分かかります。 完了すると、次のように出力されます。

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="ExpressRoute Resource Manager テンプレートの PowerShell デプロイ出力":::

テンプレートをデプロイするには Azure PowerShell を使用します。 Azure PowerShell だけでなく、Azure portal、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-portal.md)」を参照してください。

## <a name="validate-the-deployment"></a>デプロイの検証

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 左側のウィンドウから **[リソース グループ]** を選択します。

1. 前のセクションで作成したリソース グループを選択します 既定のリソース グループ名は、プロジェクト名に **rg** が追加されたものです。

1. リソース グループには、次の画面に示したリソースが含まれている必要があります。

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="ExpressRoute デプロイ リソース グループ":::

1. ExpressRoute 回線 **er-ck01** を選択して、回線の状態が **有効** であること、プロバイダーの状態が **未プロビジョニング** であること、プライベート ピアリングの状態が **プロビジョニング済み** であることを確認します。

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="ExpressRoute デプロイ回線":::

> [!NOTE]
> 仮想ネットワークを回線にリンクさせるには、まずプロバイダーを呼び出してプロビジョニング プロセスを完了する必要があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ExpressRoute 回線と共に作成したリソースが不要になった場合は、リソース グループを削除します。 これにより、ExpressRoute 回線とすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、次のものを作成しました。

* ExpressRoute 回線
* Virtual Network
* VPN Gateway
* パブリック IP
* ネットワーク セキュリティ グループ

仮想ネットワークを回線にリンクさせる方法については、ExpressRoute のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ExpressRoute のチュートリアル](expressroute-howto-linkvnet-portal-resource-manager.md)
