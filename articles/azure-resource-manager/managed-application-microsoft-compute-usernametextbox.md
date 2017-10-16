---
title: "Azure マネージ アプリケーションの UserNameTextBox UI 要素 | Microsoft Docs"
description: "Azure マネージ アプリケーションの Microsoft.Compute.UserNameTextBox UI 要素について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/12/2017
ms.author: tomfitz
ms.openlocfilehash: b6f3ca9ed88fa807648e08221dfdc313b7d57b43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox UI 要素
Windows と Linux のユーザー名の検証が組み込まれているテキスト ボックス コントロールです。 この要素は、[Azure マネージ アプリケーションを作成する](managed-application-publishing.md)ときに使用します。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

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

## <a name="remarks"></a>解説
- `constraints.required` が **true** に設定されている場合、テキスト ボックスには、正常に検証を完了できる値を含める必要があります。 既定値は **true**です。
- `osPlatform` は指定する必要があり、**Windows** と **Linux** のいずれかを使用できます。
- `constraints.regex` は JavaScript の正規表現パターンです。 指定する場合、テキスト ボックスの値は、正常に検証を完了できるパターンと一致する必要があります。 既定値は **null** です。
- `constraints.validationMessage` はテキスト ボックスの値が `constraints.regex` で指定された検証に失敗したときに表示される文字列です。 指定しない場合、テキスト ボックスの組み込みの検証メッセージが使用されます。 既定値は **null** です。
- この要素には、`osPlatform` で指定された値に基づく検証が組み込まれています。 この組み込みの検証では、カスタム正規表現も使用できます。
`constraints.regex` の値が指定されている場合、組み込み検証とカスタム検証の両方がトリガーされます。

## <a name="sample-output"></a>サンプル出力
```json
"tabrezm"
```

## <a name="next-steps"></a>次のステップ
* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](managed-application-overview.md)」を参照してください。
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](managed-application-createuidefinition-elements.md)」を参照してください。
