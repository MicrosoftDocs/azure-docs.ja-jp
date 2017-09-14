---
title: "Azure Resource Manager テンプレートの関数 - 文字列 | Microsoft Docs"
description: "Azure Resource Manager テンプレートで、文字列を操作するために使用する関数について説明します。"
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
ms.openlocfilehash: 3e5c9ca546629f782a3d722b49f5fbaf5147e823
ms.contentlocale: ja-jp
ms.lasthandoff: 08/02/2017

---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの文字列関数

リソース マネージャーには、文字列を操作する次の関数が用意されています。

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [contains](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [first](#first)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [string](#string)
* [substring](#substring)
* [take](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)

<a id="base64" />

## <a name="base64"></a>base64
`base64(inputString)`

入力文字列の base64 表現を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| inputString |はい |string |Base 64 形式として返す値。 |

### <a name="return-value"></a>戻り値

base64 形式を含む文字列。

### <a name="examples"></a>例

次の例では、base64 関数を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | オブジェクト | {"one": "a", "two": "b"} |

<a id="base64tojson" />

## <a name="base64tojson"></a>base64ToJson
`base64tojson`

base64 形式を JSON オブジェクトに変換します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| base64Value |はい |string |JSON オブジェクトに変換する base64 形式。 |

### <a name="return-value"></a>戻り値

JSON オブジェクト。

### <a name="examples"></a>例

次の例では、base64ToJson 関数を使用して base64 値を変換します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | オブジェクト | {"one": "a", "two": "b"} |

<a id="base64tostring" />

## <a name="base64tostring"></a>base64ToString
`base64ToString(base64Value)`

base64 形式を文字列に変換します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| base64Value |はい |string |文字列に変換する base64 形式。 |

### <a name="return-value"></a>戻り値

変換された base64 値の文字列。

### <a name="examples"></a>例

次の例では、base64ToString 関数を使用して base64 値を変換します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | オブジェクト | {"one": "a", "two": "b"} |



<a id="concat" />

## <a name="concat"></a>concat
`concat (arg1, arg2, arg3, ...)`

複数の文字列値を結合して連結された文字列を返します。または複数の配列を結合して連結された配列を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |あり |文字列または配列 |連結の最初の値。 |
| 残りの引数 |いいえ |string |連結する順の追加の値。 |

### <a name="return-value"></a>戻り値
連結された値の文字列または配列。

### <a name="examples"></a>例

次の例は、2 つの文字列値を結合して 1 つの連結文字列を返す方法を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| concatOutput | String | prefix-5yj4yjf5mbg72 |

次の例では、2 つの配列を結合する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "firstArray": { 
            "type": "array", 
            "defaultValue": [ 
                "1-1", 
                "1-2", 
                "1-3" 
            ] 
        },
        "secondArray": {
            "type": "array", 
            "defaultValue": [ 
                "2-1", 
                "2-2",
                "2-3" 
            ] 
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| return | array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

<a id="contains" />

## <a name="contains"></a>contains
`contains (container, itemToFind)`

配列に値が含まれるかどうか、オブジェクトにキーが含まれるかどうか、または文字列に部分文字列が含まれるかどうかを確認します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| container |はい |配列、オブジェクト、文字列 |検索対象の値を含む値。 |
| itemToFind |あり |文字列または整数 |検索対象の値。 |

### <a name="return-value"></a>戻り値

項目が見つかった場合は **True**、それ以外の場合は **False** です。

### <a name="examples"></a>例

次の例では、contains をさまざまな型で使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| stringTrue | ブール値 | True |
| stringFalse | ブール値 | False |
| objectTrue | ブール値 | True |
| objectFalse | ブール値 | False |
| arrayTrue | ブール値 | True |
| arrayFalse | ブール値 | False |

<a id="datauri" />

## <a name="datauri"></a>dataUri
`dataUri(stringToConvert)`

値をデータ URI に変換します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| stringToConvert |はい |string |データ URI に変換する値。 |

### <a name="return-value"></a>戻り値

データ URI として書式設定された文字列。

### <a name="examples"></a>例

次の例では、値をデータ URI に変換し、データ URI を文字列に変換します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

<a id="datauritostring" />

## <a name="datauritostring"></a>dataUriToString
`dataUriToString(dataUriToConvert)`

データ URI の形式で書式設定された値を文字列に変換します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |あり |string |変換するデータ URI 値。 |

### <a name="return-value"></a>戻り値

変換後の値を含む文字列。

### <a name="examples"></a>例

次の例では、値をデータ URI に変換し、データ URI を文字列に変換します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

<a id="empty" /> 

## <a name="empty"></a>empty
`empty(itemToTest)`

配列、オブジェクト、または文字列が空かどうかを判断します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| itemToTest |はい |配列、オブジェクト、文字列 |空かどうかを確認する値。 |

### <a name="return-value"></a>戻り値

値が空の場合は **True** を、値が空でない場合は **False** を返します。

### <a name="examples"></a>例

次の例では、配列、オブジェクト、および文字列が空かどうかを確認します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| arrayEmpty | ブール値 | True |
| objectEmpty | ブール値 | True |
| stringEmpty | ブール値 | True |

<a id="endswith" />

## <a name="endswith"></a>endsWith
`endsWith(stringToSearch, stringToFind)`

文字列が特定の値で終わるかどうかを判断します。 比較では大文字と小文字は区別されません。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |あり |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

文字列の最後の文字または文字列が値に一致した場合は **True**、それ以外の場合は **False**。

### <a name="examples"></a>例

次の例は、startsWith 関数と endsWith 関数の使用方法を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| startsTrue | ブール値 | True |
| startsCapTrue | ブール値 | True |
| startsFalse | ブール値 | False |
| endsTrue | ブール値 | True |
| endsCapTrue | ブール値 | True |
| endsFalse | ブール値 | False |

<a id="first" />

## <a name="first"></a>first
`first(arg1)`

文字列の最初の文字、または配列の最初の要素を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |最初の要素または文字を取得する値。 |

### <a name="return-value"></a>戻り値

文字列の最初の文字、または配列の最初の要素の型 (文字列、整数、配列、またはオブジェクト)。

### <a name="examples"></a>例

次の例では、first 関数を配列および文字列と共に使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

<a id="indexof" />

## <a name="indexof"></a>indexOf
`indexOf(stringToSearch, stringToFind)`

文字列内の値の最初の位置を返します。 比較では大文字と小文字は区別されません。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |あり |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

検索する項目の位置を表す整数。 値は 0 から始まります。 項目が見つからない場合は、-1 が返されます。

### <a name="examples"></a>例

次の例は、indexOf 関数と lastIndexOf 関数の使用方法を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

<a id="last" />

## <a name="last"></a>last
`last (arg1)`

文字列の最後の文字、または配列の最後の要素を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |最後の要素または文字を取得する値。 |

### <a name="return-value"></a>戻り値

文字列の最後の文字、または配列の最後の要素の型 (文字列、整数、配列、またはオブジェクト)。

### <a name="examples"></a>例

次の例では、last 関数を配列および文字列と共に使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

<a id="lastindexof" />

## <a name="lastindexof"></a>lastIndexOf
`lastIndexOf(stringToSearch, stringToFind)`

文字列内の値の最後の位置を返します。 比較では大文字と小文字は区別されません。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |あり |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

検索する項目の最後の位置を表す整数。 値は 0 から始まります。 項目が見つからない場合は、-1 が返されます。

### <a name="examples"></a>例

次の例は、indexOf 関数と lastIndexOf 関数の使用方法を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

<a id="length" />

## <a name="length"></a>length
`length(string)`

文字列の文字数、または配列の要素の数を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |要素の数を取得するために使用する配列、または文字の数を取得するために使用する文字列。 |

### <a name="return-value"></a>戻り値

整数。 

### <a name="examples"></a>例

次の例では、length を配列および文字列と共に使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |

<a id="padleft" />

## <a name="padleft"></a>padLeft
`padLeft(valueToPad, totalLength, paddingCharacter)`

指定された長さに到達するまで左側に文字を追加していくことで、右揃えの文字列を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| valueToPad |はい |文字列または整数 |右揃えにする値。 |
| totalLength |はい |int |返される文字列の文字合計数。 |
| paddingCharacter |なし |1 文字 |左余白の長さに到達するまで使用する文字。 既定値は空白です。 |

元の文字列が、埋め込まれる文字数よりも長い場合は、文字が追加されません。

### <a name="return-value"></a>戻り値

少なくとも指定した文字数の文字列。

### <a name="examples"></a>例

次の例では、文字の合計数に達するまでゼロ文字を追加することで、ユーザー指定のパラメーター値を埋め込む方法を示します。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

<a id="replace" />

## <a name="replace"></a>replace
`replace(originalString, oldString, newString)`

ある文字列のすべてのインスタンスを別の文字列で置き換えた、新しい文字列を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| originalString |あり |string |別の文字列で置き換えられる文字列の全インスタンスを含む値。 |
| oldString |あり |string |元の文字列から削除する文字列。 |
| newString |あり |string |削除された文字列の代わりに追加する文字列。 |

### <a name="return-value"></a>戻り値

文字が置き換えられた文字列。

### <a name="examples"></a>例

次の例では、ユーザーが指定した文字列からすべてのダッシュを削除し、その文字列の部分を別の文字列に置き換える方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secodeOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secodeOutput | String | 123-123-xxxx |

<a id="skip" />

## <a name="skip"></a>skip
`skip(originalValue, numberToSkip)`

指定した文字数の後にあるすべての文字を含む文字列を返します。または、指定した数の要素の後にあるすべての要素を含む配列を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| originalValue |はい |配列または文字列 |スキップ対象の配列または文字列。 |
| numberToSkip |はい |int |スキップする要素または文字の数。 この値が 0 以下である場合は、値内のすべての要素または文字が返されます。 配列または文字列の長さを超える場合は、空の配列または文字列が返されます。 |

### <a name="return-value"></a>戻り値

配列または文字列。

### <a name="examples"></a>例

次の例では、配列内の指定した数の要素と、文字列内の指定した数の文字をスキップします。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| arrayOutput | array | ["three"] |
| stringOutput | String | two three |

<a id="split" />

## <a name="split"></a>split
`split(inputString, delimiter)`

指定された区切り記号で区切られた、入力文字列の部分文字列が格納されている、文字列の配列を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| inputString |はい |string |分割する文字列。 |
| delimiter |あり |文字列または文字列の配列 |文字列の分割に使用する区切り記号。 |

### <a name="return-value"></a>戻り値

文字列の配列。

### <a name="examples"></a>例

次の例では、入力文字列をコンマで分割したり、コンマまたはセミコロンで分割したりします。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| firstOutput | array | ["one", "two", "three"] |
| secondOutput | array | ["one", "two", "three"] |

<a id="startswith" />

## <a name="startswith"></a>startsWith
`startsWith(stringToSearch, stringToFind)`

文字列が特定の値で始まるかどうかを判断します。 比較では大文字と小文字は区別されません。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |あり |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

文字列の最初の文字または文字列が値に一致した場合は **True**、それ以外の場合は **False**。

### <a name="examples"></a>例

次の例は、startsWith 関数と endsWith 関数の使用方法を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| startsTrue | ブール値 | True |
| startsCapTrue | ブール値 | True |
| startsFalse | ブール値 | False |
| endsTrue | ブール値 | True |
| endsCapTrue | ブール値 | True |
| endsFalse | ブール値 | False |

<a id="string" />

## <a name="string"></a>string
`string(valueToConvert)`

指定された値を文字列に変換します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |はい | 任意 |文字列に変換する値。 オブジェクトと配列を含む、あらゆる種類の値を変換できます。 |

### <a name="return-value"></a>戻り値

変換された値の文字列。

### <a name="examples"></a>例

次の例では、さまざまな型の値を文字列に変換する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| objectOutput | String | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | String | ["a","b","c"] |
| intOutput | String | 5 |

<a id="substring" />

## <a name="substring"></a>substring
`substring(stringToParse, startIndex, length)`

指定した文字位置から始まる指定された文字数分の部分文字列を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| stringToParse |はい |string |部分文字列の抽出元となる文字列。 |
| startIndex |なし |int |部分文字列の 0 から始まる開始文字位置。 |
| length |いいえ |int |部分文字列の文字数。 文字列内の場所を参照する必要があります。 |

### <a name="return-value"></a>戻り値

部分文字列。

### <a name="remarks"></a>解説

部分文字列が文字列の末尾を越えると関数は失敗します。 次の例は、"インデックス パラメーターと長さパラメーターは文字列内の場所を参照している必要があります。 インデックス パラメーター: '{0}'、長さパラメーター: '11'、文字列長パラメーター: '10'" というエラーで失敗します。

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>例

次の例では、パラメーターから部分文字列を抽出します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| substringOutput | String | two |


<a id="take" />

## <a name="take"></a>take
`take(originalValue, numberToTake)`

文字列の先頭から指定した数の文字を含む文字列を、または配列の先頭から指定した数の要素を含む配列を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| originalValue |はい |配列または文字列 |要素の取得元となる配列または文字列。 |
| numberToTake |あり |int |取得する要素または文字の数。 この値が 0 以下である場合、空の配列または文字列が返されます。 指定された配列または文字列の長さを超える場合は、その配列または文字列のすべての要素が返されます。 |

### <a name="return-value"></a>戻り値

配列または文字列。

### <a name="examples"></a>例

次の例では、指定した数の要素を配列から取得し、指定した数の文字を文字列から取得します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| arrayOutput | array | ["one", "two"] |
| stringOutput | String | on |

<a id="tolower" />

## <a name="tolower"></a>toLower
`toLower(stringToChange)`

指定された文字列を小文字に変換します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| stringToChange |あり |string |小文字に変換する値。 |

### <a name="return-value"></a>戻り値

小文字に変換された文字列。

### <a name="examples"></a>例

次の例では、パラメーター値を小文字と大文字に変換します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

<a id="toupper" />

## <a name="toupper"></a>toUpper
`toUpper(stringToChange)`

指定した文字列を大文字に変換します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| stringToChange |あり |string |大文字に変換する値。 |

### <a name="return-value"></a>戻り値

大文字に変換された文字列。

### <a name="examples"></a>例

次の例では、パラメーター値を小文字と大文字に変換します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

<a id="trim" />

## <a name="trim"></a>trim
`trim (stringToTrim)`

指定された文字列から先頭と末尾の空白文字をすべて削除します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| stringToTrim |はい |string |トリムする値。 |

### <a name="return-value"></a>戻り値

先頭および末尾の空白文字なし文字列です。

### <a name="examples"></a>例

次の例では、パラメーターから空白文字を削除します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| 戻り値 | String | one two three |

<a id="uniquestring" />

## <a name="uniquestring"></a>uniqueString
`uniqueString (baseString, ...)`

パラメーターとして渡された値に基づいて、決定論的ハッシュ文字列を作成します。 

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| baseString |あり |string |一意の文字列を作成するためにハッシュ関数で使用される値。 |
| 必要に応じて追加のパラメーター |なし |string |文字列をいくつでも追加して、一意性のレベルを指定する値を作成できます。 |

### <a name="remarks"></a>解説

この関数は、リソースの一意の名前を作成する必要がある場合に便利です。 結果の一意性のスコープを制限するパラメーターの値を指定します。 サブスクリプション、リソース グループ、またはデプロイのレベルで名前が一意であるかどうかを指定できます。 

返される値はランダムな文字列ではなく、ハッシュ関数の結果になります。 返される値は、13 文字です。 グローバルに一意ではありません。 命名規則にあるプレフィックスをこの値と組み合わせて、わかりやすい名前を作成することもできます。 次の例では、戻り値の形式を示します。 実際の値は、指定されたパラメーターによって異なります。

    tcvhiyu5h2o5o

次の例は、uniqueString を使用して、よく使用されるレベルで一意の値を作成する方法を示しています。

サブスクリプションのスコープで一意

```json
"[uniqueString(subscription().subscriptionId)]"
```

リソース グループのスコープで一意

```json
"[uniqueString(resourceGroup().id)]"
```

リソース グループのデプロイのスコープで一意

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

次の例は、リソース グループに基づいてストレージ アカウントの一意の名前を作成する方法を示しています。 リソース グループ内で、同じ方法で名前が作成されると、名前は一意ではなくなります。

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

### <a name="return-value"></a>戻り値

13 文字が含まれる文字列。

### <a name="examples"></a>例

次の例は、uniquestring から結果を返します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

<a id="uri" />

## <a name="uri"></a>uri
`uri (baseUri, relativeUri)`

baseUri と relativeUri の文字列を組み合わせることにより、絶対 URI を作成します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| baseUri |はい |string |ベース URI 文字列。 |
| relativeUri |あり |string |ベース URI 文字列に追加する相対 URI 文字列。 |

**baseUri** パラメーターの値には、特定のファイルを含めることができますが、URI の作成時には基本パスだけが使用されます。 たとえば、baseUri パラメーターとして `http://contoso.com/resources/azuredeploy.json` を渡すと、`http://contoso.com/resources/` というベース URI が作成されます。

### <a name="return-value"></a>戻り値

ベース値および相対値の絶対 URI を表す文字列。

### <a name="examples"></a>例

次の例は、親テンプレートの値に基づいて、入れ子になったテンプレートへのリンクを作成する方法を示しています。

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

次の例では、uri、uriComponent、および uriComponentToString を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |

<a id="uricomponent" />

## <a name="uricomponent"></a>uriComponent
`uricomponent(stringToEncode)`

URI をエンコードします。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| stringToEncode |あり |string |エンコード対象の値。 |

### <a name="return-value"></a>戻り値

URI エンコードされた値の文字列。

### <a name="examples"></a>例

次の例では、uri、uriComponent、および uriComponentToString を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |


<a id="uricomponenttostring" />

## <a name="uricomponenttostring"></a>uriComponentToString
`uriComponentToString(uriEncodedString)`

URI エンコードされた値の文字列を返します。

### <a name="parameters"></a>parameters

| パラメーターが含まれる必要があります。 | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| uriEncodedString |はい |string |文字列に変換する URI エンコードされた値。 |

### <a name="return-value"></a>戻り値

URI エンコードされた値のデコード済み文字列。

### <a name="examples"></a>例

次の例では、uri、uriComponent、および uriComponentToString を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | 型 | 値 |
| ---- | ---- | ----- |
| uriOutput | String | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | String | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | String | http://contoso.com/resources/nested/azuredeploy.json |


## <a name="next-steps"></a>次のステップ
* Azure Resource Manager テンプレートのセクションの説明については、[Azure Resource Manager テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* 複数のテンプレートをマージするには、[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)に関するページを参照してください。
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。
* 作成したテンプレートをデプロイする方法を確認するには、[Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。


