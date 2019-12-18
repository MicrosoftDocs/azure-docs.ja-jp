---
title: Bing Web Search API とは
titleSuffix: Azure Cognitive Services
description: Bing Web Search API は、ユーザーのクエリにすぐに回答する RESTful サービスです。 検索結果は、Web ページ、画像、ビデオ、ニュース、翻訳などを含むように簡単に構成できます。 結果は JSON として提供され、検索の関連性と Bing Web Search のサブスクリプションに基づいています。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 12/05/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 6615f166ced4a93115f9cbaae1c6ce5b57cf57bc
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892993"
---
# <a name="what-is-the-bing-web-search-api"></a>Bing Web Search API とは

Bing Web Search API は、ユーザーのクエリにすぐに回答する RESTful サービスです。 検索結果は、Web ページ、画像、ビデオ、ニュース、翻訳などを含むように簡単に構成できます。 Bing Web Search では、検索の関連性と Bing Web Search のサブスクリプションに基づいて、結果が JSON として提供されます。

この API は、ユーザーの検索クエリに関連するすべてのコンテンツにアクセスする必要があるアプリケーションに最適です。 特定の種類の結果のみを必要とするアプリケーションを構築する場合は、[Bing Image Search API](../Bing-Image-Search/overview.md)、[Bing Video Search API](../Bing-Video-Search/search-the-web.md)、または [Bing News Search API](../Bing-News-Search/search-the-web.md) を使用することを検討してください。 Bing Search API の詳細な一覧については、[Cognitive Services APIs](https://docs.microsoft.com/azure/cognitive-services) を参照してください。

しくみを確認するには、 [Bing Web Search API のデモ](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)を試してください。

## <a name="features"></a>機能  

Bing Web Search では、インスタント アンサーにアクセスできるだけではありません。 ユーザーの検索結果をカスタマイズできるさまざまな機能も利用できます。

| 機能 | 説明 |
|---------|-------------|
| [検索用語をリアルタイムで提案する](../bing-autosuggest/get-suggested-search-terms.md) | Bing Autosuggest API を使用して、入力時に候補検索用語を表示することで、アプリケーションのエクスペリエンスを向上します。 |
| [コンテンツ タイプで結果をフィルター処理して制限する](filter-answers.md) | Web ページ、画像、動画、セーフ サーチなどのフィルターとクエリ パラメーターを使用して、検索結果をカスタマイズして絞り込みます。 |
| [Unicode 文字でヒットした文字を強調表示する](hit-highlighting.md) | ヒットした文字の強調表示をユーザーに表示する前に、検索結果から不要な Unicode 文字を特定して削除します。 |
| [国、リージョン、または市場別に検索結果をローカライズする](supported-countries-markets.md) | Bing Web Search は、30 を超える国または地域をサポートしています。 特定の国/地域または市場の検索結果を絞り込むには、この機能を使用します。 |
| [Bing Statistics で検索メトリックを分析する](bing-web-stats.md) | Bing Statistics は、通話量、上位のクエリ文字列、地理的分布などの分析を提供する有料サブスクリプションです。 |

## <a name="workflow"></a>ワークフロー

Bing Web Search API は、HTTP 要求を作成して JSON 応答を解析できる任意プログラミング言語から簡単に呼び出すことができます。 このサービスには、[REST API](quickstarts/python.md) または [Bing Web Search SDK](web-sdk-python-quickstart.md) を使用してアクセスできます。

1. Bing Search API に使用する [Azure リソース](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を作成します。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)を作成できます。  
2. [要求を Bing Web Search API](quickstarts/python.md) に送信します。
3. JSON 応答を解析します。

## <a name="next-steps"></a>次の手順

* Bing Web Search API を初めて呼び出す場合は、[Python のクイック スタート](quickstarts/python.md)を参照してください。  
* [シングルページ Web アプリの作成](tutorial-bing-web-search-single-page-app.md)。
* [Web Search API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)のドキュメントを参照してください。  
* Bing Web Search の[使用と表示に関する要件](UseAndDisplayRequirements.md)の詳細を参照してください。  
