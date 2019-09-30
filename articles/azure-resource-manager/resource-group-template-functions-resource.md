---
title: Azure Resource Manager テンプレートの関数 - リソース | Microsoft Docs
description: Azure Resource Manager テンプレートで、リソースに関する値を取得するために使用する関数について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: tomfitz
ms.openlocfilehash: 9e50a2705982a022284e1c54bd5ed7360a2d1663
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390707"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートのリソース関数

リソース マネージャーには、リソース値を取得する次の関数が用意されています。

* [list*](#list)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [サブスクリプション](#subscription)

パラメーター、変数、現在のデプロイから値を取得する方法については、「 [デプロイの値関数](resource-group-template-functions-deployment.md)」を参照してください。

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>list*

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

この関数の構文はリスト操作の名前によって異なります。 実装ごとに、リスト操作をサポートするリソースの種類の値が返されます。 操作名は `list` で始める必要があります。 一般的に使用されるものに `listKeys` と `listSecrets` があります。 

### <a name="parameters"></a>parameters

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| resourceName または resourceIdentifier |はい |string |リソースの一意識別子です。 |
| apiVersion |はい |string |リソースのランタイム状態の API バージョン。 通常、**yyyy-mm-dd** の形式。 |
| functionValues |いいえ |object | 関数の値を持つオブジェクト。 このオブジェクトは、ストレージ アカウントの **listAccountSas** など、パラメーター値を持つオブジェクトの受信をサポートする関数に対してのみ指定します。 関数値を渡す例をこの記事で紹介します。 | 

### <a name="valid-uses"></a>有効な使用方法

list 関数は、リソース定義のプロパティと、テンプレートまたはデプロイの出力セクションでのみ使用できます。 [プロパティの反復処理](resource-group-create-multiple.md#property-iteration)で使用する場合には、式がリソース プロパティに割り当てられるため、`input` に対して list 関数を使用できます。 これらを `count` と一緒に使用することはできません。カウントは、list 関数が解決される前に決定される必要があるためです。

### <a name="implementations"></a>実装

list* の使用例を次の表にまとめています。

| リソースの種類 | 関数名 |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration/configurationStores | ListKeys |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.BotService/botServices/channels | listChannelWithKeys |
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
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listKeys](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
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
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | listKeys |
| Microsoft.MachineLearningServices/workspaces | listKeys |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [listKeys](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
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
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/webapps/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
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

**list** 関数を条件付きでデプロイされるリソースで使用した場合、この関数はリソースがデプロイされていなくても評価されます。 **list** 関数で存在しないリソースを参照した場合、エラーが返されます。 リソースのデプロイ中にのみこの関数が評価されるようにするには、**if** 関数を使用します。 if と list を条件付きでデプロイされるリソースで使用するサンプル テンプレートについては、[if 関数](resource-group-template-functions-logical.md#if)に関する説明を参照してください。

### <a name="list-example"></a>リストの例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json)は、outputs セクションでストレージ アカウントのプライマリ キーとセカンダリ キーを返す方法を示しています。 また、ストレージ アカウントの SAS トークンも返します。 

SAS トークンを取得するには、有効期限のオブジェクトを渡します。 有効期限は将来の日付にする必要があります。 この例は、list 関数を使用する方法を示したものです。 通常、SAS トークンを出力値として返すのではなく、リソース値で使用します。 出力値はデプロイ履歴に格納され、セキュリティで保護されません。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>providers

`providers(providerNamespace, [resourceType])`

リソース プロバイダーとサポートされているそのリソースの種類に関する情報を返します。 リソースの種類を指定しない場合、関数はリソース プロバイダーでサポートされているすべての種類を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | 種類 | 説明 |
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
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

### <a name="parameters"></a>parameters

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| resourceName または resourceIdentifier |はい |string |名前またはリソースの一意の識別子。 現在のテンプレート内のリソースを参照する場合は、パラメーターとしてリソース名のみを指定します。 以前にデプロイされたリソースを参照する場合は、リソース ID を指定します。 |
| apiVersion |いいえ |string |指定したリソースの API バージョンです。 同じテンプレート内でリソースがプロビジョニングされない場合に、このパラメーターを追加します。 通常、**yyyy-mm-dd** の形式。 リソースに有効な API のバージョンについては、[テンプレート リファレンス](/azure/templates/)を参照してください。 |
| 'Full' |いいえ |string |完全なリソース オブジェクトを返すかどうかを指定する値。 `'Full'` を指定しない場合、リソースのプロパティ オブジェクトのみが返されます。 完全なオブジェクトには、リソース ID や場所などの値が含まれます。 |

### <a name="return-value"></a>戻り値

あらゆるリソースの種類で、reference 関数のさまざまなプロパティが返されます。 この関数は、定義済みの単一の形式を返しません。 また、返される値は、完全なオブジェクトを指定したかどうかによって異なります。 あるリソースの種類のプロパティを確認するには、この例に示すように、outputs セクションでオブジェクトを返します。

### <a name="remarks"></a>解説

reference 関数は、以前にデプロイされたリソースまたは現在のテンプレートにデプロイされたリソースのいずれかのランタイム状態を取得します。 この記事では、両方のシナリオの例を示します。

通常、**reference** 関数は、BLOB エンドポイント URI や完全修飾ドメイン名などのオブジェクトから特定の値を返すために使用します。

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

`'Full'` は、プロパティのスキーマに含まれないリソースの値が必要なときに使用します。 たとえば、キー コンテナーのアクセス ポリシーを設定するために、仮想マシンの ID プロパティを取得する場合です。

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
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

reference 関数は、リソース定義のプロパティと、テンプレートまたはデプロイの出力セクションでのみ使用できます。 [プロパティの反復処理](resource-group-create-multiple.md#property-iteration)で使用する場合には、式がリソース プロパティに割り当てられるため、`input` に対して reference 関数を使用できます。 これを `count` と一緒に使用することはできません。カウントは、reference 関数が解決される前に決定される必要があるためです。

[入れ子になったテンプレート](resource-group-linked-templates.md#nested-template)の出力に reference 関数を使用して、入れ子になったテンプレートにデプロイされたリソースを返すことはできません。 その場合は、[リンク済みテンプレート](resource-group-linked-templates.md#external-template-and-external-parameters)を使用してください。

**reference** 関数を条件付きでデプロイされるリソースで使用した場合、この関数はリソースがデプロイされていなくても評価されます。  **reference** 関数で存在しないリソースを参照した場合、エラーが返されます。 リソースのデプロイ中にのみこの関数が評価されるようにするには、**if** 関数を使用します。 条件付きでデプロイされるリソースで if と reference を使用するサンプル テンプレートについては、[if 関数](resource-group-template-functions-logical.md#if)に関する説明を参照してください。

### <a name="implicit-dependency"></a>暗黙の依存関係

参照されているリソースを同じテンプレート内でプロビジョニングし、(リソース ID ではなく) 名前でリソースを参照する場合は、reference 関数を使用することにより、あるリソースが他のリソースに依存することを暗黙的に宣言します。 dependsOn プロパティを一緒に使用する必要はありません。 参照先のリソースがデプロイを完了するまで、関数は評価されません。

### <a name="resource-name-or-identifier"></a>リソースの名前または識別子

同じテンプレートでデプロイされるリソースを参照するときは、リソースの名前を指定します。

```json
"value": "[reference(parameters('storageAccountName'))]"
```

同じテンプレートでデプロイされないリソースを参照するときは、リソース ID を指定します。

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

参照しているリソースがあいまいにならないようにするには、完全修飾リソース名を指定します。

```json
"value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')))]"
```

リソースに対する完全修飾参照を作成する場合、種類と名前からセグメントを結合する順序は、単に 2 つの連結ではありません。 名前空間の後に、"*種類/名前*" のペアを具体性の低いものから高いものへの順に使用します。

**{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]**

例:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`は正しい`Microsoft.Compute/virtualMachines/extensions/myVM/myExt`は正しくない

### <a name="reference-example"></a>参照の例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json)では、リソースをデプロイし、そのリソースを参照します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

`resourceGroup()` 関数は、[サブスクリプション レベルでデプロイ](deploy-to-subscription.md)されたテンプレートで使用できません。 リソース グループにデプロイされているテンプレートでのみ使用できます。

resourceGroup 関数の一般的な用途では、リソース グループと同じ場所にリソースを作成します。 次の例では、リソース グループの場所を使用して、Web サイトの場所を割り当てます。

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

resourceGroup 関数を使用して、リソース グループからリソースにタグを適用することもできます。 詳細については、「[リソース グループからタグを適用する](resource-group-using-tags.md#apply-tags-from-resource-group)」を参照してください。

### <a name="resource-group-example"></a>リソース グループの例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json)は、リソース グループのプロパティを返します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

リソースの一意の識別子を返します。 リソース名があいまいであるか、同じテンプレート内でプロビジョニングされていないときに、この関数を使用します。 

### <a name="parameters"></a>parameters

| パラメーター | 必須 | 種類 | 説明 |
|:--- |:--- |:--- |:--- |
| subscriptionId |いいえ |文字列 (GUID 形式) |既定値は、現在のサブスクリプションです。 別のサブスクリプション内のリソースを取得する必要がある場合は、この値を指定します。 |
| resourceGroupName |いいえ |string |既定値は、現在のリソース グループです。 別のリソース グループ内のリソースを取得する必要がある場合は、この値を指定します。 |
| resourceType |はい |string |リソース プロバイダーの名前空間を含むリソースの種類。 |
| resourceName1 |はい |string |リソースの名前。 |
| resourceName2 |いいえ |string |次のリソース名セグメント (必要な場合)。 |

さらに他のセグメントがリソースの種類に含まれる場合は、続けてリソース名をパラメーターとして追加します。

### <a name="return-value"></a>戻り値

識別子は、次の形式で返されます。

**/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}**


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

`resourceId()` 関数を[サブスクリプション レベルのデプロイ](deploy-to-subscription.md)で使用した場合、そのレベルでデプロイされたリソースの ID のみを取得できます。 たとえば、ポリシー定義またはロール定義の ID は取得できますが、ストレージ アカウントの ID は取得できません。 リソース グループへのデプロイの場合は、その逆になります。 サブスクリプション レベルでデプロイされたリソースのリソース ID を取得することはできません。

サブスクリプション スコープでデプロイするときにサブスクリプション レベルのリソースのリソース ID を取得するには、次のようにします。

```json
"[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
```

代替のリソース グループで、ストレージ アカウントや仮想ネットワークを使用するときには、多くの場合にこの関数を使用する必要があります。 次の例は、外部のリソース グループのリソースを簡単に使用する方法を示しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

| Name | 種類 | 値 |
| ---- | ---- | ----- |
| sameRGOutput | string | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | string | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | string | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | string | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

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

### <a name="subscription-example"></a>サブスクリプションの例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json)は、outputs セクションで呼び出される subscription 関数を示しています。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="next-steps"></a>次の手順

* Azure Resource Manager テンプレートのセクションの説明については、[Azure Resource Manager テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* 複数のテンプレートをマージするには、[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)に関するページを参照してください。
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。
* 作成したテンプレートをデプロイする方法を確認するには、[Azure Resource Manager のテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。

