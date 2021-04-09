---
title: クイック スタート:.NET で検索インデックスを作成する
titleSuffix: Azure Cognitive Search
description: この C# クイックスタートでは、Azure.Search.Documents クライアント ライブラリを使用してインデックスの作成、データの読み込み、クエリの実行を行う方法について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f0d912d5b14932c43d109f8f955d5f16381cf773
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98180100"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>クイック スタート:Azure.Search.Documents クライアント ライブラリを使用して検索インデックスを作成する

新しい [Azure.Search.Documents (バージョン 11) クライアント ライブラリ](/dotnet/api/overview/azure/search.documents-readme)を使用して、検索インデックスの作成、読み込み、照会を行う C# の .NET Core コンソール アプリケーションを作成します。

[ソース コードをダウンロード](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11)して完成したプロジェクトから開始することも、この記事の手順に従って独自のアプリケーションを作成することもできます。

> [!NOTE]
> 以前のバージョンをお探しの場合は、 [Microsoft.Azure.Search v10 を使用して検索インデックスを作成する](search-get-started-dotnet-v10.md)方法に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

開始する前に、次のツールとサービスを用意してください。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。

+ Azure Cognitive Search サービス。 [サービスを作成](search-create-service-portal.md)するか、[既存のサービスを検索](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)します。 このクイック スタート用には、無料のサービスを使用できます。 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) (任意のエディション)。 サンプル コードは、無料の Visual Studio 2019 Community Edition でテストされています。

