---
title: "Azure 監視 REST API のチュートリアル | Microsoft Docs"
description: "要求を認証し、Azure 監視 REST API を使用する方法。"
author: mcollier
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 565e6a88-3131-4a48-8b82-3effc9a3d5c6
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: mcollier
translationtype: Human Translation
ms.sourcegitcommit: 1fe845d442c7010580d4592f205e92e8ef70e34a
ms.openlocfilehash: 6d66a8fa6eac5bc0ecdddc12b67697045556bf46


---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure 監視 REST API のチュートリアル
この記事では、コードが [Microsoft Azure Monitor REST API リファレンス](https://msdn.microsoft.com/library/azure/dn931943.aspx)を使用できるように、認証を実行する方法について説明します。         

Azure Monitor API では、使用可能な既定のメトリック定義 (CPU 時間、要求などのメトリックの種類)、粒度、およびメトリック値をプログラムによって取得できます。 取得したデータは、Azure SQL Database、DocumentDB、Azure Data Lake など、別のデータ ストアに保存し、 そこで、必要に応じて追加の分析を実行できます。

Monitor API では、さまざまなメトリック データ ポイントを処理するだけでなく、この記事で示すように、アラート ルールの一覧表示、アクティビティ ログの表示などを行うこともできます。 使用可能な操作の一覧については、 [Microsoft Azure Monitor REST API リファレンス](https://msdn.microsoft.com/library/azure/dn931943.aspx)を参照してください。

## <a name="authenticating-azure-monitor-requests"></a>Azure Monitor 要求の認証
まず、要求を認証します。

Azure Monitor API に対して実行されるすべてのタスクが、Azure Resource Manager 認証モデルを使用します。 したがって、すべての要求を Azure Active Directory (Azure AD) で認証する必要があります。 クライアント アプリケーションを認証する 1 つの方法が、Azure AD サービス プリンシパルを作成し、認証 (JWT) トークンを取得することです。 次のサンプル スクリプトは、PowerShell を使用して、Azure AD サービス プリンシパルを作成しています。 さらに詳細なチュートリアルについては、「 [リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-password)」のドキュメントを参照してください。 [Azure ポータルを使用してサービス プリンシパルを作成](../azure-resource-manager/resource-group-create-service-principal-portal.md)することもできます。

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

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

Azure Monitor API を照会するには、クライアント アプリケーションは、前に作成したサービス プリンシパルを使用して認証する必要があります。 次の PowerShell スクリプトの例は、 [Active Directory 認証ライブラリ](../active-directory/active-directory-authentication-libraries.md) (ADAL) を使用して JWT 認証トークンを取得する 1 つの方法を示しています。 JWT トークンは、要求の HTTP 承認パラメーターの一部として Azure Monitor REST API に渡されます。

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.windows.net/${tenantId}"

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

認証の設定手順が完了すると、Azure Monitor REST API に対してクエリを実行できます。 便利なクエリは次の 2 つです。

1. リソースのメトリック定義を一覧表示する
2. メトリック値を取得する

## <a name="retrieve-metric-definitions"></a>メトリック定義の取得
> [!NOTE]
> Azure Monitor REST API を使用してメトリック定義を取得するには、"2016-03-01" API バージョンを使用します。
>
>

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
Azure ロジック アプリの場合、メトリック定義は次のスクリーンショットのように表示されます。

![Alt "メトリック定義応答の JSON ビュー"](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

詳細については、「 [List the metric definitions for a resource in Azure Monitor REST API (Azure Monitor REST API でリソースのメトリック定義を一覧表示)](https://msdn.microsoft.com/library/azure/mt743621.aspx) 」を参照してください。

## <a name="retrieve-metric-values"></a>メトリック値の取得
使用可能なメトリック定義がわかったら、関連するメトリック値を取得できます。 任意のフィルター処理要求に対してメトリックの名前 "value" ("localizedValue" ではありません) を使用します。たとえば、"CpuTime" と "Requests" メトリック データ ポイントを取得します。 フィルターが指定されていない場合は、既定のメトリックが返されます。

> [!NOTE]
> Azure Monitor REST API を使用してメトリック値を取得するには、"2016-06-01" API バージョンを使用します。
>
>

**メソッド**: GET

**要求 URI**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&api-version=*{apiVersion}*

たとえば、特定の時間範囲と 1 時間の時間グレインに対して RunsSucceeded メトリック データ ポイントを取得する場合、要求は次のようになります。

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

結果は、次のスクリーンショットの例のように表示されます。

![Alt "平均応答時間のメトリック値を表示する JSON 応答"](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

複数のデータまたは集計ポイントを取得するには、次の例に示すように、メトリック定義の名前と集計の種類をフィルターに追加します。

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### <a name="use-armclient"></a>ARMClient の使用
PowerShell を使用する (前述) 代わりに、Windwos コンピューターで [ARMClient](https://github.com/projectkudu/ARMClient) を使用することもできます。 ARMClient では、Azure AD 認証 (および結果の JWT トークン) が自動的に処理されます。 次の手順は、ARMClient を使用してメトリック データを取得する方法を簡単に示しています。

1. [Chocolatey](https://chocolatey.org/) と [ARMClient](https://github.com/projectkudu/ARMClient) をインストールします。
2. ターミナル ウィンドウで、「 *armclient.exe login*」と入力します。 これにより Azure にログインするよう求められます。
3. 「*armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*」と入力します
4. 「*armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-06-01*」と入力します

![Alt "ARMClient を使用して Azure 監視 REST API を操作"](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)

## <a name="retrieve-the-resource-id"></a>リソース ID の取得
REST API を使用すると、使用可能なメトリック定義、粒度、および関連する値について理解しやすくなります。 この情報は、 [Azure 管理ライブラリ](https://msdn.microsoft.com/library/azure/mt417623.aspx)を使用するときに役立ちます。

前のコードの場合、使用するリソース ID は、目的の Azure リソースへの完全パスです。 たとえば、Azure Web アプリに対してクエリを実行する場合、リソース ID は次のようになります。

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

次の一覧は、さまざまな Azure リソースのリソース ID 形式の例をいくつか示しています。

* **IoT Hub** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Elastic SQL Pool** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **SQL Database (v12)** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{server-name}*/databases/*{database-name}*
* **Service Bus** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **VM スケール セット** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **VM** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Event Hubs** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

他にもリソース ID を取得する方法はあります。Azure リソース エクスプローラーを使用する、Azure ポータル、PowerShell、Azure CLI で目的のリソースを表示する、などの方法です。

### <a name="azure-resource-explorer"></a>Azure リソース エクスプローラー
目的のリソースのリソース ID を見つける 1 つの方法として便利なのは、 [Azure リソース エクスプローラー](https://resources.azure.com) ツールを使用することです。 目的のリソースに移動すると、次のスクリーンショットのように ID が表示されます。

![Alt "Azure リソース エクスプローラー"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure ポータル
Azure ポータルからリソース ID を取得することもできます。 これを行うには、目的のリソースに移動し、[プロパティ] を選択します。 リソース ID は、次のスクリーンショットのように [プロパティ] ブレードに表示されます。

![Alt "Azure ポータルの [プロパティ] ブレードに表示されているリソース ID"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
リソース ID は、Azure PowerShell コマンドレットを使用して取得することもできます。 たとえば Azure Web アプリのリソース ID を取得するには、次のスクリーンショットのように、Get-AzureRmWebApp コマンドレットを実行します。

![Alt "PowerShell 取得したリソース ID"](./media/monitoring-rest-api-walkthrough/resourceid_powershell.png)

### <a name="azure-cli"></a>Azure CLI
Azure CLI を使用してリソース ID を取得するには、次のスクリーンショットのように、"-json" オプションを指定して "azure webapp show" コマンドを実行します。

![Alt "PowerShell 取得したリソース ID"](./media/monitoring-rest-api-walkthrough/resourceid_azurecli.png)

## <a name="retrieve-activity-log-data"></a>アクティビティ ログ データの取得
メトリック定義と関連する値を処理するだけでなく、Azure リソースに関連するその他の興味深い洞察を取得することもできです。 たとえば、 [アクティビティ ログ](https://msdn.microsoft.com/library/azure/dn931934.aspx) データにクエリを実行できます。 次の例は、Azure Monitor REST API を使用して、Azure サブスクリプションの特定の日付範囲にあるアクティビティ ログ データにクエリを実行しています。

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## <a name="next-steps"></a>次のステップ
* [監視の概要](monitoring-overview.md)に関するページを確認します。
* [Azure Monitor のサポートされるメトリック](monitoring-supported-metrics.md)を表示します。
* [Microsoft Azure Monitor REST API リファレンス](https://msdn.microsoft.com/library/azure/dn931943.aspx)を確認します。
* [Azure 管理ライブラリ](https://msdn.microsoft.com/library/azure/mt417623.aspx)を確認します。



<!--HONumber=Feb17_HO3-->


