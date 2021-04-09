---
title: .NET で Azure.Search.Documents (v11) を使用する
titleSuffix: Azure Cognitive Search
description: C# および Azure.Search.Documents (v11) クライアント ライブラリを使用して、.NET アプリケーションの検索オブジェクトを作成および管理する方法について説明します。 コード スニペットには、サービスへの接続、インデックスの作成、およびクエリの例を示します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 15a878eb863b71a4519e75def2598f013152dfb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97881635"
---
# <a name="how-to-use-azuresearchdocuments-in-a-c-net-application"></a>C# .NET アプリケーションで Azure.Search.Documents を使用する方法

この記事では、C# および [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) (バージョン 11) クライアント ライブラリを使用して検索オブジェクトを作成および管理する方法について説明します。

## <a name="about-version-11"></a>バージョン 11 について

Azure SDK for .NET には、Azure SDK チームからの新しい [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) クライアント ライブラリが追加されています。これは [Microsoft.Azure.Search](/dotnet/api/overview/azure/search/client10) クライアント ライブラリと機能的に同等ですが、必要に応じて一般的な方法と規約が利用されます。 例としては、[`AzureKeyCredential`](/dotnet/api/azure.azurekeycredential) キー認証と、JSON シリアル化のための [System.Text.Json.Serialization](/dotnet/api/system.text.json.serialization) があります。

以前のバージョンと同様に、このライブラリを使用して次のことができます。

+ 検索インデックス、データ ソース、インデクサー、スキルセット、シノニム マップの作成と管理
+ インデックスでの検索ドキュメントの読み込みと管理
+ HTTP や JSON の詳細を処理する必要がない、クエリの実行

