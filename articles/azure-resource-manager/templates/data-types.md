---
title: テンプレートのデータ型
description: Azure Resource Manager テンプレートで利用できるデータ型について説明します。
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 7d3f15c8852e6e25c621baad9bc6f20c303ffdb9
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125015"
---
# <a name="data-types-in-arm-templates"></a>ARM テンプレートのデータ型

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) でサポートされるデータ型について説明します。 JSON と Bicep の両方のデータ型について説明します。

## <a name="supported-types"></a>サポートされている型

ARM テンプレート内では、次のデータ型を使用できます。

* array
* [bool]
* INT
* object
* secureObject - Bicep の修飾子によって示されます
* secureString - Bicep の修飾子によって示されます
* string

## <a name="arrays"></a>配列

配列は左大かっこ (`[`) で始めて、右大かっこ (`]`) で終わります。

JSON では、配列は単一行または複数行で宣言できます。 各要素はコンマで区切られます。

Bicep では、配列は複数の行で宣言する必要があります。 値の間にはコンマを使用しないでください。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleArray": {
    "type": "array",
    "defaultValue": [
      1,
      2,
      3
    ]
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleArray array = [
  1
  2
  3
]
```

---

配列の要素は同じ型にすることも、異なる型にすることもできます。

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

---

## <a name="booleans"></a>ブール値

ブール値を指定するとき、`true` または `false` を使用します。 値を引用符で囲まないでください。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleBool bool = true
```

---

## <a name="integers"></a>整数

整数値を指定するとき、引用符を使用しないでください。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleInt int = 1
```

---

インライン パラメーターとして渡される整数の場合、値の範囲はデプロイに使用する SDK またはコマンドライン ツールによって制限されることがあります。 たとえば、PowerShell を使用してテンプレートをデプロイする場合、整数型は -2147483648 から 2147483647 の範囲で指定できます。 この制限を回避するには、[パラメーター ファイル](parameter-files.md)で大きな整数値を指定します。 リソースの種類によって、整数プロパティに独自の制限が適用されます。

## <a name="objects"></a>オブジェクト

オブジェクトは左中かっこ (`{`) で始めて、右中かっこ (`}`) で終わります。 オブジェクト内の各プロパティはキーと値で構成されます。 キーと値はコロン (`:`) で区切られます。

JSON では、キーは二重引用符で囲まれています。 各プロパティはコンマで区切られます。

Bicep では、キーは引用符で囲まれません。 プロパティ間にコンマを使用しないでください。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleObject": {
    "type": "object",
    "defaultValue": {
      "name": "test name",
      "id": "123-abc",
      "isCurrent": true,
      "tier": 1
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

---

## <a name="strings"></a>文字列

JSON では、文字列は二重引用符でマークされます。 Bicep では、文字列は一重引用符でマークされます。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleString string = 'test value'
```
---

## <a name="secure-strings-and-objects"></a>セキュリティで保護された文字列とオブジェクト

セキュリティで保護された文字列では文字列と同じ形式が使用され、セキュリティで保護されたオブジェクトではオブジェクトと同じ形式が使用されます。 パラメーターをセキュリティで保護された文字列またはセキュリティで保護されたオブジェクトに設定した場合、パラメーターの値はデプロイ履歴に保存されず、ログにも記録されません。 ただし、セキュリティで保護された値を、セキュリティで保護された値を想定していないプロパティに設定した場合、その値は保護されません。 たとえば、セキュリティで保護された文字列をタグに設定すると、その値はプレーンテキストとして格納されます。 パスワードとシークレットにはセキュリティで保護された文字列を使用します。

Bicep の使用時、文字列またはオブジェクトに `@secure()` 修飾子を追加します。

次の例からは、セキュリティで保護された 2 つのパラメーターを確認できます。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "password": {
    "type": "secureString"
  },
  "configValues": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param password string

@secure()
param configValues object
```

---

## <a name="next-steps"></a>次のステップ

テンプレート構文の詳細については、「[ARM テンプレートの構造と構文の詳細](template-syntax.md)」を参照してください。
