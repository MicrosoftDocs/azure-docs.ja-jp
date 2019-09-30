---
title: Bing Visual Search API とは
titleSuffix: Azure Cognitive Services
description: Bing Visual Search は、似た画像やショッピング ソースなど、画像に関する詳細または分析情報を提供します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 08/30/2019
ms.author: scottwhi
ms.openlocfilehash: 22a8bb2609439a82a825bf7f542709d46771ad92
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195210"
---
# <a name="what-is-the-bing-visual-search-api"></a>Bing Visual Search API とは

Bing Visual Search API は、画像に関する分析情報を返します。 画像をアップロードするか、画像の URL を指定することができます。 分析情報とは、視覚的に似ている画像、ショッピング ソース、その画像が含まれる Web ページなどです。 Bing Visual Search API によって返される分析情報は、Bing.com/images に表示される情報と似ています。 

[Bing Image Search API](../bing-image-search/overview.md) を使用する場合は、画像をアップロードする代わりに、その API の検索結果の分析情報トークンを Bing Visual Search に使用できます。

> [!IMPORTANT]
> Bing Image Search API を使用して画像に関する分析情報を取得する場合は、より包括的な分析情報が取得できる Bing Visual Search API への切り替えを検討してください。

## <a name="insights"></a>洞察

Bing Visual Search を使用すると、次の分析情報を検出できます。

| 分析情報                              | 説明 |
|--------------------------------------|-------------|
| 見た目が似ている画像              | 入力画像に視覚的に似ている画像の一覧。 |
| 見た目が似ている製品            | 画像の製品と視覚的に似ている製品。            |
| ショッピング ソース                     | 入力画像に示されているアイテムを購入できる場所の一覧。            |
| 関連検索                     | 他のユーザーによって行われた関連検索または画像の内容に基づく関連検索。            |
| 画像を含む Web ページ     | 入力画像を含む Web ページ。            |
| レシピ                              | 入力画像に示されている料理を作るためのレシピを含む Web ページ。            |
| エンティティ                             | 有名な人物、場所、もの |

分析情報だけでなく、Bing Visual Search は、入力画像から派生したさまざまな用語 (つまり、タグ) も返します。 これらのタグを使用して、ユーザーは画像で見つかる概念を調べることができます。 たとえば、入力画像が有名なアスリートである場合、そのアスリートの名前や "スポーツ" がタグとして返される可能性があります。 また、入力画像がアップル パイの場合は、"アップル パイ"、"パイ"、"デザート" などのタグが使用されるでしょう。

Bing Visual Search の結果には、画像内で興味を引く部分を囲む境界ボックスも含まれます。 たとえば、画像に複数の有名人が含まれている場合、結果には認識された各有名人の境界ボックスが含まれる可能性があります。 また、Bing が画像内で製品や衣類を認識した場合、認識されたアイテムに対する境界ボックスが結果に含まれることがあります。

## <a name="workflow"></a>ワークフロー

Bing Visual Search API は RESTfulWeb サービスです。HTTP 要求を作成して JSON を解析できる任意のプログラミング言語から簡単に呼び出すことができます。 このサービスに対しては、REST API または SDK を使用できます。

1. Bing Search API シリーズにアクセスするための [Cognitive Services アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を作成します。 Azure サブスクリプションをお持ちでない場合は、[無料でアカウントを作成](https://azure.microsoft.com/free/)できます。 アカウントを作成した後に [Azure portal](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) から、または無料試用版を有効にした後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis)からサブスクリプション キーを取得できます。
2. 有効な検索クエリを使用して API に要求を送信します。
3. 返された JSON メッセージを解析して API 応答を処理します。

## <a name="next-steps"></a>次の手順

まず、Bing Visual Search API の[対話型のデモ](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)を試してみてください。
このデモは、検索クエリをすばやくカスタマイズし、Web で画像を検索する方法を示しています。

実際に要求を送信してみるには、クイック スタートを参照してください:[C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)。

## <a name="see-also"></a>関連項目

* 「[Images - Visual Search](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch)」リファレンスには、画像ベースの検索結果を要求するために使用できるエンドポイント、要求ヘッダー、応答、およびクエリ パラメーターに関する定義と情報が含まれています。

* 「[Bing Search API の利用と表示の要件](../bing-web-search/use-display-requirements.md)」では、Bing Search API シリーズを使用して得られるコンテンツと情報の許可される使用方法が指定されています。
