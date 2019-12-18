---
title: クイック スタート:Python 用の Bing Web Search SDK を使用する
titleSuffix: Azure Cognitive Services
description: Bing Web Search SDK を使用すると、Python アプリケーションに Bing Web Search を簡単に統合することができます。 このクイック スタートでは、要求を送信し、JSON 応答を受け取って、結果をフィルター処理および解析する方法を学習します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 0f6dceac3e46f8e36d2da52c15c0b0696e026a0e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977218"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-python"></a>クイック スタート:Python 用の Bing Web Search SDK を使用する

Bing Web Search SDK を使用すると、Python アプリケーションに Bing Web Search を簡単に統合することができます。 このクイック スタートでは、要求を送信し、JSON 応答を受け取って、結果をフィルター処理および解析する方法を学習します。

今すぐコードを確認したい場合は、 GitHub で、[Python 用の Bing Web Search SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)を入手できます。

## <a name="prerequisites"></a>前提条件
Bing Web Search SDK は、Python 2.7、3.3、3.4、3.5、および 3.6 と互換性があります。 このクイック スタートには仮想環境を使用することをお勧めします。

* Python 2.7、3.3、3.4、3.5、または 3.6
* Python 2.7 向けの [virtualenv](https://docs.python.org/3/tutorial/venv.html)
* Python 3.x 向けの [venv](https://pypi.python.org/pypi/virtualenv)

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-and-configure-your-virtual-environment"></a>仮想環境の作成と構成

仮想環境の設定と構成の手順は、Python 2.x と Python 3.x では異なります。 仮想環境を作成して初期化するには、以下の手順に従います。

### <a name="python-2x"></a>Python 2.x

次のように、Python 2.7 向け `virtualenv` を使用して仮想環境を作成します。

```console
virtualenv mytestenv
```

環境をアクティブ化します。

```console
cd mytestenv
source bin/activate
```

Bing Web Search SDK 依存関係をインストールします。

```console
python -m pip install azure-cognitiveservices-search-websearch
```

### <a name="python-3x"></a>Python 3.x

次のように、Python 3.X 向け `venv` を使用して仮想環境を作成します。

```console
python -m venv mytestenv
```

Bing Web Search SDK 依存関係をインストールします。

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```

## <a name="create-a-client-and-print-your-first-results"></a>クライアントの作成と最初の結果の出力

これで仮想環境の設定と依存関係のインストールができたので、次はクライアントを作成します。 クライアントは、Bing Web Search API への要求と Bing Web Search API からの応答を処理します。

応答に Web ページ、画像、ニュース、または動画が含まれている場合は、それぞれの最初の結果が出力されます。

1. 普段使用している IDE またはエディターで、新しい Python プロジェクトを作成します。

1. このサンプル コードをプロジェクトにコピーします。 `endpoint` には、以下のグローバル エンドポイントを指定するか、Azure portal に表示される、リソースの[カスタム サブドメイン](../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを指定できます。  

    ```python
    # Import required modules.
    from azure.cognitiveservices.search.websearch import WebSearchAPI
    from azure.cognitiveservices.search.websearch.models import SafeSearch
    from msrest.authentication import CognitiveServicesCredentials

    # Replace with your subscription key.
    subscription_key = "YOUR_SUBSCRIPTION_KEY"

    # Instantiate the client and replace with your endpoint.
    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key), base_url = "YOUR_ENDPOINT")

    # Make a request. Replace Yosemite if you'd like.
    web_data = client.web.search(query="Yosemite")
    print("\r\nSearched for Query# \" Yosemite \"")

    '''
    Web pages
    If the search response contains web pages, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.web_pages, 'value'):

        print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

        first_web_page = web_data.web_pages.value[0]
        print("First web page name: {} ".format(first_web_page.name))
        print("First web page URL: {} ".format(first_web_page.url))

    else:
        print("Didn't find any web pages...")

    '''
    Images
    If the search response contains images, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.images, 'value'):

        print("\r\nImage Results#{}".format(len(web_data.images.value)))

        first_image = web_data.images.value[0]
        print("First Image name: {} ".format(first_image.name))
        print("First Image URL: {} ".format(first_image.url))

    else:
        print("Didn't find any images...")

    '''
    News
    If the search response contains news, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.news, 'value'):

        print("\r\nNews Results#{}".format(len(web_data.news.value)))

        first_news = web_data.news.value[0]
        print("First News name: {} ".format(first_news.name))
        print("First News URL: {} ".format(first_news.url))

    else:
        print("Didn't find any news...")

    '''
    If the search response contains videos, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.videos, 'value'):

        print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

        first_video = web_data.videos.value[0]
        print("First Videos name: {} ".format(first_video.name))
        print("First Videos URL: {} ".format(first_video.url))

    else:
        print("Didn't find any videos...")
    ```

1. `SUBSCRIPTION_KEY` を有効なサブスクリプション キーに置き換えます。

1. `YOUR_ENDPOINT` をポータルの実際のエンドポイント url に置き換えます。

1. プログラムを実行します。 (例: `python your_program.py`)。

## <a name="define-functions-and-filter-results"></a>関数の定義と結果のフィルター処理

Bing Web Search API の最初の呼び出しを行ったので、関数をいくつか見てみましょう。 以下のセクションでは、クエリを調整して結果をフィルター処理するための SDK の機能に注目します。 各関数は、前のセクションで作成した Python プログラムに追加することができます。

### <a name="limit-the-number-of-results-returned-by-bing"></a>Bing から返される結果の数の制限

このサンプルでは、`count` パラメーターと `offset` パラメーターを使用して、SDK の [`search` メソッド](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python)によって返される結果の数を制限しています。 最初の結果の `name` と `url` が出力されます。

1. 次のコードを Python プロジェクトに追加します。

   ```python
    # Declare the function.
    def web_results_with_count_and_offset(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, offset, and count using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
            print("\r\nSearching for \"Best restaurants in Seattle\"")

            if web_data.web_pages.value:
                '''
                If web pages are available, print the # of responses, and the first and second
                web pages returned.
                '''
                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't find any web pages...")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. プログラムを実行します。

### <a name="filter-for-news-and-freshness"></a>ニュースと鮮度のフィルター処理

このサンプルでは、`response_filter` パラメーターと `freshness` パラメーターを使用して、SDK の [`search` メソッド](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations)による検索結果をフィルター処理します。 返される検索結果は、ニュース記事と、Bing が過去 24 時間以内に検出したページだけに制限されます。 最初の結果の `name` と `url` が出力されます。

1. 次のコードを Python プロジェクトに追加します。

    ```python
    # Declare the function.
    def web_search_with_response_filter(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
        try:
            '''
            Set the query, response_filter, and freshness using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="xbox",
                response_filter=["News"],
                freshness="Day")
            print("\r\nSearching for \"xbox\" with the response filter set to \"News\" and freshness filter set to \"Day\".")

            '''
            If news articles are available, print the # of responses, and the first and second
            articles returned.
            '''
            if web_data.news.value:

                print("# of news results: {}".format(len(web_data.news.value)))

                first_web_page = web_data.news.value[0]
                print("First article name: {} ".format(first_web_page.name))
                print("First article URL: {} ".format(first_web_page.url))

                print("")

                second_web_page = web_data.news.value[1]
                print("\nSecond article name: {} ".format(second_web_page.name))
                print("Second article URL: {} ".format(second_web_page.url))

            else:
                print("Didn't find any news articles...")

        except Exception as err:
            print("Encountered exception. {}".format(err))

    # Call the function.
    web_search_with_response_filter(subscription_key)
    ```

1. プログラムを実行します。

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>セーフ サーチ、回答数、昇格フィルターの使用

このサンプルでは、`answer_count`、`promote`、および `safe_search` パラメーターを使用して、SDK の [`search` メソッド](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python)による検索結果をフィルター処理します。 最初の結果の `name` と `url` が表示されます。

1. 次のコードを Python プロジェクトに追加します。

    ```python
    # Declare the function.
    def web_search_with_answer_count_promote_and_safe_search(subscription_key):

        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, answer_count, promote, and safe_search parameters using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(
                query="Niagara Falls",
                answer_count=2,
                promote=["videos"],
                safe_search=SafeSearch.strict  # or directly "Strict"
            )
            print("\r\nSearching for \"Niagara Falls\"")

            '''
            If results are available, print the # of responses, and the first result returned.
            '''
            if web_data.web_pages.value:

                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't see any Web data..")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. プログラムを実行します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このプロジェクトの使用を終了するときに、必ずプログラムのコードからサブスクリプション キーを削除し、仮想環境を非アクティブ化してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Cognitive Services の Python 向け SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="see-also"></a>関連項目

* [Azure Python SDK リファレンス](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/websearch)
