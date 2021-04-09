---
title: Azure ExpressRoute テンプレート:ExpressRoute 回線の作成
description: Azure PowerShell を使用して、Azure Resource Manager テンプレートをデプロイすることで、Azure ExpressRoute 回線を作成する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/13/2019
ms.author: duau
ms.openlocfilehash: 2e9b6ddc9da4467590946af12a47f1473a4ea494
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202057"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して ExpressRoute 回線を作成する

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager テンプレート](expressroute-howto-circuit-resource-manager-template.md)
> * [ビデオ - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (クラシック)](expressroute-howto-circuit-classic.md)
>

Azure PowerShell を使用して、Azure Resource Manager テンプレートをデプロイすることで、ExpressRoute 回線を作成する方法について説明します。 Resource Manager テンプレートの開発に関する詳細については、[Resource Manager ドキュメント](../azure-resource-manager/index.yml)と[テンプレート リファレンス](/azure/templates/microsoft.network/expressroutecircuits)をご覧ください。

## <a name="before-you-begin"></a>開始する前に

* 構成を開始する前に、[前提条件](expressroute-prerequisites.md)と[ワークフロー](expressroute-workflows.md)を確認してください。
* 新しいネットワーク リソースを作成するアクセス許可があることを確認します。 適切なアクセス許可がない場合は、アカウント管理者に連絡してください。
* 手順をより理解するため、開始する前に[ビデオを確認](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)できます。

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute 回線の作成とプロビジョニング

[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)には、Resource Manager テンプレートの適切なコレクションが含まれます。 [既存のテンプレート](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/)のいずれかを使用して、ExpressRoute 回線を作成できます。

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

その他の関連するテンプレートを確認する場合は、[こちら](https://azure.microsoft.com/resources/templates/?term=expressroute)を選択してください。

テンプレートをデプロイすることで、ExpressRoute 回線を作成するには:

1. 次のコード ブロックの **[使ってみる]** を選択し、指示に従って Azure Cloud Shell にサインインします。

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * **SKU レベル** によって、ExpressRoute 回線が [Local](expressroute-faqs.md#expressroute-local)、Standard、[Premium](expressroute-faqs.md#expressroute-premium) のどれであるかが決まります。 *Local*、*Standard、または *Premium* を指定できます。 SKU を *[Standard] または [Premium]* から *[Local]* に変更することはできません。
   * **SKU ファミリ** によって、課金の種類が決まります。 従量制課金データ プランの場合は *Metereddata*、無制限データ プランの場合は *Unlimiteddata* を指定できます。 課金の種類は *Metereddata* から *Unlimiteddata* に変更できますが、*Unlimiteddata* から *Metereddata* に変更することはできません。 "*Local*" 回線は "*Unlimiteddata*" のみです。
   * **ピアリングの場所** とは、Microsoft とピアリングしている物理的な場所です。

     > [!IMPORTANT]
     > ピアリングの場所は、Microsoft とピアリングしている[物理的な場所](expressroute-locations.md)を示します。 この場所は "Location" プロパティに **リンクされていません**。それは、Azure Network Resource Provider が配置されている地理的な場所を参照します。 それらは関連付けられていませんが、回路のピアリングの場所と地理的に近い場所にある Network Resource Provider を選択することをお勧めします。

    リソース グループ名は、**rg** が追加された Service Bus 名前空間名です。

2. **[コピー]** を選択し、PowerShell スクリプトがコピーされます。
3. シェル コンソールを右クリックし、 **[貼り付け]** を選択します。

イベント ハブが作成されるまでしばらく時間がかかります。

このチュートリアルでは、テンプレートをデプロイするために Azure PowerShell を使用します。 テンプレートのその他のデプロイ方法については、以下をご覧ください。

* [Azure portal を使用する方法](../azure-resource-manager/templates/deploy-portal.md)。
* [Azure CLI を使用する方法](../azure-resource-manager/templates/deploy-cli.md)。
* [REST API を使用する方法](../azure-resource-manager/templates/deploy-rest.md)。

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute 回線のプロビジョニング解除と削除

**[削除]** アイコンを選択し、ExpressRoute 回線を削除できます。 次の情報をメモしておきます。

* ExpressRoute 回線からすべての仮想ネットワークのリンクを解除する必要があります。 この操作が失敗した場合は、回線にリンクされている仮想ネットワークがないか確認してください。
* ExpressRoute 回線サービス プロバイダーのプロビジョニング状態が **プロビジョニング中** または **プロビジョニング済み** の場合、サービス プロバイダー側の回線のプロビジョニングを解除するには、サービス プロバイダーに連絡する必要があります。 Microsoft は、サービス プロバイダーが回線のプロビジョニング解除を完了し、通知するまで、リソースの予約と課金を続行します。
* サービス プロバイダーが回線のプロビジョニングを解除済み (サービス プロバイダーのプロビジョニング状態が **未プロビジョニング** に設定されている) の場合、回線を削除することができます。 これによって回線の課金が停止されます。

ExpressRoute 回線は、次の PowerShell コマンドを実行して削除できます。

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>次のステップ

回線を作成したら、次の手順に移ります。

* [ExpressRoute 回線のルーティングの作成と変更を行う](expressroute-howto-routing-portal-resource-manager.md)
* [仮想ネットワークを ExpressRoute 回線にリンクする](expressroute-howto-linkvnet-arm.md)