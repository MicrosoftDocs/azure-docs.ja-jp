---
title: "Azure 監視 REST API のチュートリアル | Microsoft Docs"
description: "要求を認証し、Azure Monitor REST API を使用して使用可能なメトリック定義およびメトリックの値を取得する方法を説明します。"
author: mcollier
manager: 
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 565e6a88-3131-4a48-8b82-3effc9a3d5c6
ms.service: monitoring-and-diagnostics
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.search.region: 
ms.search.scope: 
ms.search.validFrom: 
ms.dyn365.ops.version: 
ms.topic: article
ms.date: 09/18/2017
ms.author: mcollier
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ab522b444c234e1159acfea1780bae1801c4d047
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure 監視 REST API のチュートリアル
この記事では、コードが [Microsoft Azure Monitor REST API リファレンス](https://msdn.microsoft.com/library/azure/dn931943.aspx)を使用できるように、認証を実行する方法について説明します。         

Azure Monitor API では、使用可能な既定のメトリック定義、粒度、およびメトリック値をプログラムによって取得できます。 データは、Azure SQL Database、Azure Cosmos DB、Azure Data Lake など、別のデータ ストアに保存し、 そこで、必要に応じて追加の分析を実行できます。

Monitor API では、さまざまなメトリック データ ポイントを処理するだけでなく、アラート規則の一覧表示、アクティビティ ログの表示などを行うこともできます。 使用可能な操作の一覧については、 [Microsoft Azure Monitor REST API リファレンス](https://msdn.microsoft.com/library/azure/dn931943.aspx)を参照してください。

## <a name="authenticating-azure-monitor-requests"></a>Azure Monitor 要求の認証
まず、要求を認証します。

Azure Monitor API に対して実行されるすべてのタスクが、Azure Resource Manager 認証モデルを使用します。 したがって、すべての要求を Azure Active Directory (Azure AD) で認証する必要があります。 クライアント アプリケーションを認証する 1 つの方法が、Azure AD サービス プリンシパルを作成し、認証 (JWT) トークンを取得することです。 次のサンプル スクリプトは、PowerShell を使用して、Azure AD サービス プリンシパルを作成しています。 さらに詳細なチュートリアルについては、「 [リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-password)」のドキュメントを参照してください。 [Azure ポータルを使用してサービス プリンシパルを作成](../azure-resource-manager/resource-group-create-service-principal-portal.md)することもできます。

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Azure Monitor API を照会するには、クライアント アプリケーションは、前に作成したサービス プリンシパルを使用して認証する必要があります。 次の PowerShell スクリプトの例は、[Active Directory 認証ライブラリ](../active-directory/active-directory-authentication-libraries.md) (ADAL) を使用して JWT 認証トークンを取得する 1 つの方法を示しています。 JWT トークンは、要求の HTTP 承認パラメーターの一部として Azure Monitor REST API に渡されます。

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

認証後、Azure Monitor REST API に対してクエリを実行できます。 便利なクエリは次の 2 つです。

1. リソースのメトリック定義を一覧表示する
2. メトリック値を取得する

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>メトリック定義の取得 (多次元 API)

[Azure Monitor メトリック定義 REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metricdefinitions) を使用すると、サービスで使用できるメトリックの一覧にアクセスできます。

**メソッド**: GET

**要求 URI**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

たとえば、Azure Storage アカウントのメトリック定義を取得する要求は次のようになります。

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2017-05-01-preview"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```
> [!NOTE]
> 多次元 Azure Monitor メトリック REST API を使用してメトリック定義を取得するには、"2017-05-01-preview" API バージョンを使用します。
>
>

結果として得られる JSON 応答本文は次の例のようになります (2 番目のメトリックがディメンションを持つことに注意してください)。

```JSON
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P30D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P30D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P30D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P30D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>ディメンションの値の取得 (多次元 API)
使用可能なメトリック定義が判明した時点で、一部のメトリックにディメンションがある場合があります。 メトリックのクエリを実行する前に、ディメンションの値の範囲を調べます。 これらのディメンション値に基づいて、メトリックのクエリの実行中にメトリックをフィルター処理するか、分割するかを選択できます。 任意のフィルター処理要求に対してメトリックの名前 "value" ("localizedValue" ではありません) を使用します。たとえば、"CpuTime" と "Requests" メトリック データ ポイントを取得します。 フィルターが指定されていない場合は、既定のメトリックが返されます。

> [!NOTE]
> Azure Monitor REST API を使用してディメンション値を取得するには、"2017-05-01-preview" API バージョンを使用します。
>
>

**メソッド**: GET

**要求 URI**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metric=*{metric}*&timespan=*{starttime/endtime}*&$filter=*{filter}*&resultType=metadata&api-version=*{apiVersion}*

たとえば、指定の期間中に 'トランザクション' メトリックの潜在的な値 'API 名ディメンション' のリストを取得する場合、要求は次のようになります。

```PowerShell
$filter = "APIName eq '*'"
$request = "https://management.azure.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metrics?metric=Transactions&timespan=2017-09-01T00:00:00Z/2017-09-10T00:00:00Z&resultType=metadata&$filter=${filter}&api-version=2017-05-01-preview"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```
結果として得られる JSON 応答本文は次の例のようになります。

```JSON
{
  "timespan": "2017-09-01T00:00:00Z/2017-09-10T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "PutPage"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "Unknown"
            }
          ]
        },
        ...
      ]    
    }
  ]
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>メトリックの値の取得 (多次元 API)
使用可能なメトリック定義と考えられるディメンションの値がわかったら、関連するメトリック値を取得できます。 すべてのフィルター処理の要求にメトリックの名前 'value' ('localizedValue' ではありません) を使用します。 ディメンション フィルターが指定されていない場合、ロール アップされた集計メトリックが返されます。

