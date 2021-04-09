---
title: カスタム ビューの検索 - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: カスタム検索エクスペリエンスは、構成した後に Bing Custom Search ポータル内からテストできます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: 07b0dd68e39f555171e5606b71cd1eec92a4035c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353342"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>ポータルから Bing Custom Search インスタンスを呼び出す

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

カスタム検索エクスペリエンスを構成した後、Bing Custom Search [ポータル](https://customsearch.ai)内でそれをテストできます。 

![Bing Custom Search ポータルのスクリーンショット](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>検索クエリを作成する 

Bing Custom Search [ポータル](https://customsearch.ai)にサインインした後、ご自分の検索インスタンスを選択し、 **[運用]** タブをクリックします。 **[エンドポイント]** で、API エンドポイント (たとえば、Web API) を選択します。 表示されるエンドポイントは、お使いのサブスクリプションによって決まります。

検索クエリを作成するには、エンドポイントのパラメーター値を入力します。 ポータルに表示されるパラメーターは、選択したエンドポイントに応じて変更される可能性があることに注意してください。 詳細については、[Custom Search API リファレンス](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)を参照してください。 検索インスタンスで使用するサブスクリプションを変更するには、適切なサブスクリプション キーを追加し、該当する市場または言語パラメーターを更新します。

いくつかの重要なパラメーターを次に示します。


|パラメーター  |説明  |
|---------|---------|
|クエリ     | 検索する検索用語。 Web、Image、Video、および Autosuggest エンドポイントに対してのみ使用できます。 |
|カスタム構成 ID | 選択された Custom Search インスタンスの構成 ID。 このフィールドは読み取り専用です。 |
|Market     | 結果が引き出される市場。 Web、Image、Video、および Hosted UI エンドポイントに対してのみ使用できます。        |
|サブスクリプション キー | テストに使用するサブスクリプション キー。 ドロップダウン リストからキーを選択するか、手動で入力できます。          |

**[追加パラメーター]** をクリックすると、次のパラメーターが表示されます。  

|パラメーター  |説明  |
|---------|---------|
|Safe Search     | 成人向けコンテンツの Web ページをフィルター処理するために使用されるフィルター。 Web、Image、Video、および Hosted UI エンドポイントに対してのみ使用できます。 Bing Custom Video Search では、`moderate` および `strict` の 2 つの値のみがサポートされます。        |
|ユーザー インターフェイスの言語    | ユーザー インターフェイス文字列に使用する言語。 たとえば、Hosted UI でイメージおよびビデオを有効にした場合、**[イメージ]** タブおよび **[ビデオ]** タブでは指定した言語が使用されます。        |
|Count     | 応答で返される検索結果の数。 Web、Image、および Video エンドポイントに対してのみ使用できます。         |
|Offset    | 結果を返す前にスキップする検索の数です。 Web、Image、および Video エンドポイントに対してのみ使用できます。        |
    
すべての必須オプションを指定した後、**[呼び出し]** をクリックすると、右側のウィンドウに JSON 応答が表示されます。 Hosted UI エンドポイントを選択した場合は、下側のウィンドウで検索エクスペリエンスをテストできます。

## <a name="change-your-bing-custom-search-subscription"></a>Bing Custom Search サブスクリプションを変更する

新しいインスタンスを作成しなくても、Bing Custom Search インスタンスに関連付けられているサブスクリプションを変更することができます。 API 呼び出しが新しいサブスクリプションに対して送信および課金されるようにするには、Azure portal で新しい Bing Custom Search リソースを作成します。 API 要求で、新しいサブスクリプション キーをインスタンスのカスタム構成 ID と共に使用します。

## <a name="next-steps"></a>次のステップ

- [C# を使用してカスタム ビューを呼び出す](./call-endpoint-csharp.md)
- [Java を使用してカスタム ビューを呼び出す](./call-endpoint-java.md)
- [NodeJs を使用してカスタム ビューを呼び出す](./call-endpoint-nodejs.md)
- [Python を使用してカスタム ビューを呼び出す](./call-endpoint-python.md)

- [C# SDK を使用してカスタム ビューを呼び出す](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)