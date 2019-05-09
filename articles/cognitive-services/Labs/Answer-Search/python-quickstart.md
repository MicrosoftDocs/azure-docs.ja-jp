---
title: クイック スタート:Project Answer Search (Python)
titlesuffix: Azure Cognitive Services
description: Project Answer Search の使用を開始する Python の例。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 2eaf07e041998efade1091861144a2dc4d78c56d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860305"
---
# <a name="quickstart-project-answer-search-with-python"></a>クイック スタート: Python での Project Answer Search

Python での次の例では、"Rock of Gibraltar".に関する情報の要求を作成し、送信しています。

## <a name="prerequisites"></a>前提条件

[Cognitive Services Labs](https://aka.ms/answersearchsubscription) で無料試用版のアクセス キーを取得します

この例では Python 3.6.4 を使用しています

## <a name="code-scenario"></a>シナリオのコードを書く 

次のコードは、URL Preview を作成します。
これは、次の手順で実装されます。
1. エンドポイントをホストとパスで指定する変数を宣言します。
2. プレビューするクエリ URL を指定し、クエリ パラメーターを追加します。  
3. クエリ パラメーターを設定します。
4. 要求を作成し、*Ocp-Apim-Subscription-Key* ヘッダーを追加する Search 関数を定義します。
5. *Ocp-Apim-Subscription-Key* ヘッダーを設定します。 
6. 接続を作成し、要求を送信します。
7. JSON の結果を出力します。

このデモの完全なコードを次に示します。

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'https://api.labs.cognitive.microsoft.com'
path = '/answerSearch/v7.0/search '

query = 'Rock of Gibraltar'

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
- [C# のクイック スタート](c-sharp-quickstart.md)
- [Java のクイック スタート](java-quickstart.md)
- [Node のクイックスタート](node-quickstart.md)
