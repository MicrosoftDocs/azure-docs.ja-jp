---
title: CheckBox UI 要素
description: Azure portal の Microsoft.Common.CheckBox UI 要素について説明します。 ユーザーがオプションのチェックボックスをオンまたはオフにすることができます。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87094805"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>Microsoft.Common.CheckBox UI 要素

CheckBox コントロールを使用すると、ユーザーがオプションのチェックボックスをオンまたはオフにすることができます。 コントロールのチェックボックスをオンにすると **true** が返され、オフにすると **false** が返されます。

## <a name="ui-sample"></a>UI サンプル

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="Microsoft.Common.CheckBox":::

## <a name="schema"></a>スキーマ

```json
{
    "name": "legalAccept",
    "type": "Microsoft.Common.CheckBox",
    "label": "I agree to the terms and conditions.",
    "constraints": {
        "required": true,
        "validationMessage": "Please acknowledge the legal conditions."
    }
}
```

## <a name="sample-output"></a>サンプル出力

```json
true
```

## <a name="remarks"></a>解説

**required** を **true** に設定した場合は、ユーザーがチェックボックスをオンにする必要があります。 ユーザーがチェックボックスをオフにした場合は、検証メッセージが表示されます。

## <a name="next-steps"></a>次のステップ

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
