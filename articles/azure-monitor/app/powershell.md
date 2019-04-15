---
title: PowerShell での Azure Application Insights の自動化 | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して、PowerShell でのリソース、アラート、および可用性テストの作成を自動化します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: mbullwin
ms.openlocfilehash: 1c360d60a429f137c4b74ad4afe8ae9bba895b7d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883413"
---
#  <a name="create-application-insights-resources-using-powershell"></a>PowerShell を使用した Application Insights リソースの作成

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

この記事では、Azure Resource 管理を使用して [Application Insights](../../azure-monitor/app/app-insights-overview.md) リソースの作成と更新を自動化する方法を説明します。 たとえば、ビルド プロセスの一部として実行します。 基本的な Application Insights リソースと共に、[可用性 Web テスト](../../azure-monitor/app/monitor-web-app-availability.md)の作成、[アラート](../../azure-monitor/app/alerts.md)の設定、[価格の詳細](pricing.md)の設定、その他の Azure リソースの作成を行うことができます。

これらのリソースを作成する際に重要となるのが、[Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md) の JSON テンプレートです。 簡単に言うと、既存のリソースの JSON 定義をダウンロードし、名前などの特定の値をパラメーター化して、新しいリソースを作成するときに、テンプレートを実行するという手順になります。 いくつかのリソースをまとめてパッケージ化することで、すべてを一度に作成できます (例、可用性テスト、アラート、および連続エクスポート用の記憶域を使用したアプリの監視)。 パラメーター化の一部には、いくつか細かい点があります。それについては、以降で説明します。

## <a name="one-time-setup"></a>1 回限りのセットアップ
以前に Azure サブスクリプションで PowerShell を使用したことがない場合

スクリプトを実行するコンピューターに Azure PowerShell モジュールをインストールします。

1. [Microsoft Web Platform Installer (v5 以上)](https://www.microsoft.com/web/downloads/platform.aspx)をインストールします。
2. このインストーラーを使用して Microsoft Azure PowerShell をインストールします。

## <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの作成
新しい .json ファイルを作成します (この例では、 `template1.json` と呼びます)。 ファイルに、次のコンテンツをコピーします。

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the application name."
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
                    "description": "Enter the application type."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "East US",
                "allowedValues": [
                    "South Central US",
                    "West Europe",
                    "East US",
                    "North Europe"
                ],
                "metadata": {
                    "description": "Enter the application location."
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
                    "description": "1 = Basic, 2 = Enterprise"
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



## <a name="create-application-insights-resources"></a>Application Insights リソースの作成
1. PowerShell で Azure にサインインします。
   
    `Connect-AzAccount`
2. 次のようにコマンドを実行します。
   
    ```PS
   
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName`  は、新しいリソースを作成するグループです。
   * `-TemplateFile`  は、カスタム パラメーターの前に置く必要があります。
   * `-appName` 作成するリソースの名前です。

その他のパラメーターを追加することもできます。テンプレートのパラメーター セクションに説明があります。

## <a name="to-get-the-instrumentation-key"></a>インストルメンテーション キーを取得するには
アプリケーション リソースを作成したら、インストルメンテーション キーが必要になります。 

```PS
    $resource = Find-AzResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>料金プランの設定

[料金プラン](pricing.md)を設定できます。

上記のテンプレートを使用して、Enterprise 料金プランを使ったアプリ リソースを作成するには、次のコマンドを実行します。

```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|プラン|
|---|---|
|1|Basic|
|2|Enterprise|

* 既定の Basic 料金プランのみを使用する場合は、テンプレートから CurrentBillingFeatures リソースを除外することができます。
* コンポーネント リソースが作成された後に料金プランを変更する場合は、"microsoft.insights/components" リソースを除外するテンプレートを使用することができます。 また、課金リソースから `dependsOn` ノードを除外します。 

更新された料金プランを確認するには、ブラウザーで **[使用量と推定コスト] ページ** ブレードを確認します。 **ブラウザーを最新表示**し、必ず、最新の状態を表示してください。



## <a name="add-a-metric-alert"></a>メトリック アラートの追加

アプリ リソースと同時にメトリック アラートを設定するには、次のように、テンプレート ファイルにコードをマージします。

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

テンプレートを呼び出す際には、必要に応じて、次のパラメーターを追加できます。

    `-responseTime 2`

もちろん、その他のフィールドをパラメーター化することもできます。 

その他のアラート ルールのタイプ名と詳しい構成を確認するには、ルールを手動で作成した後、[Azure Resource Manager](https://resources.azure.com/) でそれらを調べます。 


## <a name="add-an-availability-test"></a>可用性テストの追加

ここでは、(単一ページをテストするための) ping テストの例を示します。  

可用性テストは **2 つの部分で構成されます**。テスト自体と、エラーを通知するアラートです。

アプリを作成するテンプレート ファイルに、次のコードをマージします。

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

他のテスト場所のコードを見つけたり、より複雑な Web テストの作成を自動化するには、サンプルを手動で作成した後、[Azure Resource Manager](https://resources.azure.com/) からコードをパラメーター化します。

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
4. webtests と alertrules セクションを開き、個々の項目の JSON をテンプレートにコピーします。 (webtests または alertrules ノードからコピーせず、その下の項目に移動してください。)
   
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
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Guid と Id を削除します。 |

### <a name="set-dependencies-between-the-resources"></a>リソース間の依存関係の設定
Azure では、厳密な順序でリソースを設定する必要があります。 次の設定を開始する前に、確実に 1 つの設定を完了するために、依存関係の行を追加します。

* 可用性テスト リソース
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* 可用性テストのアラート リソース
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>次の手順
自動化に関するその他の記事:

* [Application Insights リソースを作成するための PowerShell スクリプト](powershell-script-create-resource.md) - テンプレートを使用しない簡単な方法
* [アラートを設定する](powershell-alerts.md)
* [Web テストを作成する](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Azure 診断を Application Insights に送信する](powershell-azure-diagnostics.md)
* [GitHub から Azure にデプロイする](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [リリースの注釈を作成する](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

