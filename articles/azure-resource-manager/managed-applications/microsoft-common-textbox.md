---
title: TextBox UI 要素
description: Azure Portal の Microsoft.Common.TextBox UI 要素について説明します。 書式設定のないテキストを追加するために使用します。
author: tfitzmac
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8e4cfcc7fe46c19bf1e58ee5eadf1e0bb8c7bd8e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124331"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI 要素

書式設定されていないテキストの追加に使用できるユーザーインターフェイス (UI) 要素。 `Microsoft.Common.TextBox` 要素は単一行の入力フィールドですが、`multiLine` プロパティがあれば複数行を入力できます。

## <a name="ui-sample"></a>UI サンプル

`TextBox` 要素では、単一行または複数行のテキスト ボックスが使用されます。

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox.png" alt-text="Microsoft.Common.TextBox 要素の単一行テキスト ボックス。":::

:::image type="content" source="media/managed-application-elements/microsoft-common-textbox-multi-line.png" alt-text="Microsoft.Common.TextBox 要素の複数行テキスト ボックス。":::

## <a name="schema"></a>スキーマ

```json
{
  "name": "nameInstance",
  "type": "Microsoft.Common.TextBox",
  "label": "Name",
  "defaultValue": "contoso123",
  "toolTip": "Use only allowed characters",
  "placeholder": "",
  "multiLine": false,
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

- マウスのカーソルが情報記号の上に置かれているとき、要素に関するテキストを表示するには、`toolTip` プロパティを使用します。
- `placeholder` プロパティは、ユーザーが編集を開始したときに消えるヘルプ テキストです。 `placeholder` と `defaultValue` の両方が定義されている場合は、`defaultValue` が優先され、表示されます。
- `multiLine` プロパティはブール値、すなわち、`true` か `false` です。 複数行のテキスト ボックスを使用するには、プロパティを `true` に設定します。 複数行のテキスト ボックスが不要な場合、プロパティを `false` に設定するか、プロパティを除外します。 改行については、JSON 出力にはライン フィードの `\n` が表示されます。 複数行のテキスト ボックスでは、キャリッジ リターン (CR) として `\r` が、ライン フィード (LF) として `\n` が受け取られます。 たとえば、既定値に `\r\n` を追加し、CRLF を指定できます。
- `constraints.required` が `true` に設定されている場合、テキスト ボックスには、正常に検証を完了できる値を指定する必要があります。 既定値は `false` です。
- `validations` プロパティは、テキスト ボックスに入力された値を確認するための条件を追加する配列です。
- `regex` プロパティは JavaScript の正規表現パターンです。 指定されている場合、テキスト ボックスの値はパターンに一致しないと、有効性が正常に確認されません。 既定値は `null` です。 正規表現構文の詳細については、[正規表現のクイック リファレンス](/dotnet/standard/base-types/regular-expression-language-quick-reference)を参照してください。
- `isValid` プロパティには、`true` または `false` に評価される式が格納されます。 式内で、テキスト ボックスが有効かどうかを決定する条件を定義します。
- `message` プロパティはテキスト ボックスの値の検証に失敗したときに表示される文字列です。
- `required` が `false` に設定されている場合、`regex` の値を指定することができます。 このシナリオでは、テキスト ボックスに正常に検証を完了できる値を指定する必要はありません。 指定する場合、正規表現パターンと一致する必要があります。

## <a name="examples"></a>例

この例からは、単一行のテキスト ボックスと複数行のテキスト ボックスを使用する方法を確認できます。

### <a name="single-line"></a>単一行

次の例では、[Microsoft.Solutions.ArmApiControl](microsoft-solutions-armapicontrol.md) コントロールと共にテキスト ボックスを使用し、リソース名が使用可能かどうか確認します。

```json
"basics": [
  {
    "name": "nameApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
      "method": "POST",
      "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
      "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
    }
  },
  {
    "name": "txtStorageName",
    "type": "Microsoft.Common.TextBox",
    "label": "Storage account name",
    "constraints": {
      "validations": [
        {
          "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
          "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
        }
      ]
    }
  }
]
```

### <a name="multi-line"></a>複数行

この例では、`multiLine` プロパティを使用し、プレースホルダー テキストを含む複数行のテキスト ボックスを作成します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {
        "name": "demoTextBox",
        "type": "Microsoft.Common.TextBox",
        "label": "Multi-line text box",
        "defaultValue": "",
        "toolTip": "Use 1-60 alphanumeric characters, hyphens, spaces, periods, and colons.",
        "placeholder": "This is a multi-line text box:\nLine 1.\nLine 2.\nLine 3.",
        "multiLine": true,
        "constraints": {
          "required": true,
          "validations": [
            {
              "regex": "^[a-z0-9A-Z -.:\n]{1,60}$",
              "message": "Only 1-60 alphanumeric characters, hyphens, spaces, periods, and colons are allowed."
            }
          ]
        },
        "visible": true
      }
    ],
    "steps": [],
    "outputs": {
      "textBox": "[basics('demoTextBox')]"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

- UI 定義作成の概要については、「[Azure マネージド アプリケーションの作成エクスペリエンスのための CreateUiDefinition.json](create-uidefinition-overview.md)」を参照してください。
- UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
