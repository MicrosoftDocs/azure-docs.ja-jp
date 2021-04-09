---
title: Bing Entity Search API エンドポイント
titleSuffix: Azure Cognitive Services
description: Bing Entity Search API には、クエリに基づいて Web からのエンティティを返す 1 つのエンドポイントが含まれています。 これらの検索結果は JSON 形式で返されます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 7fc65b27c3c02d6102210ae277690795d763d91a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96338259"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entity Search API エンドポイント

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。


Bing Entity Search API には、クエリに基づいて Web からのエンティティを返す 1 つのエンドポイントが含まれています。 これらの検索結果は JSON 形式で返されます。

## <a name="get-entity-results-from-the-endpoint"></a>エンドポイントからエンティティの結果を取得する

**Bing API** を使用してエンティティの結果を取得するには、次のエンドポイントに `GET` 要求を送信します。 検索要求をカスタマイズするには、[ヘッダー](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers)と[クエリ パラメーター](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters)を使用します。 検索要求は、`?q=` パラメーターを使用して送信できます。

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Bing Entity Search API とは](overview.md)

## <a name="see-also"></a>関連項目 

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどの詳細については、「[Bing Entity Search API v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)」の参照記事をご覧ください。