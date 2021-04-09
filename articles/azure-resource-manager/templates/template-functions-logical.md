---
title: テンプレート関数 - 論理
description: Azure Resource Manager テンプレート (ARM テンプレート) で論理値を判定するために使用する関数について説明します。
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 27d94f10374daf0b9a351469579a5eb659cf5445
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96920474"
---
# <a name="logical-functions-for-arm-templates"></a>ARM テンプレート用の論理関数

Resource Manager には、Azure Resource Manager テンプレート (ARM テンプレート) で比較を行うための関数がいくつか用意されています。

* [and](#and)
* [bool](#bool)
* [false](#false)
* [if](#if)
* [not](#not)
* [or](#or)
* [true](#true)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="and"></a>および

`and(arg1, arg2, ...)`

すべてのパラメーター値が true かどうかを確認します。 `and` 関数は、Bicep ではサポートされていません。 代わりに、`&&` 演算子を使用してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |boolean |true かどうかを確認する最初の値。 |
| arg2 |はい |boolean |true かどうかを確認する 2 番目の値。 |
| 残りの引数 |いいえ |boolean |true かどうかを確認する追加の引数。 |

### <a name="return-value"></a>戻り値

すべての値が true の場合は **True** を、それ以外の場合は **False** を返します。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)では、論理関数を使用する方法を示します。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]"
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

前の例からの出力は次のようになります。

| 名前 | Type | [値] |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>[bool]

`bool(arg1)`

パラメーターをブール値に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |文字列または整数 |ブール値に変換する値。 |

### <a name="return-value"></a>戻り値

変換後の値のブール値。

### <a name="remarks"></a>解説

[true ()](#true) と [false ()](#false) を使用してブール値を取得することもできます。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json)では、ブール値を文字列または整数と共に使用する方法を示します。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueString": {
      "type": "bool",
      "value": "[bool('true')]",
    },
    "falseString": {
      "type": "bool",
      "value": "[bool('false')]"
    },
    "trueInt": {
      "type": "bool",
      "value": "[bool(1)]"
    },
    "falseInt": {
      "type": "bool",
      "value": "[bool(0)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

---
既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | [値] |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="false"></a>false

`false()`

false を返します。 `false` 関数は、Bicep では使用できません。  代わりに、`false` キーワードを使用します。

### <a name="parameters"></a>パラメーター

false 関数では、パラメーターは受け入れられません。

### <a name="return-value"></a>戻り値

常に false のブール値です。

### <a name="example"></a>例

次の例では、false の出力値が返されます。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "falseOutput": {
      "type": "bool",
      "value": "[false()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output falseOutput bool = false
```

---

前の例からの出力は次のようになります。

| 名前 | Type | [値] |
| ---- | ---- | ----- |
| falseOutput | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

条件が true か false かに基づいて値を返します。 `if` 関数は、Bicep ではサポートされていません。 代わりに、`?:` 演算子を使用してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| condition |はい |boolean |true か false かどうかを確認する値。 |
| trueValue |はい | string、int、object、または array |条件が true の場合に返される値。 |
| falseValue |はい | string、int、object、または array |条件が false の場合に返される値。 |

### <a name="return-value"></a>戻り値

最初のパラメーターが **True** の場合、2 番目のパラメーターを返します。そうでない場合、3 番目のパラメーターを返します。

### <a name="remarks"></a>解説

条件が **True** の場合、true の値のみが評価されます。 条件が **False** の場合、false の値のみが評価されます。 `if` 関数の場合、条件付きで有効な式のみを含めることができます。 たとえば、1 つの条件の下で存在し、他の条件下では存在しないリソースを参照できます。 条件付きでの式の評価の例については、次のセクションを参照してください。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json)では、`if` 関数を使用する方法を示します。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
    },
    "objectOutput": {
      "type": "object",
      "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output yesOutput string = 'a' == 'a' ? 'yes' : 'no'
output noOutput string = 'a' == 'b' ? 'yes' : 'no'
output objectOutput object = 'a' == 'a' ? json('{"test": "value1"}') : json('null')
```

---

前の例からの出力は次のようになります。

| 名前 | Type | [値] |
| ---- | ---- | ----- |
| yesOutput | String | はい |
| noOutput | String | no |
| objectOutput | Object | { "test": "value1" } |

次の[テンプレート例](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json)では、条件付きで有効な式限定で、この関数を使用する方法を示しています。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "logAnalytics": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
    {
      "condition": "[not(empty(parameters('logAnalytics')))]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
      "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
        },
        "protectedSettings": {
          "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
        }
      }
    }
  ],
  "outputs": {
    "mgmtStatus": {
      "type": "string",
      "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` は、Bicep にはまだ実装されていません。 [条件](https://github.com/Azure/bicep/issues/186)に関するページを参照してください。

---

## <a name="not"></a>not

`not(arg1)`

ブール値をその反対の値に変換します。 `not` 関数は、Bicep ではサポートされていません。 代わりに、`!` 演算子を使用してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |boolean |変換する値。 |

### <a name="return-value"></a>戻り値

パラメーターが **False** の場合、**True** を返します。 パラメーターが **True** の場合、**False** を返します。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)では、論理関数を使用する方法を示します。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]",
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

前の例からの出力は次のようになります。

| 名前 | Type | [値] |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

次の [テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json)では、**not** と [equals](template-functions-comparison.md#equals) を使用します。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output checkNotEquals bool = !(1 == 2)
```

---

前の例からの出力は次のようになります。

| 名前 | Type | [値] |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>または

`or(arg1, arg2, ...)`

いずれかのパラメーター値が true かどうかを確認します。 `or` 関数は、Bicep ではサポートされていません。 代わりに、`||` 演算子を使用してください。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |boolean |true かどうかを確認する最初の値。 |
| arg2 |はい |boolean |true かどうかを確認する 2 番目の値。 |
| 残りの引数 |いいえ |boolean |true かどうかを確認する追加の引数。 |

### <a name="return-value"></a>戻り値

いずれかの値が true の場合は **True** を返し、そうでない場合は **False** を返します。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)では、論理関数を使用する方法を示します。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

前の例からの出力は次のようになります。

| 名前 | Type | [値] |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="true"></a>true

`true()`

true を返します。 `true` 関数は、Bicep では使用できません。  代わりに、`true` キーワードを使用します。

### <a name="parameters"></a>パラメーター

true 関数では、パラメーターは受け入れられません。 `true` 関数は、Bicep では使用できません。  代わりに、`true` キーワードを使用します。

### <a name="return-value"></a>戻り値

常に true のブール値です。

### <a name="example"></a>例

次の例では、true の出力値が返されます。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueOutput": {
      "type": "bool",
      "value": "[true()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueOutput bool = true
```

---

前の例からの出力は次のようになります。

| 名前 | Type | [値] |
| ---- | ---- | ----- |
| trueOutput | Bool | True |

## <a name="next-steps"></a>次のステップ

* ARM テンプレートのセクションの説明については、「[ARM テンプレートの構造と構文について](template-syntax.md)」を参照してください。
