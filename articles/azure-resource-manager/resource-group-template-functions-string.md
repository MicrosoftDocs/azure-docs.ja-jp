---
title: Azure Resource Manager テンプレートの関数 - 文字列 | Microsoft Docs
description: Azure Resource Manager テンプレートで、文字列を操作するために使用する関数について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: tomfitz
ms.openlocfilehash: c30bb47f3f35663a6ffcfc0126758eb82c9dec4e
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194779"
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
* [format](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](#startswith)
* [string](#string)
* [substring](#substring)
* [take](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)
* [utcNow](#utcnow)

## <a name="base64"></a>base64

`base64(inputString)`

入力文字列の base64 表現を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| inputString |はい |string |Base 64 形式として返す値。 |

### <a name="return-value"></a>戻り値

base64 形式を含む文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json)では、base64 関数を使用する方法を示します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| base64Output | string | b25lLCB0d28sIHRocmVl |
| toStringOutput | string | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

base64 形式を JSON オブジェクトに変換します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| base64Value |はい |string |JSON オブジェクトに変換する base64 形式。 |

### <a name="return-value"></a>戻り値

JSON オブジェクト。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json)では、base64ToJson 関数を使用して base64 値を変換します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| base64Output | string | b25lLCB0d28sIHRocmVl |
| toStringOutput | string | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

base64 形式を文字列に変換します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| base64Value |はい |string |文字列に変換する base64 形式。 |

### <a name="return-value"></a>戻り値

変換された base64 値の文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json)では、base64ToString 関数を使用して base64 値を変換します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| base64Output | string | b25lLCB0d28sIHRocmVl |
| toStringOutput | string | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

複数の文字列値を結合して連結された文字列を返します。または複数の配列を結合して連結された配列を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |文字列または配列 |連結の最初の値。 |
| 残りの引数 |いいえ |string |連結する順の追加の値。 |

### <a name="return-value"></a>戻り値
連結された値の文字列または配列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json)は、2 つの文字列値を結合して 1 つの連結文字列を返す方法を示しています。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| concatOutput | string | prefix-5yj4yjf5mbg72 |

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json)では、2 つの配列を結合する方法を示します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| return | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>contains

`contains (container, itemToFind)`

配列に値が含まれるかどうか、オブジェクトにキーが含まれるかどうか、または文字列に部分文字列が含まれるかどうかを確認します。 文字列比較では大文字・小文字を区別します。 ただし、オブジェクトにキーが含まれているかどうかをテストする場合、比較で大文字・小文字を区別しません。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| container |はい |配列、オブジェクト、文字列 |検索対象の値を含む値。 |
| itemToFind |はい |文字列または整数 |検索対象の値。 |

### <a name="return-value"></a>戻り値

項目が見つかった場合は **True**、それ以外の場合は **False** です。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json)では、contains をさまざまな型で使用する方法を示します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

値をデータ URI に変換します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToConvert |はい |string |データ URI に変換する値。 |

### <a name="return-value"></a>戻り値

データ URI として書式設定された文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json)では、値をデータ URI に変換し、データ URI を文字列に変換します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| dataUriOutput | string | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | string | Hello, World! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

データ URI の形式で書式設定された値を文字列に変換します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |はい |string |変換するデータ URI 値。 |

### <a name="return-value"></a>戻り値

変換後の値を含む文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json)では、値をデータ URI に変換し、データ URI を文字列に変換します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| dataUriOutput | string | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | string | Hello, World! |

## <a name="empty"></a>empty

`empty(itemToTest)`

配列、オブジェクト、または文字列が空かどうかを判断します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| itemToTest |はい |配列、オブジェクト、文字列 |空かどうかを確認する値。 |

### <a name="return-value"></a>戻り値

値が空の場合は **True** を、値が空でない場合は **False** を返します。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json)では、配列、オブジェクト、および文字列が空かどうかを確認します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

文字列が特定の値で終わるかどうかを判断します。 比較では大文字と小文字は区別されません。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |はい |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

文字列の最後の文字または文字列が値に一致した場合は **True**、それ以外の場合は **False**。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json)は、startsWith 関数と endsWith 関数の使用方法を示しています。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>first

`first(arg1)`

文字列の最初の文字、または配列の最初の要素を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |最初の要素または文字を取得する値。 |

### <a name="return-value"></a>戻り値

