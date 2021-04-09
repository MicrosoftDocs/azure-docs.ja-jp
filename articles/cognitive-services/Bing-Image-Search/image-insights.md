---
title: イメージに関する分析情報を取得する - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Bing Image Search API を使用し、画像に関する情報をさらに取得する方法について説明します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: 9dffc16a1d1171343fa10fce10fddba2a12c2167
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96348247"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Bing Image Search API を利用してイメージに関する分析情報を取得する

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

> [!IMPORTANT]
> /images/details エンドポイントを使ってイメージに関する分析情報を取得するのではなく、[Visual Search](../bing-visual-search/overview.md) を使用します。これにより、より包括的な分析情報が得られます。


各イメージには分析情報トークンが含まれており、イメージに関する情報を取得することができます。 たとえば、一連の関連するイメージ、イメージを含む Web ページ、またはイメージで表示される製品を購入できる業者のリストを取得できます。  

イメージに関する分析情報を取得するには、応答でイメージの [imageInsightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-imageinsightstoken) トークンを取り込みます。

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

次に、Image Details エンドポイントを呼び出し、`imageInsightsToken` のトークンに [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) クエリ パラメーターを設定します。  

取得する分析情報を指定するには、`modules` クエリ パラメーターを設定します。 すべての分析情報を取得するには、`modules` を `All` に設定します。 キャプションとコレクションの分析情報のみを取得するには、`modules` を `Caption%2CCollection` に設定します。 使用可能な分析情報の完全なリストについては、「[modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested)」を参照してください。 すべてのイメージについて、すべての分析情報を使用できるわけではありません。 応答には要求したすべての分析情報 (使用可能な場合) が含まれます。

次の例では、前述のイメージについて、使用可能な分析情報をすべて要求します。

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>既知のイメージの分析情報の取得

分析情報を取得するイメージへの URL がある場合は、[insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) パラメーターではなく、[imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) クエリ パラメーターを使用してイメージを指定します。 または、イメージ ファイルがある場合は、POST 要求の本文でイメージのバイナリを送信できます。 POST 要求を使用する場合は、`Content-Type` ヘッダーを `multipart/data-form` に設定する必要があります。 いずれのオプションの場合でも、イメージのサイズが 1 MB を超えることはできません。  

イメージへの URL がある場合に、イメージの分析情報を要求する方法の例を以下に示します。

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>すべてのイメージの分析情報の取得  

イメージのすべての分析情報を要求するには、[modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) クエリ パラメーターを `All` に設定します。 関連検索を行うには、要求にユーザーのクエリ文字列を含める必要があります。 [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) を使用してイメージを指定する例を以下に示します。  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

最上位レベルのオブジェクトは、[Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) オブジェクトではなく、[ImageInsightsResponse](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) オブジェクトです。  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>イメージ内のエンティティの認識  

エンティティの認識機能では、イメージ内のエンティティ (現時点では人物のみ) を識別します。 イメージ内のエンティティを識別するには、[modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) クエリ パラメーターを `RecognizedEntities` に設定します。  

> [!NOTE]
> このモジュールを他のモジュールと共に指定することはできません。 このモジュールを他のモジュールと共に指定する場合、応答に認識されたエンティティは含まれません。  

[imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) パラメーターを使用してイメージを指定する方法を以下に示します。 必ず、クエリ パラメーターを URL エンコードしてください。  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

前述の要求への応答は次のようになります。 イメージに 2 人の人物が含まれているため、応答では各人物の領域が識別されます。 この場合、人物は CelebrityAnnotations グループと CelebRecognitionAnnotations グループで認識されています。 Bing では、元のイメージの人物と一致する可能性に基づいて、各グループに人物がリストされます。 このリストは信頼度の降順に示されます。 CelebRecognitionAnnotations グループでは、一致が正しい最高レベルの信頼度が提供されます。  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

