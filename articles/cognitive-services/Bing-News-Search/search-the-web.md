---
title: Bing News Search API とは
titleSuffix: Azure Cognitive Services
description: Bing News Search API を使用して、ヘッドラインやトレンド トピックなど、複数のカテゴリにおける最新ヘッドラインについて Web 検索する方法について説明します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 06/19/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 577ce53b4667928d7eb5a870f57ff7180caaf6f5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423667"
---
# <a name="what-is-the-bing-news-search-api"></a>Bing News Search API とは

Bing News Search API を使用すると、Bing のコグニティブ ニュース検索機能を簡単にアプリケーションに統合することができます。 [Bing News](https://www.bing.com/news) と同様のエクスペリエンスを API から利用して検索クエリを送信し、関連するニュース記事を受信することができます。

Bing News Search API で得られるのはニュース検索の結果だけであることに注意してください。 他の種類の Web コンテンツについては、[Bing Web Search API](../bing-web-search/search-the-web.md)、[Video Search API](../bing-video-search/search-the-web.md)、[Image Search API](../bing-image-search/overview.md) を使用します。

## <a name="bing-news-search-api-features"></a>Bing News Search API の機能

Bing News Search API は主に、関連するニュース記事を検索して返すものですが、同時に、インテリジェントで的を絞ったニュース検索を Web 上で行うための機能をいくつか備えています。

|機能  |説明  |
|---------|---------|
|[検索語句の提案と使用](concepts/search-for-news.md#suggest-and-use-search-terms)     | [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) を使用して、入力時に候補検索用語を表示することで、検索のエクスペリエンスを向上させます。         |
|[通常のニュースを取得する](concepts/search-for-news.md#get-general-news)     | Bing News Search API に検索クエリを送信し、関連するニュース記事の一覧を取得することによってニュースを検索します。           |
|[今日のトップ ニュース](concepts/search-for-news.md#get-todays-top-news)      | 全カテゴリからその日のトップ ニュース記事を取得します。       |
|[カテゴリ別のニュース](concepts/search-for-news.md)     | 特定のカテゴリのニュースを検索します。        | 
|[ヘッドライン ニュース](concepts/search-for-news.md)     | 全カテゴリのトップ記事を検索します。         |

## <a name="workflow"></a>ワークフロー

Bing News Search API は RESTful Web サービスです。HTTP 要求を作成して JSON を解析できる任意のプログラミング言語から簡単に呼び出すことができます。 REST API または SDK のいずれかを使用してサービスを使用できます。

1. Bing Search API にアクセスできる Cognitive Services API アカウントを作成します。 Azure サブスクリプションをお持ちでない場合は、[無料でアカウントを作成](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api)できます。

2. 有効な検索クエリを使用して API に要求を送信します。

3. 返された JSON メッセージを解析して API 応答を処理します。

## <a name="next-steps"></a>次の手順

まず、Bing News Search API の[対話型デモ](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/)をお試しください。 このデモには、検索クエリをすばやくカスタマイズして Web 上のニュースを検索する方法が紹介されています。

初めての API 要求を簡単に体験したい場合は、[REST API](quickstart.md) またはいずれかの [SDK](sdk.md) のクイック スタートをお試しください。

## <a name="see-also"></a>関連項目

* [Bing News Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) のリファレンス セクションには、画像ベースの検索結果を要求するために使用できるエンドポイント、ヘッダー、API 応答、およびクエリ パラメーターに関する定義と情報が含まれています。

* [Bing の利用と表示の要件](./useanddisplayrequirements.md)に関するトピックでは、Bing Search API を使用して得られるコンテンツと情報の許可される使用方法が指定されています。
