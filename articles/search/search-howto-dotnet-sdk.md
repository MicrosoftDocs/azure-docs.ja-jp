---
title: .NET アプリケーションから Azure Search を使用する方法 - Azure Search
description: C# と .NET SDK使用して、.NET アプリケーションで Azure Search を使用する方法について説明します。 コード ベースのタスクには、サービスへの接続、コンテンツのインデックス作成、およびインデックスの照会が含まれます。
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: afc60e933c9fcc154af74c47e382d8b8e7b0df8d
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286314"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>.NET アプリケーションから Azure Search を使用する方法
この記事では、 [Azure Search .NET SDK](https://aka.ms/search-sdk)を使用する手順について説明します。 .NET SDK を使用すると、Azure Search を使用してアプリケーションにリッチな検索エクスペリエンスを実装できます。

## <a name="whats-in-the-azure-search-sdk"></a>Azure Search SDK の内容
この SDK は､HTTP や JSON に関する詳しい知識がなくても､インデックスやデータ ソース､インデクサー､シノニム マップの管理､ドキュメントのアップロードと管理､クエリの実行を行うことを可能にするいくつかのクライアント ライブラリから構成されています｡ これらのクライアント ライブラリはすべて､NuGet パッケージとして配布されます｡

メインの NuGet パッケージは `Microsoft.Azure.Search` です｡このパッケージは､依存関係がある他のすべてのパッケージを含むメタパッケージです｡ 初めて取り組む場合､あるいはアプリケーションに Azure Search の全機能が必要と分かっている場合は､このパッケージを使用します｡

SDK のその他の NuGet パッケージとしては以下があります｡
 
  - `Microsoft.Azure.Search.Data`:Azure Search を使用して .NET アプリケーションを開発していて、インデックス内のドキュメントのクエリまたは更新のみを行う必要がある場合は、このパッケージを使用します。 インデックス、シノニム マップ､またはサービス レベルのその他のリソースの作成や更新も行う必要がある場合は､代わりに `Microsoft.Azure.Search` パッケージを使用します｡
  - `Microsoft.Azure.Search.Service`:.NET で、Azure Search インデックス、シノニム マップ、インデクサー、データ ソース、またはサービスレベルのその他のリソースを管理するための自動化を開発する場合は、このパッケージを使用します。 インデックス内のドキュメントのクエリまたは更新のみを行う場合は､代わりに `Microsoft.Azure.Search.Data` パッケージを使用します｡ Azure Search のすべての機能が必要な場合は､代わりに `Microsoft.Azure.Search` パッケージを使用します｡
  - `Microsoft.Azure.Search.Common`:Azure Search .NET ライブラリに必要な共通の型です。 このパッケージは、アプリケーションで直接使用する必要はないはずで、依存関係としての使用のみが想定されています｡

各種クライアント ライブラリには、`Index`、`Field`、`Document` などのクラスや、 `SearchServiceClient` や `SearchIndexClient` クラスに対する `Indexes.Create` や `Documents.Search` などの操作が定義されています。 これらのクラスは、次の名前空間にまとめられています。

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Azure Search .NET SDK の最新バージョンが一般公開されました。 次のバージョンに組み込むためにフィードバックを提供する場合は、 [フィードバック ページ](https://feedback.azure.com/forums/263029-azure-search/)を使用してください。

.NET SDK は、バージョン `2017-11-11` の [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/) をサポートします。 このバージョンには､シノニムのサポートや､インデクサーに対する漸進的な改良も反映されています｡ このバージョンには､JSON アレイや CSV ファイルのインデックス作成のサポートなどのプレビュー機能は*含まれていません*｡プレビュー機能は[プレビュー](search-api-2016-09-01-preview.md)段階であり、[4.0-preview バージョンの .NET SDK](https://aka.ms/search-sdk-preview) で提供されます。

この SDK では、Search サービスの作成とスケーリングや API キーの管理などの[管理操作](https://docs.microsoft.com/rest/api/searchmanagement/)はサポートされていません。 .NET アプリケーションから Search リソースを管理する必要がある場合は、[Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk) を使用できます。

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>最新バージョンの SDK へのアップグレード
古いバージョンの Azure Search .NET SDK を既に使用しており、一般公開された新しいバージョンにアップグレードする場合、方法については [この記事](search-dotnet-sdk-migration-version-5.md) をご覧ください。

## <a name="requirements-for-the-sdk"></a>SDK の要件
1. Visual Studio 2017。
2. 自分が所有する Azure Search サービス。 SDK を使用するには、サービスの名前および 1 つまたは複数の API キーが必要です。 [ポータルでの Azure Search サービスの作成](search-create-service-portal.md) 」は、これらの手順の参考になります。
3. Visual Studio の [NuGet パッケージの管理] を使用して、Azure Search .NET SDK の [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Search) をダウンロードします。 NuGet.org でパッケージ名 `Microsoft.Azure.Search` (あるいは機能の一部のみ必要な場合は､上記のうちの対応するパッケージ名) を検索します｡

Azure Search .NET SDK は、.NET Framework 4.5.2 以上と .NET Core を対象とするアプリケーションをサポートしています。

## <a name="core-scenarios"></a>主要なシナリオ
検索アプリケーションではいくつかの処理を実行する必要があります。 このチュートリアルではこれらの主要なシナリオについて説明します。

* インデックスの作成
* インデックスへのドキュメントの設定
* フルテキスト検索およびフィルターを使用したドキュメントの検索

次のサンプル コードではこれらについて示します。 これらのコード スニペットを独自のアプリケーションに自由に使用してください。

### <a name="overview"></a>概要
これから説明するサンプル アプリケーションは、"hotels" という名前のインデックスを新しく作成し、いくつかのドキュメントをそこに格納してから、検索クエリを実行します。 全体的な流れがわかるメイン プログラムを次に示します。

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteHotelsIndexIfExists(serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateHotelsIndex(serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

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
DeleteHotelsIndexIfExists(serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateHotelsIndex(serviceClient);
```

次に、インデックスを設定する必要があります。 そのためには、 `SearchIndexClient`が必要です。 これを取得するには、作成する方法と、`SearchServiceClient` で `Indexes.GetClient` を呼び出す方法があります。 ここでは簡単な後者を使用します。

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> 一般的な検索アプリケーションでは、インデックスの管理とインデックスの設定は、検索クエリとは別のコンポーネントによって処理されます。 `Indexes.GetClient` は、別の `SearchCredentials` を指定する手間を省くため、インデックスを作成するのに便利です。 そのためには、`SearchServiceClient` を作成するときに使用した管理者キーを新しい `SearchIndexClient` に渡します。 ただし、アプリケーションのクエリを実行する部分では、管理者キーの代わりにクエリ キーを渡すことができるように、 `SearchIndexClient` を直接作成する方が適しています。 これは、最小権限の原則にも適合しており、アプリケーションのセキュリティ強化に役立ちます。 管理者キーとクエリ キーの詳細については、 [こちら](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization)を参照してください。
> 
> 

`SearchIndexClient`を作成したので、インデックスを設定できます。 これは、後で説明する別のメソッドで行います。

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

最後に、検索クエリをいくつか実行し、結果を表示します。 今回は別の `SearchIndexClient` を使用します。

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

RunQueries(indexClientForQueries);
```

`RunQueries` メソッドについては、後ほど詳しく説明します。 新しい `SearchIndexClient` を作成するコードを次に示します。

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

ここでは、インデックスへの書き込みアクセスは不要であるため、クエリ キーを使用します。 この情報を、[サンプル アプリケーション](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)の `appsettings.json` ファイルに入力できます。

有効なサービス名と API キーを使用してこのアプリケーションを実行した場合、出力は次のようになります。

    Deleting index...
    
    Creating index...
    
    Uploading documents...
    
    Waiting for documents to be indexed...
    
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
    
    Complete.  Press any key to end application...

アプリケーションの完全なソース コードは、この記事の最後で提供します。

次に、 `Main`によって呼び出される各メソッドを詳しく見ていきます。

### <a name="creating-an-index"></a>インデックスの作成
`SearchServiceClient` を作成した後、`Main` は次に、"hotels" インデックスが既に存在する場合はそれを削除します。 この処理は次のメソッドで行います。

```csharp
private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists("hotels"))
    {
        serviceClient.Indexes.Delete("hotels");
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
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
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

フィールドに加えて、スコアリング プロファイル、サジェスター、または CORS オプションを Index に追加することもできます (簡潔さを優先し、サンプルではこれらは省略されています)。 Index オブジェクトとその構成要素の詳細については、[SDK リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)および [Azure Search REST API リファレンス](https://docs.microsoft.com/rest/api/searchservice/)をご覧ください。

### <a name="populating-the-index"></a>インデックスの設定
`Main` の次の手順では、新しく作成したインデックスを設定します。 この処理は次のメソッドで行います。

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        { 
            HotelId = "1", 
            BaseRate = 199.0, 
            Description = "Best hotel in town",
            DescriptionFr = "Meilleur hôtel en ville",
            HotelName = "Fancy Stay",
            Category = "Luxury", 
            Tags = new[] { "pool", "view", "wifi", "concierge" },
            ParkingIncluded = false, 
            SmokingAllowed = false,
            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
            Rating = 5, 
            Location = GeographyPoint.Create(47.678581, -122.131577)
        },
        new Hotel()
        { 
            HotelId = "2", 
            BaseRate = 79.99,
            Description = "Cheapest hotel in town",
            DescriptionFr = "Hôtel le moins cher en ville",
            HotelName = "Roach Motel",
            Category = "Budget",
            Tags = new[] { "motel", "budget" },
            ParkingIncluded = true,
            SmokingAllowed = true,
            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
            Rating = 1,
            Location = GeographyPoint.Create(49.678581, -122.131577)
        },
        new Hotel() 
        { 
            HotelId = "3", 
            BaseRate = 129.99,
            Description = "Close to town hall and the river"
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

このメソッドには 4 つの部分があります。 最初の部分では、インデックスにアップロードする入力データとして使用される `Hotel` オブジェクトの配列を作成します。 このデータは、わかりやすくするためハードコーディングされています。 実際のアプリケーションでは、通常、データは SQL Database などの外部データ ソースから取得されます。

2 番目の部分では、ドキュメントを含む `IndexBatch` を作成します。 この場合は `IndexBatch.Upload`を呼び出すことによって、作成時にバッチに適用する操作を指定します。 その後、バッチは `Documents.Index` メソッドによって Azure Search インデックスにアップロードされます。

> [!NOTE]
> この例では、単にドキュメントをアップロードします。 既存のドキュメントに変更をマージする、またはドキュメントを削除する場合は、代わりに `IndexBatch.Merge`、`IndexBatch.MergeOrUpload`、または `IndexBatch.Delete` を呼び出すことによってバッチを作成できます。 `IndexBatch.New` を呼び出すことによって 1 つのバッチでさまざまな操作を組み合わせることもできます。これによって、`IndexAction` オブジェクトのコレクションを受け取り、各オブジェクトがドキュメントで特定の操作を実行するよう Azure Search に指示します。 `IndexAction.Merge` や `IndexAction.Upload` などの対応するメソッドを呼び出すことによって、独自の操作を行う `IndexAction` を作成できます。
> 
> 

このメソッドの 3 番目の部分は、インデックス作成の重要なエラー ケースを処理する catch ブロックです。 Azure Search がバッチ内の一部のドキュメントのインデックス作成に失敗した場合、`Documents.Index` は `IndexBatchException` をスローします。 サービスの負荷が高いときにドキュメントのインデックスを作成すると、これが発生する場合があります。 **コードでこのケースを明示的に処理することを強くお勧めします。** しばらく待ってから失敗したドキュメントのインデックス作成を再試行したり、サンプルと同じようにログに記録してから続けることができます。または、アプリケーションのデータ整合性要件に応じて他の処理を行うこともできます。

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

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

最初に注目すべき点は、`Hotel` の各パブリック プロパティがインデックス定義のフィールドに対応していることですが、1 つ重要な違いがあります。各フィールドの名前が小文字で始まっている ("キャメル ケース") のに対し、`Hotel` の各パブリック プロパティの名前は大文字で始まっています ("パスカル ケース")。 これは、ターゲット スキーマをアプリケーション開発者が制御できない場合にデータ バインドを実行する .NET アプリケーションでの一般的なシナリオです。 プロパティ名を camel-case にして .NET の命名ガイドラインに違反するのではなく、プロパティ名を自動的に camel-case にマップするように `[SerializePropertyNamesAsCamelCase]` 属性で SDK に指示できます。

> [!NOTE]
> Azure Search .NET SDK は、 [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) ライブラリを使用して、カスタムのモデル オブジェクトから JSON 形式へのシリアル化や JSON 形式からの逆シリアル化を行います。 必要に応じてこのシリアル化をカスタマイズできます。 詳細については、「[JSON.NET 使用したシリアル化のカスタマイズ](#JsonDotNet)」をご覧ください。
> 
> 

次に注目すべき点は、各パブリック プロパティ (`IsFilterable`、`IsSearchable`、`Key`、`Analyzer` など) を装飾する属性です。 これらの属性は、[Azure Search インデックスの対応する属性](https://docs.microsoft.com/rest/api/searchservice/create-index#request)に直接マップされます。 `FieldBuilder` クラスでは、これらの属性を使用してインデックスのフィールド定義を作成します。

`Hotel` クラスに関する 3 番目の重要な点は、パブリック プロパティのデータ型です。 これらのプロパティの .NET 型は、インデックス定義でそれらと同等のフィールド型にマップします。 たとえば、`Category` 文字列プロパティは、`Edm.String` 型の `category` フィールドにマップします。 `bool?` と `Edm.Boolean`、`DateTimeOffset?` と `Edm.DateTimeOffset` などの間にも、同じような型のマッピングがあります。型のマッピングの具体的なルールについては、[Azure Search .NET SDK リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get)で `Documents.Get` メソッドを参照してください。 `FieldBuilder` クラスは、このマッピングに自動的に対処しますが、シリアル化の問題のトラブルシューティングを行う必要がある場合に、マッピングを理解しておくと役立ちます。

ドキュメントとして独自のクラスを使用するこの機能は、両方向で動作します。また、次のセクションで見るように、検索結果を取得し、SDK で自動的に任意の型に逆シリアル化することもできます。

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
SDK では、ドキュメントのシリアル化と逆シリアル化に JSON.NET を使用します。 独自の `JsonConverter` または `IContractResolver` を定義して、必要に応じてシリアル化と逆シリアル化をカスタマイズできます (詳細については、「[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)」をご覧ください)。 この機能は、アプリケーションの既存のモデル クラスを Azure Search 用に適合させる場合、およびその他の高度なシナリオに役立ちます。 たとえば、カスタム シリアル化を使用すると次のことが可能です。

* ドキュメント フィールドとして格納されるものに、モデル クラスの特定のプロパティを含める、または除外する。
* コードのプロパティ名とインデックスのフィールド名をマップする。
* ドキュメント フィールドへのプロパティのマッピングに使用できるカスタム属性を作成する。

Azure Search .NET SDK のユニット テストにカスタム シリアル化を実装する例については、GitHub を参照してください。 手始めとしては、[このフォルダー](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models)が適しています。 カスタム シリアル化のテストに使用されるクラスが含まれます。

### <a name="searching-for-documents-in-the-index"></a>インデックス内のドキュメントの検索
サンプル アプリケーションでは最後に、インデックス内のいくつかのドキュメントを検索します。 次のメソッドがこれを行います。

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
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
}
```

クエリを実行するたびに、このメソッドはまず新しい `SearchParameters` オブジェクトを作成します。 このオブジェクトは、並べ替え、フィルター処理、ページング、ファセットなどの追加オプションをクエリに対して指定するために使用されます。 このメソッドでは、さまざまなクエリの `Filter`、`Select`、`OrderBy`、`Top` の各プロパティを設定します。 `SearchParameters` のすべてのプロパティについては、[こちら](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters)をご覧ください。

次の手順では、検索クエリを実際に実行します。 これは `Documents.Search` メソッドを使用して行われます。 各クエリでは、使用する検索テキストを文字列として渡し (検索テキストがない場合は `"*"`)、以前に作成した検索パラメーターも渡します。 また、`Documents.Search` に対する型パラメーターとして `Hotel` も指定します。これは、検索結果のドキュメントを `Hotel` 型のオブジェクトに逆シリアル化するように SDK に指示します。

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
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);
```

この例では、"budget" という単語と一致するホテルを検索します。`Select` パラメーターで指定しているように、ホテル名だけを返します。 結果は次のとおりです。

    Name: Roach Motel

次に、1 泊 150 ドル未満のホテルを検索し、ホテル ID と説明だけを返します。

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

このクエリでは、OData の `$filter` 式 (`baseRate lt 150`) を使用して、インデックス内のドキュメントをフィルター処理します。 Azure Search がサポートする OData 構文の詳細については、 [こちら](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search)を参照してください。

クエリの結果は次のとおりです。

    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river

次に、最近改装された上位 2 つのホテルを検索し、ホテル名と最終改装日を表示します。 次にコードを示します。 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

この例でも、OData 構文を使用して `OrderBy` パラメーターに `lastRenovationDate desc` を指定しています。 また、上位 2 つのドキュメントだけを取得できるように、`Top` を 2 に設定しています。 前と同様に、`Select` を設定して返す必要があるフィールドを指定します。

結果は次のとおりです。

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

最後に、"motel" という単語と一致するすべてのホテルを検索します。

```csharp
parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

結果は次のとおりです。`Select` プロパティを指定しなかったので、この結果にはすべてのフィールドが含まれています。

    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

チュートリアルはここまでですが、ここで止めないでください。 **次のステップでは、Azure Search をさらに学習するための他のリソースを提供します。

## <a name="next-steps"></a>次の手順
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) と [REST API](https://docs.microsoft.com/rest/api/searchservice/) のリファレンスを参照してください。
* [名前付け規則](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) で、さまざまなオブジェクトに名前を付けるときの規則を学習してください。
* Azure Search で [サポートされるデータ型](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) を確認してください。
