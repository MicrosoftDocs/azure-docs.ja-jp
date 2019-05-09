---
title: C# (.NET SDK) で Azure Search インデックスにデータを読み込む - Azure Search
description: C# のサンプル コードと .NET SDK を使用して、Azure Search の全文検索可能なインデックスにデータをアップロードする方法について説明します。
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: d2d54d1425bbb67a3f5ba1b6081a9f74ff87f4d6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286909"
---
# <a name="quickstart-2---load-data-to-an-azure-search-index-using-c"></a>クイック スタート:2 - C# を使用して Azure Search インデックスにデータを読み込む

この記事では、C# と [.NET SDK](https://aka.ms/search-sdk) を使用して [Azure Search インデックス](search-what-is-an-index.md)にデータをインポートする方法について説明します。 ドキュメントのインデックスへのプッシュ操作は、次のタスクを実行することによって実現されます。

> [!div class="checklist"]
> * 検索インデックスに接続するための [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) オブジェクトを作成します。
> * 追加、変更、または削除するドキュメントを含む [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) オブジェクトを作成します。
> * `SearchIndexClient` で `Documents.Index` メソッドを呼び出してドキュメントをインデックスにアップロードします。

## <a name="prerequisites"></a>前提条件

[Azure Search インデックスの作成](search-create-index-dotnet.md)と、「[クライアントを作成する](search-create-index-dotnet.md#CreateSearchServiceClient)」に説明されている `SearchServiceClient` オブジェクトの作成が完了していること。


## <a name="create-a-client"></a>クライアントの作成
データをインポートするには、`SearchIndexClient` クラスのインスタンスが必要です。 このクラスを作成するには、既に作成されている `SearchServiceClient` インスタンスを使用するなど、いくつかの方法があります。 

次の例に示すように、`SearchServiceClient` インスタンスを使用してその `Indexes.GetClient` メソッドを呼び出すことができます。 このスニペットでは、`serviceClient` という名前の `SearchServiceClient` から "hotels" という名前のインデックスの `SearchIndexClient` を取得します。

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

`SearchIndexClient` には `Documents` プロパティがあります。 このプロパティは、インデックスの追加、変更、削除、クエリに必要なすべてのメソッドを提供します。

> [!NOTE]
> 一般的な検索アプリケーションでは、クエリの実行とインデックス付けは別個に処理されます。 `Indexes.GetClient` では `SearchCredentials` のようなオブジェクトを再利用できるので便利ですが、管理者キーの代わりにクエリ キーを渡すことができるように `SearchIndexClient` を直接作成すると、より堅牢になります。 この方法は、[最小限の特権の原則](https://en.wikipedia.org/wiki/Principle_of_least_privilege)にも適合しており、アプリケーションのセキュリティ強化に役立ちます。 `SearchIndexClient` は次の演習で作成します。 キーの詳細については、「[Create and manage api-keys for an Azure Search service (Azure Search サービスの API キーの作成と管理)](search-security-api-keys.md)」を参照してください。
> 
> 

<a name="construct-indexbatch"></a>

## <a name="construct-indexbatch"></a>IndexBatch の作成

.NET SDK を使用してデータをインポートするには、データを `IndexBatch` オブジェクトにパッケージ化します。 `IndexBatch` は `IndexAction` オブジェクトのコレクションをカプセル化したものです。各オブジェクトには、ドキュメント 1 つと、そのドキュメントに対して実行するアクション (upload、merge、delete、mergeOrUpload など) を Azure Search に指示するプロパティが 1 つ含まれています。 インデックス付けアクションの詳細については、「[Indexing actions: upload, merge, mergeOrUpload, delete (インデックス付けアクション: upload、merge、mergeOrUpload、delete)](search-what-is-data-import.md#indexing-actions)」を参照してください。

ドキュメントに対して実行するアクションがわかっていれば、`IndexBatch` を作成する準備が整っていることになります。 次の例は、いくつかのアクションでバッチを作成する方法を示しています。 この例では、"hotels" インデックス内でドキュメントにマップされている `Hotel` という名前のカスタム クラスを使用しています。

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

## <a name="call-documentsindex"></a>Documents.Index の呼び出し
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

ドキュメント処理の詳細については、「[How the .NET SDK handles documents (.NET SDK がドキュメントを処理する方法)](search-howto-dotnet-sdk.md#how-dotnet-handles-documents)」を参照してください。


## <a name="next-steps"></a>次の手順
Azure Search インデックスにデータを読み込んだ後の次の手順では、ドキュメントを検索するクエリを発行します。 

> [!div class="nextstepaction"]
> [C# で Azure Search インデックスに対するクエリを実行する](search-query-dotnet.md)
