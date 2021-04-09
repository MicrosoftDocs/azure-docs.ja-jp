---
title: DropDown UI 要素
description: Azure Portal の Microsoft.Common.DropDown UI 要素について説明します。 管理対象アプリケーションをデプロイするとき、利用できるオプションからユーザーが選択するために使用します。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: b9b27a432776635290c7e8e796e84d8c1e0e8675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92168343"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI 要素

ドロップダウン リストを備えた選択コントロールです。 1 つの項目のみ、または複数の項目の選択を許可できます。 必要に応じて、項目に説明を含めることもできます。

## <a name="ui-sample"></a>UI サンプル

DropDown 要素には、ポータルでの外観を決定するさまざまなオプションがあります。

1 つの項目のみの選択を許可した場合、コントロールは次のように表示されます。

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Microsoft.Common.DropDown の単一選択":::

説明が含まれている場合、コントロールは次のように表示されます。

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Microsoft.Common.DropDown の説明付きの単一選択":::

複数選択が有効になっている場合、コントロールにより **[すべて選択]** オプションと複数の項目を選択するためのチェックボックスが追加されます。

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Microsoft.Common.DropDown の複数選択":::

複数選択を有効にしながら説明を含めることができます。

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="複数選択を有効にしながら説明を含める方法がわかるスクリーンショット。":::

フィルター処理が有効になっている場合、コントロールには、フィルター処理の値を追加するためのテキスト ボックスが含まれます。

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Microsoft.Common.DropDown の説明付きの複数選択":::

## <a name="schema"></a>スキーマ

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "placeholder": "",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
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

- ユーザーが複数の項目を選択できるかどうかを指定するには、`multiselect` を使用します。
- 既定では、複数選択を有効にすると、`selectAll` が `true` になります。
- `filter` プロパティを指定すると、ユーザーがオプションの長い一覧を検索できるようになります。
- `constraints.allowedValues` のラベルは項目の表示テキストであり、その値は要素が選択された際の出力値です。
- 指定する場合、既定値は `constraints.allowedValues` 内に存在するラベルである必要があります。 指定しない場合、`constraints.allowedValues` 内の最初の項目が選択されます。 既定値は **null** です。
- `constraints.allowedValues` には、少なくとも 1 つの項目が必要です。
- 必須ではない値をエミュレートするには、ラベルと `""` という値 (空の文字列) を持つ項目を `constraints.allowedValues` に追加します。
- 説明が指定されていない項目では、`defaultDescription` プロパティが使用されます。
- `placeholder` プロパティは、ユーザーが編集を開始したときに消えるヘルプ テキストです。 `placeholder` と `defaultValue` の両方が定義されている場合は、`defaultValue` が優先され、表示されます。

## <a name="next-steps"></a>次のステップ

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
