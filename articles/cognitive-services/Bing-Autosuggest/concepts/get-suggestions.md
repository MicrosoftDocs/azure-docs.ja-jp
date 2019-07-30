---
title: Bing Autosuggest API で検索語句の候補を表示する
titleSuffix: Azure Cognitive Services
description: Bing Autosuggest API の使用方法を説明します。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 1fc2311610dd45025ddb71cdf005a6e87381ee95
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405387"
---
# <a name="suggesting-query-terms"></a>検索語句の提案

通常は、アプリケーションの検索ボックスにユーザーが新しい文字を入力するたびに、Bing Autosuggest API を呼び出します。 クエリ文字列の完全性は、API から返されるクエリ語句の候補の関連性に影響します。 クエリ文字列がより完全になるほど、より高い関連性を持つクエリ語句の候補が返されます。 たとえば、API から `s` に対して返される可能性のある候補は、`sailing dinghies` に対して返されるクエリよりもおそらく関連性が低くなります。

## <a name="example-request"></a>要求の例

次の例に、*sail* に対するクエリ文字列の候補を返す要求を示します。 [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query) クエリ パラメーターを設定する際には、忘れずにユーザーの部分的なクエリ用語を URL エンコードします。 たとえば「*sailing les*」と入力された場合、`q` を `sailing+les` または `sailing%20les` に設定します。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

次の応答には、候補となる検索語を含んだ [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction) オブジェクトのリストが格納されます。

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>検索語の候補を使用する

それぞれの候補には、`displayText`、`query`、および `url` フィールドが含まれています。 `displayText` フィールドには、検索ボックスのドロップダウン リストの設定に使用する検索候補が含まれています。 応答に含まれるすべての候補を、指定された順序で表示する必要があります。

Bing Autosuggest API からの検索語の候補を含んだドロップダウン検索ボックスの例を次に示します。

![ドロップダウン検索ボックスによる自動提案リスト](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

ユーザーがドロップダウン リストからクエリ候補を選択した場合は、`query` フィールドのクエリ用語を使用して [Bing Web Search API](../../bing-web-search/search-the-web.md) を呼び出し、独自にその結果を表示することができます。 または、`url` フィールドの URL を使用して、ユーザーを Bing 検索結果ページに誘導することもできます。

## <a name="next-steps"></a>次の手順

* [Bing Autosuggest API とは](../get-suggested-search-terms.md)