---
title: Bing Custom Search エンドポイント
titleSuffix: Azure Cognitive Services
description: 関心のあるトピックに合わせてカスタマイズした検索エクスペリエンスを作成します。 ユーザーには、関心のあるコンテンツに合わせた検索結果が表示されます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 35dd147574eb7820debba55b105deed001be80fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96338488"
---
# <a name="custom-search"></a>カスタム検索

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。
Bing Custom Search を使用すると、関心のあるトピックに合わせてカスタマイズした検索エクスペリエンスを作成できます。 ユーザーは、関係のないコンテンツが含まれる検索結果のページを移動し続けるのではなく、関心のあるコンテンツに合わせてカスタマイズされた検索結果を参照できます。

## <a name="custom-search-endpoint"></a>Custom Search エンドポイント
Bing Custom Search API を使用して結果を取得するには、次のエンドポイントに `GET` 要求を送信します。 ヘッダーと URL パラメーターを使用して、さらに詳細を定義します。

エンドポイント: 検索候補を、`?q=""` によって定義されたユーザーの入力に関連する JSON 結果として返します。
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Custom Search ソースの設定方法を説明している礼については、[チュートリアル](./tutorials/custom-search-web-page.md)をご覧ください。 ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどについて詳しくは、[Bing Custom Search API v7](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference) のリファレンスをご覧ください。

## <a name="custom-search-response-json"></a>Custom Search の応答 JSON
カスタム検索要求では、結果を JSON オブジェクトとして返します。[応答オブジェクト](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects)に関するページをご覧ください。 

## <a name="custom-autosuggest"></a>Custom Autosuggest
Custom Autosuggest API によって、部分検索のクエリ用語を Bing に送信すると、構成可能なクエリ候補の一覧が返されます。 Custom Autosuggest を使用した場合、API から返された候補を追加し、Bing によって生成された候補を含めるかどうかを必要に応じて指定します。

## <a name="custom-autosuggest-endpoint"></a>Custom Autosuggest エンドポイント
カスタム クエリ候補を要求するには、GET 要求を次の URL へ送信します。

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

カスタム候補の定義については、[カスタム検索候補の定義](define-custom-suggestions.md)に関するページをご覧ください。

## <a name="custom-image-search"></a>Custom Image Search
Custom Image Search API を使用することで、検索クエリを Bing に送信して、Custom Search インスタンスから対応するイメージの一覧を取得できます。

## <a name="custom-image-search-endpoint"></a>Custom Image Search エンドポイント
Custom Search インスタンスからのイメージを要求するには、以下の URL に GET 要求を送信します。

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Custom Search インスタンスの構成については、「[Configure your custom search experience](./define-your-custom-view.md)」(カスタム検索エクスペリエンスの構成) をご覧ください。

## <a name="next-steps"></a>次のステップ
**Bing** API では、種類に応じた結果を返す検索アクションがサポートされます。  すべての検索エンドポイントは、結果を JSON 応答オブジェクトとして返します。  すべてのエンドポイントは、特定の言語や場所を経度、緯度、検索半径によって返すクエリをサポートします。

各エンドポイントでサポートされるパラメーターについて詳しくは、各種類のリファレンス ページをご覧ください。
Custom Search API を使用した基本的な要求の例については、[Custom Search クイック スタート](/azure/cognitive-services/bing-custom-search/)のページをご覧ください。