プロジェクトを設定するときは、[Azure.Search.Documents NuGet パッケージ](https://www.nuget.org/packages/Azure.Search.Documents/)をダウンロードします。

Azure SDK for .NET は [.NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support) に準拠しています。これは、.NET Framework 4.6.1 と .NET Core 2.0 を最小要件としていることを意味します。

## <a name="set-up-your-project"></a>プロジェクトの設定

サービス接続情報を入手してから、Visual Studio を起動し、.NET Core 上で実行する新しいコンソール アプリ プロジェクトを作成します。

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>キーとエンドポイントをコピーする

サービスの呼び出しには、要求ごとに URL エンドポイントとアクセス キーが必要です。 最初に、API キーと URL を見つけてプロジェクトに追加します。 後続の手順でクライアントを作成する際に、両方の値を指定することになります。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

2. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理者キーを取得します。オブジェクトを作成したり削除したりする場合には必須となります。 キーには、プライマリとセカンダリの 2 つがあり、どちらでも同じように機能します どちらを使ってもかまいません。

   ![HTTP エンドポイントとアクセス キーを取得する](media/search-get-started-rest/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

### <a name="install-the-nuget-package"></a>NuGet パッケージのインストール

プロジェクトが作成されたら、クライアント ライブラリを追加します。 [Azure.Search.Documents パッケージ](https://www.nuget.org/packages/Azure.Search.Documents/)は、.NET の検索サービスと連携するために使用されるすべての API を備えた 1 つのクライアント ライブラリで構成されます。

1. Visual Studio を起動し、.NET Core コンソール アプリケーションを作成します。

1. **[ツール]**  >  **[NuGet パッケージ マネージャー]** で、 **[ソリューションの NuGet パッケージの管理]** を選択します。 

1. **[参照]** をクリックします。

1. `Azure.Search.Documents` を検索し、バージョン 11.0 以降を選択します。

1. 右側にある **[インストール]** をクリックして、ご自分のプロジェクトとソリューションにアセンブリを追加します。

### <a name="create-a-search-client"></a>検索クライアントを作成する

1. **Program.cs** で名前空間を `AzureSearch.SDK.Quickstart.v11` に変更し、次の `using` ディレクティブを追加します。

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. 2 つのクライアントを作成します。[SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) はインデックスを作成するクライアントで、[SearchClient](/dotnet/api/azure.search.documents.searchclient) は既存のインデックスを読み込んで照会するクライアントです。 どちらも、作成と削除の権限に関する認証のためのサービス エンドポイントと管理 API キーが必要です。

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

        // Create a SearchIndexClient to send create/delete index commands
        Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
        AzureKeyCredential credential = new AzureKeyCredential(apiKey);
        SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);

        // Create a SearchClient to load and query documents
        SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - インデックスの作成

このクイックスタートでは、Hotels インデックスを作成します。そこにホテル データを読み込んでクエリを実行することになります。 この手順では、インデックス内のフィールドを定義します。 それぞれのフィールドの定義には、名前とデータ型、属性が存在し、それらによってフィールドの使い方が決まります。

簡潔で読みやすくするために、この例では、Azure.Search.Documents ライブラリの同期メソッドを使用しています。 ただし運用環境のシナリオでは、アプリのスケーラビリティと応答性を確保するために非同期メソッドを使用する必要があります。 たとえば、[CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) ではなく [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) を使用します。

1. 空のクラス定義を **Hotel.cs** プロジェクトに追加します。

1. **Hotel.cs** に次のコードをコピーして、ホテル ドキュメントの構造を定義します。 フィールドは、その属性によって、アプリケーション内でどのように使用できるかが決まります。 たとえばフィルター式をサポートするフィールドには、それぞれ `IsFilterable` 属性が割り当てられている必要があります。

    ```csharp
    using System;
    using System.Text.Json.Serialization;
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;

    namespace AzureSearch.Quickstart
    {
        public partial class Hotel
        {
            [SimpleField(IsKey = true, IsFilterable = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            [SearchableField]
            public Address Address { get; set; }
        }
    }
    ```

   Azure.Search.Documents クライアント ライブラリでは、[SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) と [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) を使用してフィールドの定義を効率化できます。 どちらも [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) から派生したもので、コードの簡素化に役立つ可能性があります。

   + `SimpleField` は任意のデータ型にすることができます。また、常に検索不可能 (フルテキスト検索クエリでは無視される) で、取得可能 (非表示ではない) となります。 その他の属性は、既定ではオフですが、有効にすることができます。 `SimpleField` は、フィルター、ファセット、スコアリング プロファイルでのみ使用されるフィールドやドキュメント ID での使用が考えられます。 その場合は必ず、シナリオに必要な属性を適用してください (ドキュメント ID の `IsKey = true` など)。 詳細については、ソース コードの [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) を参照してください。

   + `SearchableField` は文字列であることが必要です。常に検索可能で、取得可能となります。 その他の属性は、既定ではオフですが、有効にすることができます。 検索可能なタイプのフィールドであるため、同意語がサポートされるほか、アナライザーのプロパティがすべてサポートされます。 詳細については、ソース コードの [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) を参照してください。

   基本 `SearchField` API を使用する場合も、そのいずれかのヘルパー モデルを使用する場合も、フィルター、ファセット、並べ替えの属性は明示的に有効にする必要があります。 たとえば、[IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable)、[IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)、[IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) の各属性は、上のサンプルのように明示的に指定する必要があります。 

1. 2 つ目の空のクラス定義をプロジェクトに追加します (**Address.cs**)。  このクラスに次のコードをコピーします。

   ```csharp
   using Azure.Search.Documents.Indexes;

    namespace AzureSearch.Quickstart
    {
        public partial class Address
        {
            [SearchableField(IsFilterable = true)]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
   ```

1. さらに 2 つのクラスを作成します。ToString() のオーバーライドの **Hotel.Methods.cs** と **Address.Methods.cs** です。 これらのクラスは、コンソール出力に検索結果をレンダリングするために使用されます。  これらのクラスの内容はこの記事では提供されていませんが、[GitHub のファイル](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11/AzureSearchQuickstart-v11)からコードをコピーできます。

1. **Program.cs** に [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) オブジェクトを作成し、[CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) メソッドを呼び出して、検索サービスのインデックスを表現します。 インデックスには、指定されたフィールドでオートコンプリートを有効にするための [SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester) も含まれています。

   ```csharp
    // Create hotels-quickstart index
    private static void CreateIndex(string indexName, SearchIndexClient adminClient)
    {
        FieldBuilder fieldBuilder = new FieldBuilder();
        var searchFields = fieldBuilder.Build(typeof(Hotel));

        var definition = new SearchIndex(indexName, searchFields);

        var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
        definition.Suggesters.Add(suggester);

        adminClient.CreateOrUpdateIndex(definition);
    }
   ```

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - ドキュメントを読み込む

Azure Cognitive Search は、サービスに格納されたコンテンツを対象に検索を実行します。 この手順では、先ほど作成したホテル インデックスに準拠した JSON ドキュメントを読み込みます。

Azure Cognitive Search における検索ドキュメントは、インデックス作成への入力とクエリからの出力を兼ねたデータ構造です。 外部データ ソースから取得するドキュメント入力には、データベース内の行、Blob storage 内の BLOB、ディスク上の JSON ドキュメントがあります。 この例では、手短な方法として、4 つのホテルの JSON ドキュメントをコード自体に埋め込みます。 

ドキュメントをアップロードするときは、[IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) オブジェクトを使用する必要があります。 `IndexDocumentsBatch` オブジェクトには [Actions](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions) のコレクションが含まれています。各オブジェクトには、ドキュメント 1 つと、実行するアクション ([upload、merge、delete、mergeOrUpload](search-what-is-data-import.md#indexing-actions)) を Azure Cognitive Search に指示するプロパティが 1 つ含まれています。

1. **Program.cs** で、ドキュメントとインデックス アクションの配列を作成し、その配列を `IndexDocumentsBatch` に渡します。 以下のドキュメントは、Hotel クラスで定義されている hotels-quickstart インデックスに準拠しています。

    ```csharp
    // Upload documents in a single Upload request.
    private static void UploadDocuments(SearchClient searchClient)
    {
        IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.60,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                })
            );

        try
        {
            IndexDocumentsResult result = searchClient.IndexDocuments(batch);
        }
        catch (Exception)
        {
            // If for some reason any documents are dropped during indexing, you can compensate by delaying and
            // retrying. This simple demo just logs the failed document keys and continues.
            Console.WriteLine("Failed to index some of the documents: {0}");
        }
    }
    ```

    [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) オブジェクトを初期化したら、[SearchClient](/dotnet/api/azure.search.documents.searchclient) オブジェクトの [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) を呼び出すことによって、それをインデックスに送信することができます。

1. Main() に次の行を追加します。 ドキュメントの読み込みは SearchClient を使用して実行されますが、この操作にはサービスに対する管理者権限も必要であり、通常、SearchIndexClient に関連付けられています。 この操作を設定する 1 つの方法として、SearchIndexClient (この例では adminClient) を介して SearchClient を取得します。

   ```csharp
    SearchClient ingesterClient = adminClient.GetSearchClient(indexName);

    // Load documents
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(ingesterClient);
   ```

1. これはすべてのコマンドを連続して実行するコンソール アプリであるため、インデックス作成とクエリの間に 2 秒の待ち時間を追加します。

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    2 秒の遅延により、非同期のインデックス作成を待ち、クエリの実行前にすべてのドキュメントのインデックスを作成できるようにしています。 通常、遅延のコーディングは、デモ、テスト、およびサンプル アプリケーションでのみ必要です。

## <a name="3---search-an-index"></a>3 - インデックスの検索

最初のドキュメントのインデックスが作成されるとすぐにクエリの結果を取得できますが、インデックスの実際のテストではすべてのドキュメントのインデックスが作成されるまで待つ必要があります。

このセクションでは、クエリ ロジックと結果の 2 つの機能を追加します。 クエリには、[Search](/dotnet/api/azure.search.documents.searchclient.search) メソッドを使用します。 このメソッドでは、検索テキスト (クエリ文字列) のほか、[オプション](/dotnet/api/azure.search.documents.searchoptions)を使用できます。

その結果は、[SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) クラスによって表されます。

1. **Program.cs** で、検索結果をコンソールに出力する **WriteDocuments** メソッドを作成します。

    ```csharp
    // Write search results to console
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.GetResults())
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. クエリを実行し、結果を返す **RunQueries** メソッドを作成します。 結果は、Hotel オブジェクトです。 このサンプルは、メソッド シグネチャと最初のクエリを示しています。 このクエリは、ドキュメントから選択されたフィールドを使用して結果を作成できる Select パラメーターを示しています。

    ```csharp
    // Run queries, use WriteDocuments to print output
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on empty term '*' to return all documents, showing a subset of fields...\n");

        options = new SearchOptions()
        {
            IncludeTotalCount = true,
            Filter = "",
            OrderBy = { "" }
        };

        options.Select.Add("HotelId");
        options.Select.Add("HotelName");
        options.Select.Add("Address/City");

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    ```

1. 2 つ目のクエリでは、語句を検索し、Rating が 4 を超えるドキュメントを選択するフィルターを追加したうえで、Rating の降順で並べ替えます。 フィルターは、インデックス内の [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) フィールドに対して評価されるブール式です。 フィルター クエリでは、値は包含されるか除外されるかのどちらかです。 そのため、フィルター クエリに関しては関連性スコアはありません。 

    ```csharp
    Console.WriteLine("Query #2: Search on 'hotels', filter on 'Rating gt 4', sort by Rating in descending order...\n");

    options = new SearchOptions()
    {
        Filter = "Rating gt 4",
        OrderBy = { "Rating desc" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Rating");

    response = srchclient.Search<Hotel>("hotels", options);
    WriteDocuments(response);
    ```

1. 3 番目のクエリは、フルテキスト検索操作の範囲を特定のフィールドに設定するために使用する searchFields を示しています。

    ```csharp
    Console.WriteLine("Query #3: Limit search to specific fields (pool in Tags field)...\n");

    options = new SearchOptions()
    {
        SearchFields = { "Tags" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Tags");

    response = srchclient.Search<Hotel>("pool", options);
    WriteDocuments(response);
    ```

1. 4 番目のクエリは、ファセット ナビゲーション構造を構築するために使用できるファセットを示しています。 

   ```csharp
    Console.WriteLine("Query #4: Facet on 'Category'...\n");

    options = new SearchOptions()
    {
        Filter = ""
    };

    options.Facets.Add("Category");

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Category");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
   ```

1. 5 番目のクエリでは、特定のドキュメントを返します。 ドキュメント検索は、結果セット内の OnClick イベントに対する一般的な応答です。

   ```csharp
    Console.WriteLine("Query #5: Look up a specific document...\n");

    Response<Hotel> lookupResponse;
    lookupResponse = srchclient.GetDocument<Hotel>("3");

    Console.WriteLine(lookupResponse.Value.HotelId);
   ```

1. 最後のクエリは、オートコンプリートの構文を示しています。これは、インデックスで定義した suggester に関連付けられている sourceFields の 2 つの一致候補に解決される、"sa" という部分的なユーザー入力をシミュレートしています。

   ```csharp
    Console.WriteLine("Query #6: Call Autocomplete on HotelName that starts with 'sa'...\n");

    var autoresponse = srchclient.Autocomplete("sa", "sg");
    WriteDocuments(autoresponse);
   ```

1. Main() に **RunQueries** を追加します。

    ```csharp
    // Call the RunQueries method to invoke a series of queries
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);

    // End the program
    Console.WriteLine("{0}", "Complete. Press any key to end this program...\n");
    Console.ReadKey();
    ```

上記のクエリは、[クエリで語句を照合する複数の方法](search-query-overview.md#types-of-queries) (フルテキスト検索、フィルター、オートコンプリート) を示しています。

フルテキスト検索とフィルターは、[SearchClient.Search](/dotnet/api/azure.search.documents.searchclient.search) メソッドを使用して実行されます。 検索クエリは `searchText` 文字列で渡すことができます。一方、フィルター式は [SearchOptions](/dotnet/api/azure.search.documents.searchoptions) クラスの [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) プロパティで渡すことができます。 検索せずにフィルター処理を実行するには、[Search](/dotnet/api/azure.search.documents.searchclient.search) メソッドの `searchText` パラメーターに `"*"` を渡します。 フィルター処理を行わずに検索するには、`Filter` プロパティを未設定のままにするか、`SearchOptions` インスタンスを 1 つも渡さないようにします。

## <a name="run-the-program"></a>プログラムを実行する

F5 キーを押して、アプリをリビルドし、プログラム全体を実行します。 

出力には、[Console.WriteLine](/dotnet/api/system.console.writeline) からのメッセージに加え、クエリの情報と結果が表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。 

## <a name="next-steps"></a>次のステップ

この C# クイックスタートでは、インデックスの作成、ドキュメントを利用した読み込み、およびクエリの実行を行う一連のタスクに取り組みました。 さまざまな段階で、読みやすく、理解しやすいように、手短な方法として、コードを簡略化しました。 基本的な概念に慣れている場合、次の記事で別の方法や概念を確認してご自分の知識を深めることをお勧めします。 

> [!div class="nextstepaction"]
> [.NET で開発する方法](search-howto-dotnet-sdk.md)

クラウドの支出を最適化して節約しますか?

> [!div class="nextstepaction"]
> [Cost Management を使用してコスト分析を開始する](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
