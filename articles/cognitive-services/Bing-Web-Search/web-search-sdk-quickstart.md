---
title: クイック スタート:C# 用の Bing Web Search SDK を使用する
titleSuffix: Azure Cognitive Services
description: Bing Web Search SDK を使用すると、C# アプリケーションに Bing Web Search を簡単に統合することができます。 このクイック スタートでは、クライアントをインスタンス化し、要求を送信して、応答を出力する方法を学習します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 2c55aaa0843574981a7643b11161700992ea0b09
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976453"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-c"></a>クイック スタート:C# 用の Bing Web Search SDK を使用する

Bing Web Search SDK を使用すると、C# アプリケーションに Bing Web Search を簡単に統合することができます。 このクイック スタートでは、クライアントをインスタンス化し、要求を送信して、応答を出力する方法を学習します。

## <a name="prerequisites"></a>前提条件
このクイック スタートを実行するには、以下のものが必要です。

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) または
* [Visual Studio Code 2017](https://code.visualstudio.com/download)
  * [C# for Visual Studio Code](https://visualstudio.microsoft.com/downloads/)
  * [NuGet パッケージ マネージャー](https://github.com/jmrog/vscode-nuget-package-manager)
* [.NET Core SDK](https://www.microsoft.com/net/download)

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="create-a-project-and-install-dependencies"></a>プロジェクトの作成と依存関係のインストール

> [!TIP]
> 最新のコードを Visual Studio ソリューションとして [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/) から取得してください。

最初の手順で、新しいコンソール プロジェクトを作成します。 コンソール プロジェクトの設定で支援が必要な場合は、「[Hello World -- 最初のプログラム (C# プログラミング ガイド)](https://docs.microsoft.com/dotnet/csharp/programming-guide/inside-a-program/hello-world-your-first-program)」を参照してください。 アプリケーションで Bing Web Search SDK を使用するには、NuGet パッケージ マネージャーを使用して `Microsoft.Azure.CognitiveServices.Search.WebSearch` をインストールする必要があります。

[Web Search SDK パッケージ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)によって、以下のものもインストールされます。

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="declare-dependencies"></a>依存関係の宣言

Visual Studio または Visual Studio Code でプロジェクトを開き、次の依存関係をインポートします。

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;
```

## <a name="create-project-scaffolding"></a>プロジェクトのスキャフォールディングの作成

新しいコンソール プロジェクトを作成したときに、アプリケーションの名前空間とクラスが作成されたはずです。 プログラムは次の例のようになっているものと思われます。

```csharp
namespace WebSearchSDK
{
    class YOUR_PROGRAM
    {

        // The code in the following sections goes here.

    }
}
```

以降のセクションでは、このクラス内にサンプル アプリケーションを構築します。

## <a name="construct-a-request"></a>要求の構築

このコードは、検索クエリを構築します。

```csharp
public static async void WebResults(WebSearchClient client)
{
    try
    {
        var webData = await client.Web.SearchAsync(query: "Yosemite National Park");
        Console.WriteLine("Searching for \"Yosemite National Park\"");

        // Code for handling responses is provided in the next section...

    }
    catch (Exception ex)
    {
        Console.WriteLine("Encountered exception. " + ex.Message);
    }
}
```

## <a name="handle-the-response"></a>応答の処理

次に、応答を解析し、結果を出力するコードを追加してみましょう。 最初の Web ページ、画像、ニュース記事、およびビデオの `Name` と `Url` が出力されます (応答オブジェクトに含まれている場合)。

```csharp
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results # {0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any web pages...");
    }
}
else
{
    Console.WriteLine("Didn't find any web pages...");
}

/*
 * Images
 * If the search response contains images, the first result's name
 * and url are printed.
 */
if (webData?.Images?.Value?.Count > 0)
{
    // find the first image result
    var firstImageResult = webData.Images.Value.FirstOrDefault();

    if (firstImageResult != null)
    {
        Console.WriteLine("Image Results # {0}", webData.Images.Value.Count);
        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any images...");
    }
}
else
{
    Console.WriteLine("Didn't find any images...");
}

/*
 * News
 * If the search response contains news articles, the first result's name
 * and url are printed.
 */
if (webData?.News?.Value?.Count > 0)
{
    // find the first news result
    var firstNewsResult = webData.News.Value.FirstOrDefault();

    if (firstNewsResult != null)
    {
        Console.WriteLine("\r\nNews Results # {0}", webData.News.Value.Count);
        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any news articles...");
    }
}
else
{
    Console.WriteLine("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response contains videos, the first result's name
 * and url are printed.
 */
if (webData?.Videos?.Value?.Count > 0)
{
    // find the first video result
    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

    if (firstVideoResult != null)
    {
        Console.WriteLine("\r\nVideo Results # {0}", webData.Videos.Value.Count);
        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any videos...");
    }
}
else
{
    Console.WriteLine("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>main メソッドの宣言

このアプリケーションの main メソッドには、クライアントをインスタンス化し、`subscriptionKey` を検証して、`WebResults` を呼び出すコードが含まれています。 続行する前に、ご自分の Azure アカウントの有効なサブスクリプション キーを入力してください。

```csharp
static void Main(string[] args)
{
    var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

    WebResults(client);

    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーションを実行してみましょう。

```console
dotnet run
```

## <a name="define-functions-and-filter-results"></a>関数の定義と結果のフィルター処理

これで Bing Web Search API の最初の呼び出しを実行できたので、次は SDK 機能がよくわかるいくつかの関数を調べて、クエリとフィルター処理の結果を調整しましょう。 各関数は、前のセクションで作成した C# アプリケーションに追加することができます。

### <a name="limit-the-number-of-results-returned-by-bing"></a>Bing から返される結果の数の制限

このサンプルでは、`count` パラメーターと `offset` パラメーターを使用して、"Best restaurants in Seattle" (シアトルで最高のレストラン) に対して返される結果の数を制限しています。 最初の結果の `Name` と `Url` が出力されます。

1. このコードをコンソール プロジェクトに追加します。

    ```csharp
    public static async void WebResultsWithCountAndOffset(WebSearchClient client)
    {
        try
        {
            var webData = await client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20);
            Console.WriteLine("\r\nSearching for \" Best restaurants in Seattle \"");

            if (webData?.WebPages?.Value?.Count > 0)
            {
                var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                if (firstWebPagesResult != null)
                {
                    Console.WriteLine("Web Results #{0}", webData.WebPages.Value.Count);
                    Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                    Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first web result!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any Web data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. `WebResultsWithCountAndOffset` を `main` に追加します。

    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. アプリケーションを実行します。

### <a name="filter-for-news"></a>ニュースのフィルター処理

このサンプルでは、`response_filter` パラメーターを使用して、検索結果をフィルター処理します。 返される検索結果は、"Microsoft" のニュース記事に限定されます。 最初の結果の `Name` と `Url` が出力されます。

1. このコードをコンソール プロジェクトに追加します。

    ```csharp
    public static async void WebSearchWithResponseFilter(WebSearchClient client)
    {
        try
        {
            IList<string> responseFilterstrings = new List<string>() { "news" };
            var webData = await client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings);
            Console.WriteLine("\r\nSearching for \" Microsoft \" with response filter \"news\"");

            if (webData?.News?.Value?.Count > 0)
            {
                var firstNewsResult = webData.News.Value.FirstOrDefault();

                if (firstNewsResult != null)
                {
                    Console.WriteLine("News Results #{0}", webData.News.Value.Count);
                    Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                    Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first News results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any News data..");
            }

        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. `WebResultsWithCountAndOffset` を `main` に追加します。

    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. アプリケーションを実行します。

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>セーフ サーチ、回答数、昇格フィルターの使用

このサンプルでは、`answer_count`、`promote`、および `safe_search` パラメーターを使用して、検索結果を "Music Videos" でフィルター処理します。 最初の結果の `Name` と `ContentUrl` が表示されます。

1. このコードをコンソール プロジェクトに追加します。

    ```csharp
    public static async void WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchClient client)
    {
        try
        {
            IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
            var webData = await client.Web.SearchAsync(query: "Music Videos", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict);
            Console.WriteLine("\r\nSearching for \"Music Videos\"");

            if (webData?.Videos?.Value?.Count > 0)
            {
                var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                if (firstVideosResult != null)
                {
                    Console.WriteLine("Video Results # {0}", webData.Videos.Value.Count);
                    Console.WriteLine("First Video result name: {0} ", firstVideosResult.Name);
                    Console.WriteLine("First Video result URL: {0} ", firstVideosResult.ContentUrl);
                }
                else
                {
                    Console.WriteLine("Couldn't find videos results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. `WebResultsWithCountAndOffset` を `main` に追加します。

    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);
        // Search with answer count, promote, and safe search
        WebSearchWithAnswerCountPromoteAndSafeSearch(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. アプリケーションを実行します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このプロジェクトの使用を終了するときに、アプリケーションのコードからサブスクリプション キーを必ず削除してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Cognitive Services Node.js SDK サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)