文字列の最初の文字、または配列の最初の要素の型 (文字列、整数、配列、またはオブジェクト)。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json)では、first 関数を配列および文字列と共に使用する方法を示します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | string | one |
| stringOutput | string | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

入力値から書式設定された文字列を作成します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| formatString | はい | string | 複合の書式設定文字列。 |
| arg1 | はい | 文字列、整数、またはブール値 | 書式設定された文字列に含める値。 |
| 残りの引数 | いいえ | 文字列、整数、またはブール値 | 書式設定された文字列に含める追加の値。 |

### <a name="remarks"></a>解説

この関数を使用して、テンプレート内の文字列を書式設定します。 .NET 内の[System.String.Format](/dotnet/api/system.string.format) メソッドと同じ書式設定オプションを使用します。

### <a name="examples"></a>例

次のテンプレート例に、書式設定の関数を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "greeting": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "name": {
            "type": "string",
            "defaultValue": "User"
        },
        "numberToFormat": {
            "type": "int",
            "defaultValue": 8175133
        }
    },
    "resources": [
    ],
    "outputs": {
        "formatTest": {
            "type": "string",
            "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| formatTest | string | Hello, User. Formatted number:8,175,133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

パラメーターとして指定した値に基づき、グローバル一意識別子の形式で値を作成します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| baseString |はい |string |GUID を作成するためにハッシュ関数で使用される値。 |
| 必要に応じて追加のパラメーター |いいえ |string |文字列をいくつでも追加して、一意性のレベルを指定する値を作成できます。 |

### <a name="remarks"></a>解説

この関数は、グローバル一意識別子の形式で値を作成する必要がある場合に役立ちます。 結果の一意性のスコープを制限するパラメーターの値を指定します。 サブスクリプション、リソース グループ、またはデプロイのレベルで名前が一意であるかどうかを指定できます。

返される値はランダムな文字列ではなく、パラメーターに対するハッシュ関数の結果になります。 返される値は、36 文字です。 グローバルに一意ではありません。 パラメーターのそのハッシュ値に基づかない新しい GUID を作成するには、[newGuid](#newguid) 関数を使用します。

次の例は、guid を使用して、よく使用されるレベルで一意の値を作成する方法を示しています。

サブスクリプションのスコープで一意

```json
"[guid(subscription().subscriptionId)]"
```

リソース グループのスコープで一意

```json
"[guid(resourceGroup().id)]"
```

リソース グループのデプロイのスコープで一意

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>戻り値

グローバル一意識別子の形式の 36 文字を含む文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json)は、guid から結果を返します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

文字列内の値の最初の位置を返します。 比較では大文字と小文字は区別されません。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |はい |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

検索する項目の位置を表す整数。 値は 0 から始まります。 項目が見つからない場合は、-1 が返されます。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json)は、indexOf 関数と lastIndexOf 関数の使用方法を示しています。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

## <a name="last"></a>last

`last (arg1)`

文字列の最後の文字、または配列の最後の要素を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |配列または文字列 |最後の要素または文字を取得する値。 |

### <a name="return-value"></a>戻り値

文字列の最後の文字、または配列の最後の要素の型 (文字列、整数、配列、またはオブジェクト)。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json)では、last 関数を配列および文字列と共に使用する方法を示します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | string | three |
| stringOutput | string | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

文字列内の値の最後の位置を返します。 比較では大文字と小文字は区別されません。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |はい |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

検索する項目の最後の位置を表す整数。 値は 0 から始まります。 項目が見つからない場合は、-1 が返されます。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json)は、indexOf 関数と lastIndexOf 関数の使用方法を示しています。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| notFound | int | -1 |

## <a name="length"></a>length

`length(string)`

文字列内の文字、配列内の要素、またはオブジェクト内のルート レベル プロパティの数を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |array、string、または object |要素の数を取得するために使用する配列、文字の数を取得するために使用する文字列、またはルート レベル プロパティの数を取得するために使用するオブジェクト。 |

### <a name="return-value"></a>戻り値

