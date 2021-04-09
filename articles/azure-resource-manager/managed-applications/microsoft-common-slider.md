---
title: スライダーの UI 要素
description: Azure Portal の Microsoft.Common.Slider UI 要素について説明します。 ユーザーがオプションの範囲から値を設定できるようにします。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: tomfitz
ms.openlocfilehash: bb168a4bff6d2570f539307530232b5063bb535c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87094801"
---
# <a name="microsoftcommonslider-ui-element"></a>Microsoft.Common.Slider UI 要素

Slider コントロールを使用すると、ユーザーが許可されている値の範囲から選択できるようになります。

## <a name="ui-sample"></a>UI サンプル

:::image type="content" source="./media/managed-application-elements/microsoft-common-slider.png" alt-text="Microsoft.Common.Slider":::

## <a name="schema"></a>スキーマ

```json
{
    "name": "memorySize",
    "type": "Microsoft.Common.Slider",
    "min": 1,
    "max": 64,
    "label": "Memory",
    "subLabel": "MB",
    "defaultValue": 24,
    "showStepMarkers": false,
    "toolTip": "Pick the size in MB",
    "constraints": {
        "required": false
    },
    "visible": true
}
```

## <a name="sample-output"></a>サンプル出力

```json
26
```

## <a name="remarks"></a>解説

- `min` と `max` の値は必須です。 これらは、スライダーの開始点と終了点を設定します。
- `showStepMarkers` プロパティの既定値は true です。 ステップ マーカーは、最小値から最大値の範囲が 100 以下の場合にのみ表示されます。


## <a name="next-steps"></a>次のステップ

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