> [!NOTE]
> Azure Monitor REST API を使用して多次元のメトリック値を取得するには、"2017-05-01-preview" API バージョンを使用します。
>
>

**メソッド**: GET

**要求 URI**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metric=*{metric}*&timespan=*{starttime/endtime}*&$filter=*{filter}*&interval=*{timeGrain}*&aggregation=*{aggreation}*&api-version=*{apiVersion}*

たとえば、API 名 'GetBlobProperties' へのすべてのトランザクションに対して、5 分間にストレージ メトリック 'Transactions' のメトリック値を取得する要求は次のようになります。

```PowerShell
$filter = "APIName eq 'GetBlobProperties'"
$request = "https://management.azure.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metrics?metric=Transactions&timespan=2017-09-19T02:00:00Z/2017-09-19T02:05:00Z&$filter=${filter}&interval=PT1M&aggregation=Count&api-version=2017-05-01-preview"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```
結果として得られる JSON 応答本文は次の例のようになります。

```JSON
{
  "cost": 0,
  "timespan": "2017-09-19T02:00:00Z/2017-09-19T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "count": 2.0
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "count": 1.0
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "count": 3.0
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "count": 7.0
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "count": 2.0
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="retrieve-metric-definitions"></a>メトリック定義の取得
[Azure Monitor メトリック定義 REST API](https://msdn.microsoft.com/library/mt743621.aspx) を使用すると、サービスで使用できるメトリックの一覧にアクセスできます。

**メソッド**: GET

**要求 URI**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

たとえば、Azure Logic App のメトリック定義を取得する要求は次のようになります。

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contostweets-metricdef-results.json" `
                  -Verbose
```
> [!NOTE]
> Azure Monitor REST API を使用してメトリック定義を取得するには、"2016-03-01" API バージョンを使用します。
>
>

結果として得られる JSON 応答本文は次の例のようになります。
```JSON
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

