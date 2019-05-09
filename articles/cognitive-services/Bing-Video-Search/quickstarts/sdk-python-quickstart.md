---
title: クイック スタート:Bing Video Search SDK for Python を使用して動画を検索する
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Bing Video Search SDK for Python を使用して動画の検索要求を送信します。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: rosh
ms.openlocfilehash: ead69b0165831fef04e68b4c2eb7ea43115ca4ea
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867802"
---
#  <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-python"></a>クイック スタート:Bing Video Search SDK for Python で動画の検索を実行する

このクイック スタートを使用して、Bing Video Search SDK for Python を使ったニュース検索を始めましょう。 Bing Video Search には、ほとんどのプログラミング言語に対応した REST API がありますが、SDK を使用すれば、アプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/video_search_samples.py) にあります。 そちらの方が注釈や機能が詳しく記載されています。

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="prerequisites"></a>前提条件

- [Python](https://www.python.org/) 2.x または 3.x
- Bing Video Search SDK for Python

Python の[仮想環境](https://docs.python.org/3/tutorial/venv.html)を使用することをお勧めします。 仮想環境のインストールと初期化は、[venv モジュール](https://pypi.python.org/pypi/virtualenv)を使用して行えます。 次にようにして、Python 2.7 向けの virtualenv をインストールします。

```console
python -m venv mytestenv
```
   
次のようにして、Bing Video Search SDK をインストールします。

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-videosearch
```

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. お気に入りの IDE またはエディターで新しい Python ファイルを作成し、次の import ステートメントを追加します。 

    ```python
    from azure.cognitiveservices.search.videosearch import VideoSearchAPI
    from azure.cognitiveservices.search.videosearch.models import VideoPricing, VideoLength, VideoResolution, VideoInsightModule
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. サブスクリプション キーの変数を作成します。 
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    ```

## <a name="create-the-search-client"></a>検索クライアントを作成する

`CognitiveServicesCredentials` のインスタンスを作成し、クライアントをインスタンス化します。

    ```python
    client = VideoSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-get-a-response"></a>検索要求を送信して応答を取得する

1. `client.videos.search()` と検索クエリを使用して、Bing Video Search API に要求を送信し、応答を取得します。

    ```python
    video_result = client.videos.search(query="SwiftKey")
    ```

2. 応答に検索結果が含まれている場合は、1 つ目の結果を取得して、その ID、名前、URL を出力します。

    ```python
    if video_result.value:
        first_video_result = video_result.value[0]
        print("Video result count: {}".format(len(video_result.value)))
        print("First video id: {}".format(first_video_result.video_id))
        print("First video name: {}".format(first_video_result.name))
        print("First video url: {}".format(first_video_result.content_url))
    else:
        print("Didn't see any video result data..")
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [シングル ページ Web アプリを作成する](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>関連項目 

* [Bing Video Search API とは](../overview.md)
* [Cognitive Services の .NET 向け SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)