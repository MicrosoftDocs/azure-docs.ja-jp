---
title: UserNameTextBox UI 要素
description: Azure Portal の Microsoft.Compute.UserNameTextBox UI 要素について説明します。 ユーザーが Windows または Linux のユーザー名を指定できるようにします。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 82478f322e1df22bde50769b90f0424140920e9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063588"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox UI 要素

Windows と Linux のユーザー名の検証が組み込まれているテキスト ボックス コントロールです。

## <a name="ui-sample"></a>UI サンプル

![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft-compute-usernametextbox.png)

## <a name="schema"></a>スキーマ

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="sample-output"></a>サンプル出力

```json
"Example name"
```

## <a name="remarks"></a>解説

- `constraints.required` が **true** に設定されている場合、テキスト ボックスには、正常に検証を完了できる値を指定する必要があります。 既定値は **true** です。
- `osPlatform` は指定する必要があり、**Windows** と **Linux** のいずれかを使用できます。
- `constraints.regex` は JavaScript の正規表現パターンです。 指定する場合、テキスト ボックスの値は、正常に検証を完了できるパターンと一致する必要があります。 既定値は **null** です。
- `constraints.validationMessage` はテキスト ボックスの値が `constraints.regex` で指定された検証に失敗したときに表示される文字列です。 指定しない場合、テキスト ボックスの組み込みの検証メッセージが使用されます。 既定値は **null** です。
- この要素には、`osPlatform` で指定された値に基づく検証が組み込まれています。 この組み込みの検証では、カスタム正規表現も使用できます。 `constraints.regex` の値が指定されている場合、組み込み検証とカスタム検証の両方がトリガーされます。

## <a name="next-steps"></a>次のステップ

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
