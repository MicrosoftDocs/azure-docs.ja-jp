---
title: Azure Resource Manager テンプレートの関数 - 比較 | Microsoft Docs
description: Azure Resource Manager テンプレートで値を比較するために使用する関数について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2017
ms.author: tomfitz
ms.openlocfilehash: 6591a75577670f5bb50c1275af7e51e9b8328d50
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268706"
---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの比較関数

Resource Manager には、テンプレートで比較を行うための関数がいくつか用意されています。

* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="equals"></a>equals
`equals(arg1, arg2)`

2 つの値が互いに等しいかどうかを確認します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数、文字列、配列、オブジェクト |等しいかどうかを確認する 1 番目の値。 |
| arg2 |はい |整数、文字列、配列、オブジェクト |等しいかどうかを確認する 2 番目の値。 |

### <a name="return-value"></a>戻り値

値が等しい場合は **True** を、それ以外の場合は **False** を返します。

### <a name="remarks"></a>解説

equals 関数は、リソースがデプロイされているかどうかをテストするために、多くの場合 `condition` 要素と共に使用されます。

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

### <a name="example"></a>例

この[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json)では、さまざまな型の値が等しいかどうかを確認します。 すべての既定値は True を返します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 1
        },
        "firstString": {
            "type": "string",
            "defaultValue": "a"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["a", "b"]
        },
        "firstObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"a": "b"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[equals(parameters('firstString'), parameters('secondString'))]"
        },
        "checkArrays": {
            "type": "bool",
            "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
        },
        "checkObjects": {
            "type": "bool",
            "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | type | 値 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/equals.json 
```

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json)では、[not](resource-group-template-functions-logical.md#not) と **equals** を使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

前の例からの出力は次のようになります。

| Name | type | 値 |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/not-equals.json 
```

## <a name="greater"></a>greater
`greater(arg1, arg2)`

1 番目の値が 2 番目の値より大きいかどうかを確認します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数または文字列 |大きいかどうかを比較する 1 番目の値。 |
| arg2 |はい |整数または文字列 |大きいかどうかを比較する 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値よりも大きい場合は **True** を、それ以外の場合は **False** を返します。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json)では、一方の値がもう一方の値よりも大きいかどうかを確認します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greater(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | type | 値 |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greater.json 
```

## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

1 番目の値が 2 番目の値以上かどうかを確認します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数または文字列 |以上かどうかを比較する 1 番目の値。 |
| arg2 |はい |整数または文字列 |以上かどうかを比較する 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値以上の場合は **True** を、それ以外の場合は **False** を返します。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json)では、一方の値がもう一方の値以上かどうかを確認します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | type | 値 |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/greaterorequals.json 
```

## <a name="less"></a>less
`less(arg1, arg2)`

1 番目の値が 2 番目の値より小さいかどうかを確認します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数または文字列 |小さいかどうかを比較する 1 番目の値。 |
| arg2 |はい |整数または文字列 |小さいかどうかを比較する 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値よりも小さい場合は **True** を、それ以外の場合は **False** を返します。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json)では、一方の値がもう一方の値よりも小さいかどうかを確認します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[less(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | type | 値 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/less.json 
```

## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

1 番目の値が 2 番目の値以下かどうかを確認します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数または文字列 |以下かどうかを比較する 1 番目の値。 |
| arg2 |はい |整数または文字列 |以下かどうかを比較する 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値以下の場合は **True** を、それ以外の場合は **False** を返します。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json)では、一方の値がもう一方の値以下かどうかを確認します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstInt": {
            "type": "int",
            "defaultValue": 1
        },
        "secondInt": {
            "type": "int",
            "defaultValue": 2
        },
        "firstString": {
            "type": "string",
            "defaultValue": "A"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "a"
        }
    },
    "resources": [
    ],
    "outputs": {
        "checkInts": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
        },
        "checkStrings": {
            "type": "bool",
            "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | type | 値 |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/lessorequals.json 
```

## <a name="next-steps"></a>次の手順
* Azure Resource Manager テンプレートのセクションの説明については、[Azure Resource Manager テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* 複数のテンプレートをマージするには、[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)に関するページを参照してください。
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。
* 作成したテンプレートをデプロイする方法を確認するには、[Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。

