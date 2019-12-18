---
title: クイック スタート:REST API と Python を使用してイメージを検索する - Bing Image Search
titleSuffix: Azure Cognitive Services
description: このクイック スタートを使用して、Python を使って Bing Image Search REST API にイメージ検索要求を送信し、JSON 応答を受信します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: f3d00688feb0f9d42e80cdbb51753483e53c388d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930736"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>クイック スタート:Bing Image Search REST API と Python を使用してイメージを検索する

このクイック スタートでは、Bing Image Search API に検索要求を送信する基本的な方法について説明します。 この Python アプリケーションは、検索クエリを API に送信し、その結果から最初の画像の URL を表示します。 このアプリケーションは Python で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

この例は、起動 Binder バッジ上でクリックすることで、[MyBinder](https://mybinder.org) の Jupyter Notebook として実行できます。

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


このサンプルのソース コードは、追加のエラー処理と注釈を含め、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) で入手できます。


## <a name="prerequisites"></a>前提条件

* [Python 2.x または 3.x](https://www.python.org/)
* [Python Imaging Library (PIL)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. 任意の IDE またはエディターで新しい Python ファイルを作成し、次のモジュールをインポートします。 サブスクリプション キー、検索のエンドポイント、検索語句の変数を作成します。 `search_url` には、以下のグローバル エンドポイントを指定するか、Azure portal に表示される、リソースの[カスタム サブドメイン](../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを指定できます。

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. ディクショナリを作成し、サブスクリプション キーを値として追加して、`Ocp-Apim-Subscription-Key` ヘッダーにサブスクリプション キーを追加します。 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>検索要求を作成して送信する

1. 検索要求のパラメーターに使用するディクショナリを作成します。 `q` パラメーターに検索語句を追加します。 パブリック ドメインから画像を検索するために、`license` パラメーターには "public" を使用します。 写真のみを検索する場合は、`imageType` に "photo" を使用します。

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. `requests` ライブラリを使用して、Bing Image Search API を呼び出します。 ヘッダーとパラメーターを要求に追加し、その応答を JSON オブジェクトとして返します。 応答の `thumbnailUrl` フィールドからいくつかのサムネイル画像の URL を取得します。

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
    ```

## <a name="view-the-response"></a>応答の表示

1. matplotlib ライブラリを使用して、4 つの列と 4 つの行から成る新しい図を作成します。 

2. 図の行と列を繰り返し処理し、PIL ライブラリの `Image.open()` メソッドを使用して、それぞれの領域に画像のサムネイルを追加します。 

3. `plt.show()` を使用して図を描画し、画像を表示します。

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    plt.show()
    ```


## <a name="example-json-response"></a>JSON の応答例

Bing Image Search API からの応答は、JSON として返されます。 このサンプル応答は、1 つの結果だけを表示するように切り詰められています。

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Image Search の単一ページ アプリのチュートリアル](../tutorial-bing-image-search-single-page-app.md)

* [Bing Image Search API とは](../overview.md)  
* Bing Search API シリーズの[価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [無料の Cognitive Services アクセス キーを取得する](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Azure Cognitive Services のドキュメント](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
