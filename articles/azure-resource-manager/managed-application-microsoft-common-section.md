---
title: "Azure マネージ アプリケーションの Section UI 要素 | Microsoft Docs"
description: "Azure マネージ アプリケーションの Microsoft.Common.Section UI 要素について説明します。"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 34c0f2f88e6af5a0f822ec116e7e2334e4e29e8d
ms.contentlocale: ja-jp
ms.lasthandoff: 05/13/2017

---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI 要素
見出しの下の 1 つまたは複数の要素をグループ化するコントロールです。 この要素は、[Azure マネージ アプリケーションを作成する](managed-application-publishing.md)ときに使用します。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>スキーマ
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Some section",
  "elements": [
    {
      "name": "element1",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 1"
    },
    {
      "name": "element2",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>解説
- `elements` には少なくとも 1 つの要素を含める必要があります。ここには `Microsoft.Common.Section` 以外のすべての種類の要素を含めることができます。
- この要素は `toolTip` プロパティをサポートしていません。

## <a name="sample-output"></a>サンプル出力
`elements` 内の要素の出力値にアクセスするには、[basics()](managed-application-createuidefinition-functions.md#basics) または [steps()](managed-application-createuidefinition-functions.md#steps) 関数とドット表記を使用します。

```json
basics('section1').element1
```

`Microsoft.Common.Section` という種類の要素には、それ自体の出力値はありません。

## <a name="next-steps"></a>次のステップ
* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](managed-application-overview.md)」を参照してください。
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](managed-application-createuidefinition-elements.md)」を参照してください。

