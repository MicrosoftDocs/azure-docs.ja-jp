---
title: Bing Search API とは
titleSuffix: Azure Cognitive Services
description: この記事では、Bing Search API についてと、アプリおよびサービスでコグニティブなインターネット検索を有効にする方法を、説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 5a883fcb3533374afbbf946281b6a4a1e9a2912e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57903115"
---
# <a name="what-are-the-bing-search-apis"></a>Bing Search API とは

Bing Search API を使用すると、広告を除外して Web ページ、画像、ニュース、場所などを検索する、Web に接続されたアプリやサービスを構築できます。 Bing Search REST API または SDK を使用して検索要求を送信することにより、Web 検索の関連情報とコンテンツを取得できます。 この記事では、さまざまな Bing Search API、およびアプリケーションやサービスにコグニティブな検索を統合する方法について説明します。 価格およびレート制限は、API によって異なる場合があります。

## <a name="the-bing-web-search-api"></a>Bing Web Search API

[Bing Web Search API](../Bing-Web-Search/index.yml) では、Web ページ、画像、ビデオ、ニュースなどが返されます。 この API に送信される検索クエリをフィルター処理して、特定のコンテンツの種類を含めたり除外したりできます。

すべての種類の関連 Web コンテンツを検索する必要がありそうなアプリケーションでは、Bing Web Search API の使用を検討してください。 特定の種類のオンライン コンテンツを検索するアプリケーションの場合は、以下の検索 API のいずれかを検討します。

## <a name="content-specific-bing-search-apis"></a>コンテンツに固有の Bing Search API

以下の Bing Search API では、画像、ニュース、地元企業、ビデオなどの特定のコンテンツが Web から返されます。

| Bing API | 説明 |
| -- | -- |
| [Entity Search](../Bing-Entities-Search/index.yml) | Bing Entity Search API では、人、場所、物などのエンティティを含む検索結果が返されます。 この API では、クエリの内容に応じて、条件を満たす 1 つ以上のエンティティが返されます。 検索クエリには、注目すべき個人、地元企業、ランドマーク、目的地などを含めることができます。 |
| [Image Search](../Bing-Image-Search/index.yml) | Bing Image Search API を使用すると、[Bing.com/images](https://www.Bing.com/images) のような、高品質の静止画や動画を検索して見つけることができます。 検索を微調整し、サイズ、色、ライセンス、新しさなどの属性により、画像を含めたり除外したりできます。 話題になっている画像を検索したり、画像をアップロードしてそれに関する分析情報を取得したり、サムネイルのプレビューを表示したりすることもできます。 |
| [News Search](../Bing-News-Search/index.yml) | Bing News Search API では、[Bing.com/news](https://www.Bing.com/news) のようなニュース記事を検索できます。 複数のソースまたは特定のドメインからのニュース記事が返されます。 複数のカテゴリを検索して、話題の記事、トップ記事、見出しを取得できます。 |
| [Video Search](../Bing-Video-Search/index.yml) | Bing Video Search API を使用すると、Web 全体でビデオを検索できます。 話題になっている動画、関連するコンテンツ、サムネイル プレビューを取得します。 |
| [Visual Search](../Bing-visual-search/index.yml) | 画像をアップロードできます。また、URL を使用して、見た目が似た製品、画像、関連項目の検索など、詳細な分析情報を取得することもできます。 |
 [Local Business Search](../bing-local-business-search/index.yml) | Bing Local Business Search API を使用すると、アプリケーションでは検索クエリに基づいて地元企業に関する連絡先や場所の情報を検索できます。 |

## <a name="the-bing-custom-search-api"></a>Bing Custom Search API

[Bing Custom Search](../Bing-Custom-Search/index.yml) API でカスタム検索インスタンスを作成すると、関心のあるコンテンツとトピックだけに焦点を当てた検索エクスペリエンスを作成することができます。 たとえば、Bing で検索するドメイン、Web サイト、特定の Web ページを指定すると、Bing Custom Search により、その特定のコンテンツに合わせて結果が調整されます。 Bing Custom Autosuggest、Image、Video Search API を組み込んで、検索エクスペリエンスをさらにカスタマイズすることができます。

## <a name="additional-bing-search-apis"></a>その他の Bing Search API

以下の Bing Search API を、他の Bing Search API と組み合わせることによって、検索エクスペリエンスをさらによくすることができます。

| API | 説明 |
| -- | -- |
| [Bing Autosuggest](../Bing-Autosuggest/index.yml) | Bing Autosuggest API を使用すると、検索候補がリアルタイムで返されるので、アプリケーションの検索エクスペリエンスが向上します。  |
| [Bing Statistics](bing-web-stats.md) | Bing Statistics では、アプリケーションで使用されている Bing Search API に関する分析情報が提供されます。 使用できる分析には、呼び出しのボリューム、上位のクエリ文字列、地理的な分布などが含まれます。 |

## <a name="next-steps"></a>次の手順

* Bing Search API の[価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* [Bing の利用と表示の要件](./use-display-requirements.md)に関するトピックでは、Bing Search API を使用して得られるコンテンツと情報の許可される使用方法が指定されています。
