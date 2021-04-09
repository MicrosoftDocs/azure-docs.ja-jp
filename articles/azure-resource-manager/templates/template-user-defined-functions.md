---
title: テンプレートでのユーザー定義関数
description: Azure Resource Manager テンプレート (ARM テンプレート) でユーザー定義関数を定義して使用する方法について説明します。
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 9c7480958e6315c8aea1fd8d12613bcf9d606723
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379626"
---
# <a name="user-defined-functions-in-arm-template"></a>ARM テンプレートでのユーザー定義関数

テンプレート内で、独自の関数を作成できます。 これらの関数は、テンプレートで使用可能です。 ユーザー定義関数は、テンプレート内で自動的に使用可能になる[標準テンプレート関数](template-functions.md)とは別のものです。 テンプレートで繰り返し使用される複雑な式がある場合は、独自の関数を作成します。

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) にユーザー定義関数を追加する方法について説明します。

## <a name="define-the-function"></a>関数を定義する

テンプレート関数との名前の競合を回避するために、お使いの関数には名前空間の値が必要です。 次の例は、一意の名前を返す関数を示しています。

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>関数を使用する

次の例は、ストレージ アカウントの一意の名前を取得するためのユーザー定義関数が含まれているテンプレートを示しています。 このテンプレートには、関数にパラメーターとして渡される `storageNamePrefix` という名前のパラメーターがあります。

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

デプロイ時に、`storageNamePrefix` パラメーターが関数に渡されます。

* このテンプレートでは、`storageNamePrefix` という名前のパラメーターが定義されます。
* 関数内で定義されているパラメーターのみを使用できるため、関数では `namePrefix` が使用されます。 詳細については、[制限](#limitations)に関するページを参照してください。
* テンプレートの `resources` セクションでは、`name` 要素で関数が使用され、`storageNamePrefix` の値が関数の `namePrefix` に渡されます。

## <a name="limitations"></a>制限事項

ユーザー関数を定義するときに、適用される制限がいくつかあります。

* 関数は変数にアクセスできません。
* 関数は、関数内で定義されているパラメーターのみを使用できます。 ユーザー定義関数内で [parameters](template-functions-deployment.md#parameters) 関数を使用する場合は、その関数のパラメーターに制限されます。
* 関数は、その他のユーザー定義関数を呼び出すことはできません。
* 関数では、[reference](template-functions-resource.md#reference) 関数、またはいずれの [list](template-functions-resource.md#list) 関数も使用できません。
* 関数のパラメーターでは既定値を指定できません。

## <a name="next-steps"></a>次のステップ

* ユーザー定義関数で使用できるプロパティの詳細については、「[ARM テンプレートの構造と構文の詳細](template-syntax.md)」を参照してください。
* 使用可能なテンプレート関数の一覧については、「[ARM テンプレート関数](template-functions.md)」を参照してください。
