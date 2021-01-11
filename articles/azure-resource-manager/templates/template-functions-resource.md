---
title: テンプレート関数 - リソース
description: Azure Resource Manager テンプレートで、リソースに関する値を取得するために使用する関数について説明します。
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 7f485d258074959c4a0a17449c65c38fa9648502
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661403"
---
# <a name="resource-functions-for-arm-templates"></a>ARM テンプレート用のリソース関数

Resource Manager では、Azure Resource Manager (ARM) テンプレートでリソースの値を取得するために、次の関数が提供されています。

* [extensionResourceId](#extensionresourceid)
* [list*](#list)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [subscription](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

パラメーター、変数、現在のデプロイから値を取得する方法については、「 [デプロイの値関数](template-functions-deployment.md)」を参照してください。

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

[拡張リソース](../management/extension-resource-types.md)のリソース ID を返します。これは、その機能に追加する別のリソースに適用されるリソースの種類です。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| resourceId |はい |string |拡張リソースが適用されるリソースのリソース ID。 |
| resourceType |はい |string |リソース プロバイダーの名前空間を含むリソースの種類。 |
| resourceName1 |はい |string |リソースの名前。 |
| resourceName2 |いいえ |string |次のリソース名セグメント (必要な場合)。 |

さらに他のセグメントがリソースの種類に含まれる場合は、続けてリソース名をパラメーターとして追加します。

### <a name="return-value"></a>戻り値

この関数から返されるリソース ID の基本形式は次のとおりです。

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

スコープ セグメントは、拡張されるリソースによって変わります。

拡張リソースが**リソース**に適用される場合、リソース ID は次の形式で返されます。

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

拡張リソースが**リソース グループ**に適用される場合、形式は次のようになります。

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

拡張リソースが**サブスクリプション**に適用される場合、形式は次のようになります。

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

拡張リソースが**管理グループ**に適用される場合、形式は次のようになります。

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>extensionResourceId の例

次の例では、リソース グループ ロックのリソース ID が返されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>list*

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

この関数の構文はリスト操作の名前によって異なります。 実装ごとに、リスト操作をサポートするリソースの種類の値が返されます。 操作名は `list` で始める必要があります。 一般的な使用法として、`listKeys`、`listKeyValue`、`listSecrets` などがあります。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| resourceName または resourceIdentifier |はい |string |リソースの一意識別子です。 |
| apiVersion |はい |string |リソースのランタイム状態の API バージョン。 通常、**yyyy-mm-dd** の形式。 |
| functionValues |いいえ |object | 関数の値を持つオブジェクト。 このオブジェクトは、ストレージ アカウントの **listAccountSas** など、パラメーター値を持つオブジェクトの受信をサポートする関数に対してのみ指定します。 関数値を渡す例をこの記事で紹介します。 |

### <a name="valid-uses"></a>有効な使用方法

リスト関数は、リソース定義のプロパティで使用できます。 テンプレートの outputs セクションでは、機密情報を公開するリスト関数を使用しないでください。 出力値はデプロイ履歴に格納されるため、悪意のあるユーザーによって取得される可能性があります。

[プロパティの反復処理](copy-properties.md)で使用する場合には、式がリソース プロパティに割り当てられるため、`input` に対して list 関数を使用できます。 これらを `count` と一緒に使用することはできません。カウントは、list 関数が解決される前に決定される必要があるためです。

### <a name="implementations"></a>実装

list* の使用例を次の表にまとめています。

| リソースの種類 | 関数名 |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration | [ListKeyValue](/rest/api/appconfiguration/configurationstores/listkeyvalue) |
| Microsoft.AppConfiguration/configurationStores | ListKeys |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.BotService/botServices/channels | [listChannelWithKeys](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/botservice/resource-manager/Microsoft.BotService/stable/2020-06-02/botservice.json#L553) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/accounts/shares | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listKeys](/rest/api/eventgrid/version2020-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/version2020-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Databases | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/users | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetitions | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listKeys](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [listKeys](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [list](/rest/api/loganalytics/workspaces/list) |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

どのリソースの種類にリスト操作が含まれているのかを判断するには、次のオプションを使用できます。

* リソース プロバイダーの [REST API の操作](/rest/api/)に関するページを参照して、リスト操作を検索します。 たとえば、ストレージ アカウントには [listKeys 操作](/rest/api/storagerp/storageaccounts)があります。
* [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell コマンドレットを使用します。 次の例では、ストレージ アカウントのすべてのリスト操作が取得されます。

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* 次の Azure CLI コマンドを使って、リスト操作のみをフィルター処理します。

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>戻り値

返されるオブジェクトは、使用するリスト関数によって異なります。 たとえば、ストレージ アカウントに対して listKeys は次の形式を返します。

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

他のリスト関数の戻り値の形式はさまざまです。 関数の形式を確認するには、テンプレートの例に示すように、outputs セクションにその関数を含めてください。

### <a name="remarks"></a>解説

リソース名または [resourceId 関数](#resourceid)を使用して、リソースを指定します。 参照されているリソースをデプロイするのと同じテンプレートでリスト関数を使うときは、リソース名を使ってください。

**list** 関数を条件付きでデプロイされるリソースで使用した場合、この関数はリソースがデプロイされていなくても評価されます。 **list** 関数で存在しないリソースを参照した場合、エラーが返されます。 リソースのデプロイ中にのみこの関数が評価されるようにするには、**if** 関数を使用します。 if と list を条件付きでデプロイされるリソースで使用するサンプル テンプレートについては、[if 関数](template-functions-logical.md#if)に関する説明を参照してください。

### <a name="list-example"></a>リストの例

次の例では、[デプロイ スクリプト](deployment-script-template.md)の値を設定するときに listKeys を使用します。

```json
"storageAccountSettings": {
    "storageAccountName": "[variables('storageAccountName')]",
    "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
}
```

次の例は、パラメーターを受け取るリスト関数を示しています。 この場合の関数は **listAccountSas** です。 有効期限のオブジェクトを渡します。 有効期限は将来の日付にする必要があります。

```json
"parameters": {
    "accountSasProperties": {
        "type": "object",
        "defaultValue": {
            "signedServices": "b",
            "signedPermission": "r",
            "signedExpiry": "2020-08-20T11:00:00Z",
            "signedResourceTypes": "s"
        }
    }
},
...
"sasToken": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties')).accountSasToken]"
```

listKeyValue の例については、「[クイックスタート:App Configuration と Resource Manager テンプレートを使用した VM の自動デプロイ](../../azure-app-configuration/quickstart-resource-manager.md#deploy-vm-using-stored-key-values)」を参照してください。

## <a name="providers"></a>providers

`providers(providerNamespace, [resourceType])`

リソース プロバイダーとサポートされているそのリソースの種類に関する情報を返します。 リソースの種類を指定しない場合、関数はリソース プロバイダーでサポートされているすべての種類を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| providerNamespace |はい |string |プロバイダーの名前空間 |
| resourceType |いいえ |string |指定した名前空間内にあるリソースの種類。 |

### <a name="return-value"></a>戻り値

サポートされている各種類は、次の形式で返されます。

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

戻り値の配列の順序は保証されません。

### <a name="providers-example"></a>プロバイダーの例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json)は、provider 関数の使用方法を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

**Microsoft.Web** リソース プロバイダーでリソースの種類が **sites** の場合、前の例では、次の形式のオブジェクトが返されます。

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>reference

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

リソースのランタイム状態を表すオブジェクトを返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| resourceName または resourceIdentifier |はい |string |名前またはリソースの一意の識別子。 現在のテンプレート内のリソースを参照する場合は、パラメーターとしてリソース名のみを指定します。 以前にデプロイされたリソースを参照する場合、またはリソースの名前があいまいな場合は、リソース ID を指定します。 |
| apiVersion |いいえ |string |指定したリソースの API バージョンです。 **このパラメーターは、同じテンプレート内でリソースがプロビジョニングされない場合に必要です。** 通常、**yyyy-mm-dd** の形式。 リソースに有効な API のバージョンについては、[テンプレート リファレンス](/azure/templates/)を参照してください。 |
| 'Full' |いいえ |string |完全なリソース オブジェクトを返すかどうかを指定する値。 `'Full'` を指定しない場合、リソースのプロパティ オブジェクトのみが返されます。 完全なオブジェクトには、リソース ID や場所などの値が含まれます。 |

### <a name="return-value"></a>戻り値

あらゆるリソースの種類で、reference 関数のさまざまなプロパティが返されます。 この関数は、定義済みの単一の形式を返しません。 また、戻り値は、`'Full'` 引数の値によって異なります。 あるリソースの種類のプロパティを確認するには、この例に示すように、outputs セクションでオブジェクトを返します。

### <a name="remarks"></a>解説

reference 関数は、以前にデプロイされたリソースまたは現在のテンプレートにデプロイされたリソースのいずれかのランタイム状態を取得します。 この記事では、両方のシナリオの例を示します。

通常、**reference** 関数は、BLOB エンドポイント URI や完全修飾ドメイン名などのオブジェクトから特定の値を返すために使用します。

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

`'Full'` は、プロパティのスキーマに含まれないリソースの値が必要なときに使用します。 たとえば、キー コンテナーのアクセス ポリシーを設定するために、仮想マシンの ID プロパティを取得する場合です。

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>有効な使用方法

reference 関数は、リソース定義のプロパティと、テンプレートまたはデプロイの出力セクションでのみ使用できます。 [プロパティの反復処理](copy-properties.md)で使用する場合には、式がリソース プロパティに割り当てられるため、`input` に対して reference 関数を使用できます。

reference 関数を使用してコピー ループの `count` プロパティの値を設定することはできません。 ループ内のその他のプロパティの設定には使用できます。 count プロパティの参照はブロックされます。このプロパティは reference 関数が解決される前に決定される必要があるためです。

入れ子になったテンプレートの outputs セクションで reference 関数または list* 関数を使用するには、[内部スコープ](linked-templates.md#expression-evaluation-scope-in-nested-templates)評価を使用するか、入れ子になったテンプレートの代わりにリンクを使用するように ```expressionEvaluationOptions``` を設定する必要があります。

**reference** 関数を条件付きでデプロイされるリソースで使用した場合、この関数はリソースがデプロイされていなくても評価されます。  **reference** 関数で存在しないリソースを参照した場合、エラーが返されます。 リソースのデプロイ中にのみこの関数が評価されるようにするには、**if** 関数を使用します。 条件付きでデプロイされるリソースで if と reference を使用するサンプル テンプレートについては、[if 関数](template-functions-logical.md#if)に関する説明を参照してください。

### <a name="implicit-dependency"></a>暗黙の依存関係

参照されているリソースを同じテンプレート内でプロビジョニングし、(リソース ID ではなく) 名前でリソースを参照する場合は、reference 関数を使用することにより、あるリソースが他のリソースに依存することを暗黙的に宣言します。 dependsOn プロパティを一緒に使用する必要はありません。 参照先のリソースがデプロイを完了するまで、関数は評価されません。

### <a name="resource-name-or-identifier"></a>リソースの名前または識別子

同じテンプレートでデプロイされるリソースを参照するときは、リソースの名前を指定します。

```json
"value": "[reference(parameters('storageAccountName'))]"
```

同じテンプレートでデプロイされないリソースを参照するときは、リソース ID と `apiVersion` を指定します。

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

参照しているリソースがあいまいにならないようにするには、完全修飾リソース識別子を指定します。

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

リソースに対する完全修飾参照を作成する場合、種類と名前からセグメントを結合する順序は、単に 2 つの連結ではありません。 名前空間の後に、"*種類/名前*" のペアを具体性の低いものから高いものへの順に使用します。

**{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]**

次に例を示します。

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`は正しい`Microsoft.Compute/virtualMachines/extensions/myVM/myExt`は正しくない

リソース ID の作成を簡略化するには、`concat()` 関数ではなく、このドキュメントで説明されている `resourceId()` 関数を使用します。

### <a name="get-managed-identity"></a>マネージド ID を取得する

[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) は、一部のリソースに対して暗黙的に作成される[拡張リソースの種類](../management/extension-resource-types.md)です。 テンプレートにはマネージ ID が明示的に定義されていないため、ID が適用されるリソースを参照する必要があります。 暗黙的に作成された ID を含め、すべてのプロパティを取得するには、`Full` を使用します。

パターンは次のとおりです。

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>, <API-version>, 'Full').Identity.propertyName]"`

たとえば、仮想マシンに適用されるマネージド ID のプリンシパル ID を取得するには、次を使用します。

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

または、仮想マシン スケール セットに適用されるマネージド ID のテナント ID を取得するには、次を使用します。

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>参照の例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json)では、リソースをデプロイし、そのリソースを参照します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": {
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
```

前の例では、2 つのオブジェクトが返されます。 properties オブジェクトの形式は次のとおりです。

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

完全なオブジェクトの形式は次のとおりです。

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json)では、このテンプレートでデプロイされていないストレージ アカウントが参照されます。 このストレージ アカウントは、既に同じサブスクリプション内に存在します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

現在のリソース グループを表すオブジェクトを返します。

### <a name="return-value"></a>戻り値

返されるオブジェクトの形式は次のとおりです。

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

**managedBy** プロパティは、別のサービスによって管理されているリソースを含むリソース グループに対してのみ返されます。 Managed Applications、Databricks、および AKS の場合、プロパティの値は管理しているリソースのリソース ID になります。

### <a name="remarks"></a>解説

`resourceGroup()` 関数は、[サブスクリプション レベルでデプロイ](deploy-to-subscription.md)されたテンプレートで使用できません。 リソース グループにデプロイされているテンプレートでのみ使用できます。 親テンプレートがサブスクリプションにデプロイされていても、リソース グループをターゲットとする[リンク済みまたは入れ子になったテンプレート (内側のスコープを持つ)](linked-templates.md) で `resourceGroup()` 関数を使用することができます。 そのシナリオでは、リンク済みまたは入れ子になったテンプレートはリソース グループ レベルでデプロイされます。 サブスクリプション レベル デプロイでのリソース グループのターゲット設定の詳細については、「[複数のサブスクリプションまたはリソース グループに Azure リソースをデプロイする](cross-scope-deployment.md)」を参照してください。

resourceGroup 関数の一般的な用途では、リソース グループと同じ場所にリソースを作成します。 次の例では、既定のパラメーター値にリソース グループの場所を使用します。

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

resourceGroup 関数を使用して、リソース グループからリソースにタグを適用することもできます。 詳細については、「[リソース グループからタグを適用する](../management/tag-resources.md#apply-tags-from-resource-group)」を参照してください。

入れ子になっているテンプレートを使用して複数のリソース グループにデプロイするとき、resourceGroup 関数を評価するためのスコープを指定できます。 詳細については、「[複数のサブスクリプションまたはリソース グループに Azure リソースをデプロイする](cross-scope-deployment.md)」を参照してください。

### <a name="resource-group-example"></a>リソース グループの例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json)は、リソース グループのプロパティを返します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

前の例では、次の形式のオブジェクトが返されます。

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

リソースの一意の識別子を返します。 リソース名があいまいであるか、同じテンプレート内でプロビジョニングされていないときに、この関数を使用します。 返される ID の形式は、デプロイがリソース グループ、サブスクリプション、管理グループ、またはテナントのスコープで行われるかどうかによって異なります。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| subscriptionId |いいえ |文字列 (GUID 形式) |既定値は、現在のサブスクリプションです。 別のサブスクリプション内のリソースを取得する必要がある場合は、この値を指定します。 リソース グループまたはサブスクリプションのスコープでデプロイする場合にのみ、この値を指定します。 |
| resourceGroupName |いいえ |string |既定値は、現在のリソース グループです。 別のリソース グループ内のリソースを取得する必要がある場合は、この値を指定します。 リソース グループのスコープでデプロイする場合にのみ、この値を指定します。 |
| resourceType |はい |string |リソース プロバイダーの名前空間を含むリソースの種類。 |
| resourceName1 |はい |string |リソースの名前。 |
| resourceName2 |いいえ |string |次のリソース名セグメント (必要な場合)。 |

さらに他のセグメントがリソースの種類に含まれる場合は、続けてリソース名をパラメーターとして追加します。

### <a name="return-value"></a>戻り値

テンプレートがリソース グループのスコープでデプロイされると、リソース ID は次の形式で返されます。

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

[サブスクリプションレベルのデプロイ](deploy-to-subscription.md)で使用する場合、リソース ID は次の形式で返されます。

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

[管理グループレベルのデプロイ](deploy-to-management-group.md)またはテナントレベルのデプロイで使用する場合、リソース ID は次の形式で返されます。

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

ID を他の形式で取得するには、以下を参照してください。

* [extensionResourceId](#extensionresourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

### <a name="remarks"></a>解説

指定するパラメーターの数は、リソースが親であるか子であるか、また、リソースが同じサブスクリプション (またはリソース グループ) にあるかどうかで異なります。

同じサブスクリプションおよび同じリソース グループに存在する親リソースの ID を取得するには、リソースの種類と名前を指定します。

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

子リソースのリソース ID を取得するには、リソースの種類に含まれるセグメント数に注目します。 リソースの種類を構成するセグメントごとにリソース名を指定してください。 セグメントの名前は、その階層部分に存在するリソースと対応関係にあります。

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

同じサブスクリプションで、リソース グループが異なるリソースの ID を取得するには、リソース グループの名前を指定します。

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

サブスクリプションもリソース グループも異なるリソースの ID を取得するには、サブスクリプション ID とリソース グループ名を指定します。

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

代替のリソース グループで、ストレージ アカウントや仮想ネットワークを使用するときには、多くの場合にこの関数を使用する必要があります。 次の例は、外部のリソース グループのリソースを簡単に使用する方法を示しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="resource-id-example"></a>リソース ID の例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json)では、リソース グループ内にあるストレージ アカウントのリソース ID を返します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 種類 | 値 |
| ---- | ---- | ----- |
| sameRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | String | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>subscription

`subscription()`

現在のデプロイのサブスクリプションの詳細を返します。

### <a name="return-value"></a>戻り値

この関数は次の形式を返します。

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>解説

入れ子になっているテンプレートを使用して複数のサブスクリプションにデプロイするとき、サブスクリプション関数を評価するためのスコープを指定できます。 詳細については、「[複数のサブスクリプションまたはリソース グループに Azure リソースをデプロイする](cross-scope-deployment.md)」を参照してください。

### <a name="subscription-example"></a>サブスクリプションの例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json)は、outputs セクションで呼び出される subscription 関数を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="subscriptionresourceid"></a>subscriptionResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

サブスクリプション レベルでデプロイされたリソースの一意の識別子を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| subscriptionId |いいえ |文字列 (GUID 形式) |既定値は、現在のサブスクリプションです。 別のサブスクリプション内のリソースを取得する必要がある場合は、この値を指定します。 |
| resourceType |はい |string |リソース プロバイダーの名前空間を含むリソースの種類。 |
| resourceName1 |はい |string |リソースの名前。 |
| resourceName2 |いいえ |string |次のリソース名セグメント (必要な場合)。 |

さらに他のセグメントがリソースの種類に含まれる場合は、続けてリソース名をパラメーターとして追加します。

### <a name="return-value"></a>戻り値

識別子は、次の形式で返されます。

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>解説

この関数を使用すると、リソース グループではなく、[サブスクリプションにデプロイ](deploy-to-subscription.md)されているリソースのリソース ID を取得できます。 返される ID は、リソース グループ値が含まれていないという点で、[resourceId](#resourceid) 関数から返される値とは異なります。

### <a name="subscriptionresourceid-example"></a>subscriptionResourceID の例

次のテンプレートでは、組み込みのロールが割り当てられます。 リソース グループまたはサブスクリプションにデプロイできます。 また、subscriptionResourceId 関数を使って、組み込みロールのリソース ID を取得することができます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>tenantResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

テナント レベルでデプロイされたリソースの一意の識別子を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| resourceType |はい |string |リソース プロバイダーの名前空間を含むリソースの種類。 |
| resourceName1 |はい |string |リソースの名前。 |
| resourceName2 |いいえ |string |次のリソース名セグメント (必要な場合)。 |

さらに他のセグメントがリソースの種類に含まれる場合は、続けてリソース名をパラメーターとして追加します。

### <a name="return-value"></a>戻り値

識別子は、次の形式で返されます。

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>解説

テナントにデプロイされているリソースのリソース ID を取得するには、この関数を使用します。 返される ID は、リソース グループまたはサブスクリプションの値を含まないという点で、他のリソース ID 関数から返される値とは異なります。

## <a name="next-steps"></a>次のステップ

* Azure Resource Manager テンプレートのセクションの説明については、[Azure Resource Manager テンプレートの作成](template-syntax.md)に関するページを参照してください。
* 複数のテンプレートをマージするには、[Azure Resource Manager でのリンクされたテンプレートの使用](linked-templates.md)に関するページを参照してください。
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](copy-resources.md)」を参照してください。
* 作成したテンプレートをデプロイする方法を確認するには、[Azure Resource Manager のテンプレートを使用したアプリケーションのデプロイ](deploy-powershell.md)に関するページを参照してください。