整数。 

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json)では、length を配列および文字列と共に使用する方法を示します。

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
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "propA": "one",
                "propB": "two",
                "propC": "three",
                "propD": {
                    "propD-1": "sub",
                    "propD-2": "sub"
                }
            }
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
        },
        "objectLength": {
            "type": "int",
            "value": "[length(parameters('objectToTest'))]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |
| objectLength | int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

グローバル一意識別子の形式の値を返します。 **この関数は、パラメーターの既定値でのみ使用できます。**

### <a name="remarks"></a>解説

この関数は、パラメーターの既定値に対する式の中でのみ使用できます。 この関数をテンプレートのその他の場所で使用すると、エラーが返されます。 テンプレートの他の場所でこの関数を使用することは、呼び出しのたびに異なる値が返されるため、許可されていません。 同じパラメーターで同じテンプレートをデプロイしても、同じ結果が生成される保証はありません。

newGuid 関数は、パラメーターを受け取らない点が [guid](#guid) 関数と異なります。 guid では、同じパラメーターを使用して呼び出すと、毎回同じ識別子が返されます。 特定の環境に対して同じ GUID を確実に生成する必要がある場合は、guid を使用してください。 テスト環境へのリソースのデプロイなど、毎回異なる識別子が必要なときは、newGuid を使用してください。

[以前の正常なデプロイを再デプロイするオプション](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails)を使用し、以前のデプロイに newGuid を使用するパラメーターが含まれている場合、パラメーターは再評価されません。 代わりに、以前のデプロイのパラメーター値が、ロールバック デプロイで自動的に再利用されます。

テスト環境では、短時間だけ存在するリソースを繰り返しデプロイすることが必要な場合があります。 一意の名前を構築するのではなく、[uniqueString](#uniquestring) で newGuid を使用して一意の名前を作成できます。

既定値に対する newGuid 関数に依存するテンプレートを再デプロイするときは注意が必要です。 再デプロイを行うときに、パラメーターの値を指定しないと、関数が再評価されます。 新しいリソースを作成するのではなく、既存のリソースを更新する場合は、以前のデプロイのパラメーター値を渡します。

### <a name="return-value"></a>戻り値

グローバル一意識別子の形式の 36 文字を含む文字列。

### <a name="examples"></a>例

次の例のテンプレートでは、新しい識別子を使用するパラメーターを示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "guidOutput": {
            "type": "string",
            "value": "[parameters('guidValue')]"
        }
    }
}
```

前の例からの出力はデプロイごとに変わりますが、次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

次の例では、newGuid 関数を使用して、ストレージ アカウントの一意の名前を作成します。 このテンプレートは、ストレージ アカウントが短時間だけ存在し、再デプロイされないテスト環境に適しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "variables": {
        "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "West US",
            "apiVersion": "2018-07-01",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "nameOutput": {
            "type": "string",
            "value": "[variables('storageName')]"
        }
    }
}
```

前の例からの出力はデプロイごとに変わりますが、次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

指定された長さに到達するまで左側に文字を追加していくことで、右揃えの文字列を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| valueToPad |はい |文字列または整数 |右揃えにする値。 |
| totalLength |はい |int |返される文字列の文字合計数。 |
| paddingCharacter |いいえ |1 文字 |左余白の長さに到達するまで使用する文字。 既定値は空白です。 |

元の文字列が、埋め込まれる文字数よりも長い場合は、文字が追加されません。

### <a name="return-value"></a>戻り値

少なくとも指定した文字数の文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json)では、文字の合計数に達するまでゼロ文字を追加することで、ユーザー指定のパラメーター値を埋め込む方法を示します。 

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| stringOutput | string | 0000000123 |

## <a name="replace"></a>replace

`replace(originalString, oldString, newString)`

ある文字列のすべてのインスタンスを別の文字列で置き換えた、新しい文字列を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| originalString |はい |string |別の文字列で置き換えられる文字列の全インスタンスを含む値。 |
| oldString |はい |string |元の文字列から削除する文字列。 |
| newString |はい |string |削除された文字列の代わりに追加する文字列。 |

### <a name="return-value"></a>戻り値

文字が置き換えられた文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json)では、ユーザーが指定した文字列からすべてのダッシュを削除し、その文字列の部分を別の文字列に置き換える方法を示します。

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
        "secondOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| firstOutput | string | 1231231234 |
| secondOutput | string | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

指定した文字数の後にあるすべての文字を含む文字列を返します。または、指定した数の要素の後にあるすべての要素を含む配列を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| originalValue |はい |配列または文字列 |スキップ対象の配列または文字列。 |
| numberToSkip |はい |int |スキップする要素または文字の数。 この値が 0 以下である場合は、値内のすべての要素または文字が返されます。 配列または文字列の長さを超過している場合は、空の配列または文字列が返されます。 |

### <a name="return-value"></a>戻り値

