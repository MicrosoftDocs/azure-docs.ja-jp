---
title: クイック スタート:Bing Entity Search SDK for C# を使用してエンティティを検索する
titleSuffix: Azure Cognitive Services
description: Bing Entity Search SDK for C# を使用してエンティティを検索する場合は、このクイック スタートを使用します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: aahi
ms.openlocfilehash: 20f76c0adfcbd756c71769979214ea975cb5d6d9
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360588"
---
# <a name="send-a-search-request-with-the-bing-entity-search-sdk-for-c"></a>Bing Entity Search SDK for C# を使用して検索要求を送信する

Bing Entity Search SDK for C# を使用してエンティティの検索を始める場合は、このクイック スタートを使用します。 Bing Entity Search には、ほとんどのプログラミング言語に対応した REST API がありますが、SDK を使用すれば、アプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch) にあります。


## <a name="prerequisites"></a>前提条件

* [Visual Studio 2017 またはそれ以降](https://www.visualstudio.com/downloads/)の任意のエディション。
* NuGet パッケージとして入手できる [Json.NET](https://www.newtonsoft.com/json) フレームワーク。
* Linux/macOS を使用している場合、このアプリケーションは [Mono](https://www.mono-project.com/) を使用して実行できます。
* [Bing News Search SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0)。 このパッケージをインストールすると、次のものもインストールされます。
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Bing Entity Search SDK を Visual Studio プロジェクトに追加するには、**ソリューション エクスプローラー**の **[NuGet パッケージの管理]** オプションを使用して、`Microsoft.Azure.CognitiveServices.Search.EntitySearch` パッケージを追加します。


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>アプリケーションを作成して初期化する

1. Visual Studio で、新しい C# コンソール ソリューションを作成します。 次に、メイン コード ファイルに次の内容を追加します。

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>クライアントを作成して検索要求を送信する

1. 新しい検索クライアントを作成します。 新しい `ApiKeyServiceClientCredentials` を作成して、サブスクリプション キーを追加します。

    ```csharp
    var client = new EntitySearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. クライアントの `Entities.Search()` 関数を使用して、クエリを検索します。
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>エンティティの説明を取得して表示する

1. API で検索結果が返された場合は、`entityData` からメイン エンティティを取得します。

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. メイン エンティティの説明を表示します 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](../tutorial-bing-entities-search-single-page-app.md)

* [Bing Entity Search API とは](../overview.md )