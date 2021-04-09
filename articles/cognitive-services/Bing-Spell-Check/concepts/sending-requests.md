---
title: Bing Spell Check API への要求の送信
titleSuffix: Azure Cognitive Services
description: Bing Spell Check のモードや設定など、この API に関連した情報を取り上げます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 761cc3908b677b129e85be442b7a593a38a367f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96341565"
---
# <a name="sending-requests-to-the-bing-spell-check-api"></a>Bing Spell Check API への要求の送信

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

テキスト文字列のスペルや文法の誤りを確認するには、次のエンドポイントに GET 要求を送信します。  

```
https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
```  
  
要求では、HTTPS プロトコルを使う必要があります。

すべての要求をサーバーから送信することをお勧めします。 クライアント アプリケーションの一部としてキーを配布すると、悪意のあるサードパーティがアクセスする可能性が高くなります。 また、サーバーなら、API の将来のバージョンでアップグレードする場所が 1 つで済みます。

要求では、検査するテキスト文字列が含まれている [text](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#text) クエリ パラメーターを指定する必要があります。 必須ではありませんが、要求では [mkt](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#mkt) クエリ パラメーターも指定するべきです。このパラメーターは、結果取得元の市場を示します。 `mode` などの省略可能なクエリ パラメーターの一覧については、「[クエリ パラメーター](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#query-parameters)」をご覧ください。 すべてのクエリ パラメーターの値は、URL でエンコードする必要があります。  
  
要求では、[Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#subscriptionkey) ヘッダーを指定する必要があります。 省略可能ですが、次のヘッダーも指定することをお勧めします。 これらのヘッダーは、Bing Spell Check API がより正確な結果を返すことに役立ちます。  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#location)  

すべての要求ヘッダーと応答ヘッダーのリストについては、「[Headers](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#headers)」(ヘッダー) を参照してください。

JavaScript を使用して Bing Spell Check API を呼び出すときに、ブラウザーの組み込みのセキュリティ機能によっては、これらのヘッダーの値にアクセスできない場合があります。

この問題を解決するため、CORS プロキシを介して Bing Spell Check API 要求を作成することができます。 そのようなプロキシからの応答には、応答ヘッダーをフィルター処理して JavaScript で使用できるようにする `Access-Control-Expose-Headers` ヘッダーが含まれています。

CORS プロキシをインストールして[チュートリアル アプリ](../tutorials/spellcheck.md)がオプションのクライアント ヘッダーにアクセスできるようにするのは簡単です。 まず、[Node.js をインストールします](https://nodejs.org/en/download/) (まだインストールしていない場合)。 その後、コマンド プロンプトで次のコマンドを入力します。

```console
npm install -g cors-proxy-server
```

次に、HTML ファイル内の Bing Spell Check API エンドポイントを次のように変更します。\
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/`

最後に、次のコマンドを使用して、CORS プロキシを開始します。

```console
cors-proxy-server
```

チュートリアル アプリを使用している間はコマンド ウィンドウを開いたままにしておいてください。ウィンドウを閉じるとプロキシが停止します。 検索結果の下の展開可能な HTTP ヘッダー セクションに、`X-MSEdge-ClientID` ヘッダー (など) が表示され、要求ごとに同じであることを確認できます。

## <a name="example-api-request"></a>API 要求の例

すべての推奨されるクエリ パラメーターとヘッダーを含む要求を次に示します。 いずれかの Bing API を初めて呼び出す場合は、クライアント ID ヘッダーを含めないでください。 クライアント ID を含めるのは、過去に Bing API を呼び出したことがあり、かつユーザーとデバイスの組み合わせに対応するクライアント ID が Bing から返されたことがある場合だけです。 
  
```http
GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

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

## <a name="next-steps"></a>次のステップ

- [Bing Spell Check API とは](../overview.md)
- [Bing Spell Check API v7 リファレンス](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)