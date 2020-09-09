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
ms.date: 08/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 7901254463ef052f3c13b2c9fc49c31bd8ebc454
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020866"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>クイック スタート:Azure.Search.Documents クライアント ライブラリを使用して検索インデックスを作成する

新しい [Azure.Search.Documents (バージョン 11) クライアント ライブラリ](/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)を使用して、検索インデックスの作成、読み込み、照会を行う C# の .NET Core コンソール アプリケーションを作成します。

[ソース コードをダウンロード](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart-v11)して完成済みのプロジェクトで開始するか、この記事の手順に従いながらご自身でアプリケーションを作成してみましょう。

> [!NOTE]
> 以前のバージョンをお探しの場合は、 [Microsoft.Azure.Search v10 を使用して検索インデックスを作成する](search-get-started-dotnet-v10.md)方法に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

開始する前に、次のツールとサービスを用意してください。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。

+ Azure Cognitive Search サービス。 [サービスを作成](search-create-service-portal.md)するか、[既存のサービスを検索](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)します。 このクイック スタート用には、無料のサービスを使用できます。 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) (任意のエディション)。 サンプル コードは、無料の Visual Studio 2019 Community Edition でテストされています。

<a name="get-service-info"></a>

## <a name="get-a-key-and-endpoint"></a>キーとエンドポイントを取得する

サービスの呼び出しには、要求ごとに URL エンドポイントとアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Cognitive Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

2. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理者キーを取得します。オブジェクトを作成したり削除したりする場合には必須となります。 キーには、プライマリとセカンダリの 2 つがあり、どちらでも同じように機能します どちらを使ってもかまいません。

   ![HTTP エンドポイントとアクセス キーを取得する](media/search-get-started-postman/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

## <a name="set-up-your-project"></a>プロジェクトの設定

Visual Studio を起動し、.NET Core 上で実行する新しいコンソール アプリ プロジェクトを作成します。 

### <a name="install-the-nuget-package"></a>NuGet パッケージのインストール

プロジェクトが作成されたら、クライアント ライブラリを追加します。 [Azure.Search.Documents パッケージ](https://www.nuget.org/packages/Azure.Search.Documents/)は、.NET の検索サービスと連携するために使用されるすべての API を備えた 1 つのクライアント ライブラリで構成されます。

1. **[ツール]**  >  **[NuGet パッケージ マネージャー]** で、 **[ソリューションの NuGet パッケージの管理]** を選択します。 

1. **[参照]** をクリックします。

1. `Azure.Search.Documents` を検索し、バージョン 11.0.0 を選択します。

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

1. 2 つのクライアントを作成します。[SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) はインデックスを作成するクライアントで、[SearchClient](/dotnet/api/azure.search.documents.searchclient) は既存のインデックスを扱うクライアントです。 どちらも、作成と削除の権限に関する認証のためのサービス エンドポイントと管理 API キーが必要です。

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart-v11";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

       // Create a SearchIndexClient to send create/delete index commands
       Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
       AzureKeyCredential credential = new AzureKeyCredential(apiKey);
       SearchIndexClient idxclient = new SearchIndexClient(serviceEndpoint, credential);

       // Create a SearchClient to load and query documents
       SearchClient qryclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - インデックスの作成

このクイックスタートでは、Hotels インデックスを作成します。そこにホテル データを読み込んでクエリを実行することになります。 この手順では、インデックス内のフィールドを定義します。 それぞれのフィールドの定義には、名前とデータ型、属性が存在し、それらによってフィールドの使い方が決まります。

簡潔で読みやすくするために、この例では、Azure.Search.Documents ライブラリの同期メソッドを使用しています。 ただし運用環境のシナリオでは、アプリのスケーラビリティと応答性を確保するために非同期メソッドを使用する必要があります。 たとえば、[CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) ではなく [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) を使用します。

1. 空のクラス定義を **Hotel.cs** プロジェクトに追加します。

1. **Hotel.cs** で、ホテル ドキュメントの構造を定義します。

    ```csharp
    using System;
    using System.Text.Json.Serialization;

    namespace AzureSearch.SDK.Quickstart.v11
    {
        public class Hotel
        {
            [JsonPropertyName("hotelId")]
            public string Id { get; set; }

            [JsonPropertyName("hotelName")]
            public string Name { get; set; }

            [JsonPropertyName("hotelCategory")]
            public string Category { get; set; }

            [JsonPropertyName("baseRate")]
            public Int32 Rate { get; set; }

            [JsonPropertyName("lastRenovationDate")]
            public DateTime Updated { get; set; }
        }
    }
    ```

1. **Program.cs** で、フィールドと属性を指定します。 [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) と [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) は、インデックスの作成に使用されます。

   ```csharp
    // Define an index schema using SearchIndex
    // Create the index using SearchIndexClient
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };

    Console.WriteLine("{0}", "Creating index...\n");
    idxclient.CreateIndex(index);
   ```

フィールドは、その属性によって、アプリケーション内でどのように使用できるかが決まります。 たとえばフィルター式をサポートするフィールドには、それぞれ `IsFilterable` 属性が割り当てられている必要があります。

以前のバージョンの .NET SDK では、検索可能な文字列フィールドに [IsSearchable](/dotnet/api/microsoft.azure.search.models.field.issearchable) を指定する必要がありましたが、[SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) と [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) を使用できるので、フィールドを効率よく定義することができます。

その他の属性は、以前のバージョンと同様、定義自体に必要となります。 たとえば、[IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable)、[IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)、[IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) の各属性は、上のサンプルのように明示的に指定する必要があります。 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - ドキュメントを読み込む

Azure Cognitive Search は、サービスに格納されたコンテンツを対象に検索を実行します。 この手順では、先ほど作成したホテル インデックスに準拠した JSON ドキュメントを読み込みます。

Azure Cognitive Search では、ドキュメントにはインデックス作成の入力とクエリからの出力があり、どちらもデータ構造です。 外部データ ソースから取得するドキュメント入力には、データベース内の行、Blob storage 内の BLOB、ディスク上の JSON ドキュメントがあります。 この例では、手短な方法として、5 つのホテルの JSON ドキュメントをコード自体に埋め込みます。 

ドキュメントをアップロードするときは、[IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) オブジェクトを使用する必要があります。 IndexDocumentsBatch には [Actions](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions) のコレクションが含まれていて、そのそれぞれには、ドキュメントが 1 つと、実行するアクション ([upload、merge、delete、mergeOrUpload](search-what-is-data-import.md#indexing-actions)) を Azure Cognitive Search に指示するプロパティが 1 つ含まれています。

1. **Program.cs** で、ドキュメントとインデックスの操作の配列を作成し、その配列を `ndexDocumentsBatch` に渡します。以下のドキュメントは、hotel クラスによって定義された hotels-quickstart-v11 インデックスに準拠しています。

    ```csharp
    // Load documents (using a subset of fields for brevity)
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(new Hotel { Id = "78", Name = "Upload Inn", Category = "hotel", Rate = 279, Updated = new DateTime(2018, 3, 1, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "54", Name = "Breakpoint by the Sea", Category = "motel", Rate = 162, Updated = new DateTime(2015, 9, 12, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "39", Name = "Debug Motel", Category = "motel", Rate = 159, Updated = new DateTime(2016, 11, 11, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "48", Name = "NuGet Hotel", Category = "hotel", Rate = 238, Updated = new DateTime(2016, 5, 30, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "12", Name = "Renovated Ranch", Category = "motel", Rate = 149, Updated = new DateTime(2020, 1, 24, 7, 0, 0) }));

    IndexDocumentsOptions idxoptions = new IndexDocumentsOptions { ThrowOnAnyError = true };

    Console.WriteLine("{0}", "Loading index...\n");
    qryclient.IndexDocuments(batch, idxoptions);
    ```

    [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) オブジェクトを初期化したら、[SearchClient](/dotnet/api/azure.search.documents.searchclient) オブジェクトの [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) を呼び出すことによって、それをインデックスに送信することができます。

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

1. **Program.cs** で、検索結果をコンソールに出力する WriteDocuments メソッドを作成します。

    ```csharp
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> response in searchResults.GetResults())
        {
            Hotel doc = response.Document;
            var score = response.Score;
            Console.WriteLine($"Name: {doc.Name}, Type: {doc.Category}, Rate: {doc.Rate}, Last-update: {doc.Updated}, Score: {score}");
        }

        Console.WriteLine();
    }
    ```

1. クエリを実行し、結果を返す RunQueries メソッドを作成します。 結果は、Hotel オブジェクトです。

    ```csharp
    private static void RunQueries(SearchClient qryclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on the term 'motel' and list the relevance score for each match...\n");

        options = new SearchOptions()
        {
            Filter = "",
            OrderBy = { "" }
        };

        response = qryclient.Search<Hotel>("motel", options);
        WriteDocuments(response);

        Console.WriteLine("Query #2: Find hotels where 'type' equals hotel...\n");

        options = new SearchOptions()
        {
            Filter = "hotelCategory eq 'hotel'",
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);

        Console.WriteLine("Query #3: Filter on rates less than $200 and sort by when the hotel was last updated...\n");

        options = new SearchOptions()
        {
            Filter = "baseRate lt 200",
            OrderBy = { "lastRenovationDate desc" }
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    }
    ```

この例では、[クエリにおける語句の照合方法](search-query-overview.md#types-of-queries)として、フルテキスト検索とフィルターの 2 とおりの方法を示しています。

+ フルテキスト検索では、インデックス内の検索可能フィールドに対して 1 つまたは複数の語句を照会します。 1 つ目のクエリはフルテキスト検索です。 フルテキスト検索では、結果の順位付けに使用される関連性スコアが生成されます。

+ フィルターは、インデックス内の [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) フィールドに対して評価されるブール式です。 フィルター クエリでは、値は包含されるか除外されるかのどちらかです。 そのため、フィルター クエリに関しては関連性スコアはありません。 最後の 2 つのクエリは、フィルター検索を示しています。

フルテキスト検索とフィルターは、単独でまたは組み合わせて使用できます。

検索とフィルターは、どちらも [SearchClient.Search](/dotnet/api/azure.search.documents.searchclient.search) メソッドを使用して実行できます。 検索クエリは `searchText` 文字列で渡すことができます。一方、フィルター式は [SearchOptions](/dotnet/api/azure.search.documents.searchoptions) クラスの [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) プロパティで渡すことができます。 検索せずにフィルター処理を実行するには、[Search](/dotnet/api/azure.search.documents.searchclient.search) メソッドの `searchText` パラメーターに `"*"` を渡します。 フィルター処理を行わずに検索するには、`Filter` プロパティを未設定のままにするか、`SearchOptions` インスタンスを 1 つも渡さないようにします。

## <a name="run-the-program"></a>プログラムを実行する

F5 キーを押して、アプリをリビルドし、プログラム全体を実行します。 

出力には、[Console.WriteLIne](/dotnet/api/system.console.writeline) からのメッセージに加え、クエリの情報と結果が表示されます。

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