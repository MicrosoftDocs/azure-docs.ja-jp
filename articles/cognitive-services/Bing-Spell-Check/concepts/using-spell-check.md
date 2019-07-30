---
title: Bing Spell Check API の使用
titleSuffix: Azure Cognitive Services
description: Bing Spell Check のモードや設定など、この API に関連した情報を取り上げます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 7fd528b5f21622051bdad67c6ffcb6dd8500386a
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423649"
---
# <a name="using-the-bing-spell-check-api"></a>Bing Spell Check API の使用

この記事では、Bing Spell Check API を使用して、コンテキストに応じた文法とスペルのチェックを実行する方法について説明します。 ほとんどのスペルチェック機能はディクショナリベースのルール セットに依存しますが、Bing のスペルチェック機能は、機械学習と統計的機械翻訳を活用することでコンテキストに応じた正確な修正を実現しています。 

## <a name="spell-check-modes"></a>スペル チェック モード

API は、2 つの校正モード (`Proof` と `Spell`) をサポートします。  [こちら](https://azure.microsoft.com/services/cognitive-services/spell-check/)の例を参照してください。

### <a name="proof---for-documents"></a>Proof - ドキュメント用 

既定のモードは `Proof` です。 `Proof` スペル チェック モードでは最も包括的なチェックが行われ、大文字と小文字の修正、基本的な句読点の調整、ドキュメントの作成を支援するその他の機能が提供されます。 ただし、これは、en-US (英語 (米国))、es-ES (スペイン語)、pt-BR (ポルトガル語) 市場でのみ利用できます (注: スペイン語とポルトガル語はベータ版です)。 その他のすべての市場では、mode クエリ パラメーターを Spell に設定してください。 

> [!NOTE]
> クエリ テキストの長さが 4,096 を超える場合は 4.096 文字に切り捨てられた後で処理されます。 

### <a name="spell----for-web-searchesqueries"></a>Spell - Web 検索/クエリ用

`Spell` は、より良い検索結果を返すためにもっと積極的に機能します。 `Spell` モードは、ほとんどのスペル ミスを検出しますが、`Proof` ではキャッチされる一部の文法エラーは検出されません (大文字小文字エラーや単語の繰り返しなど)。

> [!NOTE]
> * サポートされるクエリの最大長は次のとおりです。 クエリが最大長を超えた場合、クエリとその結果は変更されません。
>    * 言語コード en、de、es、fr、pl、pt、sv、ru、nl、nb、tr-tr、it、zh、ko では 130 文字。 
>    * それ以外はすべて 65 文字。
> * Spell モードでは、クエリに角かっこ文字 (`[` と `]`) がサポートされておらず、矛盾した結果が生じることがあります。 Spell モードを使用するときは、これらをクエリから削除することをお勧めします。

## <a name="market-setting"></a>市場の設定

要求には、`mkt` クエリ パラメーターで[市場コード](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes)を指定する必要があります。 指定しなかった場合は、要求の IP アドレスに基づく既定の市場が使用されます。


## <a name="http-post-and-get-support"></a>HTTP POST と HTTP GET のサポート

この API は、HTTP POST と HTTP GET のどちらもサポートします。 どちらを使用するかは、校正する予定のテキストの長さによって決まります。 文字列が常に 1,500 文字未満の場合は、GET を使用します。 最大 10,000 文字の文字列をサポートする場合は、POST を使用します。 テキスト文字列には、任意の有効な UTF-8 文字を含めることができます。

次の例は、テキスト文字列のスペルと文法のチェックを要求する POST 要求を示しています。 この例には、すべてを網羅するために [mode](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode) クエリ パラメーターが含まれています (`mode` の既定値は Proof であるため、除外される可能性があります)。 [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) クエリ パラメーターには、校正対象の文字列が含まれます。
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

HTTP GET を使用する場合は、URL のクエリ文字列に `text` クエリ パラメーターを含めます。
  
前述の要求への応答は次のようになります。 応答には、[SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck) オブジェクトが含まれます。 
  
```json
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
  
API が [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) 文字列内で検出したスペルと文法のエラーが、[flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) フィールドに一覧表示されます。 `token` フィールドには、置換される単語が含まれます。 `offset` フィールドの 0 から始まるオフセットを使用して、`text` 文字列内のトークンを検出します。 その後、その場所にある単語を`suggestion` フィールド内の単語に置き換えます。 

`type` フィールドが RepeatedToken の場合でも、トークンを `suggestion` に置き換えますが、おそらく末尾のスペースを削除する必要があります。

## <a name="throttling-requests"></a>スロットル リクエスト

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>次の手順

- [Bing Spell Check API とは](../overview.md)
- [Bing Spell Check API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
