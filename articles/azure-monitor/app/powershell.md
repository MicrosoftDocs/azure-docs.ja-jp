---
title: PowerShell での Azure Application Insights の自動化 | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して、PowerShell でのリソース、アラート、および可用性テストの作成および管理を自動化します。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/17/2019
ms.openlocfilehash: 75490edfd30541aa641656a2ccc17a259bfbe927
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951362"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>PowerShell を使用した Application Insights リソースの管理

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

この記事では、Azure Resource 管理を使用して [Application Insights](../../azure-monitor/app/app-insights-overview.md) リソースの作成と更新を自動化する方法を説明します。 たとえば、ビルド プロセスの一部として実行します。 基本的な Application Insights リソースと共に、[可用性 Web テスト](../../azure-monitor/app/monitor-web-app-availability.md)の作成、[アラート](../../azure-monitor/app/alerts.md)の設定、[価格の詳細](pricing.md)の設定、その他の Azure リソースの作成を行うことができます。

これらのリソースを作成する際に重要となるのが、[Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md) の JSON テンプレートです。 基本的な手順は、既存のリソースの JSON 定義をダウンロードし、名前などの特定の値をパラメーター化して、新しいリソースを作成するときに、テンプレートを実行するといった手順になります。 いくつかのリソースをまとめてパッケージ化することで、すべてを一度に作成できます (例、可用性テスト、アラート、および連続エクスポート用の記憶域を使用したアプリの監視)。 パラメーター化の一部には、いくつか細かい点があります。それについては、以降で説明します。

## <a name="one-time-setup"></a>1 回限りのセットアップ
以前に Azure サブスクリプションで PowerShell を使用したことがない場合

スクリプトを実行するコンピューターに Azure PowerShell モジュールをインストールします。

