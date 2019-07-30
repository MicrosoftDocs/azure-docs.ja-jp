---
title: Bing Autosuggest とは
titleSuffix: Azure Cognitive Services
description: Bing Autosuggest API の使用方法を説明します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 13606fa5a58106575648a39e5074507f5d3bf471
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405370"
---
# <a name="what-is-bing-autosuggest"></a>Bing Autosuggest とは

アプリケーションから Bing Search API のいずれかにクエリを送信する場合、ユーザーの検索エクスペリエンスを向上させるために Bing Autosuggest API を使用することができます。 Bing Autosuggest API は、検索ボックス内の部分的なクエリ文字列に基づいて、候補となるクエリの一覧を返します。 検索ボックスに文字を入力すると、ドロップダウン リストに候補が表示されます。

## <a name="bing-autosuggest-api-features"></a>Bing Autosuggest API の機能

| 機能                                                                                                                                                                                 | 説明                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [検索用語をリアルタイムで提案する](concepts/get-suggestions.md) | Autosuggest API を使用して、入力時に検索語句の候補を表示することで、アプリのエクスペリエンスを向上させます。 |

## <a name="workflow"></a>ワークフロー

Bing Autosuggest API は RESTful Web サービスです。HTTP 要求を実行して JSON を解析できる任意のプログラミング言語から簡単に呼び出すことができます。 

1. Bing Search API が利用できる [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を作成します。 Azure サブスクリプションをお持ちでない場合は、無料で[アカウントを作成](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)できます。
2. アプリケーションの検索ボックスにユーザーが新しい文字を入力するたびに、この API に要求を送信します。
3. 返された JSON メッセージを解析して API 応答を処理します。

通常は、アプリケーションの検索ボックスにユーザーが新しい文字を入力するたびに、この API を呼び出します。 新たに文字が入力されるたびに、より関連性の高い検索クエリ候補が API から返されます。 たとえば、`s` の 1 文字について API から返される候補は、`sail` で返される候補よりも関連性が低くなることが考えられます。

Bing Autosuggest API からの検索語の候補を含んだドロップダウン検索ボックスの例を次に示します。

![ドロップダウン検索ボックスによる自動提案リスト](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

ユーザーがドロップダウン リストから候補を選択したら、Bing Search API のいずれかでその候補を使用して検索を開始するか、Bing の検索結果ページに直接移動することができます。

## <a name="next-steps"></a>次の手順

実際に要求を送信してみるには、[最初のクエリを実行する](quickstarts/csharp.md)に関するページを参照してください。

[Bing Autosuggest API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) リファレンスを活用してください。 このリファレンスには、クエリ用語候補を要求する際に使用するエンドポイント、ヘッダー、クエリ パラメーターの一覧と、応答オブジェクトの定義が記載されています。

[Bing Web Search API](../bing-web-search/search-the-web.md) を使用して Web を検索する方法について説明します。

検索結果の使用に関するルールを逸脱しないよう、[Bing の使用上および表示上の要件](./useanddisplayrequirements.md)に関するページを必ず読んでください。