このライブラリは、単一の [Azure.Search.Document NuGet パッケージ](https://www.nuget.org/packages/Azure.Search.Documents/)として配布されます。これには、検索サービスへのプログラムによるアクセスに使用するすべての API が含まれます。

クライアント ライブラリでは、`SearchIndex`、`SearchField`、`SearchDocument` などのクラス、および `SearchIndexClient` や `SearchClient` クラス上の `SearchIndexClient.CreateIndex` や `SearchClient.Search` などの操作が定義されています。 これらのクラスは、次の名前空間にまとめられています。

+ [`Azure.Search.Documents`](/dotnet/api/azure.search.documents)
+ [`Azure.Search.Documents.Indexes`](/dotnet/api/azure.search.documents.indexes)
+ [`Azure.Search.Documents.Indexes.Models`](/dotnet/api/azure.search.documents.indexes.models)
+ [`Azure.Search.Documents.Models`](/dotnet/api/azure.search.documents.models)

Azure.Search.Documents (バージョン 11) は、[Azure Cognitive Search REST API のバージョン `2020-06-30`](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Azure.Search/preview/2020-06-30) を対象としています。 

このクライアント ライブラリには、検索サービスの作成とスケーリングや API キーの管理などの[サービス管理操作](/rest/api/searchmanagement/)は用意されていません。 .NET アプリケーションから検索リソースを管理する必要がある場合は、Azure SDK for .NET の [Microsoft.Azure.Management.Search](/dotnet/api/overview/azure/search/management) ライブラリを使用します。

## <a name="upgrade-to-v11"></a>v11 へのアップグレード

以前のバージョンの .NET SDK を使用していて、現在一般公開されているバージョンにアップグレードする場合は、「[Azure Cognitive Search .NET SDK バージョン 11 へのアップグレード](search-dotnet-sdk-migration-version-11.md)」を参照してください。

## <a name="sdk-requirements"></a>SDK の要件

+ Visual Studio 2019 以降。

+ 自分が所有する Azure Cognitive Search サービス。 SDK を使用するには、サービスの名前および 1 つまたは複数の API キーが必要です。 [ポータルでサービスを作成していない場合は、作成](search-create-service-portal.md)します。

+ Visual Studio の **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[ソリューションの NuGet パッケージの管理]** を使用して、[Azure.Search.Documents package](https://www.nuget.org/packages/Azure.Search.Documents) をダウンロードします。 パッケージ名 `Azure.Search.Documents` を検索します。

Azure SDK for .NET は [.NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support) に準拠しています。これは、.NET Framework 4.6.1 と .NET Core 2.0 を最小要件としていることを意味します。

## <a name="example-application"></a>サンプル アプリケーション

"例を使って説明する" この記事では、GitHub の [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) コード例を使用して、Azure Cognitive Search の基本的な概念を説明します。具体的には、検索インデックスの作成、読み込み、クエリを実行する方法です。

この記事の残りの部分では、"hotels" という名前の新しいインデックスがあり、いくつかのドキュメントが表示され、結果に一致するいくつかのクエリが作成されているものとします。

全体的な流れがわかるメイン プログラムを次に示します。

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchIndexClient indexClient = CreateSearchIndexClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, indexClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, indexClient);

    SearchClient searchClient = indexClient.GetSearchClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(searchClient);

    SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

    Console.WriteLine("{0}", "Run queries...\n");
    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

有効なサービス名と API キーを使用してこのアプリケーションを実行したと想定すると、出力は次の部分的なスクリーンショットのようになります。

:::image type="content" source="media/search-howto-dotnet-sdk/console-output.png" alt-text="サンプル プログラムからの Console.WriteLine 出力":::

### <a name="client-types"></a>クライアントの種類

クライアント ライブラリの場合、さまざまな操作に 3 種類のクライアントを使用します。[`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) はインデックスの作成、更新、または削除、[`SearchClient`](/dotnet/api/azure.search.documents.searchclient) はインデックスの読み込みまたはクエリ実行、[`SearchIndexerClient`](/dotnet/api/azure.search.documents.indexes.searchindexerclient) はインデクサーとスキルセットの操作に使用します。 この記事では、最初の 2 つを中心に説明します。 

すべてのクライアントには、少なくとも、サービス名またはエンドポイントと API キーが必要です。 この情報は、構成ファイル内に指定するのが一般的です。これは、[DotNetHowTo サンプル アプリケーション](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)の `appsettings.json` ファイル内にあるものと同様です。 構成ファイルから読み取るには、プログラムに `using Microsoft.Extensions.Configuration;` を追加します。

インデックスの作成、更新、または削除に使用するインデックス クライアントを次のステートメントで作成します。 これによって検索エンドポイントと管理 API キーが取得されます。

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceEndPoint), new AzureKeyCredential(adminApiKey));
    return indexClient;
}
```

次のステートメントで、ドキュメントの読み込みまたはクエリの実行に使用する検索クライアントを作成します。 `SearchClient` にはインデックスが必要です。 ドキュメントを読み込むには管理者 API キーが必要ですが、クエリの実行はクエリ API キーを使用してできます。 

```csharp
string indexName = configuration["SearchIndexName"];

private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

> [!NOTE]
> インポート操作に対して無効なキー (たとえば、管理者キーが必要なときにクエリ キーなど) を提供すると、操作メソッドを初めて呼び出したときに、`SearchClient` によって `CloudException` がスローされ、エラー メッセージ "アクセス不可" が表示されます。 このような場合は、API キーを再確認してください。
>

### <a name="deleting-the-index"></a>インデックスを削除する

開発の初期段階では、更新された定義を使用して作成し直すことができるように、進行中のインデックスを削除するために [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) ステートメントを含めることをお勧めします。 Azure Cognitive Search のサンプル コードには、多くの場合、サンプルを再実行できるように削除手順が含まれています。

次の行によって `DeleteIndexIfExists` が呼び出されます。

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, indexClient);
```

このメソッドは、指定した `SearchIndexClient` を使用してインデックスが存在するかどうかを確認し、存在する場合は、それを削除します。

```csharp
private static void DeleteIndexIfExists(string indexName, SearchIndexClient indexClient)
{
    try
    {
        if (indexClient.GetIndex(indexName) != null)
        {
            indexClient.DeleteIndex(indexName);
        }
    }
    catch (RequestFailedException e) when (e.Status == 404)
    {
        // Throw an exception if the index name isn't found
        Console.WriteLine("The index doesn't exist. No deletion occurred.");
```

> [!NOTE]
> この記事のコード例では、わかりやすくするために同期メソッドを使用していますが、実際のアプリケーションでは、拡張性と応答性を維持するために非同期メソッドを使用してください。 たとえば、上記のメソッドでは、[`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) の代わりに、[`DeleteIndexAsync`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindexasync) を使用できます。
>

## <a name="create-an-index"></a>インデックスを作成する

[`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) を使用してインデックスを作成できます。 

次のメソッドは、新しいインデックスのスキーマを定義する [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) オブジェクトのリストを使用して新しい [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) オブジェクトを作成します。 各フィールドには、名前、データ型、および検索動作を定義するいくつかの属性があります。 

フィールドは、[`FieldBuilder`](/dotnet/api/azure.search.documents.indexes.fieldbuilder) を使用してモデル クラスから定義できます。 `FieldBuilder` クラスでは、リフレクションを使用して指定された `Hotel` モデル クラスのパブリック プロパティと属性を調べることで、インデックスの `SearchField` オブジェクトのリストを作成します。 `Hotel` クラスについては、後ほど詳しく説明します。

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    indexClient.CreateOrUpdateIndex(definition);
}
```

フィールド以外に、スコアリング プロファイル、サジェスター、または CORS オプションもインデックスに追加できます (簡潔にするために、これらのパラメーターはサンプルから省略されています)。 SearchIndex オブジェクトとその構成要素の詳細については、[`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) プロパティ リストと、[REST API リファレンス](/rest/api/searchservice/)を参照してください。

