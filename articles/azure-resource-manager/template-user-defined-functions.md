---
title: Azure Resource Manager テンプレートのユーザー定義関数
description: Azure Resource Manager テンプレートでユーザー定義関数を定義して使用する方法について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 227ecb57b125264e7d0eba56e634966d677e0f58
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387284"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Azure Resource Manager テンプレートのユーザー定義関数

テンプレート内で、独自の関数を作成できます。 これらの関数は、テンプレートで使用可能です。 ユーザー定義関数は、テンプレート内で自動的に使用可能になる[標準テンプレート関数](resource-group-template-functions.md)とは別のものです。 テンプレートで繰り返し使用される複雑な式がある場合は、独自の関数を作成します。

この記事では、Azure Resource Manager テンプレートにユーザー定義関数を追加する方法について説明します。

## <a name="define-the-function"></a>関数を定義する

テンプレート関数との名前の競合を回避するために、お使いの関数には名前空間の値が必要です。 次の例は、ストレージ アカウント名を返す関数を示しています。

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

次の例は、自分で定義した関数を呼び出す方法を示しています。

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="limitations"></a>制限事項

ユーザー関数を定義するときに、適用される制限がいくつかあります。

* 関数は変数にアクセスできません。
* 関数は、関数内で定義されているパラメーターのみを使用できます。 ユーザー定義関数内で [parameters](resource-group-template-functions-deployment.md#parameters) 関数を使用する場合は、その関数のパラメーターに制限されます。
* 関数は、その他のユーザー定義関数を呼び出すことはできません。
* 関数では、[reference](resource-group-template-functions-resource.md#reference) 関数、またはいずれの [list](resource-group-template-functions-resource.md#list) 関数も使用できません。
* 関数のパラメーターでは既定値を指定できません。


## <a name="next-steps"></a>次の手順

* ユーザー定義関数で使用できるプロパティの詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」をご覧ください。
* 使用可能なテンプレート関数の一覧については、「[Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」をご覧ください。
