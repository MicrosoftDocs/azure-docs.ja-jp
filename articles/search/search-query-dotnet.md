---
title: C# (.NET SDK) で Azure Search インデックスのデータに対するクエリを実行する - Azure Search
description: Azure Search で検索クエリを作成するための C# コードの例です。 検索結果のフィルター処理と並べ替えを行うための検索パラメーターを追加します。
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: 1194407122123797c2564c96ac452b9582b017a4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264949"
---
# <a name="quickstart-3---query-an-azure-search-index-in-c"></a>クイック スタート:3 - C# で Azure Search インデックスに対するクエリを実行する

この記事では、C# と [.NET SDK](https://aka.ms/search-sdk) を使用して [Azure Search インデックス](search-what-is-an-index.md)に対するクエリを実行する方法について説明します。 インデックス内のドキュメントの検索は、次のタスクを実行することによって実現されます。

> [!div class="checklist"]
> * 読み取り専用権限で検索インデックスに接続するための [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) オブジェクトを作成します。
> * 検索またはフィルター定義を含む [`SearchParameters`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet) オブジェクトを作成します。
> * `SearchIndexClient` で `Documents.Search` メソッドを呼び出してインデックスにクエリを送信します。

## <a name="prerequisites"></a>前提条件

hotels サンプル データを含む [Azure Search インデックスを読み込みます](search-import-data-dotnet.md)。

ドキュメントへの読み取り専用アクセスに使用されるクエリ キーを取得します。 これまでは、管理者 API キーを使用して、検索サービスのオブジェクトとコンテンツを作成できるようにしていました。 しかし、アプリでのクエリ サポートにはクエリ キーを使用することを強くお勧めします。 手順については、[クエリ キーの作成](search-security-api-keys.md#create-query-keys)に関するページを参照してください。

## <a name="create-a-client"></a>クライアントの作成
(前のレッスンで使用した `SearchServiceClient` に対して付与された書き込みアクセス権ではなく) 読み取り専用アクセス用のクエリ キーを付与できるようにするために、`SearchIndexClient` クラスのインスタンスを作成します。

このクラスにはいくつかのコンストラクターがあります。 目的のコンストラクターは、検索サービスの名前、インデックスの名前、および [`SearchCredentials`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials?view=azure-dotnet) オブジェクトをパラメーターとして受け取ります。 `SearchCredentials` は API キーをラップします。

次のコードでは、アプリケーションの構成ファイル ([サンプル アプリケーション](https://aka.ms/search-dotnet-howto)では `appsettings.json`) に格納されている検索サービスの名前と API キーの値を使用して、"hotels" インデックス用の新しい `SearchIndexClient` を作成します。

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` は [`Documents`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient.documents?view=azure-dotnet) プロパティを持ちます。 このプロパティは、Azure Search インデックスの照会に必要なすべてのメソッドを提供します。

## <a name="construct-searchparameters"></a>SearchParameters の作成
.NET SDK を使用した検索は簡単です。`Documents.Search` メソッドを `SearchIndexClient` で呼び出します。 このメソッドは、検索テキストのほか、クエリをさらに絞り込むために使用できる `SearchParameters` オブジェクトなどのいくつかのパラメーターを受け取ります。

### <a name="types-of-queries"></a>クエリの種類
主に使用する[クエリの種類](search-query-overview.md#types-of-queries)は、`search` と `filter` の 2 つです。 `search` クエリは、インデックスのすべての *検索可能* フィールドで 1 つ以上の語句を検索します。 `filter` クエリは、インデックスのすべての *フィルター処理可能* フィールドでブール式を評価します。 検索とフィルターは、一緒に使用することも、別々に使用することもできます。

検索とフィルターは両方とも `Documents.Search` メソッドを使用して実行できます。 検索クエリは `searchText` パラメーターで渡すことができます。一方、フィルター式は `SearchParameters` クラスの `Filter` プロパティで渡すことができます。 検索せずにフィルター処理を実行するには、`"*"` を `searchText` パラメーターに渡します。 フィルター処理を行わずに検索するには、`Filter` プロパティを未設定のままにするか、`SearchParameters` インスタンスを 1 つも渡さないようにします。

### <a name="example-queries"></a>クエリの例
次のサンプル コードは、「[C# で Azure Search インデックスを作成する](search-create-index-dotnet.md#DefineIndex)」で定義した "hotels" というインデックスに対するクエリを実行するための、異なるいくつかの方法を示しています。 検索結果と共に返されるドキュメントは `Hotel` クラスのインスタンスであることに注意してください。このクラスは、[C# で Azure Search インデックスにデータをインポートする](search-import-data-dotnet.md#construct-indexbatch)方法に関するページで定義したクラスです。 サンプル コードでは、`WriteDocuments` メソッドを使用して検索結果をコンソールに出力しています。 このメソッドについては次のセクションで説明します。

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

前のセクションに示したクエリの結果は、次のようになります。インデックス "hotels" にはサンプル データが読み込まれていると想定しています。

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

上記のサンプル コードでは、コンソールを使って検索結果を出力します。 同様に、独自のアプリケーションに検索結果を表示する場合もあります。 ASP.NET MVC ベースの Web アプリケーションで検索結果を表示する方法を示した例については、GitHub の [DotNetSample プロジェクト](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample)を参照してください。

## <a name="next-steps"></a>次の手順

まだの場合は、GitHub の [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) のサンプル コードを確認すると共に、「[.NET アプリケーションから Azure Search を使用する方法](search-howto-dotnet-sdk.md)」でサンプル コードの詳しい説明を参照してください。 