---
title: Bing Spell Check API への要求の送信
titleSuffix: Azure Cognitive Services
description: Bing Spell Check のモードや設定など、この API に関連した情報を取り上げます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: ef5d812b1e55713f30928582d9aa9e61bba7c7a6
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423546"
---
# <a name="sending-requests-to-the-bing-spell-check-api"></a>Bing Spell Check API への要求の送信

テキスト文字列のスペルや文法の誤りを確認するには、次のエンドポイントに GET 要求を送信します。  

```
https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
```  
  
要求では、HTTPS プロトコルを使う必要があります。

すべての要求をサーバーから送信することをお勧めします。 クライアント アプリケーションの一部としてキーを配布すると、悪意のあるサードパーティがアクセスする可能性が高くなります。 また、サーバーなら、API の将来のバージョンでアップグレードする場所が 1 つで済みます。

要求では、検査するテキスト文字列が含まれている [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#text) クエリ パラメーターを指定する必要があります。 必須ではありませんが、要求では [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#mkt) クエリ パラメーターも指定するべきです。このパラメーターは、結果取得元の市場を示します。 `mode` などの省略可能なクエリ パラメーターの一覧については、「[クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#query-parameters)」をご覧ください。 すべてのクエリ パラメーターの値は、URL でエンコードする必要があります。  
  
要求では、[Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#subscriptionkey) ヘッダーを指定する必要があります。 省略可能ですが、次のヘッダーも指定することをお勧めします。 これらのヘッダーは、Bing Spell Check API がより正確な結果を返すことに役立ちます。  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#location)  

すべての要求ヘッダーと応答ヘッダーのリストについては、「[Headers](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#headers)」(ヘッダー) を参照してください。

JavaScript を使用して Bing Spell Check API を呼び出すときに、ブラウザーの組み込みのセキュリティ機能によっては、これらのヘッダーの値にアクセスできない場合があります。

この問題を解決するため、CORS プロキシを介して Bing Spell Check API 要求を作成することができます。 このようなプロキシからの応答には、応答ヘッダーをホワイトリストに登録し、それらを JavaScript で使用できるようにする `Access-Control-Expose-Headers` ヘッダーがあります。

CORS プロキシをインストールして[チュートリアル アプリ](../tutorials/spellcheck.md)がオプションのクライアント ヘッダーにアクセスできるようにするのは簡単です。 まず、[Node.js をインストールします](https://nodejs.org/en/download/) (まだインストールしていない場合)。 次に、コマンド プロンプトで次のコマンドを入力します。

    npm install -g cors-proxy-server

次に、HTML ファイル内の Bing Spell Check API エンドポイントを次のように変更します。

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

最後に、次のコマンドを使用して、CORS プロキシを開始します。

    cors-proxy-server

チュートリアル アプリを使用しているときはコマンド ウィンドウを開いたままにしておきます。ウィンドウを閉じるとプロキシが停止します。 検索結果の下の展開可能な HTTP ヘッダー セクションに、`X-MSEdge-ClientID` ヘッダー (など) が表示され、要求ごとに同じであることを確認できます。

## <a name="example-api-request"></a>API 要求の例

すべての推奨されるクエリ パラメーターとヘッダーを含む要求を次に示します。 いずれかの Bing API を初めて呼び出す場合は、クライアント ID ヘッダーを含めないでください。 クライアント ID を含めるのは、過去に Bing API を呼び出したことがあり、かつユーザーとデバイスの組み合わせに対応するクライアント ID が Bing から返されたことがある場合だけです。 
  
> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

前述の要求への応答は次のようになります。 例では、Bing に固有の応答ヘッダーも示されています。

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  

## <a name="next-steps"></a>次の手順

- [Bing Spell Check API とは](../overview.md)
- [Bing Spell Check API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
