---
title: 画像検索クエリをカスタマイズおよび提案する - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Bing Image Search API に送信する検索クエリのカスタマイズについて説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: d833b017004365e9dad7241e360f42ff41a55883
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542754"
---
# <a name="customize-and-suggest-image-search-queries"></a>画像検索クエリをカスタマイズおよび提案する

この記事を使用して、クエリをカスタマイズし、Bing Image Search API に送信する検索語句を提案する方法について学習します。

## <a name="suggest-search-terms"></a>検索語句を提案する

検索語句を入力するための検索ボックスがアプリに備わっている場合は、[Bing Autosuggest API](../../bing-autosuggest/get-suggested-search-terms.md) を利用して利便性を高めることができます。 API でおすすめ検索語句をリアルタイム表示できます。 この API は、検索語句の一部分や Cognitive Services に基づいてクエリ文字列の候補を返します。

## <a name="pivot-the-query"></a>クエリをピボットする

元の検索クエリを Bing が分割できる場合、返された [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) オブジェクトには `pivotSuggestions` が含まれます。 ピボット候補を任意の検索語句としてユーザーに表示できます。 たとえば、元のクエリが「*Microsoft Surface*」である場合、そのクエリが Bing によって *Microsoft* と *Surface* に分割され、それぞれについてピボット候補を得ることができます。 このような候補は、任意のクエリ語句としてユーザーに表示できます。

次の例は、*Microsoft Surface* に関するピボット候補を示しています。  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

`pivotSuggestions` フィールドには、元のクエリを分割することによって得られたセグメント (ピボット) のリストが含まれています。 応答には、ピボットごとにおすすめクエリを含んだ [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) オブジェクトのリストが格納されます。 `text` フィールドには、候補のクエリが含まれます。 `displayText` フィールドには、元のクエリでピボットを置換する語句が含まれます。 "Release Date of Surface" はその一例です。

ユーザーが探しているものがピボット クエリ文字列であれば、`text` フィールドと `thumbnail` フィールドを使用し、ピボット クエリ文字列を表示します。 `webSearchUrl` URL または `searchLink` URL を利用し、サムネイルとテキストをクリック可能にします。 `webSearchUrl` を使用し、Bing 検索結果にユーザーを送信します。 独自の結果ページを提供する場合、`searchLink` を使用します。

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>クエリを展開する

Bing がクエリを展開して元の検索を絞り込むことができる場合、[Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) オブジェクトには `queryExpansions` フィールドが含まれます。 たとえば、クエリが *Microsoft Surface* であれば、クエリは次のように展開されます。
- Microsoft Surface **Pro 3**。
- Microsoft Surface **RT**。
- Microsoft Surface **Phone**。
- Microsoft Surface **Hub**。

次の例は、*Microsoft Surface* の展開されたクエリを示しています。

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

`queryExpansions` フィールドには、[Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj) オブジェクトのリストが含まれています。 `text` フィールドには、展開されたクエリが含まれています。 `displayText` フィールドには、展開語句が含まれています。 ユーザーが探しているものが展開されたクエリ文字列であれば、`text` フィールドと `thumbnail` フィールドを使用し、展開されたクエリ文字列を表示します。 `webSearchUrl` URL または `searchLink` URL を利用し、サムネイルとテキストをクリック可能にします。 `webSearchUrl` を使用し、Bing 検索結果にユーザーを送信します。 独自の結果ページを提供する場合、`searchLink` を使用します。

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>スロットル リクエスト

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>次の手順

まだ Bing Image Search API を試していない方は、[クイック スタート](../quickstarts/csharp.md)をご覧ください。 さらに高度な内容をお求めの方は、[単一ページの Web アプリ](../tutorial-bing-image-search-single-page-app.md)の作成に関するチュートリアルをご覧ください。
