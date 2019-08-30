---
title: Azure Resource Manager テンプレートでの子リソースの名前と種類
description: Azure Resource Manager テンプレートで子リソースの名前と種類を設定する方法について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: tomfitz
ms.openlocfilehash: bd1c38547866c34eb5c4808ed5b185b50fa44a01
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035563"
---
# <a name="set-name-and-type-for-child-resources"></a>子リソースの名前と種類の設定

子リソースとは、別のリソースのコンテキスト内でのみ存在するリソースのことです。 たとえば[仮想マシン拡張機能](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions)は、[仮想マシン](/azure/templates/microsoft.compute/2019-03-01/virtualmachines)なしでは存在できません。 この拡張機能リソースが仮想マシンの子です。

Resource Manager テンプレートでは、親リソースの内側または外側に子リソースを指定できます。 次の例は、親リソースの resources プロパティ内に追加された子リソースを示しています。

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

次の例は、親リソースの外側の子リソースを示しています。 親リソースが同じテンプレート内にデプロイされていない場合、または複数の子リソースを作成するために [copy](resource-group-create-multiple.md) を使う場合は、このアプローチを使用することがあります。

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

リソースの名前と種類に指定する値は、子リソースが親リソースの内側で定義されているか、親リソースの外側で定義されているかによって変わります。

## <a name="within-parent-resource"></a>親リソースの内側

親リソースの type 内に type と name の値を定義するときは、スラッシュを使わず 1 つの単語として書式設定します。

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

次に示したのは、サブネットを含んだ仮想ネットワークの例です。 仮想ネットワークの resources 配列内にサブネットが含まれていることがわかります。 name が **Subnet1** に設定され、type が **subnets** に設定されています。 子リソースは、親リソースに依存するリソースとしてマークされています。子リソースをデプロイするためには、先に親リソースが存在していなければならないためです。

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "apiVersion": "2018-10-01",
        "type": "subnets",
        "location": "[parameters('location')]",
        "name": "Subnet1",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

完全なリソースの種類は、あくまで **Microsoft.Network/virtualNetworks/subnets** になります。 **Microsoft.Network/virtualNetworks/** は、親リソースの種類から引き継がれるので指定する必要はありません。

子リソースの名前は **Subnet1** に設定されていますが、完全名には親の名前が含まれます。 **VNet1** は、親リソースから引き継がれるので指定する必要はありません。

## <a name="outside-parent-resource"></a>親リソースの外側

親リソースの外側に type と name を定義するときは、スラッシュを使って親の種類と名前を含めるように書式設定します。

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

次の例に示した仮想ネットワークとサブネットは、どちらもルート レベルで定義されています。 仮想ネットワークの resources 配列内にはサブネットが含まれていないことがわかります。 name が **VNet1/Subnet1** に設定され、type が **Microsoft.Network/virtualNetworks/subnets** に設定されています。 子リソースは、親リソースに依存するリソースとしてマークされています。子リソースをデプロイするためには、先に親リソースが存在していなければならないためです。

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>次の手順

* Azure リソース マネージャーのテンプレートの作成の詳細については、 [テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。 

* リソースを参照する際のリソース名の形式については、[reference 関数](resource-group-template-functions-resource.md#reference)の説明を参照してください。
