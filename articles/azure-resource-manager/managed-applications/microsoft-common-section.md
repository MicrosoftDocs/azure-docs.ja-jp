---
title: Section UI 要素
description: Azure Portal の Microsoft.Common.Section UI 要素について説明します。 管理対象アプリケーションをデプロイする目的で、ポータルで要素をグループ化するために使用します。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 924aff8f2ba3d796b65f52494845f3b10018065c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063955"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI 要素

見出しの下の 1 つまたは複数の要素をグループ化するコントロールです。

## <a name="ui-sample"></a>UI サンプル

![Microsoft.Common.Section](./media/managed-application-elements/microsoft-common-section.png)

## <a name="schema"></a>スキーマ

```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>解説

- `elements` には少なくとも 1 つの要素を含める必要があります。ここには `Microsoft.Common.Section` 以外のすべての種類の要素を含めることができます。
- この要素は `toolTip` プロパティをサポートしていません。

## <a name="sample-output"></a>サンプル出力
`elements` 内の要素の出力値にアクセスするには、[basics()](create-ui-definition-referencing-functions.md#basics) または [steps()](create-ui-definition-referencing-functions.md#steps) 関数とドット表記を使用します。

```json
steps('configuration').section1.text1
```

`Microsoft.Common.Section` という種類の要素には、それ自体の出力値はありません。

## <a name="next-steps"></a>次のステップ

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
