---
title: .NET アプリケーションから Azure Search を使用する方法 - Azure Search
description: C# と .NET SDK使用して、.NET アプリケーションで Azure Search を使用する方法について説明します。 コード ベースのタスクには、サービスへの接続、コンテンツのインデックス作成、およびインデックスの照会が含まれます。
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: brjohnst
ms.openlocfilehash: 9f0af40d442747181636b50612f7d2162ead6a86
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450013"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>.NET アプリケーションから Azure Search を使用する方法

この記事では、 [Azure Search .NET SDK](https://aka.ms/search-sdk)を使用する手順について説明します。 .NET SDK を使用すると、Azure Search を使用してアプリケーションにリッチな検索エクスペリエンスを実装できます。

## <a name="whats-in-the-azure-search-sdk"></a>Azure Search SDK の内容
この SDK は､HTTP や JSON に関する詳しい知識がなくても､インデックスやデータ ソース､インデクサー､シノニム マップの管理､ドキュメントのアップロードと管理､クエリの実行を行うことを可能にするいくつかのクライアント ライブラリから構成されています｡ これらのクライアント ライブラリはすべて､NuGet パッケージとして配布されます｡

メインの NuGet パッケージは `Microsoft.Azure.Search` です｡このパッケージは､依存関係がある他のすべてのパッケージを含むメタパッケージです｡ 初めて取り組む場合､あるいはアプリケーションに Azure Search の全機能が必要と分かっている場合は､このパッケージを使用します｡

SDK のその他の NuGet パッケージとしては以下があります｡
 
  - `Microsoft.Azure.Search.Data`:Azure Search を使用して .NET アプリケーションを開発していて、インデックス内のドキュメントのクエリまたは更新のみを行う必要がある場合は、このパッケージを使用します。 インデックス、シノニム マップ､またはサービス レベルのその他のリソースの作成や更新も行う必要がある場合は､代わりに `Microsoft.Azure.Search` パッケージを使用します｡
  - `Microsoft.Azure.Search.Service`:.NET で、Azure Search インデックス、シノニム マップ、インデクサー、データ ソース、またはサービスレベルのその他のリソースを管理するための自動化を開発する場合は、このパッケージを使用します。 インデックス内のドキュメントのクエリまたは更新のみを行う場合は､代わりに `Microsoft.Azure.Search.Data` パッケージを使用します｡ Azure Search のすべての機能が必要な場合は､代わりに `Microsoft.Azure.Search` パッケージを使用します｡
  - `Microsoft.Azure.Search.Common`:Azure Search .NET ライブラリに必要な共通の型です。 このパッケージを直接アプリケーションで使用する必要はありません。 これは、依存関係としてのみ使用されるように考慮されています。

各種クライアント ライブラリには、`Index`、`Field`、`Document` などのクラスや、 `SearchServiceClient` や `SearchIndexClient` クラスに対する `Indexes.Create` や `Documents.Search` などの操作が定義されています。 これらのクラスは、次の名前空間にまとめられています。

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

SDK の今後の更新プログラムについてフィードバックを提供する場合は、[フィードバック ページ](https://feedback.azure.com/forums/263029-azure-search/) を参照するか、[GitHub](https://github.com/azure/azure-sdk-for-net/issues) でイシューを作成し、イシューのタイトルに "Azure Search" を含めます。

.NET SDK は、バージョン `2019-05-06` の [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/) をサポートします。 このバージョンには、Azure BLOB にインデックスを付ける際の、[複合型](search-howto-complex-data-types.md)、[コグニティブ検索](cognitive-search-concept-intro.md)、[オートコンプリート](https://docs.microsoft.com/rest/api/searchservice/autocomplete)、[JsonLines 分析モード](search-howto-index-json-blobs.md)に対するサポートが含まれます。 

この SDK では、Search サービスの作成とスケーリングや API キーの管理などの[管理操作](https://docs.microsoft.com/rest/api/searchmanagement/)はサポートされていません。 .NET アプリケーションから Search リソースを管理する必要がある場合は、[Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk) を使用できます。

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>最新バージョンの SDK へのアップグレード
古いバージョンの Azure Search .NET SDK を既に使用しており、一般公開されている最新のバージョンにアップグレードする場合、[この記事](search-dotnet-sdk-migration-version-9.md)に方法が説明されています。

## <a name="requirements-for-the-sdk"></a>SDK の要件
1. Visual Studio 2017 以降。
2. 自分が所有する Azure Search サービス。 SDK を使用するには、サービスの名前および 1 つまたは複数の API キーが必要です。 [ポータルでの Azure Search サービスの作成](search-create-service-portal.md) 」は、これらの手順の参考になります。
3. Visual Studio の [NuGet パッケージの管理] を使用して、Azure Search .NET SDK の [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Search) をダウンロードします。 NuGet.org でパッケージ名 `Microsoft.Azure.Search` (あるいは機能の一部のみ必要な場合は､上記のうちの対応するパッケージ名) を検索します｡

Azure Search .NET SDK では、.NET Framework 4.5.2 以上と .NET Core 2.0 以上を対象とするアプリケーションがサポートされています。

## <a name="core-scenarios"></a>主要なシナリオ
検索アプリケーションではいくつかの処理を実行する必要があります。 このチュートリアルではこれらの主要なシナリオについて説明します。

* インデックスの作成
* インデックスへのドキュメントの設定
* フルテキスト検索およびフィルターを使用したドキュメントの検索

次のサンプル コードは、これらの各シナリオを示しています。 これらのコード スニペットを独自のアプリケーションに自由に使用してください。

### <a name="overview"></a>概要
これから説明するサンプル アプリケーションは、"hotels" という名前のインデックスを新しく作成し、いくつかのドキュメントをそこに格納してから、検索クエリを実行します。 全体的な流れがわかるメイン プログラムを次に示します。

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> このチュートリアルで使用したサンプル アプリケーションの完全なソース コードが [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) にあります。
> 
>

このプログラムの手順を詳しく見ていきましょう。 最初に、新しい `SearchServiceClient`を作成する必要があります。 このオブジェクトを使用してインデックスを管理できます。 このオブジェクトを作成するには、Azure Search サービス名および管理 API キーを提供する必要があります。 この情報を、[サンプル アプリケーション](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)の `appsettings.json` ファイルに入力できます。

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> 正しくないキーを提供すると (たとえば、管理者キーが必要なときにクエリ キーを渡すなど)、`Indexes.Create` などの操作メソッドを初めて呼び出したときに、`SearchServiceClient` は `CloudException` をスローして "アクセス不可" メッセージを表示します。 このような場合は、API キーを再確認してください。
> 
> 

次の数行では、メソッドを呼び出して "hotels" という名前のインデックスを作成します。インデックスが既にある場合は最初に削除します。 これらのメソッドについては後で説明します。

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

次に、インデックスを設定する必要があります。 インデックスを設定するには、`SearchIndexClient` が必要です。 これを取得するには、作成する方法と、`SearchServiceClient` で `Indexes.GetClient` を呼び出す方法があります。 ここでは簡単な後者を使用します。

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> 一般的な検索アプリケーションでは、インデックスの管理とインデックスの設定は、検索クエリとは別のコンポーネントによって処理される場合があります。 `Indexes.GetClient` は、追加の `SearchCredentials` を指定する手間を省くため、インデックスを作成するのに便利です。 そのためには、`SearchServiceClient` を作成するときに使用した管理者キーを新しい `SearchIndexClient` に渡します。 ただし、アプリケーションのクエリを実行する部分では、管理者キーではなく、データの読み取りのみを可能にするクエリ キーを渡すことができるように、`SearchIndexClient` を直接作成する方が適しています。 これは、最小権限の原則にも適合しており、アプリケーションのセキュリティ強化に役立ちます。 管理者キーとクエリ キーの詳細については、 [こちら](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization)を参照してください。
> 
> 

`SearchIndexClient`を作成したので、インデックスを設定できます。 インデックスの設定は、後で説明する別のメソッドによって実行されます。

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

最後に、検索クエリをいくつか実行し、結果を表示します。 今回は別の `SearchIndexClient` を使用します。

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

`RunQueries` メソッドについては、後ほど詳しく説明します。 新しい `SearchIndexClient` を作成するコードを次に示します。

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

ここでは、インデックスへの書き込みアクセスは不要であるため、クエリ キーを使用します。 この情報を、[サンプル アプリケーション](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)の `appsettings.json` ファイルに入力できます。

有効なサービス名と API キーを使用してこのアプリケーションを実行すると、出力は次の例のようになります。(一部のコンソール出力は、説明のため "..." で置き換えられています。)

    Deleting index...

    Creating index...

    Uploading documents...

    Waiting for documents to be indexed...

    Search the entire index for the term 'motel' and return only the HotelName field:

    Name: Secret Point Motel

    Name: Twin Dome Motel


    Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

    HotelId: 2
    Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

    Name: Triple Landscape Hotel
    Last renovated on: 9/20/2015 12:00:00 AM +00:00

    Name: Twin Dome Motel
    Last renovated on: 2/18/1979 12:00:00 AM +00:00


    Search the hotel names for the term 'hotel':

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

    Complete.  Press any key to end application... 

アプリケーションの完全なソース コードは、この記事の最後で提供します。

次に、 `Main`によって呼び出される各メソッドを詳しく見ていきます。

### <a name="creating-an-index"></a>インデックスの作成
`SearchServiceClient` を作成した後、`Main` は次に、"hotels" インデックスが既に存在する場合はそれを削除します。 その削除は、次のメソッドによって実行されます。

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

このメソッドは、指定された `SearchServiceClient` を使用してインデックスが存在するかどうかを確認し、存在する場合は、それを削除します。

> [!NOTE]
> この記事のコード例では、わかりやすくするため、Azure Search .NET SDK の同期メソッドを使用します。 実際のアプリケーションでは、高い拡張性と応答性を維持するため、非同期メソッドを使用することをお勧めします。 たとえば、上記のメソッドでは、`Exists` と `Delete` の代わりに、`ExistsAsync` および `DeleteAsync` を使用できます。
> 
> 

次に、 `Main` は次のメソッドを呼び出すことによって、新しい "hotels" インデックスを作成します。

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

このメソッドは、新しいインデックスのスキーマを定義する `Field` オブジェクトのリストで新しい `Index` オブジェクトを作成します。 各フィールドには、名前、データ型、および検索動作を定義するいくつかの属性があります。 `FieldBuilder` クラスでは、リフレクションを使用して指定された `Hotel` モデル クラスのパブリック プロパティと属性を調べることで、インデックスの `Field` オブジェクトのリストを作成します。 `Hotel` クラスについては、後ほど詳しく説明します。

> [!NOTE]
> 必要に応じて、`FieldBuilder` を使用するのではなく、`Field` オブジェクトのリストをいつでも直接作成できます。 たとえば、モデル クラスを使用しない場合や、属性を追加して変更するのは望ましくない既存のモデル クラスを使用する必要がある場合などです。
>
> 

フィールドに加えて、スコアリング プロファイル、サジェスター、または CORS オプションも Index に追加できます (簡潔にするために、これらのパラメーターはサンプルから省略されています)。 Index オブジェクトとその構成要素の詳細については、[SDK リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)および [Azure Search REST API リファレンス](https://docs.microsoft.com/rest/api/searchservice/)をご覧ください。

### <a name="populating-the-index"></a>インデックスの設定
`Main` の次の手順では、新しく作成したインデックスを設定します。 このインデックス設定は、次のメソッドで実行されます。(説明のため "..." で置き換えられた一部のコード。  完全なデータ生成コードに対する完全なサンプル ソリューションを参照してください。)

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

このメソッドには 4 つの部分があります。 最初の部分では、インデックスにアップロードする入力データとして使用される、3 つの `Hotel` オブジェクトと、それぞれに含まれる 3 つの `Room` オブジェクトの配列を作成します。 このデータは、わかりやすくするためハードコーディングされています。 実際のアプリケーションでは、通常、データは SQL Database などの外部データ ソースから取得されます。

2 番目の部分では、ドキュメントを含む `IndexBatch` を作成します。 この場合は `IndexBatch.Upload`を呼び出すことによって、作成時にバッチに適用する操作を指定します。 その後、バッチは `Documents.Index` メソッドによって Azure Search インデックスにアップロードされます。

> [!NOTE]
> この例では、単にドキュメントをアップロードします。 既存のドキュメントに変更をマージする、またはドキュメントを削除する場合は、代わりに `IndexBatch.Merge`、`IndexBatch.MergeOrUpload`、または `IndexBatch.Delete` を呼び出すことによってバッチを作成できます。 `IndexBatch.New` を呼び出すことによって 1 つのバッチでさまざまな操作を組み合わせることもできます。これによって、`IndexAction` オブジェクトのコレクションを受け取り、各オブジェクトがドキュメントで特定の操作を実行するよう Azure Search に指示します。 `IndexAction.Merge` や `IndexAction.Upload` などの対応するメソッドを呼び出すことによって、独自の操作を行う `IndexAction` を作成できます。
> 
> 

このメソッドの 3 番目の部分は、インデックス作成の重要なエラー ケースを処理する catch ブロックです。 Azure Search がバッチ内の一部のドキュメントのインデックス作成に失敗した場合、`Documents.Index` は `IndexBatchException` をスローします。 この例外は、サービスの負荷が高いときにドキュメントのインデックスを作成していると発生する場合があります。 **コードでこのケースを明示的に処理することを強くお勧めします。** しばらく待ってから失敗したドキュメントのインデックス作成を再試行したり、サンプルと同じようにログに記録してから続けることができます。または、アプリケーションのデータ整合性要件に応じて他の処理を行うこともできます。

> [!NOTE]
> [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) メソッドを使用して、`Index` の前回の呼び出しで失敗したアクションだけを含む新しいバッチを作成できます。 このメソッドの適切な使用方法については、[StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry) をご覧ください。
>
>

最後に、`UploadDocuments` メソッドは 2 秒間遅延します。 インデックスの作成は Azure Search サービスで非同期的に行われるので、サンプル アプリケーションは短い時間待機して、確実にドキュメントを検索に使用できるようにする必要があります。 通常、このような遅延は、デモ、テスト、およびサンプル アプリケーションでのみ必要です。

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>.NET SDK がドキュメントを処理する方法
Azure Search .NET SDK が `Hotel` のようなユーザー定義クラスのインスタンスをどのようにしてインデックスにアップロードできるのか不思議に思われるかもしれません。 その質問に答えるため、 `Hotel` クラスを見ていくことにします。

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("Description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

最初に注目すべき点は、`Hotel` クラス内の各パブリック プロパティの名前がインデックス定義内の同じ名前でフィールドにマップされることです。 各フィールドを小文字で始める場合 ("camel case")、そのクラス上の `[SerializePropertyNamesAsCamelCase]` 属性を使用してプロパティ名を自動的に camel-case にマップするよう、SDK に指示することができます。 このシナリオは、ターゲット スキーマがアプリケーション開発者の制御外にあるときに、.NET の "パスカル ケース" 命名に関するガイドラインに違反することなくデータ バインドを実行する .NET アプリケーションでは一般的です。

> [!NOTE]
> Azure Search .NET SDK は、 [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) ライブラリを使用して、カスタムのモデル オブジェクトから JSON 形式へのシリアル化や JSON 形式からの逆シリアル化を行います。 必要に応じてこのシリアル化をカスタマイズできます。 詳細については、「[JSON.NET 使用したシリアル化のカスタマイズ](#JsonDotNet)」をご覧ください。
> 
> 

次に注目すべき点は、各プロパティが `IsFilterable`、`IsSearchable`、`Key`、`Analyzer` などの属性で装飾されていることです。 これらの属性は、[Azure Search インデックス内の対応するフィールド属性](https://docs.microsoft.com/rest/api/searchservice/create-index#request)に直接マップされます。 `FieldBuilder` クラスは、これらのプロパティを使用してインデックスのフィールド定義を構築します。

`Hotel` クラスに関する 3 番目の重要な点は、パブリック プロパティのデータ型です。 これらのプロパティの .NET 型は、インデックス定義でそれらと同等のフィールド型にマップします。 たとえば、`Category` 文字列プロパティは、`Edm.String` 型の `category` フィールドにマップします。 `bool?`、`Edm.Boolean`、`DateTimeOffset?`、`Edm.DateTimeOffset` などの間にも、同じような型のマッピングがあります。 型のマッピングの具体的なルールについては、[Azure Search .NET SDK リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get)で `Documents.Get` メソッドを参照してください。 `FieldBuilder` クラスは、このマッピングに自動的に対処しますが、シリアル化の問題のトラブルシューティングを行う必要がある場合に、マッピングを理解しておくと役立ちます。

`SmokingAllowed` プロパティを見つけてしまいましたか?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

このプロパティの `JsonIgnore` 属性によって、フィールドとしてインデックスにシリアル化しないよう、`FieldBuilder` に指示が与えられます。  これは、ご利用のアプリケーションでヘルパーとして使用できる、クライアント側の計算されたプロパティを作成する優れた方法です。  この場合、`SmokingAllowed` プロパティは、`Rooms` コレクション内に喫煙可能な `Room` があるかどうかを反映します。  すべて false の場合、ホテル全館で喫煙できないことを示します。

`Address` や `Rooms` など、いくつかのプロパティは、.NET クラスのインスタンスです。  これらのプロパティは、さらに複雑なデータ構造を表し、インデックス内で[複合データ型](https://docs.microsoft.com/azure/search/search-howto-complex-data-types)を備えたフィールドを必要とします。

`Address` プロパティは、以下に定義された `Address` クラス内の複数の値のセットを表します。

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

このクラスには、米国またはカナダで住所を記述するために使用される標準的な値が含まれます。 インデックス内で論理フィールドをグループ化するには、このような型を使用できます。

`Rooms` プロパティは、`Room` オブジェクトの配列を表します。

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

.NET のデータ モデルおよびその対応するインデックス スキーマは、エンド ユーザーに提供する検索エクスペリエンスをサポートするように設計されなくてはなりません。 .NET の最上位レベル オブジェクト、すなわちインデックス内のドキュメントはそれぞれ、ユーザー インターフェイスで示される検索結果に対応します。 たとえば、ホテル検索アプリケーションで、エンド ユーザーは、ホテル名、ホテルの機能、または特定の部屋の特徴で検索することができます。 後でクエリの例をいくつか説明します。

インデックス内でドキュメントの操作を行うために独自のクラスを使用するこの機能は、次のセクションで説明するように、検索結果の取得、および SDK による任意の型への自動逆シリアル化という両方向で動作します。

> [!NOTE]
> Azure Search .NET SDK は、`Document` クラスを使用して動的に型指定されたドキュメントもサポートします。これは、フィールドの値に対するフィールド名のキー/値マッピングです。 この機能は、設計時にインデックス スキーマがわからない場合、または特定のモデル クラスにバインドすると不都合な場合に便利です。 ドキュメントを処理する SDK のすべてのメソッドには、`Document` クラスを使用するオーバーロード、およびジェネリック型パラメーターを使用する厳密な型指定のオーバーロードがあります。 このチュートリアルのサンプル コードでは、後者のみを使用しています。 [`Document` クラス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document)は `Dictionary<string, object>` から継承します。
> 
>

**null 許容のデータ型を使用する理由**

Azure Search インデックスにマップする独自のモデル クラスを設計するときは、`bool` や `int` などの値型のプロパティを null 許容型として宣言することをお勧めします (たとえば、`bool` ではなく `bool?` を使用する)。 null 非許容プロパティを使用する場合、対応するフィールドに null 値が含まれるドキュメントがインデックス内に存在しないことを、開発者が **保証する** 必要があります。 SDK または Azure Search サービスで、これを強制することはできません。

これは単なる仮定上の問題ではありません。`Edm.Int32` 型の既存のインデックスに新しいフィールドを追加する場合を考えてみてください。 インデックスの定義を更新した後、(Azure Search ではすべての型が null を許容するので) すべてのドキュメントでその新しいフィールドの値が null になります。 その後、そのフィールドが null 非許容型の `int` プロパティであるモデル クラスを使用した場合、ドキュメントを取得しようとすると、次のような `JsonSerializationException` が発生します。

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

このため、ベスト プラクティスとして、モデル クラスでは null 許容型を使用することをお勧めします。

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>JSON.NET 使用したシリアル化のカスタマイズ
SDK では、ドキュメントのシリアル化と逆シリアル化に JSON.NET を使用します。 独自の `JsonConverter` または `IContractResolver` を定義することによって、必要に応じてシリアル化と逆シリアル化をカスタマイズできます。 詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。 この機能は、アプリケーションの既存のモデル クラスを Azure Search 用に適合させる場合、およびその他の高度なシナリオに役立ちます。 たとえば、カスタム シリアル化を使用すると次のことが可能です。

* ドキュメント フィールドとして格納されるものに、モデル クラスの特定のプロパティを含める、または除外する。
* コードのプロパティ名とインデックスのフィールド名をマップする。
* ドキュメント フィールドへのプロパティのマッピングに使用できるカスタム属性を作成する。

Azure Search .NET SDK のユニット テストにカスタム シリアル化を実装する例については、GitHub を参照してください。 手始めとしては、[このフォルダー](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models)が適しています。 カスタム シリアル化のテストに使用されるクラスが含まれます。

### <a name="searching-for-documents-in-the-index"></a>インデックス内のドキュメントの検索
サンプル アプリケーションの最後の手順として、インデックス内のいくつかのドキュメントを検索します。

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

クエリを実行するたびに、このメソッドはまず新しい `SearchParameters` オブジェクトを作成します。 このオブジェクトは、並べ替え、フィルター処理、ページング、ファセットなどの、クエリへの追加オプションを指定するために使用されます。 このメソッドでは、さまざまなクエリの `Filter`、`Select`、`OrderBy`、`Top` の各プロパティを設定します。 `SearchParameters` のすべてのプロパティについては、[こちら](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters)をご覧ください。

次の手順では、検索クエリを実際に実行します。 この検索は、`Documents.Search` メソッドを使用して実行されます。 各クエリでは、使用する検索テキストを文字列として渡し (検索テキストがない場合は `"*"`)、以前に作成した検索パラメーターも渡します。 また、`Documents.Search` に対する型パラメーターとして `Hotel` も指定します。これは、検索結果のドキュメントを `Hotel` 型のオブジェクトに逆シリアル化するように SDK に指示します。

> [!NOTE]
> 検索クエリ式の構文の詳細については、[こちら](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)をご覧ください。
> 
> 

最後に、各クエリの実行後、このメソッドは検索結果のすべての一致を反復処理し、各ドキュメントをコンソールに出力します。

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

各クエリについて詳しく見ていきましょう。 最初のクエリを実行するコードを次に示します。

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

この例では、検索可能なあらゆるフィールドで "motel" という単語のインデックス全体を検索し、`Select` パラメーターによって指定されるとおり、ホテル名だけを返します。 結果は次のとおりです。

    Name: Secret Point Motel

    Name: Twin Dome Motel

次のクエリは、さらに興味深いものです。  1 泊 100 ドル未満の部屋があるホテルを検索し、ホテル ID と説明だけを返します。

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

このクエリでは、OData の `$filter` 式 (`Rooms/any(r: r/BaseRate lt 100)`) を使用して、インデックス内のドキュメントをフィルター処理します。 ここでは、[あらゆる演算子](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators)を使用して、'BaseRate lt 100' をルーム コレクションのすべての項目に適用します。 Azure Search がサポートする OData 構文の詳細については、 [こちら](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax)を参照してください。

クエリの結果は次のとおりです。

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

次に、最近改装された上位 2 つのホテルを検索し、ホテル名と最終改装日を表示します。 次にコードを示します。 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

この例でも、OData 構文を使用して `OrderBy` パラメーターに `lastRenovationDate desc` を指定しています。 また、上位 2 つのドキュメントだけを取得できるように、`Top` を 2 に設定しています。 前と同様に、`Select` を設定して返す必要があるフィールドを指定します。

結果は次のとおりです。

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

最後に、"hotel" という単語に一致するすべてのホテル名を検索します。

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

結果は次のとおりです。`Select` プロパティを指定しなかったので、この結果にはすべてのフィールドが含まれています。

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

チュートリアルはここまでですが、ここで止めないでください。 **次のステップでは、Azure Search をさらに学習するための他のリソースを提供します。

## <a name="next-steps"></a>次の手順
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) と [REST API](https://docs.microsoft.com/rest/api/searchservice/) のリファレンスを参照してください。
* [名前付け規則](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) で、さまざまなオブジェクトに名前を付けるときの規則を学習してください。
* Azure Search で [サポートされるデータ型](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) を確認してください。
