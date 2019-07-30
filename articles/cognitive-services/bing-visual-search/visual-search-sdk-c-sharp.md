---
title: クイック スタート:Bing Visual Search SDK for C# を使用して画像に関する分析情報を取得する
titleSuffix: Azure Cognitive Services
description: Bing Visual Search SDK を使用して画像をアップロードし、画像に関する分析情報を取得する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 2c98484eca027d20fbbe72ffb333a3e281e6f46b
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849885"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>クイック スタート:Bing Visual Search SDK for C# を使用して画像に関する分析情報を取得する

このクイック スタートを使用すると、C# SDK を使用して、Bing Visual Search サービスから画像に関する分析情報を取得することができます。 Bing Visual Search には、ほとんどのプログラミング言語に対応した REST API があり、SDK を使用すれば、既存のアプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch) にあります。

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* Linux/macOS を使用している場合、このアプリケーションは [Mono](https://www.mono-project.com/) を使用して実行できます。
* NuGet Visual Search パッケージ。 
    - Visual Studio でソリューション エクスプ ローラーから、プロジェクトを右クリックして、メニューから `Manage NuGet Packages` を選択します。 `Microsoft.Azure.CognitiveServices.Search.VisualSearch` パッケージをインストールします。 NuGet パッケージをインストールすると、次のものもインストールされます。
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. Visual Studio で、新しいプロジェクトを作成します。 その後、次のディレクティブを追加します。
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. サブスクリプション キーを使用して、クライアントをインスタンス化します。
    
    ```csharp
    var client = new VisualSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>検索要求を送信する 

1. 画像への `FileStream` を作成します (この場合は `TestImages/image.jpg`)。 その後、クライアントを使用し、`client.Images.VisualSearchMethodAsync()` を使用して検索要求を送信します。 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open);
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. 上記のクエリの結果を解析します。

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags[0];
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions[0];
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    }
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [シングルページ Web アプリの作成](tutorial-bing-visual-search-single-page-app.md)
