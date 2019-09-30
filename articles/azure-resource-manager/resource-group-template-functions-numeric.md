---
title: Azure Resource Manager テンプレートの関数 - 数値 | Microsoft Docs
description: Azure Resource Manager テンプレートで、数値を操作するために使用する関数について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 3ec5477ca6ea1731f18b09d6393bdde6261e0c32
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194336"
---
# <a name="numeric-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの数値関数

リソース マネージャーには、整数を操作する次の関数が用意されています。

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add"></a>追加
`add(operand1, operand2)`

指定された 2 つ整数の合計を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- | 
|operand1 |はい |int |加算する最初の整数。 |
|operand2 |はい |int |加算する 2 つ目の整数。 |

### <a name="return-value"></a>戻り値

パラメーターの合計を格納する整数。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json)では、2 つのパラメーターを加算します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to add"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to add"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "addResult": {
            "type": "int",
            "value": "[add(parameters('first'), parameters('second'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| addResult | int | 8 |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/add.json 
```

<a id="copyindex" />

## <a name="copyindex"></a>copyIndex
`copyIndex(loopName, offset)`

反復処理のループのインデックスを返します。 

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| loopName | いいえ | string | 反復処理の取得対象となるループの名前。 |
| offset |いいえ |int |0 から始まる反復値に追加する整数。 |

### <a name="remarks"></a>解説

この関数は常に **copy** オブジェクトと共に使用されます。 **offset** の値が指定されていない場合、現在の反復値が返されます。 反復値は 0 から始まります。 リソースまたは変数のいずれかを定義するときに、反復処理のループを使用できます。

copyIndex がリソースの反復処理を指すのかプロパティの反復処理を指すのかは、**loopName** プロパティで指定できます。 **loopName** に値を指定しなかった場合は、現在のリソース タイプの反復処理が使われます。 プロパティに対する反復では、**loopName** に値を指定してください。 
 
**copyIndex**の使用方法の詳細については、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。

変数を定義するときに **copyIndex** を使用する例については、「[変数](resource-group-authoring-templates.md#variables)」を参照してください。

### <a name="example"></a>例

次の例では、コピー ループと、名前に含まれるインデックス値を示します。 

```json
"resources": [ 
  { 
    "name": "[concat('examplecopy-', copyIndex())]", 
    "type": "Microsoft.Web/sites", 
    "copy": { 
      "name": "websitescopy", 
      "count": "[parameters('count')]" 
    }, 
    ...
  }
]
```

### <a name="return-value"></a>戻り値

反復値の現在のインデックスを表す整数。

<a id="div" />

## <a name="div"></a>div
`div(operand1, operand2)`

指定された 2 つの整数の整数除算を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| operand1 |はい |int |除算される整数。 |
| operand2 |はい |int |除算に使用される整数。 0 にすることはできません。 |

### <a name="return-value"></a>戻り値

除算を表す整数。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json)では、一方のパラメーターをもう一方のパラメーターで除算します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 8,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "divResult": {
            "type": "int",
            "value": "[div(parameters('first'), parameters('second'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| divResult | int | 2 |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/div.json 
```

<a id="float" />

## <a name="float"></a>float
`float(arg1)`

値を浮動小数点数に変換します。 この関数は、ロジック アプリなどのアプリケーションにカスタム パラメーターを渡す場合にのみ使用します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |文字列または整数 |浮動小数点数に変換する値。 |

### <a name="return-value"></a>戻り値
浮動小数点数。

### <a name="example"></a>例

次の例では、ロジック アプリにパラメーターを渡すために浮動小数点数を使用する方法を示します。

```json
{
    "type": "Microsoft.Logic/workflows",
    "properties": {
        ...
        "parameters": {
            "custom1": {
                "value": "[float('3.0')]"
            },
            "custom2": {
                "value": "[float(3)]"
            },
```

<a id="int" />

## <a name="int"></a>int
`int(valueToConvert)`

指定された値を整数に変換します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| valueToConvert |はい |文字列または整数 |整数に変換する値。 |

### <a name="return-value"></a>戻り値

変換された値の整数。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json)では、ユーザー指定のパラメーター値を整数に変換します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToConvert": { 
            "type": "string",
            "defaultValue": "4"
        }
    },
    "resources": [
    ],
    "outputs": {
        "intResult": {
            "type": "int",
            "value": "[int(parameters('stringToConvert'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| intResult | int | 4 |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/int.json
```

<a id="max" />

## <a name="max"></a>max
`max (arg1)`

整数の配列または整数のコンマ区切りリストから最大値を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数の配列、または整数のコンマ区切りリスト |最大値を取得するコレクション。 |

### <a name="return-value"></a>戻り値

コレクションの最大値を表す整数。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json)では、max を配列および整数のリストと共に使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | int | 5 |
| intOutput | int | 5 |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>min
`min (arg1)`

整数の配列または整数のコンマ区切りリストから最小値を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数の配列、または整数のコンマ区切りリスト |最小値を取得するコレクション。 |

### <a name="return-value"></a>戻り値

コレクションの最小値を表す整数。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json)では、min を配列および整数のリストと共に使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | int | 0 |
| intOutput | int | 0 |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="mod" />

## <a name="mod"></a>mod
`mod(operand1, operand2)`

指定された 2 つの整数を使用した整数除算の剰余を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| operand1 |はい |int |除算される整数。 |
| operand2 |はい |int |除算に使用される整数。0 にすることはできません。 |

### <a name="return-value"></a>戻り値
剰余を表す整数。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json)では、一方のパラメーターをもう一方のパラメーターで除算した剰余を返します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer being divided"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer used to divide"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "modResult": {
            "type": "int",
            "value": "[mod(parameters('first'), parameters('second'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| modResult | int | 1 |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mod.json
```

<a id="mul" />

## <a name="mul"></a>mul
`mul(operand1, operand2)`

指定された 2 つの整数の乗算を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| operand1 |はい |int |乗算する最初の整数。 |
| operand2 |はい |int |乗算する 2 つ目の整数。 |

### <a name="return-value"></a>戻り値

乗算を表す整数。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json)では、一方のパラメーターをもう一方のパラメーターで乗算します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 5,
            "metadata": {
                "description": "First integer to multiply"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Second integer to multiply"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "mulResult": {
            "type": "int",
            "value": "[mul(parameters('first'), parameters('second'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| mulResult | int | 15 |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/mul.json
```

<a id="sub" />

## <a name="sub"></a>sub
`sub(operand1, operand2)`

指定された 2 つの整数の減算を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| operand1 |はい |int |減算される整数。 |
| operand2 |はい |int |減算する整数。 |

### <a name="return-value"></a>戻り値
減算を表す整数。

### <a name="example"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json)では、一方のパラメーターをもう一方のパラメーターで減算します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "first": {
            "type": "int",
            "defaultValue": 7,
            "metadata": {
                "description": "Integer subtracted from"
            }
        },
        "second": {
            "type": "int",
            "defaultValue": 3,
            "metadata": {
                "description": "Integer to subtract"
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "subResult": {
            "type": "int",
            "value": "[sub(parameters('first'), parameters('second'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| subResult | int | 4 |

Azure CLI を使用してこのテンプレート例をデプロイするには、以下を使用します。

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

PowerShell を使用してこのテンプレート例をデプロイするには、以下を使用します。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/sub.json
```

## <a name="next-steps"></a>次の手順
* Azure Resource Manager テンプレートのセクションの説明については、[Azure Resource Manager テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* 複数のテンプレートをマージするには、[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)に関するページを参照してください。
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。
* 作成したテンプレートをデプロイする方法を確認するには、[Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。

