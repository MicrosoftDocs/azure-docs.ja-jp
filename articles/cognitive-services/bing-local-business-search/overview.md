---
title: Bing Local Business Search API とは
titleSuffix: Azure Cognitive Services
description: RESTful サービスである Bing Local Business Search API を使用すると、アプリケーションで検索クエリに基づいて地元の場所や企業に関する情報を検索できます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 2078e4842f6b8af11fcf56760579ce1ec77dd23f
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423371"
---
# <a name="what-is-bing-local-business-search"></a>Bing Local Business Search とは
RESTful サービスである Bing Local Business Search API を使用すると、アプリケーションで検索クエリに基づいて地元企業に関する情報を検索できます。 たとえば、`q=<business-name> in Redmond, Washington`、または `q=Italian restaurants near me` です。 

## <a name="features"></a>機能
| 機能 | 説明 |  
| -- | -- | 
| [地元の企業と場所を検索する](quickstarts/local-quickstart.md) | Bing Local Business Search API では、クエリからローカライズされた結果が得られます。 結果には、企業の Web サイトの URL と表示テキスト、電話番号、地理的な場所(GPS 座標、市区町村、番地など) が含まれます |  
| [地理的境界を使用してローカル結果をフィルター処理する](specify-geographic-search.md) | 検索パラメーターとして座標を追加し、円領域または正方形の境界ボックスで指定された、特定の地域に結果を制限します。 | 
| [カテゴリ別に地元企業の結果をフィルター処理する](local-categories.md) | カテゴリ別に地元企業の結果を検索します。 このオプションでは、リバース IP の場所または呼び出し元の GPS 座標を使用して、さまざまな企業のカテゴリのローカライズされた結果を返します。|

## <a name="workflow"></a>ワークフロー
HTTP 要求を行って JSON 応答を解析できる任意のプログラミング言語から Bing Local Business Search API を呼び出します。 このサービスには REST API を使用してアクセスできます。
 
1. Bing Search API にアクセスできる [Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)を作成します。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)を作成できます。   
2. `q=""` クエリ パラメーターの検索語句を URL エンコードします。 たとえば、`q=nearby+restaurant` または `q=nearby%20restaurant` です。 必要に応じて、改ページ位置の自動修正も設定します。 
3. [Bing Local Business Search API に要求](quickstarts/local-quickstart.md) を送信します 
4. JSON 応答を解析します 

> [!NOTE]
> 現時点で Local Business Search でサポートされているのは、`en-US` 市場のみです。 
> [!NOTE]
> 現時点では、Local Business Search で Autosuggest はサポートされていません。 

## <a name="next-steps"></a>次の手順
- [クエリと応答](local-search-query-response.md)
- [Local Business Search のクイック スタート](quickstarts/local-quickstart.md)
- [Local Business Search API リファレンス](local-search-reference.md)
- [使用と表示の要件](use-display-requirements.md)
