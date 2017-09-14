---
title: "Azure Resource Manager テンプレートの関数 - 比較 | Microsoft Docs"
description: "Azure Resource Manager テンプレートで値を比較するために使用する関数について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 521e5ed06c138bcd374913588f06a2e6c1e99963
ms.contentlocale: ja-jp
ms.lasthandoff: 08/02/2017

---
# <a name="comparison-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの比較関数

Resource Manager には、テンプレートで比較を行うための関数がいくつか用意されています。

* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

## <a name="equals"></a>equals
`equals(arg1, arg2)`

2 つの値が互いに等しいかどうかを確認します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
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

この例のテンプレートでは、さまざまな型の値が等しいかどうかを確認します。 すべての既定値は True を返します。

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

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| checkInts | ブール値 | True |
| checkStrings | ブール値 | True |
| checkArrays | ブール値 | True |
| checkObjects | ブール値 | True |


次の例では、[not](resource-group-template-functions-logical.md#not) と **equals** を使用します。

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
```

前の例からの出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| checkNotEquals | ブール値 | True |


## <a name="greater"></a>greater
`greater(arg1, arg2)`

1 番目の値が 2 番目の値より大きいかどうかを確認します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数または文字列 |大きいかどうかを比較する 1 番目の値。 |
| arg2 |はい |整数または文字列 |大きいかどうかを比較する 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値よりも大きい場合は **True** を、それ以外の場合は **False** を返します。

### <a name="example"></a>例

この例のテンプレートでは、一方の値がもう一方の値よりも大きいかどうかを確認します。

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

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| checkInts | ブール値 | False |
| checkStrings | ブール値 | True |


## <a name="greaterorequals"></a>greaterOrEquals
`greaterOrEquals(arg1, arg2)`

1 番目の値が 2 番目の値以上かどうかを確認します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数または文字列 |以上かどうかを比較する 1 番目の値。 |
| arg2 |はい |整数または文字列 |以上かどうかを比較する 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値以上の場合は **True** を、それ以外の場合は **False** を返します。

### <a name="example"></a>例

この例のテンプレートでは、一方の値がもう一方の値以上かどうかを確認します。

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

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| checkInts | ブール値 | False |
| checkStrings | ブール値 | True |



## <a name="less"></a>less
`less(arg1, arg2)`

1 番目の値が 2 番目の値より小さいかどうかを確認します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数または文字列 |小さいかどうかを比較する 1 番目の値。 |
| arg2 |はい |整数または文字列 |小さいかどうかを比較する 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値よりも小さい場合は **True** を、それ以外の場合は **False** を返します。

### <a name="example"></a>例

この例のテンプレートでは、一方の値がもう一方の値よりも小さいかどうかを確認します。

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

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| checkInts | ブール値 | True |
| checkStrings | ブール値 | False |


## <a name="lessorequals"></a>lessOrEquals
`lessOrEquals(arg1, arg2)`

1 番目の値が 2 番目の値以下かどうかを確認します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |はい |整数または文字列 |以下かどうかを比較する 1 番目の値。 |
| arg2 |はい |整数または文字列 |以下かどうかを比較する 2 番目の値。 |

### <a name="return-value"></a>戻り値

1 番目の値が 2 番目の値以下の場合は **True** を、それ以外の場合は **False** を返します。

### <a name="example"></a>例

この例のテンプレートでは、一方の値がもう一方の値以下かどうかを確認します。

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

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| checkInts | ブール値 | True |
| checkStrings | ブール値 | False |



## <a name="next-steps"></a>次のステップ
* Azure Resource Manager テンプレートのセクションの説明については、[Azure Resource Manager テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* 複数のテンプレートをマージするには、[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)に関するページを参照してください。
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。
* 作成したテンプレートをデプロイする方法を確認するには、[Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。


