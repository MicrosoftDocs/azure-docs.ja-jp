---
title: クイック スタート:Python で検索を実行する - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: このクイック スタートを使用して、Python を使って Bing Web Search REST API に要求を送信し、JSON 応答を受信します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c1af1142faca76cc58b6b3ca9a7106bc0433ea18
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976368"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>クイック スタート:Python を使用して Bing Web Search API を呼び出す  

このクイック スタートを使用すると、Bing Web Search API への最初の呼び出しを行い、JSON 応答を受け取ることができます。 この Python アプリケーションは、API に検索要求を送信してその応答を表示します。 このアプリケーションは Python で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

この例は、[MyBinder](https://mybinder.org) の Jupyter Notebook として実行されます。 起動バインダー バッジを選択します。

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>前提条件

* [Python 2.x または 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>変数の定義

`subscription_key` の値を Azure アカウントの有効なサブスクリプション キーに置き換えます。

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Bing Web Search API エンドポイントを宣言します。 以下のグローバル エンドポイントを使用するか、Azure portal に表示される、リソースの[カスタム サブドメイン](../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用できます。

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

`search_term` の値を置き換えると、検索クエリを自由にカスタマイズすることができます。

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>要求を行う

このブロックは、`requests` ライブラリを使用して Bing Web Search API を呼び出し、その結果を JSON オブジェクトとして返します。 API キーは `headers` ディクショナリで渡され、検索用語とクエリ パラメーターは `params` ディクショナリで渡されます。 オプションとパラメーターの完全なリストについては、[Bing Web Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) のドキュメントを参照してください。

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>応答の書式設定と表示

`search_results` オブジェクトには、検索結果と、関連するクエリやページなどのメタデータが含まれています。 このコードは、`IPython.display` ライブラリを使用して、応答を書式設定し、ブラウザーに表示します。

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>GitHub のサンプル コード

このコードをローカルで実行する場合は、完全な[サンプルを GitHub で入手](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py)することができます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Web 検索単一ページ アプリのチュートリアル](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
