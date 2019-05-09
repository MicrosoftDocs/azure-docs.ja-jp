---
title: Project URL Preview エンドポイント
titlesuffix: Azure Cognitive Services
description: URL Preview エンドポイントの概要。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 7cc52493ec0e2b9c81d52da4bb22102c2c7e5e5c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861989"
---
# <a name="project-url-preview-endpoint"></a>Project URL Preview エンドポイント

URL Preview API には、エンドポイントが 1 つ含まれています。

## <a name="endpoint"></a>エンドポイント
URL のプレビューを取得するには、次のエンドポイントに要求を送信します。 その他の指定については、ヘッダーと URL パラメーターを使用します。

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>クエリ パラメーター
|Name|値|type|必須|  
|----------|-----------|----------|--------------|  
|q|プレビューする URL|String |はい|
|safeSearch|違法な成人向けコンテンツや海賊版コンテンツはエラー コード 400 でブロックされ、*isFamilyFriendly* フラグは返されません。 <p>合法的な成人向けコンテンツの場合は、次のような動作になります。 状態コード 200 が返され、*isFamilyFriendly* フラグが false に設定されます。<ul><li>safeSearch=strict:タイトル、説明、URL、画像は返されません。</li><li>safeSearch=moderate: タイトル、URL、説明は取得しますが、説明的な画像は取得しません。</li><li>safeSearch=off: すべての応答オブジェクト/要素 (タイトル、URL、説明、画像) を取得します。</li></ul> |String|不要。 </br> 既定値は safeSearch=strict です。| 

## <a name="response-object"></a>応答オブジェクト

応答には HTTP ヘッダーと WebPage オブジェクトが含まれます。WebPage オブジェクトには、次の例に示すように `name`、`url`、`description`、`isFamilyFriendly`、`primaryImageOfPage` の属性があります。

```
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

## <a name="next-steps"></a>次の手順
- [C# のクイック スタート](csharp.md)
- [Java のクイック スタート](java-quickstart.md)
- [JavaScript のクイック スタート](javascript.md)
- [Node のクイック スタート](node-quickstart.md)
- [Python のクイック スタート](python-quickstart.md)
