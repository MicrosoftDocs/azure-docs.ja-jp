---
title: クイックスタート:Azure Front Door を使用して高可用性を設定する - Azure PowerShell
description: このクイックスタートでは、Azure Front Door を使用して、Azure PowerShell を使用した高可用性でハイ パフォーマンスのグローバル Web アプリケーションを作成する方法を説明します。
services: front-door
documentationcenter: na
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: 8ccc50dfc4c53f1ee207a04d7d7775775f13cb65
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91348044"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>クイックスタート:Azure PowerShell を使用して高可用性のグローバル Web アプリケーション向けの Front Door を作成する

Azure PowerShell を使用して、高可用性でハイ パフォーマンスのグローバル Web アプリケーションを作成することにより、Azure Front Door の使用を開始します。

Front Door により、バックエンド プール内の特定のリソースに Web トラフィックが送信されます。 フロントエンド ドメインを定義し、バックエンド プールにリソースを追加して、ルーティング規則を作成します。 この記事では、1 つのバックエンド プールに 2 つの Web アプリ リソースがある単純な構成と、"/*" に一致する既定のパスを使用する 1 つのルーティング規則を使用します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>リソース グループの作成

Azure で、関連するリソースをリソース グループに割り当てます。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、次のようにリソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>Web アプリの 2 つのインスタンスを作成する

このクイックスタートには、別々の Azure リージョンで実行される、Web アプリケーションの 2 つのインスタンスが必要です。 どちらの Web アプリケーション インスタンスもアクティブ/アクティブ モードで実行されるため、どちらでもトラフィックを受け入れることができます。 この構成は、一方がフェールオーバーとして機能するアクティブ/スタンバイ構成とは異なります。

まだ Web アプリがない場合は、次のスクリプトを使用して 2 つのサンプル Web アプリを設定します。

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanSouthCentralUS
```

## <a name="create-a-front-door"></a>Front Door を作成する

このセクションでは、Front Door の以下のコンポーネントを作成および構成する方法について説明します。
    
* フロントエンド オブジェクトには、Front Door の既定のドメインが含まれます。
* バックエンド プールは、Front Door がクライアント要求の負荷を分散する同等のバックエンドのセットです。
* ルーティング規則により、フロントエンド ホストと、対応する URL パス パターンが特定のバックエンド プールにマップされます。

### <a name="create-a-frontend-object"></a>フロントエンド オブジェクトを作成する

フロントエンド オブジェクトにより、Front Door のホスト名が構成されます。 既定では、ホスト名には **azurefd.net* のサフィックスが付きます。

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>バックエンド プールの作成

バックエンド プールは、このクイックスタートの冒頭で作成した 2 つの Web アプリで構成されます。 この手順で定義した正常性プローブおよび負荷分散の設定では、既定値が使用されます。

```azurepowershell-interactive
# Create backend objects that points to the hostname of the web apps
$backendObject1 = New-AzFrontDoorBackendObject `
-Address $webapp1.DefaultHostName
$backendObject2 = New-AzFrontDoorBackendObject `
-Address $webapp2.DefaultHostName

# Create a health probe object
$HealthProbeObject = New-AzFrontDoorHealthProbeSettingObject `
-Name "HealthProbeSetting"

# Create the load balancing setting object
$LoadBalancingSettingObject = New-AzFrontDoorLoadBalancingSettingObject `
-Name "Loadbalancingsetting" `
-SampleSize "4" `
-SuccessfulSamplesRequired "2" `
-AdditionalLatencyInMilliseconds "0"

# Create a backend pool using the backend objects, health probe, and load balancing settings
$BackendPoolObject = New-AzFrontDoorBackendPoolObject `
-Name "myBackendPool" `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-Backend $backendObject1,$backendObject2 `
-HealthProbeSettingsName "HealthProbeSetting" `
-LoadBalancingSettingsName "Loadbalancingsetting"
```

### <a name="create-a-routing-rule"></a>ルーティング規則を作成する

このルーティング規則では、バックエンド プールがフロントエンド ドメインにマップされ、既定のパスが値 "/*" に設定されます。

```azurepowershell-interactive
# Create a routing rule mapping the frontend host to the backend pool
$RoutingRuleObject = New-AzFrontDoorRoutingRuleObject `
-Name LocationRule `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-FrontendEndpointName "frontendEndpoint1" `
-BackendPoolName "myBackendPool" `
-PatternToMatch "/*"
```
### <a name="create-the-front-door"></a>Front Door を作成する

必要なオブジェクトを作成したので、Front Door を作成します。

```azurepowershell-interactive
# Creates the Front Door
New-AzFrontDoor `
-Name $fdname `
-ResourceGroupName myResourceGroupFD `
-RoutingRule $RoutingRuleObject `
-BackendPool $BackendPoolObject `
-FrontendEndpoint $FrontendEndObject `
-LoadBalancingSetting $LoadBalancingSettingObject `
-HealthProbeSetting $HealthProbeObject
```

デプロイが正常に完了したら、次のセクションの手順に従ってそれをテストできます。

## <a name="test-the-front-door"></a>Front Door をテストする

次のコマンドを実行して、Front Door のホスト名を取得します。

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

Web ブラウザーを開き、コマンドから取得したホスト名を入力します。 この Front Door によって、要求がバックエンド リソースのいずれかに送信されるようになります。 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Front Door のテスト ページ":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Front Door で作成したリソースが不要になった場合は、リソース グループを削除します。 リソース グループを削除すると、Front Door とそのすべての関連リソースも削除されます。 

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、次のものを作成しました。
* Front Door
* 2 つの Web アプリ

Front Door にカスタム ドメインを追加する方法を学習するには、Front Door のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [カスタム ドメインの追加](front-door-custom-domain.md)
