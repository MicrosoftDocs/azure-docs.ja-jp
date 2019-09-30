---
title: Azure Resource Manager テンプレートのパラメーター
description: Azure Resource Manager テンプレートでパラメーターを定義する方法について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 4947b00d6fad5007751cd97d43ad6aca8d775330
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383271"
---
# <a name="parameters-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートのパラメーター

この記事では、Azure Resource Manager テンプレートでパラメーターを定義および使用する方法について説明します。 パラメーターに異なる値を指定することにより、さまざまな環境でテンプレートを再利用できます。

Resource Manager は、デプロイ操作を開始する前にパラメーター値を解決します。 テンプレートでパラメーターが使用されている場合、Resource Manager はそれを解決済みの値に置き換えます。

## <a name="define-parameter"></a>パラメーターを定義する

単純なパラメーター定義の例を次に示します。 **storageSKU** という名前のパラメーターを定義します。 パラメーターは、文字列値であり、使用目的に対して有効な値のみを受け入れます。 デプロイ時に値が指定されなかった場合、パラメーターは既定値を使用します。

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>パラメーターを使用する

テンプレートでは、[parameters](resource-group-template-functions-deployment.md#parameters) 関数を使用してパラメーターの値を参照します。 次の例では、パラメーター値を使用してストレージ アカウントの SKU を設定します。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>テンプレート関数

パラメーターの既定値を指定する場合は、ほとんどのテンプレート関数を使用できます。 別のパラメーター値を使用して既定値を作成できます。 次のテンプレートは、既定値での関数の使用方法を示します。 サイトの名前が指定されていない場合は、一意の文字列値が作成されて、**site** に付加されます。 ホスト プランの名前が指定されていない場合は、サイトの値が使用され、 **-plan** が付加されます。

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

パラメーター セクションでは、[reference](resource-group-template-functions-resource.md#reference) 関数も、いずれの [list](resource-group-template-functions-resource.md#list) 関数も使用できません。 これらの関数は、リソースのランタイム状態を取得します。パラメーターが解決されるとき、デプロイの前に実行することはできません。

## <a name="objects-as-parameters"></a>パラメーターとしてのオブジェクト

関連する値は 1 つのオブジェクトとして渡すことにより整理しやすくなります。 この方法により、テンプレート内のパラメータ数も減少します。

次の例は、オブジェクトであるパラメーターを示しています。 既定値は、オブジェクトの予想されるプロパティを示しています。

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

ドット演算子を使用して、オブジェクトのプロパティを参照します。

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="example-templates"></a>サンプル テンプレート

次の例は、パラメーターを使用するためのシナリオを示しています。

|Template  |説明  |
|---------|---------|
|[既定値のための関数を含むパラメーター](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | パラメーターの既定値を定義する際のテンプレート関数の使用方法を説明します。 このテンプレートではリソースをデプロイしません。 パラメーターの値を作成して、その値を返します。 |
|[パラメーター オブジェクト](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | パラメーターのオブジェクトの使用方法を示します。 このテンプレートではリソースをデプロイしません。 パラメーターの値を作成して、その値を返します。 |


## <a name="next-steps"></a>次の手順

* パラメーターに使用できるプロパティの詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」をご覧ください。
* パラメーター値をファイルとして渡す方法については、「[Resource Manager パラメーター ファイルの作成](resource-manager-parameter-files.md)」を参照してください。
* パラメーターの作成に関する推奨事項については、[ベスト プラクティス - パラメーター](template-best-practices.md#parameters)に関する記事をご覧ください。