1. [Microsoft Web Platform Installer (v5 以上)](https://www.microsoft.com/web/downloads/platform.aspx)をインストールします。
2. このインストーラーを使用して Microsoft Azure PowerShell をインストールします。

Resource Manager テンプレートの使用に加えて、[Application Insights PowerShell コマンドレット](https://docs.microsoft.com/powershell/module/az.applicationinsights)の豊富なセットが用意されています。これにより、Application Insights リソースをプログラムによって簡単に構成できます。 コマンドレットによって有効になる機能は次のとおりです。

* Application Insights リソースの作成と削除
* Application Insights リソースとそのプロパティの一覧の取得
* 連続エクスポートの作成と管理
* アプリケーション キーの作成と管理
* 1 日の上限を設定する
* 料金プランの設定

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>PowerShell コマンドレットを使用して Application Insights リソースを作成する

[New-AzApplicationInsights](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights) コマンドレットを使用して、Azure 米国東部データセンターに新しい Application Insights リソースを作成する方法を次に示します。

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Application Insights リソースを作成する

Resource Manager テンプレートを使用して新しい Application Insights リソースを作成する方法を次に示します。

### <a name="create-the-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの作成

新しい .json ファイルを作成します (この例では、 `template1.json` と呼びます)。 ファイルに、次のコンテンツをコピーします。

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "retentionInDays": "[parameters('retentionInDays')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Resource Manager テンプレートを使用して新しい Application Insights リソースを作成する

1. PowerShell で `$Connect-AzAccount` を使用して Azure にサインインします。
2. `Set-AzContext "<subscription ID>"` を使用して、コンテキストをサブスクリプションに設定します。
2. 新しいデプロイを実行して、新しい Application Insights リソースを作成します。
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` は、新しいリソースを作成するグループです。
   * `-TemplateFile` は、カスタム パラメーターの前に置く必要があります。
   * `-appName` 作成するリソースの名前です。

その他のパラメーターを追加することもできます。テンプレートのパラメーター セクションに説明があります。

## <a name="get-the-instrumentation-key"></a>インストルメンテーション キーの取得

アプリケーション リソースを作成したら、インストルメンテーション キーが必要になります。 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Application Insights リソースの他の多くのプロパティの一覧を表示するには、以下を使用します。

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

その他のプロパティについては、以下のコマンドレットを使用して確認できます。
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

これらのコマンドレットのパラメーターについては、[詳細なドキュメント](https://docs.microsoft.com/powershell/module/az.applicationinsights)を参照してください。  

## <a name="set-the-data-retention"></a>データ保有期間を設定する 

Application Insights リソースの現在のデータ保有期間を取得するには、OSS ツール [ARMClient](https://github.com/projectkudu/ARMClient) を使用します。  (ARMClient の詳細については、[David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) および [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/) による記事を参照してください。)`ARMClient` を使用して現在の保有期間を取得する例を次に示します。

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

保有期間を設定するには、次のような PUT コマンドを使用します。

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

上記のテンプレートを使用してデータ保有期間を 365 日に設定するには、次のように実行します。

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

次のスクリプトは、保有期間の変更にも使用できます。 `Set-ApplicationInsightsRetention.ps1` として保存するには、このスクリプトをコピーします。

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

このスクリプトを使用して次のことを行えます。

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>1 日の上限を設定する

1 日あたりの上限のプロパティを取得するには、[Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) コマンドレットを使用します。 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

1 日あたりの上限のプロパティを設定するには、同じコマンドレットを使用します。 たとえば、上限を 300 GB/日に設定するには、以下のようにします。 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>料金プランを設定する 

現在の料金プランを取得するには、[Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) コマンドレットを使用します。 

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

料金プランを設定するには、`-PricingPlan` を指定して、同じコマンドレットを使用します。  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

また、上記の Resource Manager テンプレートを使用して、"microsoft.insights/components" リソースと `dependsOn` ノードを課金リソースから省略して、既存の Application Insights リソースに対して料金プランを設定することもできます。 たとえば、GB あたりのプラン (以前は Basic プランと呼ばれていました) に設定するには、次のように実行します。

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

|priceCode|プラン|
|---|---|
|1|GB あたり (以前は Basic プランと呼ばれていました)|
|2|ノードごと (以前は Enterprise プランと呼ばれていました)|

## <a name="add-a-metric-alert"></a>メトリック アラートの追加

メトリック アラートの作成を自動化するには、[メトリック アラート テンプレートに関する記事](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-simple-static-threshold-metric-alert)を参照


## <a name="add-an-availability-test"></a>可用性テストの追加

可用性テストを自動化するには、[メトリック アラート テンプレートに関する記事](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-an-availability-test-along-with-a-metric-alert)を参照してください。

## <a name="add-more-resources"></a>リソースの追加

他のリソース (任意の種類) の作成を自動化するには、サンプルを手動で作成した後、[Azure Resource Manager](https://resources.azure.com/) からそのコードをパラメーター化します。 

1. [Azure リソース マネージャー](https://resources.azure.com/)を開きます。 `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` に移動して、目的のアプリケーション リソースに移動します。 
   
    ![Azure リソース エクスプローラーのナビゲーション](./media/powershell/01.png)
   
    *コンポーネント* はアプリケーションを表示するための基本的な Application Insights リソースです。 関連するアラート ルールおよび可用性 Web テストに対して別々 のリソースがあります。
2. コンポーネントの JSON を `template1.json`の適切な場所にコピーします。
3. 次のプロパティを削除します。
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. `webtests` と `alertrules` セクションを開き、個々の項目の JSON をテンプレートにコピーします。 (`webtests` または `alertrules` ノードからコピーせず、その下の項目に移動してください。)
   
    各 Web テストには、関連するアラート ルールがあるため、両方をコピーする必要があります。
   
    メトリックでのアラートを含めることもできます。 [メトリック名](powershell-alerts.md#metric-names)。
5. 各リソースに次の行を挿入します。
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>テンプレートのパラメーター化
ここでは、特定の名前をパラメーターで置き換える必要があります。 [テンプレート](../../azure-resource-manager/resource-group-authoring-templates.md)をパラメーター化するには、[一連のヘルパー関数](../../azure-resource-manager/resource-group-template-functions.md)を使用して式を記述します。 

文字列の一部のみをパラメーター化することはできません。そのため、`concat()` を使用して、文字列を構築します。

次に、作成する代替文字列の例を示します。 それぞれの代替文字列は、複数回現れます。 作成するテンプレートには、その他のものが必要になる場合があります。 これらの例では、テンプレートの上部で定義したパラメーターと変数を使用しています。

| find | 置き換え |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (小文字) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>リソース間の依存関係の設定
Azure では、厳密な順序でリソースを設定する必要があります。 次の設定を開始する前に、確実に 1 つの設定を完了するために、依存関係の行を追加します。

* 可用性テスト リソース
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* 可用性テストのアラート リソース
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>次の手順
自動化に関するその他の記事:

* [Application Insights リソースを作成するための PowerShell スクリプト](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) - テンプレートを使用しない簡単な方法
* [PowerShell を使用して Application Insights のアラートを設定する](powershell-alerts.md)
* [Web テストを作成する](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Azure Diagnostics を Application Insights に送信する](powershell-azure-diagnostics.md)
* [GitHub から Azure にデプロイする](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [リリースの注釈を作成する](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)
