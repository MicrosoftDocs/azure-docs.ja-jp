---
title: Bing Autosuggest API への要求の送信
titleSuffix: Azure Cognitive Services
description: Bing Autosuggest API への要求を送信する方法について説明します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: 6ece70b78166578cd4ef391d761b0e4e478f4f04
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405365"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Bing Autosuggest API への要求の送信

アプリケーションから Bing Search API のいずれかにクエリを送信する場合、ユーザーの検索エクスペリエンスを向上させるために Bing Autosuggest API を使用することができます。 Bing Autosuggest API は、検索ボックス内の部分的なクエリ文字列に基づいて、候補となるクエリの一覧を返します。 アプリケーションの検索ボックスに文字が入力されると、ドロップダウン リスト内に候補を表示できます。 この記事を使用して、この API への要求の送信について確認してください。 

## <a name="bing-autosuggest-api-endpoint"></a>Bing Autosuggest API のエンドポイント

**Bing Autosuggest API** には、検索用語の一部から取得されたクエリ候補の一覧を返す 1 つのエンドポイントが含まれています。

Bing API を使用してクエリ候補を取得するには、次のエンドポイントに `GET` 要求を送信します。 ヘッダーと URL パラメーターを使用して、さらに詳細を定義します。

**エンドポイント:** 検索候補を、`?q=""` によって定義されたユーザーの入力に関連する JSON 結果として返します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

ヘッダー、パラメーター、市場コード、応答オブジェクト、エラーなどについて詳しくは、[Bing Autosuggest API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) のリファレンスをご覧ください。

**Bing** API では、種類に応じた結果を返す検索アクションがサポートされます。 すべての検索エンドポイントは、結果を JSON 応答オブジェクトとして返します。
すべてのエンドポイントは、特定の言語や場所を経度、緯度、検索半径によって返すクエリをサポートします。

各エンドポイントでサポートされるパラメーターについて詳しくは、各種類のリファレンス ページをご覧ください。
Autosuggest API を使用した基本的な要求の例については、[Autosuggest のクイック スタート](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest)をご覧ください。

## <a name="bing-autosuggest-api-requests"></a>Bing Autosuggest API の要求

> [!NOTE]
> * Bing Autosuggest API への要求には、HTTPS プロトコルを使用する必要があります。

すべての要求をサーバーから送信することをお勧めします。 クライアント アプリケーションの一部としてキーを配布すると、悪意のあるサードパーティがアクセスする可能性が高くなります。 また、サーバーから呼び出しを行うと、今後の更新プログラムにアップグレードする場所が 1 つで済みます。

要求では、ユーザーの部分検索語句が含まれている [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) クエリ パラメーターを指定する必要があります。 必須ではありませんが、要求では [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) クエリ パラメーターも指定するべきです。このパラメーターは、結果取得元の市場を示します。 省略可能なクエリ パラメーターの一覧については、「[Query Parameters (クエリ パラメーター)](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters)」を参照してください。 すべてのクエリ パラメーターの値は、URL でエンコードする必要があります。

要求では、[Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) ヘッダーを指定する必要があります。 省略可能ですが、次のヘッダーも指定することをお勧めします。

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

クライアント IP と場所のヘッダーは、場所に対応したコンテンツを返すために重要です。

すべての要求ヘッダーと応答ヘッダーのリストについては、「[Headers](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers)」(ヘッダー) を参照してください。

> [!NOTE]
> JavaScript から Bing Autosuggest API を呼び出すときに、ブラウザーの組み込みのセキュリティ機能によっては、これらのヘッダーの値にアクセスできない場合があります。

これを解決するため、CORS プロキシを介して Bing Autosuggest API 要求を作成することができます。 このようなプロキシからの応答には、応答ヘッダーをホワイトリストに登録し、それらを JavaScript で使用できるようにする `Access-Control-Expose-Headers` ヘッダーがあります。

CORS プロキシをインストールして[チュートリアル アプリ](../tutorials/autosuggest.md)が省略可能なクライアント ヘッダーにアクセスできるようにするのは簡単です。 まず、[Node.js をインストールします](https://nodejs.org/en/download/) (まだインストールしていない場合)。 その後、コマンド プロンプトで次のコマンドを入力します。

    npm install -g cors-proxy-server

次に、HTML ファイル内の Bing Autosuggest API エンドポイントを次のように変更します。

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

最後に、次のコマンドを使用して、CORS プロキシを開始します。

    cors-proxy-server

チュートリアル アプリを使用している間はコマンド ウィンドウを開いたままにしておいてください。ウィンドウを閉じるとプロキシが停止します。 検索結果の下の展開可能な HTTP ヘッダー セクションに、`X-MSEdge-ClientID` ヘッダー (など) が表示され、各要求で同じであることを確認できます。

要求には、推奨されるすべてのクエリ パラメーターとヘッダーが含まれている必要があります。 

次の例に、*sail* に対するクエリ文字列の候補を返す要求を示します。

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

いずれかの Bing API を初めて呼び出す場合は、クライアント ID ヘッダーを含めないでください。 クライアント ID ヘッダーを含めるのは、過去に Bing API を呼び出したことがあり、かつユーザーとデバイスの組み合わせに対応するクライアント ID が Bing から返されたことがある場合だけです。

次の Web 候補グループは、上の要求に対する応答です。 このグループには、検索クエリ候補の一覧が含まれています。各候補には、`displayText`、`query`、および `url` フィールドが含まれています。

`displayText` フィールドには、検索ボックスのドロップダウン リストの設定に使用するクエリ候補が含まれています。 応答に含まれるすべての候補を、指定された順序で表示する必要があります。  

ユーザーがドロップダウン リストからクエリを選択すると、それを使用して [Bing Search API](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) の 1 つを呼び出して自身の結果を表示したり、返された `url` フィールドを使用して Bing 結果ページにそのユーザーを送信したりできます。

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>次の手順

- [Bing Autosuggest とは](../get-suggested-search-terms.md)
- [Bing Autosuggest API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Bing Autosuggest API から検索語句の候補を取得する](get-suggestions.md)
