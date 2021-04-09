---
title: TagsByResource UI 要素
description: Azure Portal の Microsoft.Common.TagsByResource UI 要素について説明します。 デプロイ中にリソースにタグを適用するために使用します。
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: e730201812005a9b469a964e4acd081ebe86b100
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063940"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Microsoft.Common.TagsByResource UI 要素

デプロイ中に[タグ](../management/tag-resources.md)をリソースに関連付けるためのコントロール。

## <a name="ui-sample"></a>UI サンプル

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft-common-tagsbyresource.png)

## <a name="schema"></a>スキーマ

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>サンプル出力

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>解説

- `resources` アレイ内の少なくとも 1 つの項目を指定する必要があります。
- `resources` 内の各要素は、完全に修飾されたリソースの種類である必要があります。 これらの要素は **[リソース]** ドロップダウンに表示され、ユーザーがタグ付けできます。
- コントロールの出力は、Azure Resource Manager テンプレートで簡単にタグ値を割り当てることができるように書式設定されています。 テンプレートでコントロールの出力を受け取るには、次の例に示すように、テンプレートにパラメーターを含めます。

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  タグを割り当てることができるリソースごとに、そのリソースの種類のパラメーター値にタグ プロパティを割り当てます。

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- TagsByResource パラメーターにアクセスする場合は、[IF](../templates/template-functions-logical.md#if) 関数を使用します。 これにより、指定されたリソースの種類にタグが割り当てられていない場合、空のオブジェクトを割り当てることができます。

## <a name="next-steps"></a>次のステップ

- UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
- UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
