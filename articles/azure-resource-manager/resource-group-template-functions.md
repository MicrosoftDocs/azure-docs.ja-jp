---
title: "リソース マネージャーのテンプレートの関数 | Microsoft Docs"
description: "値の取得、文字列の処理、デプロイ情報の取得のために、Azure リソース マネージャーのテンプレートで使用する関数について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0644abe1-abaa-443d-820d-1966d7d26bfd
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 1ed23fc5a69cea70636de8b18911c1b11119d3a3
ms.lasthandoff: 03/22/2017


---
# <a name="azure-resource-manager-template-functions"></a>Azure リソース マネージャーのテンプレートの関数
このトピックでは、Azure Resource Manager テンプレートで使用できるすべての関数について説明します。

テンプレート関数とそのパラメーターでは大文字と小文字が区別されません。 たとえば、Resource Manager では、**variables('var1')** と **VARIABLES('VAR1')** が同じものとして解決されます。 評価の際、関数は、大文字/小文字を明確に変更する (toUpper、toLower など) 場合を除き、大文字/小文字を保持します。 特定のリソースの種類では、関数の評価方法とは無関係に、大文字/小文字の要件が存在する場合があります。

## <a name="numeric-functions"></a>数値関数
リソース マネージャーには、整数を操作する次の関数が用意されています。

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [int](#int)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

<a id="add" />

### <a name="add"></a>追加
`add(operand1, operand2)`

指定された 2 つ整数の合計を返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- | 
|operand1 |あり |整数 |加算する最初の整数。 |
|operand2 |はい |整数 |加算する 2 つ目の整数。 |

次の例では、2 つのパラメーターを加算します。

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "First integer to add"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Second integer to add"
    }
  }
},
...
"outputs": {
  "addResult": {
    "type": "int",
    "value": "[add(parameters('first'), parameters('second'))]"
  }
}
```

<a id="copyindex" />

### <a name="copyindex"></a>copyIndex
`copyIndex(offset)`

反復処理のループのインデックスを返します。 

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| offset |なし |整数 |0 から始まる反復値に追加する整数。 |

この関数は常に **copy** オブジェクトと共に使用されます。 **offset** の値が指定されていない場合、現在の反復値が返されます。 反復値は 0 から始まります。 **copyIndex**の使用方法の詳細については、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。

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

<a id="div" />

### <a name="div"></a>div
`div(operand1, operand2)`

指定された 2 つの整数の整数除算を返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| operand1 |あり |整数 |除算される整数。 |
| operand2 |はい |整数 |除算に使用される整数。 0 にすることはできません。 |

次の例では、一方のパラメーターをもう一方のパラメーターで除算します。

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "Integer being divided"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Integer used to divide"
    }
  }
},
...
"outputs": {
  "divResult": {
    "type": "int",
    "value": "[div(parameters('first'), parameters('second'))]"
  }
}
```

<a id="int" />

### <a name="int"></a>int
`int(valueToConvert)`

指定された値を整数に変換します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |はい |文字列または整数 |整数に変換する値。 |

次の例では、ユーザー指定のパラメーター値を整数に変換します。

```json
"parameters": {
    "appId": { "type": "string" }
},
"variables": { 
    "intValue": "[int(parameters('appId'))]"
}
```

<a id="mod" />

### <a name="mod"></a>mod
`mod(operand1, operand2)`

指定された 2 つの整数を使用した整数除算の剰余を返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| operand1 |あり |整数 |除算される整数。 |
| operand2 |はい |整数 |除算に使用される整数。0 にすることはできません。 |

次の例では、一方のパラメーターをもう一方のパラメーターで除算した剰余を返します。

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "Integer being divided"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Integer used to divide"
    }
  }
},
...
"outputs": {
  "modResult": {
    "type": "int",
    "value": "[mod(parameters('first'), parameters('second'))]"
  }
}
```

<a id="mul" />

### <a name="mul"></a>mul
`mul(operand1, operand2)`

指定された 2 つの整数の乗算を返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| operand1 |あり |整数 |乗算する最初の整数。 |
| operand2 |はい |整数 |乗算する 2 つ目の整数。 |

次の例では、一方のパラメーターをもう一方のパラメーターで乗算します。

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "First integer to multiply"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Second integer to multiply"
    }
  }
},
...
"outputs": {
  "mulResult": {
    "type": "int",
    "value": "[mul(parameters('first'), parameters('second'))]"
  }
}
```

