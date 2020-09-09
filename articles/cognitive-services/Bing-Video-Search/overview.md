---
title: Bing Video Search API とは
titleSuffix: Azure Cognitive Services
description: Bing Video Search API を使用して Web 全体から動画を検索する方法について説明します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 2c52f909cf3cc77b4f5e40ee9804d0c473e575c5
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85601940"
---
# <a name="what-is-the-bing-video-search-api"></a>Bing Video Search API とは

Bing Video Search API を使用すると、サービスやアプリケーションに動画の検索機能を簡単に追加することができます。 この API を使用してユーザーの検索クエリを送信すると、[Bing Video](https://www.bing.com/video) のような関連性のある高品質の動画を取得して表示できます。 この API は、動画のみを含んだ検索結果に使用します。 [Bing Web Search API](../bing-web-search/search-the-web.md) は、Web ページ、動画、ニュース、画像など、他の種類の Web コンテンツを返すことができます。

## <a name="bing-video-search-api-features"></a>Bing Video Search API の機能

| 特徴量                                                                                                                                                                                 | 説明                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [検索用語をリアルタイムで提案する](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) を使用して、入力時に候補検索用語を表示することで、アプリのエクスペリエンスを向上します。 |
| [動画の結果をフィルター処理して制限する](concepts/get-videos.md#filtering-videos)                      | 返される動画を、クエリ パラメーターを編集してフィルター処理します。                                                                                                       |
| [サムネイルのクロップ、サイズ変更、表示](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Bing Video Search API から返された動画のサムネイル プレビューを編集して表示します。                                                                                      |
| [急上昇中の動画の取得](trending-videos.md) | 世界で注目を集めている動画を検索します。                                                                                                          |
| [ビデオの分析情報の取得](video-insights.md) | 世界で注目を集めている動画の検索をカスタマイズします。                                                                                                          |

## <a name="workflow"></a>ワークフロー

Bing Video Search API は RESTful Web サービスです。HTTP 要求を作成して JSON を解析できる任意のプログラミング言語から簡単に呼び出すことができます。 [REST API](csharp.md) または [SDK](video-search-sdk-quickstart.md) のいずれかを使用してサービスを使用できます。

1. Bing Search API が利用できる [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を作成します。 Azure サブスクリプションをお持ちでない場合は、無料で[アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)できます。
2. 有効な検索クエリを使用して API に要求を送信します。
3. 返された JSON メッセージを解析して API 応答を処理します。


## <a name="next-steps"></a>次のステップ

Bing Video Search API の[対話型デモ](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)では、検索クエリをカスタマイズし、Web から動画を検索する方法を紹介しています。

[クイック スタート](csharp.md)で、初めての API 要求を手軽に体験してみましょう。

## <a name="see-also"></a>関連項目

* [Bing Video Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) リファレンス ページには、検索結果を要求する際に使用するエンドポイント、ヘッダー、クエリ パラメーターの一覧が記載されています。

* [Bing の利用と表示の要件](./useanddisplayrequirements.md)に関するトピックでは、Bing Search API を使用して得られるコンテンツと情報の許可される使用方法が指定されています。

* その他の提供されている API については、[Bing Search API ハブ ページ](../bing-web-search/search-the-web.md)をご覧ください。