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
ms.date: 06/16/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 115d0204b4e1a05e5d4c655efb52b3fc3123bebe
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851836"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>クイック スタート:Python と Bing News Search REST API を使用してニュース検索を実行する

このクイックスタートを使用して、Bing News Search API を呼び出してみましょう。 このシンプルな Python アプリケーションは、検索クエリを API に送信し、JSON の結果を処理します。 

このアプリケーションは Python で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

このコードを [MyBinder](https://mybinder.org) で Jupyter Notebook として実行するには、**起動バインダー** バッジを選択します。 

[![起動バインダー](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py) でも入手できます。

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

任意の IDE またはエディターで新しい Python ファイルを作成し、request モジュールをインポートします。 サブスクリプション キー、エンドポイント、検索語句の変数を作成します。 次のコードのグローバル エンドポイントを使用するか、Azure portal に表示される、対象のリソースの[カスタム サブドメイン](../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用することができます。

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

## <a name="create-parameters-for-the-request"></a>要求のパラメーターを作成する

`Ocp-Apim-Subscription-Key` をキーに使用して新しいディクショナリにご利用のサブスクリプション キーを追加します。 検索のパラメーターも同様にします。

```python
headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
```

## <a name="send-a-request-and-get-a-response"></a>要求を送信して応答を取得する

1. 要求ライブラリを使用して Bing Visual Search API を呼び出します。その際、サブスクリプション キー、および前の手順で作成したディクショナリ オブジェクトを使用します。

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = json.dumps(response.json())
    ```

2. JSON オブジェクトとして `search_results` に格納されている API から、応答に含まれている記事の説明にアクセスします。 
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="display-the-results"></a>結果を表示する

これらの説明は、太字で強調表示される検索キーワードと共に、テーブルで表示されます。

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](tutorial-bing-news-search-single-page-app.md)
