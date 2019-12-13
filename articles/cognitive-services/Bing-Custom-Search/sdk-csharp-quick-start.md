---
title: クイック スタート:C# SDK を使用して Bing Custom Search エンドポイントを呼び出す | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、C# SDK を使用して、Bing Custom Search インスタンスに検索結果を要求します。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: scottwhi
ms.openlocfilehash: 21b192d29f30d66e3d5f0ce6e04317d3ca7c860e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977150"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>クイック スタート:C# SDK を使用して Bing Custom Search エンドポイントを呼び出す 

このクイック スタートでは、C# SDK を使用して、Bing Custom Search インスタンスに検索結果を要求します。 Bing Custom Search には、ほとんどのプログラミング言語に対応した REST API があり、Bing Custom Search SDK を使用すれば、既存のアプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch) にあります。

## <a name="prerequisites"></a>前提条件

- Bing Custom Search インスタンス。 「[クイック スタート:初めての Bing Custom Search インスタンスを作成する](quick-start.md)」で詳細を確認する。
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- [Visual Studio 2017 またはそれ以降](https://www.visualstudio.com/downloads/)の任意のエディション
- Linux/macOS を使用している場合、このアプリケーションは [Mono](https://www.mono-project.com/) を使用して実行できます。
- [Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) の NuGet パッケージ 
    - Visual Studio の**ソリューション エクスプローラー**でプロジェクトを右クリックし、メニューから **[NuGet パッケージの管理]** を選択します。 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` パッケージをインストールします。 NuGet Custom Search パッケージをインストールすると、次のアセンブリもインストールされます。
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


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
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](./tutorials/custom-search-web-page.md)
