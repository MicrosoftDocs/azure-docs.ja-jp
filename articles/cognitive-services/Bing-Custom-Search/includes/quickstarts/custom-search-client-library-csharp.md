---
title: Bing Custom Search C# クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 70f97b73b5cc6a06caf8f1dac4dec61b6ad6de29
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947776"
---
C# 用 Bing Custom Search クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 Bing Custom Search API を使用すると、関心のあるトピックに合わせてカスタマイズした、広告なしの検索エクスペリエンスを作成できます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch) にあります。

C# 用 Bing Custom Search クライアント ライブラリを使用して、次のことを行います。
* Web で Bing Custom Search インスタンスの検索結果を探します。

[リファレンスのドキュメント](/dotnet/api/overview/azure/cognitiveservices/bing-custom-search-readme) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) | [サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>前提条件

- Bing Custom Search インスタンス。 「[クイック スタート:初めての Bing Custom Search インスタンスを作成する](../../quick-start.md)」で詳細を確認する。
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- [Visual Studio 2017 またはそれ以降](https://www.visualstudio.com/downloads/)の任意のエディション
- Linux/macOS を使用している場合、このアプリケーションは [Mono](https://www.mono-project.com/) を使用して実行できます。
- [Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) の NuGet パッケージ 
    - Visual Studio の **ソリューション エクスプローラー** でプロジェクトを右クリックし、メニューから **[NuGet パッケージの管理]** を選択します。 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` パッケージをインストールします。 NuGet Custom Search パッケージをインストールすると、次のアセンブリもインストールされます。
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. Visual Studio で、新しい C# コンソール アプリケーションを作成します。 次のパッケージをプロジェクトに追加します。

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. アプリケーションのメイン メソッドで、必要な API キーを使用して検索クライアントをインスタンス化します。

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>検索要求を送信して応答を受信する
    
1. クライアントの `SearchAsync()` メソッドを使用して検索クエリを送信し、応答を保存します。 `YOUR-CUSTOM-CONFIG-ID` を自分のインスタンスの構成の ID で置き換えます (この ID は [Bing Custom Search ポータル](https://www.customsearch.ai/)にあります)。 この例では、"Xbox" を検索します。

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. `SearchAsync()` メソッドが `WebData` オブジェクトを返します。 このオブジェクトを使用して、検出された `WebPages` を反復処理します。 このコードは、最初の Web ページの結果を検索し、Web ページの `Name` と `URL` を印刷します。

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Custom Search Web アプリの作成](../../tutorials/custom-search-web-page.md)
