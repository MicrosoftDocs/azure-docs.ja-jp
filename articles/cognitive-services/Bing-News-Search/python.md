---
title: クイック スタート:Python と Bing News Search REST API を使用してニュース検索を実行する
titleSuffix: Azure Cognitive Services
description: このクイック スタートを使用して、Python を使って Bing News Search REST API に要求を送信し、JSON 応答を受信します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 6/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2fc67cbfedccd69ff9063b0575ae51197eceb09d
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423688"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>クイック スタート:Python と Bing News Search REST API を使用してニュース検索を実行する

このクイック スタートを使用すると、Bing News Search API への最初の呼び出しを行い、JSON 応答を受け取ることができます。 このシンプルな JavaScript アプリケーションは、検索クエリを API に送信し、その結果を処理します。 このアプリケーションは Python で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

このコード サンプルは、起動 Binder バッジ上でクリックすることで、[MyBinder](https://mybinder.org) の Jupyter Notebook として実行できます。 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py) でも入手できます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」もご覧ください。

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. 任意の IDE またはエディターで新しい Python ファイルを作成し、request モジュールをインポートします。 サブスクリプション キー、エンドポイント、検索語句の変数を作成します。 エンドポイントは、Azure ダッシュボードで確認できます。

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>要求のパラメーターを作成する

1. `"Ocp-Apim-Subscription-Key"` をキーに使用して新しいディクショナリにご利用のサブスクリプション キーを追加します。 検索のパラメーターも同様にします。

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>要求を送信して応答を取得する

1. requests ライブラリを使用して Bing Visual Search API を呼び出します。その際、サブスクリプション キーと、直前の手順で作成したディクショナリ オブジェクトを使用します。

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. `search_results` には、API からの応答が JSON オブジェクトとして格納されています。 応答に格納されている記事の説明にアクセスします。
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>結果を表示する

これらの説明は、**太字**で強調表示される検索キーワードと一緒に表としてテーブルとして出力できます。

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](tutorial-bing-news-search-single-page-app.md)
