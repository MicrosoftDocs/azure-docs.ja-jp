---
title: Azure Resource Manager テンプレートの関数 - デプロイ | Microsoft Docs
description: Azure Resource Manager テンプレートで、デプロイ情報を取得するために使用する関数について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/03/2019
ms.author: tomfitz
ms.openlocfilehash: 9cf81058d79d474a4d61195850636e428a1dbd0d
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206465"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートのデプロイ関数 

リソース マネージャーには、テンプレートのセクションから値を取得し、デプロイに関連する値を取得する次の関数が用意されています。

* [デプロイ](#deployment)
* [parameters](#parameters)
* [variables](#variables)

リソース、リソース グループ、サブスクリプションから値を取得する方法については、「 [リソース関数](resource-group-template-functions-resource.md)」を参照してください。

<a id="deployment" />

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment"></a>deployment
`deployment()`

現在のデプロイ操作に関する情報を返します。

### <a name="return-value"></a>戻り値

この関数は、デプロイ中に渡されたオブジェクトを返します。 返されるオブジェクトのプロパティは、デプロイ オブジェクトがリンクとして渡されたか、インライン オブジェクトとして渡されたかによって異なります。 デプロイ オブジェクトがインラインで渡された場合 (Azure PowerShell の **TemplateFile** パラメーターを使用してローカル ファイルを参照する場合など)、返されるオブジェクトは次の形式になります。

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
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

オブジェクトがリンクとして渡された場合 ( **-TemplateUri** パラメーターを使用してリモート オブジェクトを参照する場合など)、オブジェクトは次の形式で返されます。 

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
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

[Azure のサブスクリプションにデプロイする](deploy-to-subscription.md)場合は、リソース グループの代わりに、返されるオブジェクトに `location` プロパティが含まれています。 ローカル テンプレートまたは外部テンプレートのいずれかをデプロイする場合は、場所プロパティが含まれています。

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
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
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
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

デプロイ関数を使用するサブスクリプションレベルのテンプレートについては、[サブスクリプション デプロイ関数](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json)に関するページを参照してください。 これは `az deployment create` コマンドまたは `New-AzDeployment` コマンドのいずれかでデプロイされます。

<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

パラメーター値を返します。 指定したパラメーター名は、テンプレートのパラメーター セクションで定義されている必要があります。

### <a name="parameters"></a>parameters

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
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

| Name | Type | 値 |
| ---- | ---- | ----- |
| stringOutput | string | option 1 |
| intOutput | int | 1 |
| objectOutput | Object | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | string | option 1 |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

<a id="variables" />

## <a name="variables"></a>variables
`variables(variableName)`

変数の値を返します。 指定した変数名は、テンプレートの変数セクションで定義されている必要があります。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| variableName |はい |string |返す変数の名前。 |

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
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

| Name | Type | 値 |
| ---- | ---- | ----- |
| exampleOutput1 | string | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | string | myVariable |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

## <a name="next-steps"></a>次の手順
* Azure Resource Manager テンプレートのセクションの説明については、[Azure Resource Manager テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* 複数のテンプレートをマージするには、[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)に関するページを参照してください。
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。
* 作成したテンプレートをデプロイする方法を確認するには、[Azure Resource Manager のテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。

