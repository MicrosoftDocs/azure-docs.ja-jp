---
title: クイック スタート:イメージを検索する - Bing Image Search REST API と C#
titleSuffix: Azure Cognitive Services
description: このクイック スタートを使用して、C# を使って Bing Image Search REST API にイメージ検索要求を送信し、JSON 応答を受信します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.openlocfilehash: e52101b873e03cd6fcd847e9aa2a2d801f8f583d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918859"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-c"></a>クイック スタート:Bing Image Search REST API と C# を使用してイメージを検索する

このクイック スタートでは、Bing Image Search API に検索要求を送信する基本的な方法について説明します。 この C# アプリケーションは、検索クエリを API に送信し、その結果から最初の画像の URL を表示します。 このアプリケーションは C# で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

このサンプルのソース コードは、追加のエラー処理と注釈を含め、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingImageSearchv7Quickstart.cs) で入手できます。

## <a name="prerequisites"></a>前提条件
* [Visual Studio 2017 またはそれ以降](https://www.visualstudio.com/downloads/)の任意のエディション。
* NuGet パッケージとして入手できる [Json.NET](https://www.newtonsoft.com/json) フレームワーク。
* Linux/macOS を使用している場合、このアプリケーションは [Mono](https://www.mono-project.com/) を使用して実行できます。

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>プロジェクトの作成と初期化

1. Visual Studio で、`BingSearchApisQuickStart` という新しいコンソール ソリューションを作成します。 次に、メイン コード ファイルに次の名前空間を追加します。

    ```csharp
    using System;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    using Newtonsoft.Json.Linq;
    ```

2. API エンドポイント、サブスクリプション キー、および検索用語の変数を作成します。 `uriBase` には、以下のグローバル エンドポイントを指定するか、Azure portal に表示される、リソースの[カスタム サブドメイン](../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを指定できます。

    ```csharp
    //...
    namespace BingSearchApisQuickstart
    {
        class Program
        {
        // Replace the this string with your valid access key.
        const string subscriptionKey = "enter your key here";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";
        const string searchTerm = "tropical ocean";
    //...
    ```

## <a name="create-a-struct-to-format-the-bing-image-search-response"></a>Bing Image Search 応答の書式を設定する構造体を作成する

画像の検索結果と JSON ヘッダー情報を含む `SearchResult` 構造体を定義します。

```csharp
    namespace BingSearchApisQuickstart
    {
        class Program
        {
        //...
            struct SearchResult
            {
                public String jsonResult;
                public Dictionary<String, String> relevantHeaders;
            }
//...
```

## <a name="create-a-method-to-send-search-requests"></a>検索要求を送信するメソッドを作成する

`BingImageSearch` という名前のメソッドを作成して API に対する呼び出しを実行し、以前に作成した`SearchResult` 構造体に戻り値の型を設定します。

```csharp
//...
namespace BingSearchApisQuickstart
{
    //...
    class Program
    {
        //...
        static SearchResult BingImageSearch(string searchTerm)
        {
        }
//...
```

## <a name="create-and-handle-an-image-search-request"></a>画像の検索要求の作成と処理

`BingImageSearch` メソッドで次の手順を実行します。

1. 検索要求の URI を構築します。 検索語句 `SearchTerm` は、書式を設定したうえで文字列に追加する必要があります。

    ```csharp
    static SearchResult BingImageSearch(string SearchTerm){

        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(SearchTerm);
    //...
    ```

2. Web 要求を送信し、応答を JSON 文字列として取得します。

    ```csharp
    WebRequest request = WebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = subscriptionKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    ```

3. 検索結果オブジェクトを作成し、Bing HTTP ヘッダーを抽出します。 次に `searchResult` を返します。

    ```csharp
    // Create the result object for return
    var searchResult = new SearchResult()
    {
        jsonResult = json,
        relevantHeaders = new Dictionary<String, String>()
    };

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

## <a name="process-and-view-the-response"></a>応答の処理と表示

1. メイン メソッドで `BingImageSearch()` を呼び出し、返された応答を格納します。 次に、JSON をオブジェクトに逆シリアル化します。

    ```csharp
    SearchResult result = BingImageSearch(searchTerm);
    //deserialize the JSON response from the Bing Image Search API
    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result.jsonResult);
    ```

2. `jsonObj` から最初に返された画像を取得し、画像のタイトルと URL を出力します。
    ```csharp
    var firstJsonObj = jsonObj["value"][0];
    Console.WriteLine("Title for the first image result: " + firstJsonObj["name"]+"\n");
    //After running the application, copy the output URL into a browser to see the image.
    Console.WriteLine("URL for the first image result: " + firstJsonObj["webSearchUrl"]+"\n");
    ```  


## <a name="example-json-response"></a>JSON の応答例

Bing Image Search API からの応答は、JSON として返されます。 このサンプル応答は、1 つの結果だけを表示するように切り詰められています。

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Bing Image Search の単一ページ アプリのチュートリアル](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>関連項目

* [Bing Image Search とは](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [オンラインの対話型デモを試す](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 
* Bing Search API シリーズの[価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [無料の Cognitive Services アクセス キーを取得する](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Azure Cognitive Services のドキュメント](https://docs.microsoft.com/azure/cognitive-services)
* [Bing Image Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