詳細については、「 [List the metric definitions for a resource in Azure Monitor REST API (Azure Monitor REST API でリソースのメトリック定義を一覧表示)](https://msdn.microsoft.com/library/azure/mt743621.aspx) 」を参照してください。

## <a name="retrieve-metric-values"></a>メトリック値の取得
使用可能なメトリック定義がわかったら、関連するメトリック値を取得できます。 任意のフィルター処理要求に対してメトリックの名前 "value" ("localizedValue" ではありません) を使用します。たとえば、"CpuTime" と "Requests" メトリック データ ポイントを取得します。 フィルターが指定されていない場合は、既定のメトリックが返されます。

> [!NOTE]
> Azure Monitor REST API を使用してメトリック値を取得するには、"2016-09-01" API バージョンを使用します。
>
>

**メソッド**: GET

**要求 URI**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&api-version=*{apiVersion}*

たとえば、特定の時間範囲と 1 時間の時間グレインに対して RunsSucceeded メトリック データ ポイントを取得する場合、要求は次のようになります。

```PowerShell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-results.json" `
    -Verbose
```

結果として得られる JSON 応答本文は次の例のようになります。

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

複数のデータまたは集計ポイントを取得するには、次の例に示すように、メトリック定義の名前と集計の種類をフィルターに追加します。

```PowerShell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-multiple-results.json" `
    -Verbose
```
結果として得られる JSON 応答本文は次の例のようになります。

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>ARMClient の使用
その他のアプローチは、お使いの Windows マシンで [ARMClient](https://github.com/projectkudu/armclient) を使用することです。 ARMClient では、Azure AD 認証 (および結果の JWT トークン) が自動的に処理されます。 次の手順は、ARMClient を使用してメトリック データを取得する方法を簡単に示しています。

1. [Chocolatey](https://chocolatey.org/) と [ARMClient](https://github.com/projectkudu/armclient) をインストールします。
2. ターミナル ウィンドウで、「 *armclient.exe login*」と入力します。 これにより Azure にログインするよう求められます。
3. 「*armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*」と入力します
4. 「*armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*」と入力します

たとえば、特定の Logic App のメトリック定義を取得するためには、次のコマンドを実行します。
```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```


## <a name="retrieve-the-resource-id"></a>リソース ID の取得
REST API を使用すると、使用可能なメトリック定義、粒度、および関連する値について理解しやすくなります。 この情報は、 [Azure 管理ライブラリ](https://msdn.microsoft.com/library/azure/mt417623.aspx)を使用するときに役立ちます。

前のコードの場合、使用するリソース ID は、目的の Azure リソースへの完全パスです。 たとえば、Azure Web アプリに対してクエリを実行する場合、リソース ID は次のようになります。

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

次の一覧は、さまざまな Azure リソースのリソース ID 形式の例をいくつか示しています。

* **IoT Hub** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Elastic SQL Pool** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **SQL Database (v12)** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{server-name}*/databases/*{database-name}*
* **Service Bus** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **仮想マシン スケール セット** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **VM** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Event Hubs** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

他にもリソース ID を取得する方法はあります。Azure リソース エクスプローラーを使用する、Azure ポータル、PowerShell、Azure CLI で目的のリソースを表示する、などの方法です。

### <a name="azure-resource-explorer"></a>Azure リソース エクスプローラー
目的のリソースのリソース ID を見つける 1 つの方法として便利なのは、 [Azure リソース エクスプローラー](https://resources.azure.com) ツールを使用することです。 目的のリソースに移動すると、次のスクリーンショットのように ID が表示されます。

![Alt "Azure リソース エクスプローラー"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure ポータル
Azure ポータルからリソース ID を取得することもできます。 これを行うには、目的のリソースに移動し、[プロパティ] を選択します。 リソース ID は、次のスクリーンショットのように [プロパティ] セクションに表示されます。

![Alt "Azure ポータルの [プロパティ] ブレードに表示されているリソース ID"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
リソース ID は、Azure PowerShell コマンドレットを使用して取得することもできます。 たとえば Azure Logic アプリのリソース ID を取得するには、次の例のように、Get-AzureLogicApp コマンドレットを実行します。

```PowerShell
Get-AzureRmLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

結果は次の例のようになります。
```
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```


### <a name="azure-cli"></a>Azure CLI
Azure CLI を使用して Azure Storage アカウントのリソース ID を取得するには、次の例で示すように、'az storage account show' コマンドを実行します。

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

結果は次の例のようになります。
```JSON
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Azure Logic Apps はまだ Azure CLI 経由では利用できないため、上記の例では Azure Storage アカウントが示されています。
>
>

## <a name="retrieve-activity-log-data"></a>アクティビティ ログ データの取得
メトリック定義と関連する値だけでなく、Azure Monitor REST API を使用して Azure リソースに関連するその他の興味深い分析情報を取得することもできます。 たとえば、 [アクティビティ ログ](https://msdn.microsoft.com/library/azure/dn931934.aspx) データにクエリを実行できます。 次の例は、Azure Monitor REST API を使用して、Azure サブスクリプションの特定の日付範囲にあるアクティビティ ログ データにクエリを実行しています。

```PowerShell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>次のステップ
* [監視の概要](monitoring-overview.md)に関するページを確認します。
* [Azure Monitor のサポートされるメトリック](monitoring-supported-metrics.md)を表示します。
* [Microsoft Azure Monitor REST API リファレンス](https://msdn.microsoft.com/library/azure/dn931943.aspx)を確認します。
* [Azure 管理ライブラリ](https://msdn.microsoft.com/library/azure/mt417623.aspx)を確認します。

