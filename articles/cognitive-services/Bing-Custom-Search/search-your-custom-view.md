---
title: カスタム ビューの検索 - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Web のカスタム ビューを検索する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: maheshb
ms.openlocfilehash: 814f57d4011823da80e53cce41ffcb523fc0bf1b
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405011"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>ポータルから Bing Custom Search インスタンスを呼び出す

カスタム検索エクスペリエンスを構成した後、Bing Custom Search [ポータル](https://customsearch.ai)内でそれをテストできます。 

![Bing Custom Search ポータルのスクリーンショット](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>検索クエリを作成する 

Bing Custom Search [ポータル](https://customsearch.ai)にサインインした後、ご自分の検索インスタンスを選択し、 **[運用]** タブをクリックします。 **[エンドポイント]** で、API エンドポイント (たとえば、Web API) を選択します。 表示されるエンドポイントは、お使いのサブスクリプションによって決まります。

検索クエリを作成するには、エンドポイントのパラメーター値を入力します。 ポータルに表示されるパラメーターは、選択したエンドポイントに応じて変更される可能性があることに注意してください。 詳細については、[Custom Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)を参照してください。 検索インスタンスで使用するサブスクリプションを変更するには、適切なサブスクリプション キーを追加し、該当する市場または言語パラメーターを更新します。

いくつかの重要なパラメーターを次に示します。


|パラメーター  |説明  |
|---------|---------|
|Query     | 検索する検索用語。 Web、Image、Video、および Autosuggest エンドポイントに対してのみ使用できます。 |
|カスタム構成 ID | 選択された Custom Search インスタンスの構成 ID。 このフィールドは読み取り専用です。 |
|Market     | 結果が引き出される市場。 Web、Image、Video、および Hosted UI エンドポイントに対してのみ使用できます。        |
|サブスクリプション キー | テストに使用するサブスクリプション キー。 ドロップダウン リストからキーを選択するか、手動で入力できます。          |

**[追加パラメーター]** をクリックすると、次のパラメーターが表示されます。  

|パラメーター  |説明  |
|---------|---------|
|Safe Search     | 成人向けコンテンツの Web ページをフィルター処理するために使用されるフィルター。 Web、Image、Video、および Hosted UI エンドポイントに対してのみ使用できます。        |
|ユーザー インターフェイスの言語    | ユーザー インターフェイス文字列に使用する言語。 たとえば、Hosted UI でイメージおよびビデオを有効にした場合、 **[イメージ]** タブおよび **[ビデオ]** タブでは指定した言語が使用されます。        |
|Count     | 応答で返される検索結果の数。 Web、Image、および Video エンドポイントに対してのみ使用できます。         |
|Offset    | 結果を返す前にスキップする検索の数です。 Web、Image、および Video エンドポイントに対してのみ使用できます。        |
    
すべての必須オプションを指定した後、 **[呼び出し]** をクリックすると、右側のウィンドウに JSON 応答が表示されます。 Hosted UI エンドポイントを選択した場合は、下側のウィンドウで検索エクスペリエンスをテストできます。

## <a name="change-your-bing-custom-search-subscription"></a>Bing Custom Search サブスクリプションを変更する

新しいインスタンスを作成しなくても、Bing Custom Search インスタンスに関連付けられているサブスクリプションを変更することができます。 API 呼び出しが新しいサブスクリプションに対して送信および課金されるようにするには、Azure portal で新しい Bing Custom Search リソースを作成します。 API 要求で、新しいサブスクリプション キーをインスタンスのカスタム構成 ID と共に使用します。

## <a name="next-steps"></a>次の手順

- [C# を使用してカスタム ビューを呼び出す](./call-endpoint-csharp.md)
- [Java を使用してカスタム ビューを呼び出す](./call-endpoint-java.md)
- [NodeJs を使用してカスタム ビューを呼び出す](./call-endpoint-nodejs.md)
- [Python を使用してカスタム ビューを呼び出す](./call-endpoint-python.md)

- [C# SDK を使用してカスタム ビューを呼び出す](./sdk-csharp-quick-start.md)