> [!NOTE]
> 必要に応じて、`FieldBuilder` を使用するのではなく、`Field` オブジェクトのリストをいつでも直接作成できます。 たとえば、モデル クラスを使用しない場合や、属性を追加して変更するのは望ましくない既存のモデル クラスを使用する必要がある場合などです。
>

### <a name="call-createindex-in-main"></a>Main() で CreateIndex を呼び出す

上記のメソッドを呼び出すことによって、`Main` は新しい "hotels" インデックスを作成します。

```csharp
Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, indexClient);
```

## <a name="use-a-model-class-for-data-representation"></a>データ表現にモデル クラスを使用する

DotNetHowTo サンプルでは、[Hotel](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Hotel.cs)、[Address](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Address.cs)、[Room](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Room.cs) の各データ構造にモデル クラスを使用しています。 `Hotel` は単一レベルの複合型 (マルチパート フィールド) である `Address` と、`Room` (マルチパート フィールドのコレクション) を参照します。

これらの型を使用して、インデックスの作成と読み込み、およびクエリからの応答の構造化を行うことができます。

```csharp
// Use-case: <Hotel> in a field definition
FieldBuilder fieldBuilder = new FieldBuilder();
var searchFields = fieldBuilder.Build(typeof(Hotel));

// Use-case: <Hotel> in a response
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

別の方法として、インデックスにフィールドを直接追加する方法もあります。 次の例では、いくつかのフィールドのみを示します。

   ```csharp
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
   ```

### <a name="field-definitions"></a>フィールド定義

.NET のデータ モデルおよびその対応するインデックス スキーマは、エンド ユーザーに提供する検索エクスペリエンスをサポートしている必要があります。 .NET の最上位レベル オブジェクト (検索インデックス内の検索ドキュメントなど) はそれぞれ、ユーザー インターフェイスで示される検索結果に対応します。 たとえば、ホテル検索アプリケーションで、エンド ユーザーは、ホテル名、ホテルの機能、または特定の部屋の特徴で検索することができます。 

フィールドは、使用方法を決定するデータ型と属性によって各クラス内に定義されます。 各クラス内の各パブリック プロパティの名前は、インデックス定義内の同じ名前を持つフィールドにマップされます。 

Hotel クラスから複数のフィールド定義を取得する次のスニペットを見てみましょう。 Address と Rooms は独自のクラス定義を持つ C# 型であることに注意してください (確認するには、サンプル コードを参照してください)。 両方とも複合型です。 詳細については、[複合型のモデル化の方法に関するページ](search-howto-complex-data-types.md)を参照してください。

```csharp
public partial class Hotel
{
    [SimpleField(IsKey = true, IsFilterable = true)]
    public string HotelId { get; set; }

    [SearchableField(IsSortable = true)]
    public string HotelName { get; set; }

