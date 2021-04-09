---
title: 高密度ホスティングのためのアプリごとのスケーリング
description: App Service プランからは独立してアプリをスケーリングし、プラン内でスケールアウトされたインスタンスを最適化します。
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74672349"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>アプリごとのスケーリングを使って Azure App Service で高密度ホスティングを実現する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

App Service を使用するときは、そのアプリを実行する [App Service プラン](overview-hosting-plans.md)をスケーリングすることにより、アプリをスケーリングできます。 同じ App Service プランでアプリを複数実行している場合には、スケールアウトしたインスタンスのそれぞれがプラン内のアプリをすべて実行することになります。

App Service プラン レベルで *アプリごとのスケーリング* を有効にして、アプリをホストする App Service プランとは無関係にそのアプリをスケーリングできます。 これにより、App Service プランを 10 個のインスタンスにスケーリングしながら、5 個のインスタンスだけを使用するようにアプリを設定することが可能になります。

> [!NOTE]
> アプリごとのスケーリングは、**Standard**、**Premium**、**Premium V2**、および **Isolated** の価格レベルに限り利用できます。
>

アプリは、インスタンス間で均等に分散するためのベスト エフォート アプローチを使用して、使用可能な App Service プランに割り当てられます。 均等に分散は保証されませんが、プラットフォームは、同じアプリの 2 つのインスタンスが同じ App Service プラン インスタンスでホストされていないことを確認します。

プラットフォームは、ワーカーの割り当てを決定するメトリックには依存しません。 インスタンスが App Service プランに対して追加または削除されるときにのみ、アプリケーションは再調整されます。

## <a name="per-app-scaling-using-powershell"></a>PowerShell を使用したアプリごとのスケーリング

プランの作成時にアプリごとのスケーリングを有効にする場合には、```New-AzAppServicePlan``` コマンドレットに ```-PerSiteScaling $true``` パラメーターを渡します。

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

```Set-AzAppServicePlan```コマンドレットに `-PerSiteScaling $true` パラメーターを渡すことで、既存の App Service プランによるアプリごとのスケーリングを有効にします。

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

アプリ レベルでは、App Service プランでアプリが使用できるインスタンスの数を構成します。

次の例では、App Service プランでスケール アウトされるインスタンスの数に関係なく、アプリが使用できるインスタンスは 2 個までに制限されます。

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` は `$newapp.MaxNumberOfWorkers` とは異なるものです。 アプリごとのスケーリングでは、アプリのスケーリング特性の特定に `$newapp.SiteConfig.NumberOfWorkers` が使用されます。

## <a name="per-app-scaling-using-azure-resource-manager"></a>Azure Resource Manager を使用したアプリごとのスケーリング

次の Azure Resource Manager テンプレートは、以下を作成します。

- 10 個のインスタンスにスケール アウトされる App Service プラン
- 最大 5 個のインスタンスまでスケーリングされるように構成されたアプリ

App Service プランは、**PerSiteScaling** プロパティを true に設定します (`"perSiteScaling": true`)。 アプリは、使用する **ワーカーの数** を 5 に設定します (`"properties": { "numberOfWorkers": "5" }`)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>高密度ホスティングの推奨される構成

アプリごとのスケーリングは、世界各地の Azure リージョンと [App Service Environment](environment/app-service-app-service-environment-intro.md) のどちらでも有効にできる機能です。 ただし推奨されるのは App Service Environment です。App Service Environment を使用した方が、その高度な機能と大きな App Service プラン容量を利用できます。  

アプリに対して高密度ホスティングを構成するには、次の手順に従います。

1. 高密度のプランとして App Service プランを指定し、それを目的の容量にスケール アウトします。
1. App Service プランの `PerSiteScaling` フラグを true に設定します。
1. 新しいアプリが作成され、その App Service プランに **1** に設定された **numberOfWorkers** プロパティが割り当てられます。
   - この構成を使用すると、可能な最高の密度が得られます。
1. ワーカーの数はアプリごとに個別に構成でき、必要に応じて追加リソースを許可できます。 次に例を示します。
   - 使用率が高いアプリでは、**numberOfWorkers** を **3** に設定してそのアプリの処理能力を上げます。
   - 使用率の低いアプリでは、**numberOfWorkers** を **1** に設定します。

## <a name="next-steps"></a>次のステップ

- [Azure App Service プランの詳細な概要](overview-hosting-plans.md)
- [App Service Environment の概要](environment/app-service-app-service-environment-intro.md)