`region` フィールドでは、Bing でエンティティが認識されたイメージの領域が識別されます。 人物の場合、領域はその人物の顔を表します。  

四角形の値は、元のイメージの幅と高さに対して相対的であり、0.0 から 1.0 の範囲内にあります。 たとえば、イメージが 300 x 200 で、領域の左上隅の位置が (10, 20)、右下隅の位置が (290, 150) の場合、正規化された四角形は次のようになります。  

-   左: 10 / 300 = 0.03333...  
-   上: 20 / 200 = 0.1  
-   右: 290 / 300 = 0.9667...  
-   下: 150 / 200 = 0.75  

Bing によって後続の分析情報呼び出しで返される領域を使用することができます。 たとえば、認識されたエンティティの視覚的に類似するイメージを取得する場合です。 詳細については、「視覚的に類似するモジュールとエンティティ認識モジュールで使用するイメージのトリミング」を参照してください。 イメージのトリミングに使用する領域フィールドとクエリ パラメーター間のマッピングを以下に示します。  

-   左: [cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
-   上: [cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
-   右: [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
-   下: [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>視覚的に類似するイメージの検索  

元のイメージと視覚的に類似するイメージを検索するには、[modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) クエリ パラメーターを SimilarImages に設定します。  

次の要求は、視覚的に類似するイメージを取得する方法を示します。 この要求では、[insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) クエリ パラメーターを使用して、元のイメージを識別します。 関連性を向上させるには、ユーザーのクエリ文字列を含める必要があります。  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


前述の要求への応答は次のようになります。  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>視覚的に類似するモジュールとエンティティ認識モジュールで使用するイメージのトリミング  

Bing で、イメージが視覚的に類似しているかどうかを判断する場合や、エンティティ認識を実行する場合に使用されるイメージの領域を指定するには、[cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)、[cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)、[cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)、[car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car) クエリ パラメーターを使用します。 既定では、Bing でイメージ全体が使用されます。  

パラメーターでは、Bing で比較するために使用される領域の左上隅と右下隅を指定します。 元のイメージの幅と高さの分数として値を指定します。 小数値は左上隅の (0.0, 0.0) で始まり、右下隅の (1.0, 1.0) で終わります。 たとえば、左上隅が上部から下に 4 分の 1、左側から内側に 4 分の 1 の位置で始まるように指定する場合は、`cal` を 0.25 に、`cat` を 0.25 に設定します。  

次の一連の呼び出しは、トリミング領域を指定する効果を示しています。 最初の呼び出しにはトリミングは含まれておらず、Bing ではイメージの中央の横一列に並んで立っている 2 人の人物を認識します。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

応答には、2 つの認識されたエンティティが示されます。  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

2 番目の呼び出しではイメージが縦半分にトリミングされ、Bing ではイメージの右側の 1 人の人物が認識されています。  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

応答には、1 つの認識されたエンティティが示されます。  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>視覚的に類似する製品の検索  

元のイメージにある製品と視覚的に類似する製品を含むイメージを検索するには、[modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) クエリ パラメーターを SimilarProducts に設定します。  

次の要求は、視覚的に類似する製品のイメージを取得する方法を示しています。 この要求では、[insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) クエリ パラメーターを使用して、前述の要求で返された元のイメージを識別します。 関連性を向上させるには、ユーザーのクエリ文字列を含める必要があります。  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

前述の要求への応答は次のようになります。 応答には類似製品のイメージが含まれており、製品をオンラインで提供する業者の数、製品の評価があるかどうか、検出された最低価格が示されます (`aggregateOffer` フィールドを参照)。  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

オンラインで製品を提供する業者のリストを取得するには ([offerCount](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) フィールドを参照)、API をもう一度呼び出し、`modules` を ShoppingSources に設定します。 次に、`insightsToken` クエリ パラメーターを、製品の概要イメージで検出されたトークンに設定します。  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

前述の要求への応答は次のようになります。  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```