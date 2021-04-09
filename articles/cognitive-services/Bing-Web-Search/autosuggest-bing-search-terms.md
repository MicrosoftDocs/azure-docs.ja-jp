---
title: 検索語句の自動提案 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing Web Search API を Bing Autosuggest API と組み合わせて、強化された検索エクスペリエンスをユーザーに提供します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 1a3f479fbbe68c8880cd7fefb3b57c77d4cfbbfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349573"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>アプリケーションで Bing 検索語句を自動提案する

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

ユーザーが検索語句を入力するための検索ボックスを用意する場合は、[Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) を使用することでエクスペリエンスが向上します。 この API は、検索語句をユーザーが入力している最中に、その一部分に基づいてクエリ文字列の候補を返します。

ユーザーによって入力された検索語句は、[q](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) クエリ パラメーターが設定される前に URL エンコードされる必要があります。 たとえば「*sailing dinghies*」と入力された場合、`q` を `sailing+dinghies` または `sailing%20dinghies` に設定します。

検索語にスペル ミスが含まれていると、検索応答に [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) オブジェクトが含まれます。 このオブジェクトは元のスペルと Bing が検索に使用した修正済みのスペルを示します。

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

この情報を使用して、検索結果を表示するときにクエリ文字列を変更したことをユーザーに知らせることができます。

![クエリ コンテキスト UX の例](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>次のステップ  

* サンプルの [Bing Web Search API 応答](search-responses.md)を確認します。  

## <a name="see-also"></a>関連項目  

* [Bing Web Search API リファレンス](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)