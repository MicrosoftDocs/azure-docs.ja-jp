---
title: Azure Resource Manager テンプレートの構文と式
description: Azure Resource Manager テンプレートの宣言型 JSON 構文について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 1e7288da19e2e81d609b952e03d5143b03a65c63
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259435"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの構文と式

テンプレートの基本的な構文は JSON です。 ただし、式を使用して、テンプレート内で使用できる JSON の値を拡張できます。  式の始まりと終わりは、それぞれ角かっこ `[` と `]` です。 式の値は、テンプレートのデプロイ時に評価されます。 式では、文字列、整数、ブール値、配列、またはオブジェクトを返すことができます。

テンプレート式は、24,576 文字を超えることはできません。

## <a name="use-functions"></a>関数を使用する

次の例では、パラメーターの既定値での式を示します。

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

式の構文 `resourceGroup()` では、Resource Manager によってテンプレート内で使用するために提供されている関数の 1 つを呼び出します。 このケースでは、[resourceGroup](resource-group-template-functions-resource.md#resourcegroup) 関数です。 JavaScript の場合と同様に、関数呼び出しは `functionName(arg1,arg2,arg3)` という形式になります。 構文 `.location` では、その関数によって返されたオブジェクトから 1 つのプロパティを取得します。

テンプレート関数とそのパラメーターでは大文字と小文字が区別されません。 たとえば、Resource Manager では、**variables('var1')** と **VARIABLES('VAR1')** が同じものとして解決されます。 評価の際、関数は、大文字/小文字を明確に変更する (toUpper、toLower など) 場合を除き、大文字/小文字を保持します。 特定の種類のリソースでは、関数の評価方法とは別に、大文字/小文字の要件が存在する場合があります。

文字列値をパラメーターとして関数に渡すには、単一引用符を使用します。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

## <a name="escape-characters"></a>エスケープ文字

左かっこ `[` で始まり右かっこ `]`で終わるリテラル文字列を使用するが、それが式として解釈されないようにするには、余分にかっこを追加して文字列が `[[` から始まるようにします。 たとえば、変数は次のようになります。

```json
"demoVar1": "[[test value]"
```

`[test value]` に解決されます。

しかし、リテラル文字列がかっこで終わらないのであれば、最初のかっこはエスケープしないでください。 たとえば、変数は次のようになります。

```json
"demoVar2": "[test] value"
```

`[test] value` に解決されます。

テンプレートでの JSON オブジェクトの追加など、式の中で二重引用符をエスケープするには、円記号を使用します。

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="next-steps"></a>次の手順

* テンプレート関数の完全一覧が必要な場合、「 [Azure リソース マネージャーのテンプレートの関数](resource-group-template-functions.md)」を参照してください。
* テンプレート ファイルの詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」を参照してください。
