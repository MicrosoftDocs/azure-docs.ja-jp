---
title: Bing Image Search C# クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 9c3bae9d2ad388409c40a8e8c89bcdd52f536cdb
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85806025"
---
このクイックスタートでは、Bing Image Search クライアント ライブラリを使用して最初の画像検索を行います。このクライアント ライブラリは、API のラッパーであり、同じ機能を含んでいます。 このシンプルな C# アプリケーションは、画像検索クエリを送信し、JSON 応答を解析して、返された最初の画像の URL を表示します。

このサンプルのソース コードは、追加のエラー処理と注釈を含め、[GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) で入手できます。

## <a name="prerequisites"></a>前提条件
* [Visual Studio 2017 またはそれ以降](https://visualstudio.microsoft.com/vs/whatsnew/)の任意のエディション。
* [Cognitive Image Search NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch/)。

Visual Studio に Bing Image Search クライアント ライブラリをインストールするには、**ソリューション エクスプローラー**から **[NuGet パッケージの管理]** オプションを使用します。

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

「[Cognitive Services の価格 - Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)」もご覧ください。

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

まず Visual Studio で新しい C# コンソール アプリケーションを作成します。 次のパッケージをプロジェクトに追加します。

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
```

プロジェクトの main メソッドでは、有効なサブスクリプション キー、Bing で返す必要のある画像の結果、および検索語句の変数を作成します。 その後、キーを使用して画像検索クライアントをインスタンス化します。

```csharp
//IMPORTANT: replace this variable with your Cognitive Services subscription key
string subscriptionKey = "ENTER YOUR KEY HERE";
//stores the image results returned by Bing
Images imageResults = null;
// the image search term to be used in the query
string searchTerm = "canadian rockies";

//initialize the client
//NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
// use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.

var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
```

## <a name="send-a-search-query-using-the-client"></a>クライアントを使用して検索クエリを送信する

クライアントを使用して、クエリ テキストを指定して検索します。

```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>最初の画像の結果を解析して表示する

応答で返された画像の結果を解析します。
応答に検索結果が含まれている場合は、最初の結果を格納して、返された URL の合計数と共にサムネイルの URL などの詳細を出力します。  

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Bing Image Search の単一ページ アプリのチュートリアル](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>関連項目

* [Bing Image Search とは](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [オンラインの対話型デモを試す](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Azure Cognitive Services SDK の .NET サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Azure Cognitive Services のドキュメント](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
