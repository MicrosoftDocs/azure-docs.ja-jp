---
title: テンプレート関数 - デプロイ
description: Azure Resource Manager テンプレートで、デプロイ情報を取得するために使用する関数について説明します。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: e8240c05cba82d5563c4b327ecbc65a9c358720f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677816"
---
# <a name="deployment-functions-for-arm-templates"></a>ARM テンプレートのデプロイ関数

Resource Manager には、Azure Resource Manager (ARM) テンプレートの現在のデプロイに関連する値を取得する次の関数が用意されています。

* [デプロイ](#deployment)
* [environment](#environment)
* [parameters](#parameters)
* [variables](#variables)

リソース、リソース グループ、サブスクリプションから値を取得する方法については、「 [リソース関数](template-functions-resource.md)」を参照してください。

## <a name="deployment"></a>deployment

`deployment()`

現在のデプロイ操作に関する情報を返します。

### <a name="return-value"></a>戻り値

この関数は、デプロイ中に渡されたオブジェクトを返します。 返されたオブジェクトのプロパティは、以下に該当するかどうかによって異なります。

* ローカル ファイルであるテンプレートをデプロイするか、URI を使用してアクセスされるリモート ファイルであるテンプレートをデプロイする。
* リソース グループにデプロイするか、他のいずれかのスコープ ([Azure サブスクリプション](deploy-to-subscription.md)、[管理グループ](deploy-to-management-group.md)、または[テナント](deploy-to-tenant.md)) にデプロイする。

ローカル テンプレートをリソース グループにデプロイする場合は、関数から次の形式が返されます。

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

リモート テンプレートをリソース グループにデプロイする場合は、関数から次の形式が返されます。

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Azure サブスクリプション、管理グループ、またはテナントにデプロイする場合は、返されるオブジェクトに `location` プロパティが含まれています。 ローカル テンプレートまたは外部テンプレートのいずれかをデプロイする場合は、場所プロパティが含まれています。 形式は次のようになります:

```json
{
    "name": "",
    "location": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "resources": [],
            "outputs": {}
        },
        "templateHash": "",
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

### <a name="remarks"></a>解説

deployment() を使い、親テンプレートの URI に基づいて、別のテンプレートにリンクできます。

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

ポータル内のデプロイ履歴からテンプレートを再デプロイすると、テンプレートがローカル ファイルとしてデプロイされます。 デプロイ関数の中では、`templateLink` プロパティは返されません。 お使いのテンプレートが `templateLink` に依存して別のテンプレートへのリンクを構成している場合は、ポータルを使用して再デプロイしないでください。 代わりに、最初にテンプレートのデプロイに利用したコマンドを使用してください。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json)では、デプロイ オブジェクトが返されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "deploymentOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

前の例では、次のオブジェクトが返されます。

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

## <a name="environment"></a>環境

`environment()`

デプロイに使用される Azure 環境に関する情報を返します。

### <a name="return-value"></a>戻り値

この関数では、現在の Azure 環境のプロパティが返されます。 次の例では、グローバル Azure のプロパティを示しています。 ソブリン クラウドでは、少し異なるプロパティが返される場合があります。

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>例

次のテンプレート例では、環境オブジェクトが返されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "environmentOutput": {
            "value": "[environment()]",
            "type" : "object"
        }
    }
}
```

前の例では、グローバル Azure にデプロイされる場合は、次のオブジェクトが返されます。

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parameters

`parameters(parameterName)`

パラメーター値を返します。 指定したパラメーター名は、テンプレートのパラメーター セクションで定義されている必要があります。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| parameterName |はい |string |返されるパラメーターの名前。 |

### <a name="return-value"></a>戻り値

指定したパラメーターの値。

### <a name="remarks"></a>解説

通常、パラメーターを使ってリソースの値を設定します。 次の例では、Web サイトの名前を、デプロイ時に渡されるパラメーターの値に設定します。

```json
"parameters": {
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json)では、parameters 関数の簡単な使用方法を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| stringOutput | String | option 1 |
| intOutput | int | 1 |
| objectOutput | Object | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | option 1 |

パラメーターの使用方法の詳細については、「[Azure Resource Manager テンプレートのパラメーター](template-parameters.md)」を参照してください。

## <a name="variables"></a>variables

`variables(variableName)`

変数の値を返します。 指定した変数名は、テンプレートの変数セクションで定義されている必要があります。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| variableName |はい |String |返す変数の名前。 |

### <a name="return-value"></a>戻り値

指定した変数の値。

### <a name="remarks"></a>解説

通常、変数を使って、複雑な値を 1 回だけ作成することによりテンプレートを簡略化します。 次の例では、ストレージ アカウントの一意の名前を作成します。

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json)は異なる変数値を返します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
          }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

変数の使用方法の詳細については、「[Azure Resource Manager テンプレートの変数](template-variables.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* Azure Resource Manager テンプレートのセクションの説明については、「[ARM テンプレートの構造と構文について](template-syntax.md)」を参照してください。
