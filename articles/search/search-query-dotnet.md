---
title: ".NET SDK を使用した Azure Search インデックスの照会 | Microsoft Docs"
description: "Azure Search の検索クエリを作成し、検索パラメーターを使用して検索結果のフィルター処理と並べ替えを行います。"
services: search
manager: jhubbard
documentationcenter: 
author: brjohnstmsft
ms.assetid: 12c3efba-ea99-4187-9d2d-f63b5ec7040d
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/19/2017
ms.author: brjohnst
ms.translationtype: Human Translation
ms.sourcegitcommit: 125f05f5dce5a0e4127348de5b280f06c3491d84
ms.openlocfilehash: ffc27db4de5bd699dbd8175930a597fb85947140
ms.contentlocale: ja-jp
ms.lasthandoff: 05/22/2017


---
# <a name="query-your-azure-search-index-using-the-net-sdk"></a>.NET SDK を使用した Azure Search インデックスの照会
> [!div class="op_single_selector"]
> * [概要](search-query-overview.md)
> * [ポータル](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

この記事では、 [Azure Search .NET SDK](https://aka.ms/search-sdk)を使用してインデックスを照会する方法について説明します。

このチュートリアルを開始する前に、既に [Azure Search インデックスを作成](search-what-is-an-index.md)し、[インデックスにデータを読み込んで](search-what-is-data-import.md)います。

この記事に記載されたすべてのサンプル コードは、C# で記述されていることにご注意ください。 [GitHub](http://aka.ms/search-dotnet-howto)に完全なソース コードがあります。

## <a name="identify-your-azure-search-services-query-api-key"></a>Azure Search サービスのクエリ API キーの識別
Azure Search インデックスの作成は済んでいるので、.NET SDK を使用してクエリを発行する準備はほとんどできています。 まず、プロビジョニングした検索サービス用に生成されたクエリ API キーの 1 つを取得する必要があります。 .NET SDK は、サービスに対する要求ごとに、この API キーを送信します。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

1. サービスの API キーを探すには、[Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Search サービスのブレードに移動します。
3. "キー" アイコンをクリックします。

サービスで*管理者キー*と*クエリ キー*を使用できるようになります。

* プライマリおよびセカンダリ *管理者キー* は、サービスの管理のほか、インデックス、インデクサー、データ ソースの作成と削除など、すべての操作に対する完全な権限を付与するものです。 キーは 2 つあるため、プライマリ キーを再生成することにした場合もセカンダリ キーを使い続けることができます (その逆も可能です)。
* *クエリ キー* はインデックスとドキュメントに対する読み取り専用アクセスを付与するものであり、通常は、検索要求を発行するクライアント アプリケーションに配布されます。

インデックスを照会する目的では、いずれかのクエリ キーを使用できます。 クエリには管理者キーを使うこともできますが、アプリケーション コードではクエリ キーを使うようにしてください。この方が、[最少権限の原則](https://en.wikipedia.org/wiki/Principle_of_least_privilege)に適っています。

## <a name="create-an-instance-of-the-searchindexclient-class"></a>SearchIndexClient クラスのインスタンスの作成
Azure Search .NET SDK を使用してクエリを発行するには、`SearchIndexClient` クラスのインスタンスを作成する必要があります。 このクラスにはいくつかのコンストラクターがあります。 目的のコンストラクターは、検索サービスの名前、インデックスの名前、および `SearchCredentials` オブジェクトをパラメーターとして使用します。 `SearchCredentials` は API キーをラップします。

次のコードは、アプリケーションの構成ファイル ([サンプル アプリケーション](http://aka.ms/search-dotnet-howto)では `appsettings.json`) に保存されている検索サービスの名前と API キーを表す値を使用して、"hotels" というインデックス (「[.NET SDK を使用した Azure Search インデックスの作成](search-create-index-dotnet.md)」で作成したもの) の `SearchIndexClient` を新たに作成します。

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` には `Documents` プロパティがあります。 このプロパティは、Azure Search インデックスの照会に必要なすべてのメソッドを提供します。

## <a name="query-your-index"></a>インデックスの照会
.NET SDK を使用した検索は簡単です。`Documents.Search` メソッドを `SearchIndexClient` で呼び出します。 このメソッドは、検索テキストのほか、クエリをさらに絞り込むために使用できる `SearchParameters` オブジェクトなどのいくつかのパラメーターを受け取ります。

#### <a name="types-of-queries"></a>クエリの種類
主に使用する[クエリの種類](search-query-overview.md#types-of-queries)は、`search` と `filter` の 2 つです。 `search` クエリは、インデックスのすべての *検索可能* フィールドで 1 つ以上の語句を検索します。 `filter` クエリは、インデックスのすべての *フィルター処理可能* フィールドでブール式を評価します。

検索とフィルターは両方とも `Documents.Search` メソッドを使用して実行できます。 検索クエリは `searchText` パラメーターで渡すことができます。一方、フィルター式は `SearchParameters` クラスの `Filter` プロパティで渡すことができます。 検索せずにフィルター処理を実行するには、`"*"` を `searchText` パラメーターに渡します。 フィルター処理を行わずに検索するには、`Filter` プロパティを未設定のままにするか、`SearchParameters` インスタンスを 1 つも渡さないようにします。

#### <a name="example-queries"></a>クエリの例
次のサンプル コードは、「[.NET SDK を使用した Azure Search インデックスの作成](search-create-index-dotnet.md#DefineIndex)」で定義した "hotels" というインデックスを照会するための、異なるいくつかの方法を示しています。 検索結果で返されるドキュメントは `Hotel` クラスのインスタンスであることに注意してください。このクラスは、「[.NET SDK を使用した Azure Search へのデータのアップロード](search-import-data-dotnet.md#HotelClass)」で定義したクラスです。 サンプル コードでは、`WriteDocuments` メソッドを使用して検索結果をコンソールに出力しています。 このメソッドについては次のセクションで説明します。

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="handle-search-results"></a>検索結果の処理方法
`Documents.Search` メソッドは、クエリの結果を含む `DocumentSearchResult` オブジェクトを返します。 前のセクションの例では、 `WriteDocuments` というメソッドを使用して検索結果をコンソールに出力しています。

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

前のセクションに示したクエリの結果は、次のようになります。インデックス "hotels" には「[.NET SDK を使用した Azure Search へのデータのアップロード](search-import-data-dotnet.md)」で示したサンプル データが読み込まれていると想定しています。

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
```

上記のサンプル コードでは、コンソールを使って検索結果を出力します。 同様に、独自のアプリケーションに検索結果を表示する場合もあります。 ASP.NET MVC ベースの Web アプリケーションで検索結果を表示する方法を示した例については、 [GitHub でこちらのサンプル](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) を参照してください。


