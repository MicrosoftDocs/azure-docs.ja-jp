---
title: EditableGrid UI 要素
description: Azure portal の Microsoft.Common.EditableGrid UI 要素について説明します。 ユーザーが表形式の入力を収集できるようにします。
author: tfitzmac
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tomfitz
ms.openlocfilehash: 04f86883a75110985d1cbe050fe3fd3e0582986a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88893649"
---
# <a name="microsoftcommoneditablegrid-ui-element"></a>Microsoft.Common.EditableGrid UI 要素

表形式の入力を収集するためのコントロール。 グリッド内のすべてのフィールドが編集可能であり、行の数は変わる場合があります。

## <a name="ui-sample"></a>UI サンプル

:::image type="content" source="./media/managed-application-elements/microsoft-common-editablegrid.png" alt-text="Microsoft.Common.EditableGrid":::

## <a name="schema"></a>スキーマ

```json
{
  "name": "people",
  "type": "Microsoft.Common.EditableGrid",
  "ariaLabel": "Enter information per person",
  "label": "People",
  "constraints": {
    "width": "Full",
    "rows": {
      "count": {
        "min": 1,
        "max": 10
      }
    },
    "columns": [
      {
        "id": "colName",
        "header": "Name",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.TextBox",
          "placeholder": "Full name",
          "constraints": {
            "required": true,
            "validations": [
              {
                "isValid": "[startsWith(last(take(steps('grid').people, $rowIndex)).colName, 'contoso')]",
                "message": "Must start with 'contoso'."
              },
              {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
              }
            ]
          }
        }
      },
      {
        "id": "colGender",
        "header": "Gender",
        "width": "1fr",
        "element": {
          "name": "dropDown1",
          "type": "Microsoft.Common.DropDown",
          "placeholder": "Select a gender...",
          "constraints": {
            "allowedValues": [
              {
                "label": "Female",
                "value": "female"
              },
              {
                "label": "Male",
                "value": "male"
              },
              {
                "label": "Other",
                "value": "other"
              }
            ],
            "required": true
          }
        }
      },
      {
        "id": "colContactPreference",
        "header": "Contact preference",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.OptionsGroup",
          "constraints": {
            "allowedValues": [
              {
                "label": "Email",
                "value": "email"
              },
              {
                "label": "Text",
                "value": "text"
              }
            ],
            "required": true
          }
        }
      }
    ]
  }
}
```

## <a name="sample-output"></a>サンプル出力

```json
{
  "colName": "contoso",
  "colGender": "female",
  "colContactPreference": "email"
}
```

## <a name="remarks"></a>解説

- 列の配列内で有効なコントロールは [TextBox](microsoft-common-textbox.md)、[OptionsGroup](microsoft-common-optionsgroup.md)、および [DropDown](microsoft-common-dropdown.md) のみです。
- `$rowIndex` 変数は、グリッドの列の子の中に含まれる式でのみ有効です。 これは、要素の相対行インデックスを表す整数で、カウントは 1 から始まり、1 ずつ増分します。 スキーマの `"columns":` セクションに示すように、`$rowIndex` は検証に使用されます。
- `$rowIndex` 変数を使用して検証を実行する場合、`last()` と `take()` のコマンドを組み合わせることにより、現在の行の値を取得できます。

  次に例を示します。

  `last(take(<reference_to_grid>, $rowIndex))`

- `label` プロパティはコントロールの一部として表示されませんが、最終的なタブの概要に表示されます。
- `ariaLabel` プロパティは、グリッドのアクセシビリティ ラベルです。 スクリーン リーダーを使用するユーザーに便利なテキストを指定します。
- `constraints.width` プロパティは、グリッドの全体の幅を設定するために使用されます。 オプションは _[Full]\(全体\)_ 、 _[Medium]\(中間\)_ 、 _[Small]\(小\)_ です。 既定値は _[Full]\(全体\)_ です。
- 列の子の `width` プロパティによって、列の幅が決まります。 幅は _3fr_ のような小数単位を使用して指定され、合計領域はその単位に応じて列に割り当てられます。 列幅が指定されていない場合、既定値は _1fr_ になります。

## <a name="next-steps"></a>次のステップ

- UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
- UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