    [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
    public string Description { get; set; }

    [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
    public string Category { get; set; }

    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [SearchableField]
    public Address Address { get; set; }

    public Room[] Rooms { get; set; }
```

#### <a name="choosing-a-field-class"></a>フィールド クラスを選択する

フィールドを定義するときは、[`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) 基底クラスを使用することも、プロパティが事前に構成された "テンプレート" として機能する派生ヘルパー モデルを使用することもできます。

インデックス内の厳密に 1 つのフィールドが、ドキュメント キー (`IsKey = true`) として機能する必要があります。 これは文字列でなければならず、各ドキュメントを一意に識別する必要があります。 また、`IsHidden = true` を使用している必要があります。これは、検索結果に表示できないことを意味します。

| フィールドの種類 | 説明と使用方法 |
|------------|-----------------------|
| [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) | ほとんどのプロパティが null に設定された基底クラス (必須の `Name` と、既定で標準の Lucene になる `AnalyzerName` は除く)。 |
| [`SimpleField`](/dotnet/api/azure.search.documents.indexes.models.simplefield) | ヘルパー モデル。 任意のデータ型にすることができます。また、常に検索不可能 (フルテキスト検索クエリでは無視される) で、取得可能 (非表示ではない) となります。 その他の属性は、既定ではオフですが、有効にすることができます。 `SimpleField` は、フィルター、ファセット、スコアリング プロファイルでのみ使用されるフィールドやドキュメント ID での使用が考えられます。 その場合は必ず、シナリオに必要な属性を適用してください (ドキュメント ID の `IsKey = true` など)。 詳細については、ソース コードの [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) を参照してください。 |
| [`SearchableField`](/dotnet/api/azure.search.documents.indexes.models.searchablefield) | ヘルパー モデル。 文字列であることが必要です。常に検索可能で、取得可能となります。 その他の属性は、既定ではオフですが、有効にすることができます。 検索可能なタイプのフィールドであるため、同意語がサポートされるほか、アナライザーのプロパティがすべてサポートされます。 詳細については、ソース コードの [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) を参照してください。 |

基本 `SearchField` API を使用する場合も、そのいずれかのヘルパー モデルを使用する場合も、フィルター、ファセット、並べ替えの属性は明示的に有効にする必要があります。 たとえば、[IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable)、[IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)、[IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) の各属性は、上のサンプルのように明示的に指定する必要があります。

#### <a name="adding-field-attributes"></a>フィールド属性を追加する

各フィールドが `IsFilterable`、`IsSortable`、`IsKey`、`AnalyzerName` などの属性で装飾されていることに注意してください。 これらの属性は、[Azure Cognitive Search インデックス内の対応するフィールド属性](/rest/api/searchservice/create-index)に直接マップされます。 `FieldBuilder` クラスは、これらのプロパティを使用してインデックスのフィールド定義を構築します。

#### <a name="field-type-mapping"></a>フィールド型のマッピング

プロパティの .NET 型は、インデックス定義でそれらと同等のフィールド型にマップされます。 たとえば、`Category` 文字列プロパティは、`Edm.String` 型の `category` フィールドにマップします。 `bool?`、`Edm.Boolean`、`DateTimeOffset?`、`Edm.DateTimeOffset` などの間にも、同じような型のマッピングがあります。 

`SmokingAllowed` プロパティを見つけてしまいましたか?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

このプロパティの `JsonIgnore` 属性によって、フィールドとしてインデックスにシリアル化しないよう、`FieldBuilder` に指示が与えられます。  これは、ご利用のアプリケーションでヘルパーとして使用できる、クライアント側の計算されたプロパティを作成する優れた方法です。  この場合、`SmokingAllowed` プロパティは、`Rooms` コレクション内に喫煙可能な `Room` があるかどうかを反映します。 すべて false の場合、ホテル全館で喫煙できないことを示します。

## <a name="load-an-index"></a>インデックスを読み込む

`Main` の次の手順で、新しく作成した "hotels" インデックスを設定します。 このインデックス設定は、次のメソッドで実行されます。(説明のため "..." で置き換えられた一部のコード。 完全なデータ生成コードに対する完全なサンプル ソリューションを参照してください。)

```csharp
private static void UploadDocuments(SearchClient searchClient)
{
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
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
            }),
        IndexDocumentsAction.Upload(
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
            }),
        IndexDocumentsAction.Upload(
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

    try
    {
        IndexDocumentsResult result = searchClient.IndexDocuments(batch);
    }
    catch (Exception)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine("Failed to index some of the documents: {0}");
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
```

このメソッドには 4 つの部分があります。 最初の部分では、インデックスにアップロードする入力データとして使用される、3 つの `Hotel` オブジェクトと、それぞれに含まれる 3 つの `Room` オブジェクトの配列を作成します。 このデータは、わかりやすくするためハードコーディングされています。 実際のアプリケーションでは、データは SQL Database などの外部データ ソースから取得されます。

2 番目の部分では、ドキュメントを含む [`IndexDocumentsBatch`](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) を作成します。 この場合は [`IndexDocumentsAction.Upload`](/dotnet/api/azure.search.documents.models.indexdocumentsaction.upload) を呼び出すことによって、作成時にバッチに適用する操作を指定します。 その後、バッチは [`IndexDocuments`](/dotnet/api/azure.search.documents.searchclient.indexdocuments) メソッドによって Azure Cognitive Search インデックスにアップロードされます。

> [!NOTE]
> この例では、単にドキュメントをアップロードします。 既存のドキュメントに変更をマージする、またはドキュメントを削除する場合は、代わりに `IndexDocumentsAction.Merge`、`IndexDocumentsAction.MergeOrUpload`、または `IndexDocumentsAction.Delete` を呼び出すことによってバッチを作成できます。 `IndexBatch.New` を呼び出すことによって 1 つのバッチでさまざまな操作を組み合わせることもできます。これによって、`IndexDocumentsAction` オブジェクトのコレクションを受け取り、各オブジェクトがドキュメントで特定の操作を実行するよう Azure Cognitive Search に指示します。 `IndexDocumentsAction.Merge` や `IndexAction.Upload` などの対応するメソッドを呼び出すことによって、独自の操作を行う `IndexDocumentsAction` を作成できます。
> 

このメソッドの 3 番目の部分は、インデックス作成の重要なエラー ケースを処理する catch ブロックです。 検索サービスでバッチ内の一部のドキュメントのインデックス作成に失敗した場合、`IndexDocuments` から `IndexBatchException` がスローされます。 この例外は、サービスの負荷が高いときにドキュメントのインデックスを作成していると発生する場合があります。 **コードでこのケースを明示的に処理することを強くお勧めします。** しばらく待ってから失敗したドキュメントのインデックス作成を再試行したり、サンプルと同じようにログに記録してから続けることができます。または、アプリケーションのデータ整合性要件に応じて他の処理を行うこともできます。

最後に、`UploadDocuments` メソッドは 2 秒間遅延します。 インデックスの作成は検索サービスで非同期的に行われるので、サンプル アプリケーションは短い時間待機して、確実にドキュメントを検索に使用できるようにする必要があります。 通常、このような遅延は、デモ、テスト、およびサンプル アプリケーションでのみ必要です。

### <a name="call-uploaddocuments-in-main"></a>Main() で UploadDocuments を呼び出す

次のコード スニペットでは、indexClient の [`GetSearchClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient.getsearchclient) メソッドを使用して、[`SearchClient`](/dotnet/api/azure.search.documents.searchclient) のインスタンスを設定します。 indexClient で、ドキュメントの読み込みまたは更新に必要な管理 API キーをその要求に対して使用します。

別の方法として、`SearchClient` を直接呼び出し、`AzureKeyCredential` で管理者 API キーを渡します。

```csharp
SearchClient searchClient = indexClient.GetSearchClient(indexName);

Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(searchClient);
```

## <a name="run-queries"></a>クエリを実行する

まず、**appsettings.json** から検索エンドポイントとクエリ API キーを読み取る `SearchClient` を設定します。

```csharp
private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

次に、クエリ要求を送信するメソッドを定義します。 

メソッドでクエリが実行されるたびに、新しい [`SearchOptions`](/dotnet/api/azure.search.documents.searchoptions) オブジェクトが作成されます。 このオブジェクトは、並べ替え、フィルター処理、ページング、ファセットなどの、クエリへの追加オプションを指定するために使用されます。 このメソッドで、さまざまなクエリの `Filter`、`Select`、`OrderBy` の各プロパティを設定します。 検索クエリ式の構文の詳細については、[単純なクエリ構文](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)のページを参照してください。

次の手順では、検索クエリを実際に実行します。 この検索は、`SearchClient.Search` メソッドを使用して実行されます。 各クエリでは、使用する検索テキストを文字列として (または、検索テキストがない場合は `"*"` を) 渡し、以前に作成した検索オプションも渡します。 また、`SearchClient.Search` に対する型パラメーターとして `Hotel` も指定します。これは、検索結果のドキュメントを `Hotel` 型のオブジェクトに逆シリアル化するように SDK に指示します。

```csharp
private static void RunQueries(SearchClient searchClient)
{
    SearchOptions options;
    SearchResults<Hotel> results;

    Console.WriteLine("Query 1: Search for 'motel'. Return only the HotelName in results:\n");

    options = new SearchOptions();
    options.Select.Add("HotelName");

    results = searchClient.Search<Hotel>("motel", options);

    WriteDocuments(results);

    Console.Write("Query 2: Apply a filter to find hotels with rooms cheaper than $100 per night, ");
    Console.WriteLine("returning the HotelId and Description:\n");

    options = new SearchOptions()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)"
    };
    options.Select.Add("HotelId");
    options.Select.Add("Description");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.Write("Query 3: Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    options =
        new SearchOptions()
        {
            Size = 2
        };
    options.OrderBy.Add("LastRenovationDate desc");
    options.Select.Add("HotelName");
    options.Select.Add("LastRenovationDate");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.WriteLine("Query 4: Search the HotelName field for the term 'hotel':\n");

    options = new SearchOptions();
    options.SearchFields.Add("HotelName");

    //Adding details to select, because "Location" is not supported yet when deserialize search result to "Hotel"
    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Category");
    options.Select.Add("Tags");
    options.Select.Add("ParkingIncluded");
    options.Select.Add("LastRenovationDate");
    options.Select.Add("Rating");
    options.Select.Add("Address");
    options.Select.Add("Rooms");

    results = searchClient.Search<Hotel>("hotel", options);

    WriteDocuments(results);
}
```

第 3 に、応答を書き込み、各ドキュメントをコンソールに出力するメソッドを定義します。

```csharp
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="call-runqueries-in-main"></a>Main() で RunQueries を呼び出す

```csharp
SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

Console.WriteLine("{0}", "Running queries...\n");
RunQueries(indexClientForQueries);
```

### <a name="explore-query-constructs"></a>クエリのコンストラクトを調べる

各クエリについて詳しく見ていきましょう。 最初のクエリを実行するコードを次に示します。

```csharp
options = new SearchOptions();
options.Select.Add("HotelName");

results = searchClient.Search<Hotel>("motel", options);

WriteDocuments(results);
```

この例では、検索可能なあらゆるフィールドで "motel" という単語のインデックス全体を検索し、`Select` オプションによって指定されるとおり、ホテル名だけを返します。 結果は次のようになります。

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

2 番目のクエリで、フィルターを使用して 1 泊 100 ドル未満の部屋を選択します。 結果にホテルの ID と説明のみを返します。

```csharp
options = new SearchOptions()
{
    Filter = "Rooms/any(r: r/BaseRate lt 100)"
};
options.Select.Add("HotelId");
options.Select.Add("Description");

results = searchClient.Search<Hotel>("*", options);
```

上記のクエリでは、OData の `$filter` 式 (`Rooms/any(r: r/BaseRate lt 100)`) を使用して、インデックス内のドキュメントをフィルター処理しています。 ここでは、[あらゆる演算子](./search-query-odata-collection-operators.md)を使用して、'BaseRate lt 100' をルーム コレクションのすべての項目に適用します。 詳細については、[OData のフィルター構文](./query-odata-filter-orderby-syntax.md)のページを参照してください。

3 番目のクエリで、最近改装された上位 2 つのホテルを検索し、ホテル名と最終改装日を表示します。 次にコードを示します。 

```csharp
options =
    new SearchOptions()
    {
        Size = 2
    };
options.OrderBy.Add("LastRenovationDate desc");
options.Select.Add("HotelName");
options.Select.Add("LastRenovationDate");

results = searchClient.Search<Hotel>("*", options);

WriteDocuments(results);
```

最後のクエリで、"hotel" という単語に一致するすべてのホテル名を検索します。

```csharp
options.Select.Add("HotelId");
options.Select.Add("HotelName");
options.Select.Add("Description");
options.Select.Add("Category");
options.Select.Add("Tags");
options.Select.Add("ParkingIncluded");
options.Select.Add("LastRenovationDate");
options.Select.Add("Rating");
options.Select.Add("Address");
options.Select.Add("Rooms");

results = searchClient.Search<Hotel>("hotel", options);

WriteDocuments(results);
```

.NET SDK の概要についての説明はこのセクションが最後ですが、これで終わりにしないでください。 次のセクションでは、Azure Cognitive Search を使用したプログラミングの詳細について学ぶためのリソースを示します。

## <a name="next-steps"></a>次のステップ

+ [Azure.Search.Documents](/dotnet/api/azure.search.documents) と [REST API](/rest/api/searchservice/) の API リファレンス ドキュメントを参照する

+ Azure.Search.Documents に基づいた他のコード サンプルを [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) および [search-getting-started-dotnet](https://github.com/Azure-Samples/search-dotnet-getting-started) で参照する

+ さまざまなオブジェクトに名前を付けるときの規則を学ぶために、[名前付け規則](/rest/api/searchservice/Naming-rules)を確認する

+ [サポートされていないデータ型](/rest/api/searchservice/Supported-data-types)を確認する
