---
title: ".NET SDK を使用した Azure Search でのデータ アップロード | Microsoft Docs"
description: ".NET SDK を使用して Azure Search のインデックスにデータをアップロードする方法について説明します。"
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: 
ms.assetid: 0e0e7e7b-7178-4c26-95c6-2fd1e8015aca
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/13/2017
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: 1f06a7197cc1a6dcf7a39c91183a4317bef126bb
ms.openlocfilehash: 3c8f30583ebcb5b4e4182bd2770079882c088c50


---
# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>.NET SDK を使用した Azure Search へのデータのアップロード
> [!div class="op_single_selector"]
> * [概要](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

この記事では、 [Azure Search .NET SDK](https://aka.ms/search-sdk) を使用して Azure Search インデックスにデータをインポートする方法について説明します。

このチュートリアルを開始する前に、既に [Azure Search インデックスを作成](search-what-is-an-index.md)してあります。 また、この記事では、「[.NET SDK を使用した Azure Search インデックスの作成](search-create-index-dotnet.md#CreateSearchServiceClient)」で説明されているとおりに、`SearchServiceClient` オブジェクトを作成済みであることを前提としています。

この記事に記載されたすべてのサンプル コードは、C# で記述されていることにご注意ください。 [GitHub](http://aka.ms/search-dotnet-howto)に完全なソース コードがあります。

.NET SDK を使用してインデックスにドキュメントをプッシュするには、次の手順を実行する必要があります。

1. 検索インデックスに接続するための `SearchIndexClient` オブジェクトを作成します。
2. 追加、変更、または削除するドキュメントを含む `IndexBatch` オブジェクトを作成します。
3. `SearchIndexClient` の `Documents.Index` メソッドを呼び出して、`IndexBatch` を検索インデックスに送信します。

## <a name="create-an-instance-of-the-searchindexclient-class"></a>SearchIndexClient クラスのインスタンスの作成
Azure Search .NET SDK を使用してインデックスにデータをインポートするには、 `SearchIndexClient` クラスのインスタンスを作成する必要があります。 このインスタンスは自分で作成することもできますが、既に `SearchServiceClient` インスタンスがある場合は、`Indexes.GetClient` メソッドを呼び出すほうが簡単に済みます。 たとえば、`serviceClient` という名前の `SearchServiceClient` から "hotels" という名前のインデックスの `SearchIndexClient` を取得する方法は次のようになります。

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> 一般的な検索アプリケーションでは、インデックスの管理とインデックスの設定は、検索クエリとは別のコンポーネントによって処理されます。 `Indexes.GetClient` は、別の `SearchCredentials` を指定する手間を省くため、インデックスを作成するのに便利です。 そのためには、`SearchServiceClient` を作成するときに使用した管理者キーを新しい `SearchIndexClient` に渡します。 ただし、アプリケーションのクエリを実行する部分では、管理者キーの代わりにクエリ キーを渡すことができるように、 `SearchIndexClient` を直接作成する方が適しています。 これは、 [最小権限の原則](https://en.wikipedia.org/wiki/Principle_of_least_privilege) にも適合しており、アプリケーションのセキュリティ強化に役立ちます。 管理者キーとクエリ キーの詳細については、[Azure Search REST API のリファレンス](https://docs.microsoft.com/rest/api/searchservice/)を参照してください。
> 
> 

`SearchIndexClient` には `Documents` プロパティがあります。 このプロパティは、インデックスの追加、変更、削除、クエリに必要なすべてのメソッドを提供します。

## <a name="decide-which-indexing-action-to-use"></a>利用するインデックス作成アクションの決定
.NET SDK を使用してデータをインポートするには、データを `IndexBatch` オブジェクトにパッケージ化する必要があります。 `IndexBatch` は複数の `IndexAction` オブジェクトをカプセル化したものです。このオブジェクトにはそれぞれ、ドキュメント 1 つと、Azure Search にそのドキュメントへのアクション (アップロード、マージ、削除など) を指示するプロパティが 1 つ含まれています。 以下のアクションのうちどれを選ぶかに応じて、各ドキュメントに含める必要のあるフィールドは異なります。

| アクション | Description | 各ドキュメントに必要なフィールド | メモ |
| --- | --- | --- | --- |
| `Upload` |`Upload` アクションは、ドキュメントが新しい場合は挿入され、存在する場合は更新/置換される "upsert" に似ています。 |キーのほか、定義するその他すべてのフィールド |既存のドキュメントを更新または置換する際に、要求で指定されていないフィールドは `null`に設定されます。 この処理は、フィールドが null 以外の値に設定されていた場合にも行われます。 |
| `Merge` |指定されたフィールドで既存のドキュメントを更新します。 ドキュメントがインデックスに存在しない場合、マージは失敗します。 |キーのほか、定義するその他すべてのフィールド |マージで指定したすべてのフィールドは、ドキュメント内の既存のフィールドを置き換えます。 これには、 `DataType.Collection(DataType.String)`型のフィールドも含まれます。 たとえば、ドキュメントにフィールド `tags` があり、その値が `["budget"]` である場合、`tags` に値 `["economy", "pool"]` を指定してマージを実行すると、`tags` フィールドの最終的な値は `["economy", "pool"]` になります。 `["budget", "economy", "pool"]`にはなりません。 |
| `MergeOrUpload` |このアクションは、指定したキーを持つドキュメントがインデックスに既に存在する場合は、`Merge` と同様の処理になります。 ドキュメントが存在しない場合は、 `Upload` と同様の処理になり、新しいドキュメントが挿入されます。 |キーのほか、定義するその他すべてのフィールド |- |
| `Delete` |インデックスから指定したドキュメントを削除します。 |キーのみ |指定したフィールドは、キー フィールド以外すべて無視されます。 ドキュメントから個々のフィールドを削除する場合は、代わりに `Merge` を使用して、フィールドを明示的に null に設定します。 |

`IndexBatch` クラスと `IndexAction` クラスの各種静的メソッドで使用するアクションを指定できます。詳しくは、次のセクションで説明します。

## <a name="construct-your-indexbatch"></a>IndexBatch の作成
ドキュメントに対して実行するアクションを特定したら、`IndexBatch` を作成できます。 次の例は、いくつかのアクションでバッチを作成する方法を示しています。 この例では、"hotels" インデックス内でドキュメントにマップされている `Hotel` という名前のカスタム クラスを使用している点に注目してください。

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
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
            }),
        IndexAction.Upload(
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
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

この場合は、`IndexAction` クラスで呼び出されたメソッドで指定されているとおり、検索アクションとして、`Upload`、`MergeOrUpload`、`Delete` を使用しています。

この例の "hotels" インデックスには、既にさまざまなドキュメントが設定されているものとします。 `MergeOrUpload` を使用する場合はすべてのドキュメント フィールドを指定する必要はなく、`Delete` を使用する場合はドキュメント キー (`HotelId`) のみを指定しています。

また、単一のインデックス作成要求に含めることのできるドキュメントは最大 1,000 個であることにも注意が必要です。

> [!NOTE]
> この例では、複数のドキュメントに各種アクションを適用しています。 バッチ内のすべてのドキュメントに同じアクションを実行する場合は、`IndexBatch.New` を呼び出す代わりに、`IndexBatch` の他の静的メソッドを使用できます。 たとえば、`IndexBatch.Merge`、`IndexBatch.MergeOrUpload`、または `IndexBatch.Delete` を呼び出してバッチを作成できます。 これらのメソッドでは、`IndexAction` オブジェクトの代わりにドキュメント (この例では `Hotel` 型のオブジェクト) のコレクションを受け取ります。
> 
> 

## <a name="import-data-to-the-index"></a>インデックスへのデータのインポート
`IndexBatch` オブジェクトが初期化されたので、`SearchIndexClient` オブジェクトで `Documents.Index` を呼び出してインデックスに送信できます。 次の例は、 `Index`を呼び出す方法と、実行が必要な追加手順を示しています。

```csharp
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
```

`Index` メソッドの呼び出しを `try`/`catch` が囲んでいる点に注目してください。 catch ブロックがインデックス作成の重要なエラー ケースを処理します。 Azure Search がバッチ内の一部のドキュメントのインデックス作成に失敗した場合、`Documents.Index` は `IndexBatchException` をスローします。 サービスの負荷が高いときにドキュメントのインデックスを作成すると、これが発生する場合があります。 **コードでこのケースを明示的に処理することを強くお勧めします。** しばらく待ってから失敗したドキュメントのインデックス作成を再試行したり、サンプルと同じようにログに記録してから続けることができます。または、アプリケーションのデータ整合性要件に応じて他の処理を行うこともできます。

上記の例のコードは、最後に 2 秒間遅延します。 インデックスの作成は Azure Search サービスで非同期的に行われるので、サンプル アプリケーションは短い時間待機して、確実にドキュメントを検索に使用できるようにする必要があります。 通常、このような遅延は、デモ、テスト、およびサンプル アプリケーションでのみ必要です。

<a name="HotelClass"></a>

### <a name="how-the-net-sdk-handles-documents"></a>.NET SDK がドキュメントを処理する方法
Azure Search .NET SDK が `Hotel` のようなユーザー定義クラスのインスタンスをどのようにしてインデックスにアップロードできるのか不思議に思われるかもしれません。 この疑問に答えるために、`Hotel` クラスを見てみましょう。このクラスは、「[.NET SDK を使用した Azure Search インデックスの作成](search-create-index-dotnet.md#DefineIndex)」で定義したインデックス スキーマにマップされています。

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [Key]
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

    // ToString() method omitted for brevity...
}
```

最初に気付くのは、`Hotel` の各パブリック プロパティがインデックス定義のフィールドに対応していることですが、1 つ重要な違いがあります。各フィールドの名前が小文字で始まっているのに対し ("camel case")、`Hotel` の各パブリック プロパティの名前は大文字で始まっています ("Pascal case")。 これは、ターゲット スキーマをアプリケーション開発者が制御できない場合にデータ バインドを実行する .NET アプリケーションでの一般的なシナリオです。 プロパティ名を camel-case にして .NET の命名ガイドラインに違反するのではなく、プロパティ名を自動的に camel-case にマップするように `[SerializePropertyNamesAsCamelCase]` 属性で SDK に指示できます。

> [!NOTE]
> Azure Search .NET SDK は、 [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) ライブラリを使用して、カスタムのモデル オブジェクトから JSON 形式へのシリアル化や JSON 形式からの逆シリアル化を行います。 必要に応じてこのシリアル化をカスタマイズできます。 詳細については、「[JSON.NET を使用したシリアル化のカスタマイズ](search-howto-dotnet-sdk.md#JsonDotNet)」を参照してください。 一例として、上記のサンプル コードで `DescriptionFr` プロパティに `[JsonProperty]` 属性を使用できます。
> 
> 

`Hotel` クラスに関する 2 番目の重要な点は、パブリック プロパティのデータ型です。 これらのプロパティの .NET 型は、インデックス定義でそれらと同等のフィールド型にマップします。 たとえば、`Category` 文字列プロパティは、`DataType.String` 型の `category` フィールドにマップします。 `bool?` と `DataType.Boolean`、`DateTimeOffset?` と `DataType.DateTimeOffset` などの間にも、同じような型のマッピングがあります。 型のマッピングの具体的なルールについては、[Azure Search .NET SDK リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_)で `Documents.Get` メソッドを参照してください。

独自のクラスをドキュメントとして使用するこの方法は、どちらの方向でも機能します。また、[次の記事](search-query-dotnet.md)で示すように、検索結果を取得し、SDK で自動的に任意の型に逆シリアル化することもできます。

> [!NOTE]
> Azure Search .NET SDK は、`Document` クラスを使用して動的に型指定されたドキュメントもサポートします。これは、フィールドの値に対するフィールド名のキー/値マッピングです。 この機能は、設計時にインデックス スキーマがわからない場合、または特定のモデル クラスにバインドすると不都合な場合に便利です。 ドキュメントを処理する SDK のすべてのメソッドには、`Document` クラスを使用するオーバーロード、およびジェネリック型パラメーターを使用する厳密な型指定のオーバーロードがあります。 この記事のサンプル コードでは、後者のみを使用しています。
> 
> 

**null 許容のデータ型を使用する理由**

Azure Search インデックスにマップする独自のモデル クラスを設計するときは、`bool` や `int` などの値型のプロパティを null 許容型として宣言することをお勧めします (たとえば、`bool` ではなく `bool?` を使用する)。 null 非許容プロパティを使用する場合、対応するフィールドに null 値が含まれるドキュメントがインデックス内に存在しないことを、開発者が **保証する** 必要があります。 SDK または Azure Search サービスで、これを強制することはできません。

これは単なる仮定上の問題ではありません。`DataType.Int32` 型の既存のインデックスに新しいフィールドを追加する場合を考えてみてください。 インデックスの定義を更新した後、(Azure Search ではすべての型が null を許容するので) すべてのドキュメントでその新しいフィールドの値が null になります。 その後、そのフィールドが null 非許容型の `int` プロパティであるモデル クラスを使用した場合、ドキュメントを取得しようとすると、次のような `JsonSerializationException` が発生します。

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

このため、ベスト プラクティスとして、モデル クラスでは null 許容型を使用することをお勧めします。

## <a name="next-steps"></a>次のステップ
Azure Search インデックスにデータを読み込んだら、ドキュメントを検索するクエリを発行できるようになります。 詳細については、「 [Azure Search インデックスの照会](search-query-overview.md) 」を参照してください。




<!--HONumber=Feb17_HO3-->


