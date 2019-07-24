---
title: Application Insights リソースを作成するための PowerShell スクリプト | Microsoft Docs
description: Application Insights リソースの作成を自動化します。
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: 8a7b19dd6e5bc08c0c7e278b514ecaa9dc13a00e
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58485240"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Application Insights リソースを作成するための PowerShell スクリプト

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/) を使って新しいアプリケーションを監視する際や、新しいバージョンのアプリケーションを監視する際、Microsoft Azure で新しいリソースを設定します。 このリソースは、アプリのテレメトリ データを分析、表示する場所です。 

PowerShell を使用して、新しいリソースの作成を自動化できます。

たとえば、モバイル デバイス アプリを開発している場合、顧客によって使用されている発行済みのアプリのバージョンは常に複数存在する可能性があります。 複数のバージョンから取得したテレメトリの結果を混在させないようにします。 それで、ビルド プロセスを取得して、各ビルドで新しいリソースを作成します。

> [!NOTE]
> 一連のリソースすべてを同時に作成したい場合は、[Azure テンプレートを使用したリソースの作成](powershell.md)を検討してください。
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Application Insights リソースを作成するスクリプト
関連するコマンドレットの仕様を参照してください。

* [New-AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*PowerShell スクリプト*  

```powershell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzAccount / Connect-AzAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>iKey を使用して行うこと
各リソースは、インストルメンテーション キー (iKey) によって識別されます。 iKey はリソース作成スクリプトの出力です。 ビルド スクリプトで、アプリに組み込まれた Application Insights SDK に iKey を提供する必要があります。

iKey を SDK で使用できるようにする方法は 2 つあります。

* [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)の場合: 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* [初期化コードの場合](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>関連項目
* [テンプレートから Application Insights と Web テスト リソースを作成する](powershell.md)
* [PowerShell で Azure 診断の監視を設定する](powershell-azure-diagnostics.md) 
* [PowerShell を使用したアラートの設定](powershell-alerts.md)

