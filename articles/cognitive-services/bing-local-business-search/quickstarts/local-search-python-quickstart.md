---
title: クイック スタート - Python で Bing Local Business Search API にクエリを送信する | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: この記事を使用して、Python で Bing Local Business Search API の使用を開始します。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 241ade7d4866186946c371aa1786bc913d045483
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762944"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>クイック スタート:Python で Bing Local Business Search API にクエリを送信する

このクイック スタートを利用して、Azure Cognitive Service である Bing Local Business Search API への要求の送信を開始します。 このシンプルなアプリケーションは Python で記述されていますが、API は、HTTP 要求の発行と JSON の解析が可能な任意のプログラミング言語と互換性がある RESTful Web サービスです。

このサンプル アプリケーションでは、検索クエリ `hotel in Bellevue` に対する API からのローカルな応答データを取得します。

## <a name="prerequisites"></a>前提条件

* [Python](https://www.python.org/) 2.x または 3.x
 
Bing API を使用して [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を取得している必要があります。 このクイック スタートには[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)で十分です。 無料試用版で提供されるアクセス キーを使用します。  「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」もご覧ください。

## <a name="run-the-complete-application"></a>完全なアプリケーションを実行する

次のコードでは、ローカライズされた結果を取得します。 これは、次の手順で実装されます。
1. エンドポイントをホストとパスで指定する変数を宣言します。
2. クエリ パラメーターを指定します。 
3. 要求を作成し、Ocp-Apim-Subscription-Key ヘッダーを追加する Search 関数を定義します。
4. Ocp-Apim-Subscription-Key ヘッダーを設定します。 
5. 接続して要求を送信します。
6. JSON の結果を出力します。

このデモの完全なコードを次に示します。

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com/bing'
path = '/v7.0/localbusinesses/search'

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

## <a name="next-steps"></a>次の手順
- [Local Business Search (Java) のクイック スタート](local-search-java-quickstart.md)
- [Local Business Search (C#) のクイック スタート](local-quickstart.md)
- [Local Business Search (Node.js) のクイック スタート](local-search-node-quickstart.md)
