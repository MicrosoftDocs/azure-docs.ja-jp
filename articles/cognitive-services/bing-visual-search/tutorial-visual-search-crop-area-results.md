---
title: チュートリアル:Bing Visual Search SDK で画像をトリミングする
description: Bing Visual Search SDK を使用して、画像の特定の範囲から分析情報を取得します。
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: rosh
ms.openlocfilehash: a6b625325e2adfe441d8abd2012f100780b659b4
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910026"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>チュートリアル:C# 用の Bing Visual Search SDK で画像をトリミングする

Bing Visual Search SDK を使用すると、類似したオンライン画像を検索する前に画像をトリミングすることができます。 このアプリケーションでは、複数の人が含まれる画像から 1 人をトリミングした後、オンラインで見つかった似た画像を含む検索結果を返します。

エラー処理と注釈が追加されたこのアプリケーションの完全なソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs) で入手できます。

このチュートリアルでは、次の方法を示します。

> [!div class="checklist"]
> * Bing Visual Search SDK を使用して要求を送信する
> * Bing Visual Search で検索するために画像の範囲をトリミングする
> * 応答を受信して処理する
> * 応答でアクション項目の URL を検索する

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2019](https://www.visualstudio.com/downloads/) のいずれかのエディション。
* Linux/macOS を使用している場合、このアプリケーションは [Mono](https://www.mono-project.com/) を使用して実行できます。
* [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) パッケージがインストール済みであること。
    - Visual Studio のソリューション エクスプローラーでプロジェクトを右クリックし、メニューから **[NuGet パッケージの管理]** を選択します。 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` パッケージをインストールします。 NuGet Custom Search パッケージをインストールすると、次のアセンブリもインストールされます。
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>画像のトリミング範囲を指定する

このアプリケーションでは、Microsoft シニア リーダーシップ チームの画像の範囲をトリミングします。 このトリミング範囲を定義するには、画像全体に対する割合として表された、左上と右下の座標を使用します。  

![Microsoft のシニア リーダーシップ チーム](./media/MS_SrLeaders.jpg)

この画像は、トリミング範囲から `ImageInfo` オブジェクトを作成し、`ImageInfo` オブジェクトを `VisualSearchRequest` に読み込むことによって、トリミングされます。 `ImageInfo` オブジェクトには、画像の URL も含まれます。

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>トリミング範囲に類似する画像を検索する

変数 `VisualSearchRequest` には、画像のトリミング範囲に関する情報とその URL が含まれています。 `VisualSearchMethodAsync()` メソッドを使用して結果を取得します。

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>URL データを `ImageModuleAction` から取得します。

Bing Visual Search の結果は `ImageTag` オブジェクトです。 各タグには、`ImageAction` オブジェクトのリストが含まれます。 各 `ImageAction` には、アクションのタイプに依存する値のリストである `Data` フィールドが含まれます。

次のコードを使用すると、さまざまなタイプを表示できます。

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

完全なアプリケーションでは、次の項目が返されます。

|ActionType  |URL  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entity -> WebSearchUrl     | https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

上で示されているように、`Entity` ActionType には、認識可能な人物、場所、または物に関する情報を返す Bing 検索クエリが含まれています。 `TopicResults` タイプと `ImageResults` タイプには、関連する画像のクエリが含まれています。 リスト内の URL は、Bing Search の結果にリンクしています。

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>`PagesIncluding``ActionType` イメージの URL の取得

実際の画像 URL を取得するには、キャストにより、`ActionType` を、値のリストを持つ `Data` 要素が含まれる `ImageModuleAction` として読み取る必要があります。 各値は、画像の URL です。 次のコードでは、`PagesIncluding` アクション タイプを `ImageModuleAction` にキャストして、値を読み取ります。

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Visual Search のシングルページ Web アプリを作成する](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>関連項目
> [Bing Visual Search API とは](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
