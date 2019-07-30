---
title: ImageInsightsToken を使用して以前の検索から似た画像を見つける - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: Bing Visual Search SDK を使用して、ImageInsightsToken で指定されている画像の URL を取得します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 06/18/2019
ms.author: rosh
ms.openlocfilehash: 78584c2c0419bb27fb58c07eb97b1aa38501951f
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204079"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>ImageInsightsToken を使用して以前の検索から似た画像を見つける

Visual Search SDK を使用すると、前に実行された `ImageInsightsToken` を返す検索からオンラインで画像を検索できます。 このアプリケーションでは `ImageInsightsToken` を取得し、このトークンをその後の検索で使用します。 その後、`ImageInsightsToken` を Bing に送信し、Bing Search URL とオンラインで見つかった類似する画像の URL を含む結果を返します。

このチュートリアルのソース コード全体は、追加のエラー処理と注釈を含め、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs) で入手できます。

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2019](https://www.visualstudio.com/downloads/) のいずれかのエディション。
* Linux/macOS を使用している場合、このアプリケーションは [Mono](https://www.mono-project.com/) を使用して実行できます。
* NuGet Visual Search パッケージと Image Search パッケージ。
    - Visual Studio のソリューション エクスプローラーでプロジェクトを右クリックし、メニューから **[NuGet パッケージの管理]** を選択します。 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` パッケージと `Microsoft.Azure.CognitiveServices.Search.ImageSearch` パッケージをインストールします。 NuGet パッケージをインストールすると、次のものもインストールされます。
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Bing Image Search SDK から ImageInsightsToken を取得する

このアプリケーションでは、[Bing Image Search SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) によって取得された `ImageInsightsToken` を使用します。 新しい C# コンソール アプリケーションで、`ImageSearchClient()` を使用して API を呼び出すクライアントを作成します。 その後、クエリで `SearchAsync()` を使用します。

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

`imageResults.Value.First()` を使用して最初の検索結果を格納した後、画像の分析情報の `ImageInsightsToken` を格納します。

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

この `ImageInsightsToken` は、要求で Bing Visual Search に送信されます。

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Visual Search の要求に ImageInsightsToken を追加する

Bing Visual Search からの応答に含まれる `ImageInsightsToken` から `ImageInfo` オブジェクトを作成することにより、Visual Search の要求に対する `ImageInsightsToken` を指定します。

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Bing Visual Search を使用して ImageInsightsToken から画像を検索する

`VisualSearchRequest` オブジェクトでは、検索対象の画像に関する情報が `ImageInfo` に含まれます。 `VisualSearchMethodAsync()` メソッドを使用して結果を取得します。 画像はトークンによって表されるので、画像のバイナリを提供する必要はありません。

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Visual Search の結果を反復処理する

Visual Search の結果は `ImageTag` オブジェクトです。 各タグには、`ImageAction` オブジェクトのリストが含まれます。 各 `ImageAction` には、アクションのタイプに依存する値のリストである `Data` フィールドが含まれます。 たとえば、`visualSearchResults.Tags` 内の `ImageTag` オブジェクトを反復処理して、その中の `ImageAction` タグを取得できます。 次のサンプルでは、`PagesIncluding` アクションの詳細が表示されます。

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

アクションのタイプから実際の画像 URL を取得するには、キャストにより、`ActionType` を、値のリストを持つ `Data` 要素が含まれる `ImageModuleAction` として読み取る必要があります。 各値は、画像の URL です。  次のコードでは、`PagesIncluding` アクション タイプを `ImageModuleAction` にキャストして、値を読み取ります。

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

これらのデータ型の詳細については、「[Images - Visual Search (画像 - Visual Search)](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch)」を参照してください。

## <a name="returned-urls"></a>返された URL

完全なアプリケーションでは、次の URL が返されます。

|ActionType  |URL  | |
|---------|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | [https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&amp;CID=1BA795A21EAF6A63175699B71FC36B7C&amp;rd=1&amp;h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&amp;v=1&amp;r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&amp;p=DevEx](https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx ),5823.1       |
|ImageResults -> WebSearchUrl    |  [https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&amp;CID=1BA795A21EAF6A63175699B71FC36B7C&amp;rd=1&amp;h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&amp;v=1&amp;r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&amp;p=DevEx](https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx ),5831.1       |

上で示されているように、`TopicResults` タイプと `ImageResults` タイプには、関連する画像のクエリが含まれています。 URL は、Bing Search の結果にリンクしています。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Visual Search のシングルページ Web アプリを作成する](tutorial-bing-visual-search-single-page-app.md)