配列または文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json)では、配列内の指定した数の要素と、文字列内の指定した数の文字をスキップします。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | string | two three |

## <a name="split"></a>split

`split(inputString, delimiter)`

指定された区切り記号で区切られた、入力文字列の部分文字列が格納されている、文字列の配列を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| inputString |はい |string |分割する文字列。 |
| delimiter |はい |文字列または文字列の配列 |文字列の分割に使用する区切り記号。 |

### <a name="return-value"></a>戻り値

文字列の配列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json)では、入力文字列をコンマで分割したり、コンマまたはセミコロンで分割したりします。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| firstOutput | Array | ["one", "two", "three"] |
| secondOutput | Array | ["one", "two", "three"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

文字列が特定の値で始まるかどうかを判断します。 比較では大文字と小文字は区別されません。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToSearch |はい |string |検索対象の項目を含む値。 |
| stringToFind |はい |string |検索対象の値。 |

### <a name="return-value"></a>戻り値

文字列の最初の文字または文字列が値に一致した場合は **True**、それ以外の場合は **False**。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json)は、startsWith 関数と endsWith 関数の使用方法を示しています。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>string

`string(valueToConvert)`

指定された値を文字列に変換します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| valueToConvert |はい | Any |文字列に変換する値。 オブジェクトと配列を含む、あらゆる種類の値を変換できます。 |

### <a name="return-value"></a>戻り値

変換された値の文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json)では、さまざまな型の値を文字列に変換する方法を示します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| objectOutput | string | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | string | ["a","b","c"] |
| intOutput | string | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

指定した文字位置から始まる指定された文字数分の部分文字列を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToParse |はい |string |部分文字列の抽出元となる文字列。 |
| startIndex |いいえ |int |部分文字列の 0 から始まる開始文字位置。 |
| length |いいえ |int |部分文字列の文字数。 文字列内の場所を参照する必要があります。 0 以上である必要があります。 |

### <a name="return-value"></a>戻り値

部分文字列。 または、長さが 0 の場合は空の文字列。

### <a name="remarks"></a>解説

この関数は、部分文字列が文字列の最後を超えるか、または長さが 0 未満のときは失敗します。 次の例は、"インデックス パラメーターと長さパラメーターは文字列内の場所を参照している必要があります。 インデックス パラメーター:'0'、長さパラメーター:'11'、文字列パラメーターの長さ:'10'。" というエラーで失敗します。

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json)では、パラメーターから部分文字列を抽出します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| substringOutput | string | two |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

文字列の先頭から指定した数の文字を含む文字列を、または配列の先頭から指定した数の要素を含む配列を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| originalValue |はい |配列または文字列 |要素の取得元となる配列または文字列。 |
| numberToTake |はい |int |取得する要素または文字の数。 この値が 0 以下である場合、空の配列または文字列が返されます。 指定された配列または文字列の長さを超過している場合は、その配列または文字列のすべての要素が返されます。 |

### <a name="return-value"></a>戻り値

配列または文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json)では、指定した数の要素を配列から取得し、指定した数の文字を文字列から取得します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | string | on |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

指定された文字列を小文字に変換します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToChange |はい |string |小文字に変換する値。 |

### <a name="return-value"></a>戻り値

小文字に変換された文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json)では、パラメーター値を小文字と大文字に変換します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| toLowerOutput | string | one two three |
| toUpperOutput | string | ONE TWO THREE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

指定した文字列を大文字に変換します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToChange |はい |string |大文字に変換する値。 |

### <a name="return-value"></a>戻り値

大文字に変換された文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json)では、パラメーター値を小文字と大文字に変換します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| toLowerOutput | string | one two three |
| toUpperOutput | string | ONE TWO THREE |

## <a name="trim"></a>trim

`trim (stringToTrim)`

指定された文字列から先頭と末尾の空白文字をすべて削除します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToTrim |はい |string |トリムする値。 |

### <a name="return-value"></a>戻り値

先頭および末尾の空白文字なし文字列です。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json)では、パラメーターから空白文字を削除します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| 戻り値 | string | one two three |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

パラメーターとして渡された値に基づいて、決定論的ハッシュ文字列を作成します。 

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| baseString |はい |string |一意の文字列を作成するためにハッシュ関数で使用される値。 |
| 必要に応じて追加のパラメーター |いいえ |string |文字列をいくつでも追加して、一意性のレベルを指定する値を作成できます。 |

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

