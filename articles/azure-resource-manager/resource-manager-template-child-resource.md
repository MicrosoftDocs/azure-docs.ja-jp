---
title: "Azure テンプレート内で子リソースを定義する | Microsoft Docs"
description: "Azure Resource Manager テンプレートで子リソースに関するリソースの種類と名前を設定する方法を説明します"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: tomfitz
ms.openlocfilehash: 5b6ce5526f354008eb4a697deec737876f22391f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="set-name-and-type-for-child-resource-in-resource-manager-template"></a>Resource Manager テンプレートの子リソースに関する名前と種類の設定
テンプレートの作成時に、親リソースに関連する子リソースを含めなければならない場合が頻繁にあります。 たとえば、テンプレートに SQL サーバーとデータベースが含まれているとします。 SQL サーバーは親リソースで、データベースは子リソースです。 

子リソースの種類の形式は次の通りです。`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

子リソースの名前の形式は次の通りです。`{parent-resource-name}/{child-resource-name}`

しかし、テンプレート内の種類と名前の指定方法は、親リソース内で入れ子になっているか、単独で最上位レベルにあるかによって異なります。 このトピックでは、この 2 つのアプローチに対する対応方法について説明します。

リソースに対する完全修飾参照を作成する場合、種類と名前からセグメントを結合する順序は、単に 2 つの連結ではありません。  名前空間の後に、"*種類/名前*" のペアを具体性の低いものから高いものへの順に使用します。

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

For example:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`は正しい`Microsoft.Compute/virtualMachines/extensions/myVM/myExt`は正しくない

## <a name="nested-child-resource"></a>入れ子になった子リソース
子リソースを定義する最も簡単な方法では、親のリソース内で入れ子にすることです。 次の例では、SQL Server 内で入れ子になっている SQL データベースを示します。

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

子リソースの種類には `databases` が設定されていますが、完全なリソースの種類は `Microsoft.Sql/servers/databases` です。 親リソースから想定されるため、`Microsoft.Sql/servers/` は不要です。 子リソースの名前は `exampledatabase` が設定されていますが、完全な名前には親の名前が含まれています。 親リソースから想定されるため、`exampleserver` は不要です。

## <a name="top-level-child-resource"></a>最上位レベルの子リソース
最上位レベルで子リソースを定義できます。 親のリソースが同じテンプレート内に展開されていない場合、または複数の子リソースを作成するために `copy` を使用したい場合は、このアプローチを使用することがあります。 このアプローチの場合、完全なリソースの種類を指定する必要があり、子リソースの名前に親のリソースの名前を含める必要があります。

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

テンプレート内の同じレベルに定義されていますが、データベースはサーバーの子リソースです。

## <a name="next-steps"></a>次のステップ
* テンプレートの作成方法の推奨事項については、「[Azure Resource Manager テンプレートを作成するためのベスト プラクティス](resource-manager-template-best-practices.md)」を参照してください。
* 複数の子リソースを作成する例は、「[Azure Resource Manager テンプレート内における複数のリソースのインスタンスのデプロイ](resource-group-create-multiple.md)」をご覧ください。
