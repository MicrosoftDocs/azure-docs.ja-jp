---
title: クイック スタート:Python SDK を使用して Bing Custom Search エンドポイントを呼び出す | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Bing Custom Search SDK for Python を使用してカスタム検索結果を取得します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: aahi
ms.openlocfilehash: c4c5059bc57ea33357145f6b119456dc6c5bdb7b
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571815"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>クイック スタート:Python SDK を使用して Bing Custom Search エンドポイントを呼び出す 

このクイック スタートでは、Python SDK を使用して、Bing Custom Search インスタンスに検索結果を要求します。 Bing Custom Search には、ほとんどのプログラミング言語に対応した REST API があり、Bing Custom Search SDK を使用すれば、既存のアプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、追加のエラー処理と注釈を含め、[GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) で入手できます。

## <a name="prerequisites"></a>前提条件

- Bing Custom Search インスタンス。 「[クイック スタート:初めての Bing Custom Search インスタンスを作成する](quick-start.md)」で詳細を確認する。
- Python [2.x または 3.x](https://www.python.org/) 
- [Bing Custom Search SDK for Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>Python SDK のインストール

次のコマンドを使用して、Bing Custom Search SDK をインストールします。

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>新しいアプリケーションを作成する

好みのエディターまたは IDE で新しい Python ファイルを作成し、次の import を追加します。

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>検索クライアントを作成して要求を送信する

1. サブスクリプション キーの変数を作成します。

    ```python
    subscription_key = 'your-subscription-key'
    ```

2. サブスクリプション キーを使用して、`CognitiveServicesCredentials` オブジェクトを使って、`CustomSearchClient` のインスタンスを作成します。 

    ```python
    client = CustomSearchClient(CognitiveServicesCredentials(subscription_key))
    ```

3. `client.custom_instance.search()` を含む検索要求を送信します。 検索語句を `query` パラメーターに追加し、検索インスタンスを使用するためにカスタム構成 ID に `custom_config` を設定します。 ID を [Bing Custom Search ポータル](https://www.customsearch.ai/)から取得するには、**[運用]** タブをクリックします。

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>検索結果を表示する

任意の Web ページの検索結果が見つかった場合は、1 つ目を取得し、その名前、URL、および見つかった Web ページの合計を印刷します。

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Custom Search Web アプリの作成](./tutorials/custom-search-web-page.md)
