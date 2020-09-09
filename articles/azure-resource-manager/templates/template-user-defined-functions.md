---
title: テンプレートでのユーザー定義関数
description: Azure Resource Manager テンプレートでユーザー定義関数を定義して使用する方法について説明します。
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 69f4e98d389cc8dbe5cd3f4b628189676c501106
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84672937"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Azure Resource Manager テンプレートのユーザー定義関数

テンプレート内で、独自の関数を作成できます。 これらの関数は、テンプレートで使用可能です。 ユーザー定義関数は、テンプレート内で自動的に使用可能になる[標準テンプレート関数](template-functions.md)とは別のものです。 テンプレートで繰り返し使用される複雑な式がある場合は、独自の関数を作成します。

この記事では、Azure Resource Manager テンプレートにユーザー定義関数を追加する方法について説明します。

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

次の例は、ユーザー定義関数を含むテンプレートを示しています。 この関数を使用して、ストレージ アカウントの一意の名前を取得します。 このテンプレートには、関数にパラメーターとして渡す **storageNamePrefix** という名前のパラメーターがあります。

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

## <a name="limitations"></a>制限事項

ユーザー関数を定義するときに、適用される制限がいくつかあります。

* 関数は変数にアクセスできません。
* 関数は、関数内で定義されているパラメーターのみを使用できます。 ユーザー定義関数内で [parameters](template-functions-deployment.md#parameters) 関数を使用する場合は、その関数のパラメーターに制限されます。
* 関数は、その他のユーザー定義関数を呼び出すことはできません。
* 関数では、[reference](template-functions-resource.md#reference) 関数、またはいずれの [list](template-functions-resource.md#list) 関数も使用できません。
* 関数のパラメーターでは既定値を指定できません。


## <a name="next-steps"></a>次のステップ

* ユーザー定義関数で使用できるプロパティの詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](template-syntax.md)」をご覧ください。
* 使用可能なテンプレート関数の一覧については、「[Azure Resource Manager テンプレートの関数](template-functions.md)」をご覧ください。
