---
title: クイック スタート - Python で API にクエリを送信する - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: このクイックスタートを使用して、Python で Bing Local Business Search API の使用を開始します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 30e66260f0603139bf45f9a0f0b5f19539ae468e
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843557"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>クイック スタート:Python で Bing Local Business Search API にクエリを送信する

このクイックスタートを利用して、Azure Cognitive Service である Bing Local Business Search API への要求を送信する方法について説明します。 このシンプルなアプリケーションは Python で記述されていますが、この API は、HTTP 要求の発行と JSON の解析が可能な任意のプログラミング言語と互換性がある RESTful Web サービスです。

このサンプル アプリケーションでは、検索クエリについて、API からのローカルな応答データを取得します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/) 2.x または 3.x。
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Bing Search リソースを作成"  target="_blank">Bing Search リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。

## <a name="run-the-complete-application"></a>完全なアプリケーションを実行する

次の例では、ローカライズされた結果を取得します。この結果は、次の手順で実装されます。
1. エンドポイントをホストとパスで指定する変数を宣言します。
2. クエリ パラメーターを指定します。 
3. 要求を作成し、`Ocp-Apim-Subscription-Key` ヘッダーを追加する search 関数を定義します。
4. `Ocp-Apim-Subscription-Key` ヘッダーを設定します。 
5. 接続して要求を送信します。
6. JSON の結果を出力します。

このデモの完全なコードは次のとおりです。

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>次のステップ
- [Local Business Search (Java) のクイック スタート](local-search-java-quickstart.md)
- [Local Business Search (C#) のクイック スタート](local-quickstart.md)
- [Local Business Search (Node.js) のクイック スタート](local-search-node-quickstart.md)
