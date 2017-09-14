---
title: "Azure App Service での高密度ホスティング | Microsoft Docs"
description: "Azure App Service での高密度ホスティング"
author: btardif
manager: erikre
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: byvinyal
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 459a310a719695f6366470976d857ec2f9d6f4a1
ms.contentlocale: ja-jp
ms.lasthandoff: 06/15/2017


---
# <a name="high-density-hosting-on-azure-app-service"></a>Azure App Service での高密度ホスティング
App Service を使用するとき、アプリケーションは 2 つの概念によって割り当てられた容量から切り離されます。

* **アプリケーション:** アプリとその実行時構成を表します。 たとえば、ランタイムで読み込む必要のある .NET のバージョンや、アプリの設定が該当します。
* **App Service プラン:** 容量、使用可能な機能セット、アプリケーションの局所性の特性を定義します。 たとえば、米国東部に存在する L サイズ (4 コア) マシン 4 インスタンスのプレミアム機能といった特性が考えられます。

アプリは常に App Service プランにリンクされていますが、App Service プランは 1 つまたは複数のアプリに容量を提供できます。

この結果、プラットフォームは柔軟に、1 つのアプリを分離したり、App Service プランを共有して複数のアプリでリソースを共有したりできます。

ただし、複数のアプリが 1 つの App Service プランを共有するときは、その App Service プランの各インスタンス上でそのアプリのインスタンスが実行されます。

## <a name="per-app-scaling"></a>アプリごとのスケーリング
*アプリごとのスケーリング* " は、App Service プラン レベルで有効にしてアプリケーションごとに利用できる機能です。

アプリごとのスケーリングでは、アプリがそのホストとなる App Service プランとは無関係にスケーリングされます。 これにより、App Service プランを 10 個のインスタンスにスケーリングしながら、5 個のインスタンスだけを使用するようにアプリを設定することが可能になります。

   >[!NOTE]
   >アプリごとのスケーリングは、**Premium** SKU App Service プランのみで使用できます。
   >

### <a name="per-app-scaling-using-powershell"></a>PowerShell を使用したアプリごとのスケーリング

```-perSiteScaling $true``` 属性を```New-AzureRmAppServicePlan``` コマンドレットに渡すことで、"*アプリごとのスケーリング*" プランとして構成されたプランを作成できます。

```
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

既存の App Service プランをこの機能を使用するように更新する場合: 

- ターゲット プランを取得します。```Get-AzureRmAppServicePlan```
- プロパティをローカルに変更します。```$newASP.PerSiteScaling = $true```
- 変更を Azure に反映させます。```Set-AzureRmAppServicePlan``` 

```
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

アプリ レベルでは、App Service プランでアプリが使用できるインスタンスの数を構成する必要があります。

次の例では、App Service プランでスケール アウトされるインスタンスの数に関係なく、アプリが使用できるインスタンスは 2 個までに制限されます。

```
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> $newapp.SiteConfig.NumberOfWorkers は $newapp.MaxNumberOfWorkers の別の形です。 アプリごとのスケーリングでは、$newapp.SiteConfig.NumberOfWorkers を使用してアプリのスケーリング特性を指定します。

### <a name="per-app-scaling-using-azure-resource-manager"></a>Azure Resource Manager を使用したアプリごとのスケーリング

次の *Azure Resource Manager テンプレート*は、以下を作成します。

- 10 個のインスタンスにスケール アウトされる App Service プラン
- 最大 5 個のインスタンスまでスケーリングされるように構成されたアプリ

App Service プランは、**PerSiteScaling** プロパティを true に設定します (```"perSiteScaling": true```)。 アプリは、使用する**ワーカーの数**を 5 に設定します (```"properties": { "numberOfWorkers": "5" }```)。

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
アプリごとのスケーリングは、グローバル Azure リージョンと App Service Environment のどちらでも有効にできます。 ただし推奨されるのは App Service Environment です。App Service Environment を使用した方が、その高度な機能と大きなプール容量を利用できます。  

アプリに対して高密度ホスティングを構成するには、次の手順に従います。

1. App Service Environment を構成し、高密度ホスティング シナリオ専用のワーカー プールを選択します。
1. 1 つの App Service プランを作成し、ワーカー プールの全使用可能容量を使用するようにスケーリングします。
1. App Service プランの PerSiteScaling フラグを true に設定します。
1. 新しいアプリが作成され、その App Service プランに **1** に設定された **numberOfWorkers** プロパティが割り当てられます。 この構成を使用すると、このワーカー プールで可能な最高の密度になります。
1. ワーカーの数はアプリごとに個別に構成でき、必要に応じて追加リソースを許可できます。 For example:
    - 使用率が高いアプリでは、**numberOfWorkers** を **3** に設定してそのアプリの処理能力を上げます。 
    - 使用率の低いアプリでは、**numberOfWorkers** を **1** に設定します。

## <a name="next-steps"></a>次のステップ

- [Azure App Service プランの詳細な概要](azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [App Service 環境の概要](../app-service-web/app-service-app-service-environment-intro.md)
