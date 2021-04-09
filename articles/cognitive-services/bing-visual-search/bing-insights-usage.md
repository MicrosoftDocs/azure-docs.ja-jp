---
title: Bing 分析情報の例 - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: この記事では、Bing Visual Search で Bing.com の画像分析情報が使用および表示される方法の例を示します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: e0a3bdc7e68301c067b7e1ca80a3e708522e0df1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96493357"
---
# <a name="examples-of-bing-insights-usage"></a>Bing 分析情報の使用例

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

この記事では、Bing.com で画像分析情報が使用および表示される方法の例を示します。

## <a name="pagesincluding-insight-example"></a>PagesIncluding 分析情報の例

以下では、最初の Web ページへのリンクを表示し、この画像を含む他の Web ページの一覧をユーザーが展開したり折りたたんだりできるようにしています。

![画像を含むページの展開表示](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>ShoppingSources 分析情報の例

画像に表示されている製品のショッピング ソースが Bing でどのように表示されるかを次に示します。

![ショッピング ソース](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>VisualSearch 分析情報の例

見た目が似ている画像が Bing でどのように表示されるかを次に示します (例の **[関連画像]** を参照)。

![見た目が似ている画像](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Recipes 分析情報の例

画像に表示されている料理のレシピが Bing でどのように表示されるかを次に示します。 この例により、使用可能なレシピがあることがユーザーにわかります。

![レシピと画像を含むページ](./media/recipes-pages-including.PNG)

 また、ユーザーが一覧を展開すると、レシピへのリンクが表示されます。

![画像を含むレシピ ページの展開表示](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelatedSearches 分析情報の例

他のユーザーによる画像の関連検索が Bing でどのように表示されるかを次に示します。 ユーザーが画像をクリックすると、関連するクエリの Bing.com/images の検索結果ページに移動します。

![画像の関連検索](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Entity 分析情報の例

画像に表示されるエンティティ (人、場所、物など) に関する情報が Bing でどのように表示されるかを次に示します。 ユーザーがエンティティのリンクをクリックすると、そのエンティティに関する Bing.com の検索結果ページに移動します。

![画像に表示されるエンティティ](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>ユーザーが検索する可能性がある他の分析情報の表示

画像に関してユーザーが検索する可能性がある他の情報が Bing でどのように表示されるかを次に示します。

![画像に関する他の分析情報の検索](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>境界ボックスとホット スポット

既定以外のタグには、画像内でタグが適用される対象領域を特定する境界ボックスが含まれています。 境界ボックスによって画像全体が特定される場合以外は、境界ボックスを使用して画像のホット スポットを作成します。 ユーザーがホット スポットをクリックすると、ホット スポット (四角形) に含まれるコンテンツに関連する情報を取得できます。 たとえば、画像が最新ファッションに関するものである場合、結果にはその画像に表示されているアクセサリ (バッグ、ジュエリー、スカーフなど) のタグ (および境界ボックス) が含まれる可能性があります。 次の例は、画像に表示されているサングラスを特定するホット スポットの四角形です。

![境界ボックスとホット スポット](./media/click-to-search.PNG)

## <a name="next-steps"></a>次のステップ

最初の要求を送信してみるには、クイック スタートを参照してください。

* [C#](quickstarts/csharp.md)

* [Java](quickstarts/java.md)

* [node.js](quickstarts/nodejs.md)

* [Python](quickstarts/python.md)