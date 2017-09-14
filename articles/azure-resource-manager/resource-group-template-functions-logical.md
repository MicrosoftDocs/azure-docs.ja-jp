---
title: "Azure Resource Manager テンプレートの関数 - 論理 | Microsoft Docs"
description: "Azure Resource Manager テンプレートで論理値を判定するために使用する関数について説明します。"
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
ms.openlocfilehash: 313601ad99cdc12c4b50f5469959d37a9fa70d35
ms.contentlocale: ja-jp
ms.lasthandoff: 08/02/2017

---
# <a name="logical-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの論理関数

Resource Manager には、テンプレートで比較を行うための関数がいくつか用意されています。

* [and](#and)
* [bool](#bool)
* [if](#if)
* [not](#not)
* [or](#or)

## <a name="and"></a>and
`and(arg1, arg2)`

両方のパラメーター値が true かどうかを確認します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |あり |boolean |true かどうかを確認する最初の値。 |
| arg2 |あり |boolean |true かどうかを確認する 2 番目の値。 |

### <a name="return-value"></a>戻り値

両方の値が true の場合は **True** を、それ以外の場合は **False** を返します。

### <a name="examples"></a>例

次の例では、論理関数を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

前の例からの出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| andExampleOutput | ブール値 | False |
| orExampleOutput | ブール値 | True |
| notExampleOutput | ブール値 | False |


## <a name="bool"></a>bool
`bool(arg1)`

パラメーターをブール値に変換します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |はい |文字列または整数 |ブール値に変換する値。 |

### <a name="return-value"></a>戻り値
変換後の値のブール値。

### <a name="examples"></a>例

次の例では、ブール値を文字列または整数と共に使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| trueString | ブール値 | True |
| falseString | ブール値 | False |
| trueInt | ブール値 | True |
| falseInt | ブール値 | False |

## <a name="if"></a>if
`if(condition, trueValue, falseValue)`

条件が true か false かに基づいて値を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| condition |あり |boolean |true かどうかを確認する値。 |
| trueValue |あり | string、int、object、または array |条件が true の場合に返される値。 |
| falseValue |あり | string、int、object、または array |条件が false の場合に返される値。 |

### <a name="return-value"></a>戻り値

最初のパラメーターが **True** の場合、2 番目のパラメーターを返します。そうでない場合、3 番目のパラメーターを返します。

### <a name="remarks"></a>解説

この関数を使用して、条件付きでリソース プロパティを設定することができます。 次の例はテンプレート全体ではありませんが、条件付きでの可用性セットの設定に関連する部分を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "availabilitySet": {
            "type": "string",
            "allowedValues": [
                "yes",
                "no"
            ]
        }
    },
    "variables": {
        ...
        "availabilitySetName": "availabilitySet1",
        "availabilitySet": {
            "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
        }
    },
    "resources": [
        {
            "condition": "[equals(parameters('availabilitySet'),'yes')]",
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            ...
        },
        {
            "apiVersion": "2016-03-30",
            "type": "Microsoft.Compute/virtualMachines",
            "properties": {
                "availabilitySet": "[if(equals(parameters('availabilitySet'),'yes'), variables('availabilitySet'), json('null'))]",
                ...
            }
        },
        ...
    ],
    ...
}
```

### <a name="examples"></a>例

次の例は、`if` 関数の使用方法を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        }
    }
}
```

前の例からの出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| yesOutput | String | ○ |
| noOutput | String | × |


## <a name="not"></a>not
`not(arg1)`

ブール値をその反対の値に変換します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |あり |boolean |変換する値。 |


### <a name="return-value"></a>戻り値

パラメーターが **False** の場合、**True** を返します。 パラメーターが **True** の場合、**False** を返します。

### <a name="examples"></a>例

次の例では、論理関数を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

前の例からの出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| andExampleOutput | ブール値 | False |
| orExampleOutput | ブール値 | True |
| notExampleOutput | ブール値 | False |

次の例では、**not** と [equals](resource-group-template-functions-comparison.md#equals) を使用します。

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


## <a name="or"></a>または
`or(arg1, arg2)`

どちらかのパラメーター値が true かどうかを確認します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |あり |boolean |true かどうかを確認する最初の値。 |
| arg2 |あり |boolean |true かどうかを確認する 2 番目の値。 |

### <a name="return-value"></a>戻り値

どちらかの値が true の場合は **True** を返し、そうでない場合は **False** を返します。

### <a name="examples"></a>例

次の例では、論理関数を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

前の例からの出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| andExampleOutput | ブール値 | False |
| orExampleOutput | ブール値 | True |
| notExampleOutput | ブール値 | False |


## <a name="next-steps"></a>次のステップ
* Azure Resource Manager テンプレートのセクションの説明については、[Azure Resource Manager テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* 複数のテンプレートをマージするには、[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)に関するページを参照してください。
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。
* 作成したテンプレートをデプロイする方法を確認するには、[Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。


