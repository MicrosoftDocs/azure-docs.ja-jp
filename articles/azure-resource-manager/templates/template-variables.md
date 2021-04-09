---
title: テンプレートにおける変数
description: Azure Resource Manager テンプレート (ARM テンプレート) と Bicep ファイルで変数を定義する方法について説明します。
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 3ab14c9acfcc2d6c9edd23fb3bc4d876cd5ac756
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123379"
---
# <a name="variables-in-arm-templates"></a>ARM テンプレートにおける変数

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) または Bicep ファイルで変数を定義および使用する方法について説明します。 変数を使用してテンプレートを簡略化します。 テンプレート全体で複雑な式を繰り返すのではなく、複雑な式を含む変数を定義します。 次に、テンプレート全体で、必要に応じてその変数を使用します。

Resource Manager は、デプロイ操作を開始する前に変数を解決します。 テンプレートで変数が使用されている場合、Resource Manager はそれを解決済みの値に置き換えます。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-variable"></a>変数を定義する

変数を定義するときは、その変数の[データ型](data-types.md)は指定しません。 代わりに、値またはテンプレート式を指定します。 変数の型は解決済みの値から推定されます。 次の例では、変数を文字列に設定します。

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "stringVar": "example value"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var stringVar = 'example value'
```

---

変数を構築する際には、パラメーターまたは別の変数からの値を使用できます。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputValue": {
    "defaultValue": "deployment parameter",
    "type": "string"
  }
},
"variables": {
  "stringVar": "myVariable",
  "concatToVar": "[concat(variables('stringVar'), '-addtovar') ]",
  "concatToParam": "[concat(parameters('inputValue'), '-addtoparam')]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'myVariable'
var concatToVar =  '${stringVar}-addtovar'
var concatToParam = '${inputValue}-addtoparam'
```

---

[テンプレート関数](template-functions.md)を使用すると、変数の値を作成できます。

次の例では、ストレージ アカウント名に文字列値を作成します。 テンプレート関数をいくつか使用してパラメーター値を取得し、連結して一意の文字列にします。

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

---

JSON テンプレートでは、変数宣言で [reference](template-functions-resource.md#reference) 関数も、いずれの [list](template-functions-resource.md#list) 関数も使用できません。 これらの関数は、リソースのランタイム状態を取得します。これらの関数は、変数が解決されるときに、デプロイ前に実行することはできません。

Bicep ファイルでは、変数を宣言するときに、参照関数とリスト関数が有効です。

## <a name="use-variable"></a>変数を使用する

次の例は、リソース プロパティに変数を使用する方法を示しています。

# <a name="json"></a>[JSON](#tab/json)

JSON テンプレートでは、[variables](template-functions-deployment.md#variables) 関数を使用して、変数の値を参照します。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep ファイルでは、変数名を指定することによって変数の値を参照します。

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'

resource demoAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
```

---

## <a name="example-template"></a>テンプレートの例

次のテンプレートでは、リソースはデプロイされません。 さまざまな型の変数を宣言するいくつかの方法を示します。

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep では現在、ループはサポートされていません。

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/variables.bicep":::

---

## <a name="configuration-variables"></a>構成変数

環境を構成するための関連する値を保持する変数を定義できます。 変数は、値を持つオブジェクトとして定義します。 次の例は、**test** と **prod** という 2 つの環境の値を保持するオブジェクトを示しています。デプロイ中に、これらの値のいずれかを渡します。

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.bicep":::

---

## <a name="next-steps"></a>次のステップ

* 変数に使用できるプロパティの詳細については、「[ARM テンプレートの構造と構文について](template-syntax.md)」をご覧ください。
* 変数の作成に関する推奨事項については、[ベスト プラクティス - 変数](template-best-practices.md#variables)に関する記事をご覧ください。
* セキュリティ規則をネットワーク セキュリティ グループに割り当てるテンプレートの例については、[ネットワーク セキュリティ規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json)に関するページ、および[パラメーター ファイル](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)に関するページを参照してください。
