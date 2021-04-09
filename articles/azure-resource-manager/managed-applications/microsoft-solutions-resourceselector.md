---
title: ResourceSelector UI 要素
description: Azure portal 用の Microsoft.Solutions.ResourceSelector UI 要素について説明します。 既存のリソース リストを取得するために使用されます。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: d4b21f092733f6ebb3c2bf5aa06c1d2782323730
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87094974"
---
# <a name="microsoftsolutionsresourceselector-ui-element"></a>Microsoft. Solutions Selector UI 要素

ResourceSelector を使用すると、ユーザーはサブスクリプションから既存のリソースを選択できます。

## <a name="ui-sample"></a>UI サンプル

![Microsoft.Solutions.ResourceSelector](./media/managed-application-elements/microsoft-solutions-resourceselector.png)

## <a name="schema"></a>スキーマ

```json
{
    "name": "storageSelector",
    "type": "Microsoft.Solutions.ResourceSelector",
    "label": "Select storage accounts",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "options": {
        "filter": {
            "subscription": "onBasics",
            "location": "onBasics"
        }
    }
}
```

## <a name="sample-output"></a>サンプル出力

```json
"name": "{resource-name}",
"id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}",
"location": "{deployed-location}"
```

## <a name="remarks"></a>解説

`resourceType` プロパティでは、リストに表示させるリソースのリソース プロバイダー名前空間とリソースの種類の名前を指定します。

`filter` プロパティでは、リソースで使用できるオプションが制限されます。 結果を場所またはサブスクリプションで制限できます。 基本の選択に一致するリソースのみを表示するには、`onBasics` を使用します。 すべてのリソースを表示するには、`all` を使用します。 既定値は `all` です。

## <a name="next-steps"></a>次のステップ

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
