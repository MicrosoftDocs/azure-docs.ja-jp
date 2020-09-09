---
title: クイック スタート:Python を使用して Bing Custom Search エンドポイントを呼び出す | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Python を使用して、Bing Custom Search インスタンスに検索結果を要求します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: b29d9921de0226cb8660b3f7e3c6d3e0d4a22754
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852516"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>クイック スタート:Python を使用して Bing Custom Search エンドポイントを呼び出す

このクイックスタートでは、Bing Custom Search インスタンスに検索結果を要求する方法を学習します。 このアプリケーションは Python で記述されていますが、Bing Custom Search API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py) で入手できます。

## <a name="prerequisites"></a>前提条件

- Bing Custom Search インスタンス。 詳細については、「[クイック スタート: 最初の Bing Custom Search インスタンスの作成](quick-start.md)」を参照してください。
- [Python](https://www.python.org/) 2.x または 3.x。

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

- お気に入りの IDE またはエディターで新しい Python ファイルを作成し、次の import ステートメントを追加します。 サブスクリプション キー、カスタム構成 ID、および検索語句のための変数を作成します。

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>検索要求の送信と受信 

1. 検索語句を `q=` クエリ パラメーターに追加し、検索インスタンスのカスタム構成 ID を `customconfig=` パラメーターに追加して、要求 URL を作成します。 パラメーターの区切りには、アンパサンド (`&`) を使用します。 次のコードのグローバル エンドポイントか、Azure portal に表示される、対象のリソースの[カスタム サブドメイン](../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用できます。

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. 対象の Bing Custom Search インスタンスに要求を送信し、返された検索結果を出力します。  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Custom Search Web アプリの作成](./tutorials/custom-search-web-page.md)
