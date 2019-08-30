---
title: クイック スタート:イメージを検索する - Bing Image Search SDK for Python
titleSuffix: Azure Cognitive Services
description: このクイック スタートを利用し、Bing Image Search SDK を使用して最初の画像検索を行います。この SDK は、API のラッパーであり、同じ機能を含んでいます。 このシンプルな Python アプリケーションは、画像検索クエリを送信し、JSON 応答を解析して、返された最初の画像の URL を表示します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 08/26/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: b5a4cd36f204dfe708e5acd8818d9925b5accc30
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034707"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-python"></a>クイック スタート:Bing Image Search SDK for Python を使用してイメージを検索する

このクイック スタートを利用し、Bing Image Search SDK を使用して最初の画像検索を行います。この SDK は、API のラッパーであり、同じ機能を含んでいます。 このシンプルな Python アプリケーションは、画像検索クエリを送信し、JSON 応答を解析して、返された最初の画像の URL を表示します。

このサンプルのソース コードは、追加のエラー処理と注釈を含め、[GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) で入手できます。

## <a name="prerequisites"></a>前提条件
**[検索]** で [Cognitive Services のアクセス キー](https://azure.microsoft.com/try/cognitive-services/)を取得します。  「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」もご覧ください。

* [Python 2.7 または 3.4](https://www.python.org/) 以降。

* Python 用 [Azure Image Search SDK](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/)
    * `pip install azure-cognitiveservices-search-imagesearch` を使用してインストールする

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションの作成と初期化

1. お気に入りの IDE またはエディターで新しい Python スクリプトを作成し、次のインポートを行います。

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. サブスクリプション キーと検索用語の変数を作成します。

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>画像検索クライアントを作成する

1. `CognitiveServicesCredentials` のインスタンスを作成し、それを使用してクライアントをインスタンス化します。

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
1. Bing Image Search API に検索クエリを送信します。
    ```python
    image_results = client.images.search(query=search_term)
    ```
   ## <a name="process-and-view-the-results"></a>結果の処理と表示

応答で返された画像の結果を解析します。


応答に検索結果が含まれている場合は、最初の結果を格納して、返された URL の合計数と共にサムネイルの URL などの詳細を出力します。  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Image Search の単一ページ アプリのチュートリアル](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>関連項目

* [Bing Image Search とは](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [オンラインの対話型デモを試す](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [無料の Cognitive Services アクセス キーを取得する](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Azure Cognitive Services SDK の Python サンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Azure Cognitive Services のドキュメント](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
