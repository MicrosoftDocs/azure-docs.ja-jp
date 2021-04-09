---
title: OptionsGroup UI 要素
description: Azure Portal の Microsoft.Common.OptionsGroup UI 要素について説明します。 管理対象アプリケーションをデプロイするとき、利用できるオプションからユーザーが選択できるようにします。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: aa73b4cbded98291a14792a7151df9fdfb885b53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87004194"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft.Common.OptionsGroup UI 要素

OptionsGroup コントロールを使用して、ユーザーが 2 つ以上の選択肢から 1 つのオプションを選択できるようにします。 ユーザーは、オプションを 1 つだけ選択できます。

> [!NOTE]
> 以前は、このコントロールでは、オプションが水平方向に表示されていました。 現在、このコントロールでは、オプションはラジオ ボタンのように垂直に表示されます。

## <a name="ui-sample"></a>UI サンプル

:::image type="content" source="./media/managed-application-elements/microsoft-common-optionsgroup-2.png" alt-text="Microsoft.Common.OptionsGroup":::

## <a name="schema"></a>スキーマ

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>サンプル出力

```json
"two"
```

## <a name="remarks"></a>解説

- `constraints.allowedValues` のラベルは項目の表示テキストであり、その値は要素が選択された際の出力値です。
- 指定する場合、既定値は `constraints.allowedValues` 内に存在するラベルである必要があります。 指定しない場合、`constraints.allowedValues` 内の最初の項目が既定で選択されます。 既定値は **null** です。
- `constraints.allowedValues` には、少なくとも 1 つの項目が必要です。

## <a name="next-steps"></a>次のステップ

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
