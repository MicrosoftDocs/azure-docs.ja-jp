---
title: テンプレートの構文と式
description: Azure Resource Manager テンプレート (ARM テンプレート) の宣言型 JSON 構文について説明します。
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 44a386ed849771dfba717c8d1414e64422d0c7bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97797045"
---
# <a name="syntax-and-expressions-in-arm-templates"></a>ARM テンプレートの構文と式

Azure Resource Manager テンプレート (ARM テンプレート) の基本構文は JavaScript Object Notation (JSON) です。 ただし、式を使用して、テンプレート内で使用できる JSON の値を拡張できます。  式の始まりと終わりは、それぞれ角かっこ `[` と `]` です。 式の値は、テンプレートのデプロイ時に評価されます。 式では、文字列、整数、ブール値、配列、またはオブジェクトを返すことができます。

テンプレート式は、24,576 文字を超えることはできません。

## <a name="use-functions"></a>関数を使用する

Azure Resource Manager には、テンプレートで使用できる[関数](template-functions.md)が提供されています。 次の例では、パラメーターの既定値で関数を使用する式を示します。

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

式の構文 `resourceGroup()` では、Resource Manager によってテンプレート内で使用するために提供されている関数の 1 つを呼び出します。 このケースでは、[resourceGroup](template-functions-resource.md#resourcegroup) 関数です。 JavaScript の場合と同様に、関数呼び出しは `functionName(arg1,arg2,arg3)` という形式になります。 構文 `.location` では、その関数によって返されたオブジェクトから 1 つのプロパティを取得します。

テンプレート関数とそのパラメーターでは大文字と小文字が区別されません。 たとえば、Resource Manager は `variables('var1')` と `VARIABLES('VAR1')` を同時に解決します。 評価の際、関数は、大文字/小文字を明確に変更する (`toUpper`、`toLower` など) 場合を除き、大文字/小文字を保持します。 特定の種類のリソースでは、関数の評価方法とは別に、大文字/小文字の要件が存在する場合があります。

文字列値をパラメーターとして関数に渡すには、単一引用符を使用します。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

ほとんどの関数は、リソース グループ、サブスクリプション、管理グループ、またはテナントのいずれにデプロイされても、同じように動作します。 次の関数には、スコープに基づく制限があります。

* [resourceGroup](template-functions-resource.md#resourcegroup) - リソース グループへのデプロイでのみ使用できます。
* [resourceId](template-functions-resource.md#resourceid) - 任意のスコープで使用できますが、有効なパラメーターはスコープに応じて変わります。
* [subscription](template-functions-resource.md#subscription) - リソース グループまたはサブスクリプションへのデプロイでのみ使用できます。

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

パラメーター値を渡す場合、エスケープ文字の使用は、パラメーター値が指定されている場所によって異なります。 テンプレートに既定値を設定する場合は、左角かっこを追加する必要があります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

既定値を使用する場合、テンプレートから `[test value]` が返されます。

ただし、コマンド ラインを使用してパラメーター値を渡すと、文字は文字どおりに解釈されます。 次のものを使用して、前述のテンプレートをデプロイします。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

`[[test value]` が返されます。 代わりに以下を使用します。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

パラメーター ファイルから値を渡すときも、同じ書式設定が適用されます。 文字は文字どおりに解釈されます。 前述のテンプレートと共に使用すると、次のパラメーター ファイルによって `[test value]` が返されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Null 値

プロパティを null に設定するには、`null` または `[json('null')]` を使用します。 パラメーターとして `null` を指定すると、[json 関数](template-functions-object.md#json) は空のオブジェクトを返します。 どちらの場合も、Resource Manager テンプレートでは、プロパティが存在しないかのように扱われます。

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>次のステップ

* テンプレート関数の完全な一覧については、「[ARM テンプレート関数](template-functions.md)」を参照してください。
* テンプレート ファイルの詳細については、「[ARM テンプレートの構造と構文の詳細](template-syntax.md)」を参照してください。
