---
title: Bing Visual Search Python クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.openlocfilehash: 3af9b9e4f07d3d88043dcf7f6a262fdf6a3b316c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947102"
---
このクイックスタートを使用すると、Python クライアント ライブラリを使用して、Bing Visual Search サービスから画像に関する分析情報を取得することができます。 Bing Visual Search の REST API はほとんどのプログラミング言語に対応していますが、このクライアント ライブラリを使用すると、サービスをお使いのアプリケーションに簡単に統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) にあります。 

[リファレンス ドキュメント](/python/api/azure-cognitiveservices-search-visualsearch/) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-visualsearch) | [パッケージ (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-visualsearch/) | [サンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)

## <a name="prerequisites"></a>前提条件

* [Python](https://www.python.org/) 2.x または 3.x
* [仮想環境](https://docs.python.org/3/tutorial/venv.html)を使用することをお勧めします。 [venv モジュール](https://pypi.python.org/pypi/virtualenv)を使用して仮想環境をインストールして初期化します。
* Python 用 Bing Visual Search クライアント ライブラリ。 これは、次のコマンドを使用してインストールできます。
    1. `cd mytestenv`
    2. `python -m pip install azure-cognitiveservices-search-visualsearch`

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. お気に入りの IDE またはエディターで新しい Python ファイルを作成し、次の import ステートメントを追加します。 

    ```python
    import http.client, urllib.parse
    import json
    import os.path
    from azure.cognitiveservices.search.visualsearch import VisualSearchClient
    from azure.cognitiveservices.search.visualsearch.models import (
        VisualSearchRequest,
        CropArea,
        ImageInfo,
        Filters,
        KnowledgeRequest,
    )
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. サブスクリプション キー、カスタム構成 ID、およびアップロードする画像のための変数を作成します。 
    
    ```python
    subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
    PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
    image_path = os.path.join(PATH, "image.jpg")
    
    ```

3. クライアントのインスタンス化

    ```python
    client = VisualSearchClient(endpoint="https://api.cognitive.microsoft.com", credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-the-search-request"></a>検索要求を送信する

1. 画像ファイルを開いた状態で、`VisualSearchRequest()` をシリアル化し、`visual_search()` の `knowledge_request` パラメーターとして渡します。

    ```python
    with open(image_path, "rb") as image_fd:
        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(VisualSearchRequest().serialize())
    
        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
    ```

2. 結果が返されると、結果、タグ、および最初のタグのアクションが出力されます。

    ```python
    if not result:
            print("No visual search result data.")
    
            # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")
    
        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))
    
            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [シングルページ Web アプリの作成](../../tutorial-bing-visual-search-single-page-app.md)
