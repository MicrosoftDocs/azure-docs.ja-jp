---
title: クイック スタート:C# を使用して Bing Custom Search エンドポイントを呼び出す | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、C# で Bing Custom Search インスタンスに検索結果を要求します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: eae9565db5fd88a38343423422cfcc92a3fac33f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936811"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>クイック スタート:C# を使用して Bing Custom Search エンドポイントを呼び出す 

このクイックスタートでは、Bing Custom Search インスタンスに検索結果を要求する方法を学習します。 このアプリケーションは C# で記述されていますが、Bing Custom Search API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs) で入手できます。

## <a name="prerequisites"></a>前提条件

- Bing Custom Search インスタンス。 詳細については、「[クイック スタート: 最初の Bing Custom Search インスタンスの作成](quick-start.md)」を参照してください。
- [Microsoft .NET Core](https://www.microsoft.com/net/download/core)。
- [Visual Studio 2019 以降](https://www.visualstudio.com/downloads/)の任意のエディション。
- Linux/macOS を使用している場合、このアプリケーションは [Mono](https://www.mono-project.com/) を使用して実行できます。
- [Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0) の NuGet パッケージ 

   Visual Studio にこのパッケージをインストールするには: 
     1. **ソリューション エクスプローラー**で目的のプロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。 
     2. *Microsoft.Azure.CognitiveServices.Search.CustomSearch* を検索して選択し、このパッケージをインストールします。

   Bing Custom Search NuGet パッケージをインストールすると、次のパッケージも Visual Studio によってインストールされます。
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft.Rest.ClientRuntime.Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. Visual Studio で、新しい C# コンソール アプリケーションを作成します。 次に、次のパッケージを目的のプロジェクトに追加します。

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Bing Custom Search API から返された検索結果を格納する次のクラスを作成します。

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. プロジェクトのメイン メソッドで、Bing Custom Search API のサブスクリプション キー、検索インスタンスのカスタム構成 ID、検索語句に使用する次の各変数を作成します。

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. 検索語句を `q=` クエリ パラメーターに追加し、検索インスタンスのカスタム構成 ID を `customconfig=` パラメーターに追加して、要求 URL を作成します。 パラメーターの区切りには、アンパサンド (`&`) を使用します。 `url` 変数値には、次のコードのグローバル エンドポイントを使用するか、Azure portal に表示される、対象のリソースの[カスタム サブドメイン](../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用することができます。

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>検索要求の送信と受信 

1. 要求のクライアントを作成し、`Ocp-Apim-Subscription-Key` ヘッダーにサブスクリプション キーを追加します。

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. 検索要求を実行し、応答を JSON オブジェクトとして取得します。

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>結果の処理と表示

- 応答オブジェクトを反復処理して、それぞれの検索結果についての情報 (名前、URL、Web ページが最後にクロールされた日付など) を表示します。

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Custom Search Web アプリの作成](./tutorials/custom-search-web-page.md)