<a id="sub" />

### <a name="sub"></a>sub
`sub(operand1, operand2)`

指定された 2 つの整数の減算を返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| operand1 |あり |整数 |減算される整数。 |
| operand2 |はい |整数 |減算する整数。 |

次の例では、一方のパラメーターからもう一方のパラメーターを減算します。

```json
"parameters": {
  "first": {
    "type": "int",
    "metadata": {
      "description": "Integer subtracted from"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Integer to subtract"
    }
  }
},
...
"outputs": {
  "subResult": {
    "type": "int",
    "value": "[sub(parameters('first'), parameters('second'))]"
  }
}
```

## <a name="string-functions"></a>文字列関数
リソース マネージャーには、文字列を操作する次の関数が用意されています。

* [base64](#base64)
* [concat](#concat)
* [length](#lengthstring)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skipstring)
* [split](#split)
* [string](#string)
* [substring](#substring)
* [take](#takestring)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)

<a id="base64" />

### <a name="base64"></a>base64
`base64 (inputString)`

入力文字列の base64 表現を返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| inputString |はい |String |Base 64 形式として返す値。 |

次の例では、base64 関数を使用する方法を示します。

```json
"variables": {
  "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
  "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

<a id="concat" />

### <a name="concat---string"></a>concat - 文字列
`concat (string1, string2, string3, ...)`

複数の文字列値を結合し、連結された文字列を返します。 

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| string1 |はい |string |連結の最初の値。 |
| 追加の文字列 |なし |String |連結する順の追加の値。 |

この関数は、任意の数の引数を取ることができ、パラメーターに文字列または配列を使用できます。 配列を連結する例については、「 [concat - 配列](#concatarray)」を参照してください。

次の例は、複数の文字列値を結合して 1 つの連結文字列を返す方法を示しています。

```json
"outputs": {
    "siteUri": {
      "type": "string",
      "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
    }
}
```

<a id="lengthstring" />

### <a name="length---string"></a>length - 文字列
`length(string)`

文字列の文字数を返します。

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| string |あり |String |文字数の取得に使用する値。 |

配列に対して length を使用する例については、「 [length - 配列](#length)」を参照してください。

次の例では、文字列の文字数を返します。 

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "nameLength": "[length(parameters('appName'))]"
}
```

<a id="padleft" />

### <a name="padleft"></a>padLeft
`padLeft(valueToPad, totalLength, paddingCharacter)`

指定された長さに到達するまで左側に文字を追加していくことで、右揃えの文字列を返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| valueToPad |あり |文字列または整数 |右揃えにする値。 |
| totalLength |はい |整数 |返される文字列の文字合計数。 |
| paddingCharacter |いいえ |1 文字 |左余白の長さに到達するまで使用する文字。 既定値は空白です。 |

次の例では、文字列が 10 文字に達するまでゼロ文字を追加することで、ユーザー指定のパラメーター値に埋め込む方法を示します。 元のパラメーター値が 10 文字より長い場合、文字は追加されません。

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
}
```

<a id="replace" />

### <a name="replace"></a>replace
`replace(originalString, oldCharacter, newCharacter)`

指定された文字列内で、1 文字を別の文字で置き換えたすべてのインスタンスを含む新しい文字列を返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| originalString |はい |String |ある文字のすべてのインスタンスが別の文字に置き換えられた値。 |
| oldCharacter |あり |string |元の文字列から削除する文字。 |
| newCharacter |あり |String |削除された文字の代わりに追加する文字。 |

次の例では、ユーザーが指定した文字列からすべてのダッシュ (-) を削除する方法を示します。

```json
"parameters": {
    "identifier": { "type": "string" }
},
"variables": { 
    "newidentifier": "[replace(parameters('identifier'),'-','')]"
}
```

<a id="skipstring" />

### <a name="skip---string"></a>skip - 文字列
`skip(originalValue, numberToSkip)`

文字列内の指定した位置より後ろにある文字をすべて含む文字列を返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| originalValue |はい |String |スキップの対象の文字列。 |
| numberToSkip |はい |整数 |スキップする文字数。 この値が 0 以下である場合は、文字列内のすべての文字が返されます。 文字列の長さを超える場合は、空の文字列が返されます。 |

配列に対して skip を使用する例については、「 [skip - 配列](#skip)」を参照してください。

次の例では、文字列内で指定した数の文字をスキップします。

```json
"parameters": {
  "first": {
    "type": "string",
    "metadata": {
      "description": "Value to use for skipping"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of characters to skip"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "string",
    "value": "[skip(parameters('first'),parameters('second'))]"
  }
}
```

<a id="split" />

### <a name="split"></a>split
`split(inputString, delimiterString)`

`split(inputString, delimiterArray)`

指定された区切り記号で区切られた、入力文字列の部分文字列が格納されている、文字列の配列を返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| inputString |はい |string |分割する文字列。 |
| delimiter |はい |文字列または文字列の配列 |文字列の分割に使用する区切り記号。 |

次の例では、入力文字列をコンマで分割します。

```json
"parameters": {
    "inputString": { "type": "string" }
},
"variables": { 
    "stringPieces": "[split(parameters('inputString'), ',')]"
}
```

次の例では、入力文字列をコンマまたはセミコロンで分割します。

```json
"variables": {
  "stringToSplit": "test1,test2;test3",
  "delimiters": [ ",", ";" ]
},
"resources": [ ],
"outputs": {
  "exampleOutput": {
    "value": "[split(variables('stringToSplit'), variables('delimiters'))]",
    "type": "array"
  }
}
```

<a id="string" />

### <a name="string"></a>string
`string(valueToConvert)`

指定された値を文字列に変換します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |はい | 任意 |文字列に変換する値。 オブジェクトと配列を含む、あらゆる種類の値を変換できます。 |

次の例では、ユーザー指定のパラメーター値を文字列に変換します。

```json
"parameters": {
  "jsonObject": {
    "type": "object",
    "defaultValue": {
      "valueA": 10,
      "valueB": "Example Text"
    }
  },
  "jsonArray": {
    "type": "array",
    "defaultValue": [ "a", "b", "c" ]
  },
  "jsonInt": {
    "type": "int",
    "defaultValue": 5
  }
},
"variables": { 
  "objectString": "[string(parameters('jsonObject'))]",
  "arrayString": "[string(parameters('jsonArray'))]",
  "intString": "[string(parameters('jsonInt'))]"
}
```

<a id="substring" />

### <a name="substring"></a>substring
`substring(stringToParse, startIndex, length)`

指定した文字位置から始まる指定された文字数分の部分文字列を返します。

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| stringToParse |あり |String |部分文字列の抽出元となる文字列。 |
| startIndex |いいえ |整数 |部分文字列の 0 から始まる開始文字位置。 |
| length |なし |整数 |部分文字列の文字数。 文字列内の場所を参照する必要があります。 |

次の例では、パラメーターから先頭の 3 文字を抽出します。

```json
"parameters": {
    "inputString": { "type": "string" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 3)]"
}
```

次の例は、"インデックス パラメーターと長さパラメーターは文字列内の場所を参照している必要があります。 インデックス パラメーター: '{0}'、長さパラメーター: '11'、文字列長パラメーター: '10'" というエラーで失敗します。

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

<a id="takestring" />

### <a name="take---string"></a>take - 文字列
`take(originalValue, numberToTake)`

文字列の先頭から指定された数の文字を含む文字列を返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| originalValue |はい |String |文字の取得元となる値。 |
| numberToTake |あり |整数 |取得する文字数。 この値が 0 以下である場合、空の文字列が返されます。 指定された文字列の長さを超える場合、その文字列のすべての文字が返されます。 |

配列に対して take を使用する例については、「 [take - 配列](#take)」を参照してください。

次の例では、文字列から指定された数の文字を取得します。

```json
"parameters": {
  "first": {
    "type": "string",
    "metadata": {
      "description": "Value to use for taking"
    }
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of characters to take"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "string",
    "value": "[take(parameters('first'), parameters('second'))]"
  }
}
```

<a id="tolower" />

### <a name="tolower"></a>toLower
`toLower(stringToChange)`

指定された文字列を小文字に変換します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| stringToChange |あり |String |小文字に変換する値。 |

次の例では、ユーザー指定のパラメーター値を小文字に変換します。

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "lowerCaseAppName": "[toLower(parameters('appName'))]"
}
```

<a id="toupper" />

### <a name="toupper"></a>toUpper
`toUpper(stringToChange)`

指定した文字列を大文字に変換します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| stringToChange |あり |String |大文字に変換する値。 |

次の例では、ユーザー指定のパラメーター値を大文字に変換します。

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "upperCaseAppName": "[toUpper(parameters('appName'))]"
}
```

<a id="trim" />

### <a name="trim"></a>trim
`trim (stringToTrim)`

指定された文字列から先頭と末尾の空白文字をすべて削除します。

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| stringToTrim |はい |String |トリムする値。 |

次の例では、ユーザー指定のパラメーター値から空白文字を削除します。

```json
"parameters": {
    "appName": { "type": "string" }
},
"variables": { 
    "trimAppName": "[trim(parameters('appName'))]"
}
```

<a id="uniquestring" />

### <a name="uniquestring"></a>uniqueString
`uniqueString (baseString, ...)`

パラメーターとして渡された値に基づいて、決定論的ハッシュ文字列を作成します。 

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| baseString |はい |String |一意の文字列を作成するためにハッシュ関数で使用される値。 |
| 必要に応じて追加のパラメーター |なし |String |文字列をいくつでも追加して、一意性のレベルを指定する値を作成できます。 |

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


<a id="uri" />

### <a name="uri"></a>uri
`uri (baseUri, relativeUri)`

baseUri と relativeUri の文字列を組み合わせることにより、絶対 URI を作成します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| baseUri |はい |String |ベース URI 文字列。 |
| relativeUri |はい |String |ベース URI 文字列に追加する相対 URI 文字列。 |

**baseUri** パラメーターの値には、特定のファイルを含めることができますが、URI の作成時には基本パスだけが使用されます。 たとえば、baseUri パラメーターとして `http://contoso.com/resources/azuredeploy.json` を渡すと、`http://contoso.com/resources/` というベース URI が作成されます。

次の例は、親テンプレートの値に基づいて、入れ子になったテンプレートへのリンクを作成する方法を示しています。

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

## <a name="array-functions"></a>配列関数
リソース マネージャーには、配列値を操作する関数がいくつか用意されています。

* [concat](#concatarray)
* [length](#length)
* [skip](#skip)
* [take](#take)

文字列値の配列をある値で区切られた状態にするには、「 [split](#split)」を参照してください。

<a id="concatarray" />

### <a name="concat---array"></a>concat - 配列
`concat (array1, array2, array3, ...)`

複数の配列を結合し、連結された配列を返します。 

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| array1 |はい |array |連結の最初の配列。 |
| 追加の配列 |いいえ |array |連結する順の追加の配列。 |

この関数は、任意の数の引数を取ることができ、パラメーターに文字列または配列を使用できます。 文字列値を連結する例については、「 [concat - 文字列](#concat)」を参照してください。

次の例では、2 つの配列を結合する方法を示します。

```json
"parameters": {
    "firstarray": {
      "type": "array"
    }
    "secondarray": {
      "type": "array"
    }
},
"variables": {
    "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]"
}
```

<a id="length" />

### <a name="length---array"></a>length - 配列
`length(array)`

配列内の要素数を返します。

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| array |はい |array |要素の数を取得する対象の配列。 |

この関数を配列と共に使用して、リソースを作成するときのイテレーション数を指定できます。 次の例では、 **siteNames** パラメーターは、Web サイトの作成時に使用する名前の配列を参照します。

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

この関数を配列と共に使用する方法の詳細については、「 [Azure リソース マネージャーでリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」をご覧ください。 

文字列値に対して length を使用する例については、「 [length - 文字列](#lengthstring)」を参照してください。

<a id="skip" />

### <a name="skip---array"></a>skip - 配列
`skip(originalValue, numberToSkip)`

配列内の指定した位置より後ろにある要素をすべて含む配列を返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| originalValue |はい |array |スキップの対象の配列。 |
| numberToSkip |はい |整数 |スキップする要素数。 この値が 0 以下である場合は、配列内のすべての要素が返されます。 配列の長さを超える場合は、空の配列が返されます。 |

文字列に対して skip を使用する例については、「 [skip - 文字列](#skipstring)」を参照してください。

指定した数の配列要素をスキップする例を次に示します。

```json
"parameters": {
  "first": {
    "type": "array",
    "metadata": {
      "description": "Values to use for skipping"
    },
    "defaultValue": [ "one", "two", "three" ]
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of elements to skip"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "array",
    "value": "[skip(parameters('first'), parameters('second'))]"
  }
}
```

<a id="take" />

### <a name="take---array"></a>take - 配列
`take(originalValue, numberToTake)`

配列の先頭から指定された数の要素を含む配列を返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| originalValue |はい |array |要素の取得元となる配列。 |
| numberToTake |あり |整数 |取得する要素数。 この値が 0 以下である場合は、空の配列が返されます。 指定された配列の長さを超える場合は、その配列のすべての要素が返されます。 |

文字列に対して take を使用する例については、「 [take - 文字列](#takestring)」を参照してください。

指定した数の要素を配列から取得する例を次に示します。

```json
"parameters": {
  "first": {
    "type": "array",
    "metadata": {
      "description": "Values to use for taking"
    },
    "defaultValue": [ "one", "two", "three" ]
  },
  "second": {
    "type": "int",
    "metadata": {
      "description": "Number of elements to take"
    }
  }
},
"resources": [
],
"outputs": {
  "return": {
    "type": "array",
    "value": "[take(parameters('first'),parameters('second'))]"
  }
}
```

## <a name="deployment-value-functions"></a>デプロイの値関数
リソース マネージャーには、テンプレートのセクションから値を取得し、デプロイに関連する値を取得する次の関数が用意されています。

* [デプロイ](#deployment)
* [parameters](#parameters)
* [variables](#variables)

リソース、リソース グループ、サブスクリプションから値を取得する方法については、「 [リソース関数](#resource-functions)」を参照してください。

<a id="deployment" />

### <a name="deployment"></a>deployment
`deployment()`

現在のデプロイ操作に関する情報を返します。

この関数は、デプロイ中に渡されたオブジェクトを返します。 返されるオブジェクトのプロパティは、デプロイ オブジェクトがリンクとして渡されたか、インライン オブジェクトとして渡されたかによって異なります。 

デプロイ オブジェクトがインラインで渡された場合 (Azure PowerShell の **TemplateFile** パラメーターを使用してローカル ファイルを参照する場合など)、返されるオブジェクトは次の形式になります。

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

オブジェクトがリンクとして渡された場合 (**-TemplateUri** パラメーターを使用してリモート オブジェクトを参照する場合など)、オブジェクトは次の形式で返されます。 

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

次の例は、deployment() を使用し、親テンプレートの URI に基づいて別のテンプレートにリンクする方法を示しています。

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

<a id="parameters" />

### <a name="parameters"></a>parameters
`parameters (parameterName)`

パラメーター値を返します。 指定したパラメーター名は、テンプレートのパラメーター セクションで定義されている必要があります。

| パラメーター | 必須 | 型 | 説明 |
|:--- |:--- |:--- |:--- |
| parameterName |あり |String |返されるパラメーターの名前。 |

次の例では、parameters 関数の簡単な使用方法を示しています。

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2014-06-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

<a id="variables" />

### <a name="variables"></a>variables
`variables (variableName)`

変数の値を返します。 指定した変数名は、テンプレートの変数セクションで定義されている必要があります。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| variableName |はい |string |返す変数の名前。 |

次の例では、変数の値を使用します。

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
],
```

## <a name="resource-functions"></a>リソース関数
リソース マネージャーには、リソース値を取得する次の関数が用意されています。

* [listKeys と list{Value}](#listkeys)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [サブスクリプション](#subscription)

パラメーター、変数、現在のデプロイから値を取得する方法については、「 [デプロイの値関数](#deployment-value-functions)」を参照してください。

<a id="listkeys" />
<a id="list" />

### <a name="listkeys-and-listvalue"></a>listKeys と list{Value}
`listKeys (resourceName or resourceIdentifier, apiVersion)`

`list{Value} (resourceName or resourceIdentifier, apiVersion)`

list 操作をサポートする任意の種類のリソースの値を返します。 最も一般的に使用されるのは、 **listKeys**です。 

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| resourceName または resourceIdentifier |あり |String |リソースの一意識別子です。 |
| apiVersion |あり |string |リソースのランタイム状態の API バージョン。 通常、**yyyy-mm-dd** の形式。 |

**list** で始まるすべての操作は、テンプレート内で関数として使用できます。 使用可能な操作には、**listKeys** だけでなく、**list**、**listAdminKeys**、**listStatus** などの操作も含まれます。 どのリソースの種類にリスト操作を含めるかを指定するには、リソース プロバイダーの [REST API の操作](/rest/api/)に関するページを参照してください。

リソース プロバイダーのリスト操作を検索するには、次の PowerShell コマンドレットを使用します。

```powershell
Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
```

リソース プロバイダーのリスト操作を検索するには、次の Azure CLI コマンドと、JSON ユーティリティを [jq](http://stedolan.github.io/jq/download/) を使用して、リスト操作のみをフィルター処理します。

```azurecli
azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains(\"list\"))"
```

resourceId の指定には、[resourceId 関数](#resourceid)または **{providerNamespace}/{resourceType}/{resourceName}** 形式を使用できます。

次の例は、outputs セクションでストレージ アカウントのプライマリ キーとセカンダリ キーを返す方法を示しています。

```json
"outputs": { 
  "listKeysOutput": { 
    "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2016-01-01')]", 
    "type" : "object" 
  } 
}
``` 

listKeys から返されるオブジェクトの形式は次のとおりです。

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

<a id="providers" />

### <a name="providers"></a>providers
`providers (providerNamespace, [resourceType])`

リソース プロバイダーとサポートされているそのリソースの種類に関する情報を返します。 リソースの種類を指定しない場合、関数はリソース プロバイダーでサポートされているすべての種類を返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| providerNamespace |はい |String |プロバイダーの名前空間 |
| resourceType |いいえ |String |指定した名前空間内にあるリソースの種類。 |

サポートされている各種類は、次の形式で返されます。 配列の順序は保証されません。

```json
{
    "resourceType": "",
    "locations": [ ],
    "apiVersions": [ ]
}
```

次の例は、provider 関数の使用方法を示しています。

```json
"outputs": {
    "exampleOutput": {
        "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
        "type" : "object"
    }
}
```

<a id="reference" />

### <a name="reference"></a>reference
`reference (resourceName or resourceIdentifier, [apiVersion])`

別のリソースのランタイム状態を表すオブジェクトを返します。

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| resourceName または resourceIdentifier |あり |String |名前またはリソースの一意の識別子。 |
| apiVersion |いいえ |String |指定したリソースの API バージョンです。 同じテンプレート内でリソースがプロビジョニングされない場合に、このパラメーターを追加します。 通常、**yyyy-mm-dd** の形式。 |

**reference** 関数はその値をランタイム状態から取得するので、変数セクションでは使用できません。 これは、テンプレートの出力セクションで使用できます。

reference 関数を使用して、参照先のリソースが同じテンプレート内でプロビジョニングされる場合に、あるリソースが他のリソースに依存することを暗黙的に宣言します。 **dependsOn** プロパティを一緒に使用する必要はありません。 参照先のリソースがデプロイを完了するまで、関数は評価されません。

次の例では、同じテンプレートでデプロイされるストレージ アカウントが参照されます。

```json
"outputs": {
    "NewStorage": {
        "value": "[reference(parameters('storageAccountName'))]",
        "type" : "object"
    }
}
```

次の例では、このテンプレートでデプロイされないが、デプロイされるリソースと同じリソース グループ内に存在するストレージ アカウントが参照されます。

```json
"outputs": {
    "ExistingStorage": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
        "type" : "object"
    }
}
```

次の例に示すように、BLOB エンドポイント URI など、返されたオブジェクトから特定の値を取得できます。

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    }
}
```

次の例では、別のリソース グループにあるストレージ アカウントが参照されます。

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    }
}
```

**reference** 関数から返されるオブジェクトのプロパティは、リソースの種類によって異なります。 ある種類のリソースによって返されるプロパティの名前と値を確認するには、 **outputs** セクションでオブジェクトを返す、単純なテンプレートを作成します。 その種類のリソースが既にある場合、このテンプレートは新しいリソースはデプロイせずにオブジェクトを返します。 その種類のリソースがまだない場合、このテンプレートはその種類のリソースだけをデプロイしてオブジェクトを返します。 その後、これらのプロパティを、デプロイ時に値を動的に取得する必要がある他のテンプレートに追加します。 

<a id="resourcegroup" />

### <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

現在のリソース グループを表すオブジェクトを返します。 

返されるオブジェクトの形式は次のとおりです。

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

次の例では、リソース グループの場所を使用して、Web サイトの場所を割り当てます。

```json
"resources": [
   {
      "apiVersion": "2014-06-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

<a id="resourceid" />

### <a name="resourceid"></a>resourceId
`resourceId ([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

リソースの一意の識別子を返します。 

| パラメーター | 必須 | 型 | Description |
|:--- |:--- |:--- |:--- |
| subscriptionId |なし |文字列 (GUID 形式) |既定値は、現在のサブスクリプションです。 別のサブスクリプション内のリソースを取得する必要がある場合は、この値を指定します。 |
| resourceGroupName |なし |string |既定値は、現在のリソース グループです。 別のリソース グループ内のリソースを取得する必要がある場合は、この値を指定します。 |
| resourceType |はい |String |リソース プロバイダーの名前空間を含むリソースの種類。 |
| resourceName1 |はい |String |リソースの名前。 |
| resourceName2 |いいえ |String |リソースが入れ子になっている場合、次のリソース名セグメント。 |

リソース名があいまいであるか、同じテンプレート内でプロビジョニングされていないときに、この関数を使用します。 識別子は、次の形式で返されます。

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}

次の例では、Web サイトとデータベースのリソース ID を取得する方法を示します。 Web サイトは **myWebsitesGroup** という名前のリソース グループ内にあり、データベースはこのテンプレートの現在のリソース グループ内にあります。

```json
[resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
[resourceId('Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
```

代替のリソース グループで、ストレージ アカウントや仮想ネットワークを使用するときには、多くの場合にこの関数を使用する必要があります。 ストレージ アカウントや仮想ネットワークは、複数のリソース グループ間で使用される場合があるので、単一のリソース グループを削除するときにそれらを削除しないでください。 次の例は、外部のリソース グループのリソースを簡単に使用する方法を示しています。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
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

<a id="subscription" />

### <a name="subscription"></a>サブスクリプション
`subscription()`

サブスクリプションの詳細を次の形式で返します。

```json
{
    "id": "/subscriptions/#####",
    "subscriptionId": "#####",
    "tenantId": "#####"
}
```

次の例は、outputs セクションで呼び出される subscription 関数を示しています。 

```json
"outputs": { 
  "exampleOutput": { 
      "value": "[subscription()]", 
      "type" : "object" 
  } 
} 
```

## <a name="next-steps"></a>次のステップ
* Azure リソース マネージャー テンプレートのセクションの説明については、「 [Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)
* 複数のテンプレートをマージするには、 [Azure リソース マネージャーでのリンクされたテンプレートの使用](resource-group-linked-templates.md)
* 1 種類のリソースを指定した回数分繰り返し作成するには、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)
* 作成したテンプレートをデプロイする方法を確認するには、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)


