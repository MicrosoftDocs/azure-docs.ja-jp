---
title: Bing News Search エンドポイント
titleSuffix: Azure Cognitive Services
description: News Search API エンドポイントの概要。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 82e37e8fa47b467e7c2fe98f801482675809a266
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423770"
---
# <a name="bing-news-search-api-endpoints"></a>Bing News Search API エンドポイント

**News Search API** は、ニュース記事、Web ページ、画像、動画、[エンティティ](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web)を返します。 エンティティには、人物、場所、またはトピックに関する概要情報が含まれます。

## <a name="endpoints"></a>エンドポイント

Bing News Search API を使用してニュース検索の結果を取得するには、次のエンドポイントのいずれかに `GET` 要求を送信します。 ヘッダーと URL パラメーターは、さらに仕様を定義します。

### <a name="news-items-by-search-query"></a>検索クエリによるニュース項目

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

検索クエリに基づいてニュース項目を返します。 検索クエリが空の場合、API はさまざまなカテゴリのトップ ニュース記事を返します。 検索語を URL エンコードし、それを `q=""` パラメーターに追加することで、クエリを送信します。 可用性については、[サポートされている国/地域と市場](language-support.md#supported-markets-for-news-search-endpoint)に関する記事を参照してください。

### <a name="top-news-items-by-category"></a>カテゴリ別のトップ ニュース項目

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

カテゴリ別にトップ ニュース項目を返します。 `category=business`、`category=sports`、または `category=entertainment` を使用してビジネス、スポーツ、またはエンターテイメントのトップ記事を具体的に要求できます。  `category` パラメーターは、`/news` URL でのみ使用できます。 カテゴリを指定するための正式な要件がいくつかあります。[クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters)に関するドキュメントの `category` に関するページをご覧ください。 検索語を URL エンコードし、それを `q=""` パラメーターに追加することで、クエリを送信します。 可用性については、[サポートされている国/地域と市場](language-support.md#supported-markets-for-news-endpoint)に関する記事を参照してください。

### <a name="trending-news-topics"></a>ニュース トピックのトレンド 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

ソーシャル ネットワークで現在注目されているニュース トピックを返します。 `/trendingtopics` オプションが含まれている場合、Bing 検索は `freshness` や `?q=""` などの他のいくつかのパラメーターを無視します。 可用性については、[サポートされている国/地域と市場](language-support.md#supported-markets-for-news-trending-endpoint)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどについて詳しくは、[Bing News Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) のリファレンスをご覧ください。

各エンドポイントでサポートされるパラメーターについて詳しくは、各種類のリファレンス ページをご覧ください。
News Search API を使用した基本的な要求の例については、[Bing News Search クイック スタート](https://docs.microsoft.com/azure/cognitive-services/bing-news-search)のページをご覧ください。