次の例は、リソース グループに基づいてストレージ アカウントの一意の名前を作成する方法を示しています。 リソース グループ内では、名前は、同じ方法で作成されると一意ではなくなります。

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

テンプレートをデプロイするたびに一意の新しい名前を作成する必要があり、リソースを更新しない場合は、[utcNow](#utcnow) 関数と共に uniqueString を使用できます。 この方法は、テスト環境で使用できます。 例については、「[utcNow](#utcnow)」をご覧ください。

### <a name="return-value"></a>戻り値

13 文字が含まれる文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json)は、uniquestring から結果を返します。

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

## <a name="uri"></a>uri

`uri (baseUri, relativeUri)`

baseUri と relativeUri の文字列を組み合わせることにより、絶対 URI を作成します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| baseUri |はい |string |ベース URI 文字列。 |
| relativeUri |はい |string |ベース URI 文字列に追加する相対 URI 文字列。 |

**baseUri** パラメーターの値には、特定のファイルを含めることができますが、URI の作成時には基本パスだけが使用されます。 たとえば、baseUri パラメーターとして `http://contoso.com/resources/azuredeploy.json` を渡すと、`http://contoso.com/resources/` というベース URI が作成されます。

### <a name="return-value"></a>戻り値

ベース値および相対値の絶対 URI を表す文字列。

### <a name="examples"></a>例

次の例は、親テンプレートの値に基づいて、入れ子になったテンプレートへのリンクを作成する方法を示しています。

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json)では、uri、uriComponent、および uriComponentToString を使用する方法を示します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| uriOutput | string | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | string | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | string | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

URI をエンコードします。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| stringToEncode |はい |string |エンコード対象の値。 |

### <a name="return-value"></a>戻り値

URI エンコードされた値の文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json)では、uri、uriComponent、および uriComponentToString を使用する方法を示します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| uriOutput | string | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | string | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | string | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

URI エンコードされた値の文字列を返します。

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| uriEncodedString |はい |string |文字列に変換する URI エンコードされた値。 |

### <a name="return-value"></a>戻り値

URI エンコードされた値のデコード済み文字列。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json)では、uri、uriComponent、および uriComponentToString を使用する方法を示します。

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

| Name | Type | 値 |
| ---- | ---- | ----- |
| uriOutput | string | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | string | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | string | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="utcnow"></a>utcNow

`utcNow(format)`

指定された形式で現在 (UTC) の datetime 値を返します。 形式が指定されていない場合は、ISO 8601 (yyyyMMddTHHmmssZ) 形式を使用します。 **この関数は、パラメーターの既定値でのみ使用できます。**

### <a name="parameters"></a>parameters

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| format |いいえ |string |文字列に変換する URI エンコードされた値。 [標準書式指定文字列](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式指定文字列](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)を使用します。 |

### <a name="remarks"></a>解説

この関数は、パラメーターの既定値に対する式の中でのみ使用できます。 この関数をテンプレートのその他の場所で使用すると、エラーが返されます。 テンプレートの他の場所でこの関数を使用することは、呼び出しのたびに異なる値が返されるため、許可されていません。 同じパラメーターで同じテンプレートをデプロイしても、同じ結果が生成される保証はありません。

[以前の正常なデプロイを再デプロイするオプション](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails)を使用し、以前のデプロイに utcNow を使用するパラメーターが含まれている場合、パラメーターは再評価されません。 代わりに、以前のデプロイのパラメーター値が、ロールバック デプロイで自動的に再利用されます。

既定値に対する utcNow 関数に依存するテンプレートを再デプロイするときは注意が必要です。 再デプロイを行うときに、パラメーターの値を指定しないと、関数が再評価されます。 新しいリソースを作成するのではなく、既存のリソースを更新する場合は、以前のデプロイのパラメーター値を渡します。

### <a name="return-value"></a>戻り値

現在の UTC の datetime 値。

### <a name="examples"></a>例

次の例のテンプレートでは、datetime 値のさまざまな形式を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

前の例からの出力はデプロイごとに変わりますが、次のようになります。

| Name | Type | 値 |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

次の例では、タグ値を設定するときに関数からの値を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>次の手順
* Azure Resource Manager テンプレートのセクションの説明については、[Azure Resource Manager テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* 複数のテンプレートをマージするには、[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)に関するページを参照してください。
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。
* 作成したテンプレートをデプロイする方法を確認するには、[Azure Resource Manager のテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)に関するページを参照してください。

