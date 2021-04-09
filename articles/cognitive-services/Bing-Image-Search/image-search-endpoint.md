---
title: Bing Image Search API のエンドポイント
titleSuffix: Azure Cognitive Services
description: Image Search API には 3 つのエンドポイントが含まれています。 エンドポイント 1 は、Web から画像を返します。 エンドポイント 2 は ImageInsights を返します。 エンドポイント 3 は注目の画像を返します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 67fec77136f5d593279be2846e63c51b60e16bb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94593520"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Bing Image Search API のエンドポイント

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

**Image Search API** には 3 つのエンドポイントが含まれています。  エンドポイント 1 は、クエリに基づいて Web からの画像を返します。 エンドポイント 2 は [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) を返します。  エンドポイント 3 は注目の画像を返します。

## <a name="endpoints"></a>エンドポイント

Bing API を使用して画像の結果を取得するには、次のエンドポイントのいずれかに要求を送信します。 ヘッダーと URL パラメーターを使用して、さらに詳細を定義します。

**エンドポイント 1:**`?q=""` によって定義されたユーザーの検索クエリに関連する画像を返します。
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**エンドポイント 2:**`GET` または `POST` を使用して、画像に関する分析情報を返します。
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
GET 要求では、画像を含む Web ページなど、画像に関する分析情報が返されます。 [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) パラメーターを `GET` 要求に含めます。

または、`POST` 要求の本文にバイナリ イメージを含めて、[modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) パラメーターを `RecognizedEntities` に設定することもできます。 これは、後続の `GET` 要求でパラメーターとして使用する [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) を返します。これは、画像内の人物に関する情報を返します。  `modules` を `All` に設定して、`insightsToken` を使用して別の呼び出しを行わずに、`POST` の結果内の `RecognizedEntities` を除くすべての分析情報を取得します。


**エンドポイント 3:** 他のユーザーが行った検索要求に基づく注目の画像を返します。 画像は、たとえば注目の人物やイベントに基づいて異なるカテゴリに分離されます。
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

注目の画像をサポートする市場の一覧については、[注目の画像](./trending-images.md)に関する記事をご覧ください。

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどについて詳しくは、[Bing Image Search API v7](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) のリファレンスをご覧ください。
## <a name="response-json"></a>応答 JSON
画像検索要求に対する応答には、結果が JSON オブジェクトとして含まれます。 結果の解析例については、[チュートリアル](./tutorial-bing-image-search-single-page-app.md)と[ソース コード](./tutorial-bing-image-search-single-page-app.md)をご覧ください。

## <a name="next-steps"></a>次のステップ
**Bing** API では、種類に応じた結果を返す検索アクションがサポートされます。  すべての検索エンドポイントは、結果を JSON 応答オブジェクトとして返します。  すべてのエンドポイントは、特定の言語や場所を経度、緯度、検索半径によって返すクエリをサポートします。

各エンドポイントでサポートされるパラメーターについて詳しくは、各種類のリファレンス ページをご覧ください。
Image Search API を使用した基本的な要求の例については、[Image Search クイック スタート](./overview.md)のページをご覧ください。