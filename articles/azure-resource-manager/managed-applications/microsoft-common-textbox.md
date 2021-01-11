---
title: TextBox UI 要素
description: Azure Portal の Microsoft.Common.TextBox UI 要素について説明します。 書式設定のないテキストを追加するために使用します。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c89bc434d9d67144a95b5c2f23e7664078fe7825
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87474310"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI 要素

書式設定されていないテキストの編集に使用できるコントロールです。

## <a name="ui-sample"></a>UI サンプル

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textbox.png)

## <a name="schema"></a>スキーマ

```json
{
    "name": "nameInstance",
    "type": "Microsoft.Common.TextBox",
    "label": "Name",
    "defaultValue": "contoso123",
    "toolTip": "Use only allowed characters",
    "placeholder": "",
    "constraints": {
        "required": true,
        "validations": [
            {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
            },
            {
                "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
                "message": "Must start with 'contoso'."
            }
        ]
    },
    "visible": true
}
```

## <a name="sample-output"></a>サンプル出力

```json
"contoso123"
```

## <a name="remarks"></a>解説

- `constraints.required` が **true** に設定されている場合、テキスト ボックスには、正常に検証を完了できる値を指定する必要があります。 既定値は **false** です。
- `validations` プロパティは、テキスト ボックスに入力された値を確認するための条件を追加する配列です。
- `regex` プロパティは JavaScript の正規表現パターンです。 指定する場合、テキスト ボックスの値は、正常に検証を完了できるパターンと一致する必要があります。 既定値は **null** です。
- `isValid` プロパティには、true または false に評価される式が格納されます。 式内で、テキスト ボックスが有効かどうかを決定する条件を定義します。
- `message` プロパティはテキスト ボックスの値の検証に失敗したときに表示される文字列です。
- `required` が **false** に設定されている場合、`regex` の値を指定することができます。 このシナリオでは、テキスト ボックスに正常に検証を完了できる値を指定する必要はありません。 指定する場合、正規表現パターンと一致する必要があります。
- `placeholder` プロパティは、ユーザーが編集を開始したときに消えるヘルプ テキストです。 `placeholder` と `defaultValue` の両方が定義されている場合は、`defaultValue` が優先され、表示されます。

## <a name="example"></a>例

次の例では、[Microsoft.Solutions.ArmApiControl](microsoft-solutions-armapicontrol.md) コントロールと共にテキスト ボックスを使用し、リソース名が使用可能かどうか確認します。

```json
"basics": [
    {
        "name": "nameApi",
        "type": "Microsoft.Solutions.ArmApiControl",
        "request": {
            "method": "POST",
            "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
            "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
        }
    },
    {
        "name": "txtStorageName",
        "type": "Microsoft.Common.TextBox",
        "label": "Storage account name",
        "constraints": {
            "validations": [
                {
                    "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
                    "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
                }
            ]
        }
    }
]
```

## <a name="next-steps"></a>次のステップ

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
