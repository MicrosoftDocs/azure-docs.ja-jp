---
title: ArmApiControl UI 要素
description: Azure portal 用の Microsoft.Solutions.ArmApiControl UI 要素について説明します。 API 操作の呼び出しに使用されます。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: bbe36e072d10b81c421331b2212d8b161afd2693
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87094693"
---
# <a name="microsoftcommonarmapicontrol-ui-element"></a>Microsoft.Common.ArmApiControl UI 要素

ArmApiControl を使用すると、Azure Resource Manager API の操作から結果を取得できます。 その結果を使用して、他のコントロール内に動的なコンテンツを格納します。

## <a name="ui-sample"></a>UI サンプル

このコントロールには UI がありません。

## <a name="schema"></a>スキーマ

次の例は、このコントロールのスキーマを示しています。

```json
{
    "name": "testApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "{HTTP-method}",
        "path": "{path-for-the-URL}",
        "body": {
            "key1": "val1",
            "key2": "val2"
        }
    }
}
```

## <a name="sample-output"></a>サンプル出力

このコントロールの出力は、ユーザーには表示されません。 その代わり、他のコントロール内で操作の結果が使用されます。

## <a name="remarks"></a>解説

- `request.method` プロパティでは、HTTP メソッドを指定します。 指定できるのは `GET` または `POST` だけです。
- `request.path` プロパティでは、URL の相対パスを指定します。 これは、静的パスにすることも、他のコントロールの出力値を参照して動的に構築することもできます。
- `request.body` プロパティは省略可能です。 これを使用して、要求と共に送信される JSON 本文を指定します。 本文は、静的コンテンツにすることも、他のコントロールからの出力値を参照して動的に構築することもできます。

## <a name="example"></a>例

次の例では、`providersApi` 要素で API を呼び出して、プロバイダー オブジェクトの配列を取得しています。

`providersDropDown` 要素の `allowedValues` プロパティは、プロバイダーの名前を取得するように構成されています。 それらはドロップダウン リスト内に表示されます。

```json
{
    "name": "providersApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "GET",
        "path": "[concat(subscription().id, '/providers/Microsoft.Network/expressRouteServiceProviders?api-version=2019-02-01')]"
    }
},
{
    "name": "providerDropDown",
    "type": "Microsoft.Common.DropDown",
    "label": "Provider",
    "toolTip": "The provider that offers the express route connection.",
    "constraints": {
        "allowedValues": "[map(steps('settings').providersApi.value, (item) => parse(concat('{\"label\":\"', item.name, '\",\"value\":\"', item.name, '\"}')))]",
        "required": true
    },
    "visible": true
}
```

ArmApiControl を使用してリソース名が使用可能かどうかを調べる例については、「[Microsoft.Common.TextBox](microsoft-common-textbox.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
