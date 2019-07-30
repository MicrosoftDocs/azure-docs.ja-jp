---
title: Bing Entity Search API エンドポイント
titleSuffix: Azure Cognitive Services
description: Bing Entity Search API エンドポイントの概要を学習して、エンドポイントに要求を送信します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9d08091d0ea6869d13e294e60454f85a84f672ad
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424042"
---
# <a name="bing-entity-search-api-endpoint"></a>Bing Entity Search API エンドポイント


Bing Entity Search API には、クエリに基づいて Web からのエンティティを返す 1 つのエンドポイントが含まれています。 これらの検索結果は JSON 形式で返されます。

## <a name="get-entity-results-from-the-endpoint"></a>エンドポイントからエンティティの結果を取得する

**Bing API** を使用してエンティティの結果を取得するには、次のエンドポイントに `GET` 要求を送信します。 検索要求をカスタマイズするには、[ヘッダー](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers)と[クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters)を使用します。 検索要求は、`?q=` パラメーターを使用して送信できます。

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Entity Search API とは](overview.md)

## <a name="see-also"></a>関連項目 

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどの詳細については、「[Bing Entity Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)」の参照記事をご覧ください。
