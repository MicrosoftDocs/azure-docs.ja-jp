---
title: 'C# チュートリアル: プッシュ API を使用してインデックス作成を最適化する'
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のプッシュ API を使用してデータのインデックスを効率的に作成する方法について説明します。 このチュートリアルとサンプル コードは C# で記述されています。
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 1/29/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: f97a99bf2d055805ee665ab51aff8cff12dc5a69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99094194"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>チュートリアル:プッシュ API を使用してインデックス作成を最適化する

Azure Cognitive Search は、検索インデックスにデータをインポートする [2 つの基本的な手法](search-what-is-data-import.md)をサポートしています。1 つは、プログラムを使用してインデックスにデータを "*プッシュ*" する方法、もう 1 つは、サポート対象データ ソースで [Azure Cognitive Search のインデクサー](search-indexer-overview.md)をポイントしてデータを "*プル*" する方法です。

このチュートリアルでは、[プッシュ モデル](search-what-is-data-import.md#pushing-data-to-an-index)を使用して効率的にデータのインデックスを作成する方法について説明します。その際、要求はバッチで処理し、エクスポネンシャル バックオフの再試行戦略を使用しています。 [サンプル アプリケーションをダウンロードし、実行](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing)できます。 この記事では、アプリケーションの主な特徴と、データのインデックスを作成する際に考慮すべき事柄について説明します。

このチュートリアルでは、C# と [.NET SDK](/dotnet/api/overview/azure/search) を使用して次のタスクを実行します。

> [!div class="checklist"]
> * インデックスを作成する
> * さまざまなバッチ サイズをテストして最も効率的なサイズを特定する
> * バッチに非同期でインデックスを付ける
> * 複数のスレッドを使用してインデックスの作成速度を高める
> * エクスポネンシャル バックオフの再試行戦略を使用して、失敗したドキュメントを再試行する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルには、次のサービスとツールが必要です。

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/) (任意のエディション)。 サンプル コードと手順については、無料の Community エディションでテストされています。

+ [Azure Cognitive Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

<a name="get-service-info"></a>

## <a name="download-files"></a>ファイルのダウンロード

このチュートリアルのソース コードは、[Azure-Samples/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) GitHub リポジトリの [optimzize-data-indexing/v11](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing/v11) フォルダーにあります。

## <a name="key-considerations"></a>重要な考慮事項

インデックスにデータをプッシュするとき、インデックスの作成速度を左右するいくつかの重要な考慮事項があります。 これらの事項について詳しくは、[大規模なデータ セットのインデックス作成に関する記事](search-howto-large-index.md)を参照してください。

重要な考慮事項は次の 6 つです。

+ **サービス レベルとパーティションまたはレプリカの数** - パーティションを追加したりレベルを上げたりすることによって、インデックスの作成速度が上がります。
+ **インデックス スキーマ** - フィールドを追加したり、フィールドに別のプロパティ (*searchable*、*facetable*、*filterable* など) を追加したりすると、インデックスの作成速度が落ちます。
+ **バッチ サイズ** - 最適なバッチ サイズは、インデックス スキーマとデータセットによって異なります。
+ **スレッドまたはワーカーの数** - シングル スレッドでは、インデックスの作成速度が十分に発揮されません。
+ **再試行戦略** - インデックス作成を最適化するには、エクスポネンシャル バックオフの再試行戦略を使用する必要があります。
+ **ネットワーク データ転送速度** - データ転送速度が制限要因になる場合があります。 データのインデックス作成を Azure 環境内から行えば、データの転送速度が上がります。


## <a name="1---create-azure-cognitive-search-service"></a>1 - Azure Cognitive Search サービスを作成する

このチュートリアルをすべて実行するには、[ポータルで作成](search-create-service-portal.md)できる Azure Cognitive Search サービスが必要です。 インデックスの作成速度を正確にテストして最適化できるよう、運用環境で使用するのと同じレベルを使用することをお勧めします。

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Cognitive Search のための管理者 API キーと URL を取得する

API 呼び出しには、サービス URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Cognitive Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

   ![HTTP エンドポイントとアクセス キーを取得する](media/search-get-started-rest/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

## <a name="2---set-up-your-environment"></a>2 - 環境を設定する

1. Visual Studio を起動し、**OptimizeDataIndexing.sln** を開きます。
1. ソリューション エクスプローラーで **appsettings.json** を開き、接続情報を指定します。

```json
{
  "SearchServiceUri": "https://{service-name}.search.windows.net",
  "SearchServiceAdminApiKey": "",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3 - コードを調べる

*appsettings.json* を更新したら、**OptimizeDataIndexing.sln** のサンプル プログラムをビルドして実行できます。

このコードは、この [C# クイックスタート](search-get-started-dotnet.md)から派生したものです。 .NET SDK の取り扱いの基本については、そちらの記事でさらに詳しい情報をご覧いただけます。

この単純な C# または .NET コンソール アプリは次のタスクを実行します。

+ (Address クラスも参照する) C# Hotel クラスのデータ構造に基づいて新しいインデックスを作成する
+ さまざまなバッチ サイズをテストして最も効率的なサイズを特定する
+ データのインデックスを非同期的に作成する
    + 複数のスレッドを使用してインデックスの作成速度を高める
    + エクスポネンシャル バックオフの再試行戦略を使用して、失敗した項目を再試行する

 プログラムを実行する前に、時間を取って、このサンプルのコードとインデックスの定義を確認しましょう。 関連するコードはいくつかのファイルにあります。

  + **Hotel.cs** と **Address.cs** には、インデックスを定義するスキーマが含まれています。
  + **DataGenerator.cs** には、大量のホテル データを簡単に作成できる単純なクラスが含まれています。
  + **ExponentialBackoff.cs** には、以下で説明するインデックス作成プロセスを最適化するためのコードが含まれています。
  + **Program.cs** には、Azure Cognitive Search のインデックスの作成と削除、バッチ単位でのデータのインデックス作成、さまざまなバッチサイズのテストを行う関数が含まれています。

### <a name="creating-the-index"></a>インデックスを作成する

このサンプル プログラムでは、.NET SDK を使用して、Azure Cognitive Search のインデックスを定義および作成します。 `FieldBuilder` クラスを利用して、C# データ モデル クラスからインデックス構造を生成します。

データ モデルは、Hotel クラスによって定義されています。Hotel クラスには、Address クラスへの参照も含まれています。 FieldBuilder は、複数のクラス定義をドリルダウンして、このインデックスの複雑なデータ構造を生成します。 メタデータ タグは、検索や並べ替えが可能かどうかなど、各フィールドの属性を定義するために使用されます。

**Hotel.cs** ファイルの次のスニペットは、単一のフィールドと、別のデータ モデル クラスへの参照を指定する方法を示しています。

```csharp
. . .
[SearchableField(IsSortable = true)]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

**Program.cs** ファイルでは、インデックスが、`FieldBuilder.Build(typeof(Hotel))` メソッドで生成された名前とフィールド コレクションを使用して定義され、次のように作成されます。

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="generating-data"></a>データを生成する

**DataGenerator.cs** ファイルには、テスト用のデータを生成するための単純なクラスが実装されています。 インデックス作成用に一意の ID を持った大量のドキュメントを簡単に作成できるようにすることが、このクラスの唯一の目的です。

たとえば、一意の ID を持った 100,000 件のホテルのリストを取得する場合、次のコード行を実行します。

```csharp
long numDocuments = 100000;
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(numDocuments, "large");
```

このサンプルのテスト用に用意されたホテルには、**small** と **large** の 2 つのサイズがあります。

インデックスのスキーマは、インデックスの作成速度に著しい影響を及ぼす場合があります。 この影響を踏まえて、このチュートリアルを実行し終えたら、意図したインデックス スキーマに合ったデータを生成するようこのクラスを変換することをお勧めします。

## <a name="4---test-batch-sizes"></a>4 - バッチ サイズをテストする

Azure Cognitive Search は、1 つまたは複数のドキュメントをインデックスに読み込む次の API をサポートしています。

+ [ドキュメントの追加、更新、削除 (REST API)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [IndexDocumentsAction class](/dotnet/api/azure.search.documents.models.indexdocumentsaction) または [IndexDocumentsBatch class](/dotnet/api/azure.search.documents.models.indexdocumentsbatch)

ドキュメントのインデックスをバッチ単位で作成することによって、インデックス作成のパフォーマンスが大幅に向上します。 バッチの最大サイズは、1,000 ドキュメントまたは約 16 MB です。

実際のデータに最適なバッチ サイズを見極めることが、インデックスの作成速度を最適化するうえで重要な要素となります。 最適なバッチ サイズは主に、次の 2 つの要因によって左右されます。

+ インデックスのスキーマ
+ データのサイズ

最適なバッチ サイズはインデックスとデータによって異なるため、さまざまなバッチ サイズをテストしながら、実際のシナリオにおいてインデックスの作成速度が最速となるサイズを見極めるのが最善のアプローチとなります。

次の関数は、バッチ サイズをテストするための単純なアプローチを示したものです。

```csharp
public static async Task TestBatchSizesAsync(SearchClient searchClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocumentsAsync(searchClient, hotels).ConfigureAwait(false);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }

    Console.WriteLine();
}
```

このサンプルではすべてのドキュメントが同じサイズですが、実際にはそうとは限らないため、検索サービスに送信するデータのサイズを見積もります。 そのために使用しているのが以下の関数です。オブジェクトを JSON に変換したうえで、そのサイズをバイト単位で調べます。 この手法によって、インデックスの作成速度 (MB/秒) の観点から最も効率のよいバッチ サイズを特定することができます。

```csharp
// Returns size of object in MB
public static double EstimateObjectSize(object data)
{
    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    // converting data to json for more accurate sizing
    var json = JsonSerializer.Serialize(data);
    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

この関数には、`SearchClient` のほか、バッチ サイズごとのテストの試行回数を指定する必要があります。 インデックス作成時間には、バッチごとに多少のばらつきがあると考えられます。そこで統計的に有意な結果を得るために、それぞれのバッチが既定では 3 回試行されます。

```csharp
await TestBatchSizesAsync(searchClient, numTries: 3);
```

この関数を実行すると、次のような出力がコンソールに表示されます。

   ![バッチ サイズのテスト関数の出力](media/tutorial-optimize-data-indexing/test-batch-sizes.png "バッチ サイズのテスト関数の出力")

どのバッチ サイズが最も効率的であるかを見極め、そのバッチ サイズをチュートリアルの次の手順で使用します。 バッチ サイズが異なっていても、1 秒あたりのバイト数 (MB) が頭打ちになっていることがわかります。

## <a name="5---index-data"></a>5 - データのインデックスを作成する

使用するバッチ サイズを特定できたので、データのインデックス作成を開始します。 データのインデックスを効率よく作成するために、このサンプルでは、次のことを行っています。

+ 複数のスレッド (ワーカー) を使用する。
+ エクスポネンシャル バックオフの再試行戦略を実装する。

### <a name="use-multiple-threadsworkers"></a>複数のスレッド (ワーカー) を使用する

Azure Cognitive Search のインデックス作成速度を最大限に引き出すには、おそらく複数のスレッドを使用して、インデックス作成要求のバッチをサービスに対して同時に送信する必要があります。  

最適なスレッド数は、前述した主な考慮事項のいくつかによって左右されます。 さまざまなスレッド数でこのサンプルを変更、テストすることによって、実際のシナリオに最適なスレッド数を見極めてください。 ただし、複数のスレッドを同時に実行すれば、効率向上の利点はおおよそ活かすことができるはずです。

検索サービスに対する要求を増やしていくと、要求が完全には成功しなかったことを示す [HTTP 状態コード](/rest/api/searchservice/http-status-codes)が返されることがあります。 インデックスの作成時によく発生する HTTP 状態コードは次の 2 つです。

+ **503 Service Unavailable** - このエラーは、システムが過負荷の状態にあり、この時点では要求を処理できないことを示します。
+ **207 Multi-Status** - このエラーは、ドキュメントの一部は成功しましたが、少なくとも 1 つが失敗したことを示します。

### <a name="implement-an-exponential-backoff-retry-strategy"></a>エクスポネンシャル バックオフの再試行戦略を実装する

失敗した要求は、[エクスポネンシャル バックオフの再試行戦略](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff)を使用して再試行する必要があります。

503 などで失敗した要求は、Azure Cognitive Search の .NET SDK によって自動的に再試行されますが、207 には、独自の再試行ロジックを実装する必要があります。 [Polly](https://github.com/App-vNext/Polly) などのオープンソース ツールを使用して、再試行戦略を実装することもできます。

このサンプルでは、エクスポネンシャル バックオフの再試行戦略を独自に実装します。 この戦略を実装するにはまず、失敗した要求の `maxRetryAttempts` と `delay` (初期延期期間) を含め、いくつかの変数を定義します。

```csharp
// Create batch of documents for indexing
var batch = IndexDocumentsBatch.Upload(hotels);

// Create an object to hold the result
IndexDocumentsResult result = null;

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

インデックス作成捜査の結果は変数 `IndexDocumentResult result` に格納されます。 下の画像のようにバッチに含まれるドキュメントが失敗したかどうかを確認できるため、この変数は重要です。 部分的に失敗している場合、失敗したドキュメントの ID に基づいて新しいバッチが作成されます。

`RequestFailedException` 例外は要求が完全に失敗しており、再試行が必要であることを示すため、これもキャッチする必要があります。

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        result = await searchClient.IndexDocumentsAsync(batch).ConfigureAwait(false);

        var failedDocuments = result.Results.Where(r => r.Succeeded != true).ToList();

        // handle partial failure
        if (failedDocuments.Count > 0)
        {
            if (attempts == maxRetryAttempts)
            {
                Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
                break;
            }
            else
            {
                Console.WriteLine("[Batch starting at doc {0} had partial failure]", id);
                Console.WriteLine("[Retrying {0} failed documents] \n", failedDocuments.Count);

                // creating a batch of failed documents to retry
                var failedDocumentKeys = failedDocuments.Select(doc => doc.Key).ToList();
                hotels = hotels.Where(h => failedDocumentKeys.Contains(h.HotelId)).ToList();
                batch = IndexDocumentsBatch.Upload(hotels);

                Task.Delay(delay).Wait();
                delay = delay * 2;
                continue;
            }
        }

        return result;
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("[Batch starting at doc {0} failed]", id);
        Console.WriteLine("[Retrying entire batch] \n");

        if (attempts == maxRetryAttempts)
        {
            Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
            break;
        }

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

ここでは、エクスポネンシャル バックオフのコードを呼び出しやすいよう関数にラップしています。

さらに、アクティブなスレッドを管理するための関数も別途作成します。 その関数は、簡潔にするためにここでは省略していますが、[ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/v11/OptimizeDataIndexing/ExponentialBackoff.cs) でご覧いただけます。 その関数は、次のコマンドで呼び出すことができます。`hotels` はアップロードするデータで、`1000` はバッチ サイズ、また `8` は、コンカレント スレッド数です。

```csharp
await ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8);
```

この関数を実行すると、次のような出力が表示されます。

![データのインデックス作成関数の出力](media/tutorial-optimize-data-indexing/index-data-start.png "データのインデックス作成関数の出力")

ドキュメントのバッチが失敗すると、処理に失敗したこと、またそのバッチが再試行されていることを示すエラーが出力されます。

```
[Batch starting at doc 6000 had partial failure]
[Retrying 560 failed documents]
```

関数の実行が完了したら、すべてのドキュメントがインデックスに追加されたことを確認できます。

## <a name="6---explore-index"></a>6 - インデックスを調べる

プログラムの実行後に、ポータルの [**Search エクスプローラー**](search-explorer.md)またはプログラムを使用して、設定された検索インデックスを確認できます。

### <a name="programatically"></a>プログラム

インデックス内のドキュメント数は、主に 2 つの方法で確認できます。[Count Documents API](/rest/api/searchservice/count-documents) を使用する方法と [Get Index Statistics API](/rest/api/searchservice/get-index-statistics) を使用する方法です。 どちらの方法も、ある程度の更新時間が別途かかることがあります。返されたドキュメント数が、最初に予想していた数よりも少なくても心配しないでください。

#### <a name="count-documents"></a>ドキュメントのカウント

Count Documents 操作は、検索インデックス内のドキュメントの数を取得します。

```csharp
long indexDocCount = await searchClient.GetDocumentCountAsync();
```

#### <a name="get-index-statistics"></a>インデックス統計の取得

Get Index Statistics 操作は、現在のインデックスに対するドキュメントの数と記憶域の使用状況を返します。 インデックスの統計は、ドキュメント数よりも更新に時間がかかります。

```csharp
var indexStats = await indexClient.GetIndexStatisticsAsync(indexName);
```

### <a name="azure-portal"></a>Azure portal

Azure portal で、検索サービスの **[概要]** ページを開き、 **[インデックス]** 一覧内で **optimize-indexing** インデックスを見つけます。

  ![Azure Cognitive Search インデックスの一覧](media/tutorial-optimize-data-indexing/portal-output.png "Azure Cognitive Search インデックスの一覧")

*[ドキュメント数]* と *[ストレージ サイズ]* は、[Get Index Statistics API](/rest/api/searchservice/get-index-statistics) から得られるため、更新に数分かかる場合があります。

## <a name="reset-and-rerun"></a>リセットして再実行する

開発の初期の実験的な段階では、設計反復のための最も実用的なアプローチは、Azure Cognitive Search からオブジェクトを削除してリビルドできるようにすることです。 リソース名は一意です。 オブジェクトを削除すると、同じ名前を使用して再作成することができます。

このチュートリアルのサンプル コードでは、コードを再実行できるよう、既存のインデックスをチェックしてそれらを削除しています。

インデックスは、ポータルを使用して削除することもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

所有するサブスクリプションを使用している場合は、プロジェクトの終了時に、不要になったリソースを削除することをお勧めします。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

## <a name="next-steps"></a>次のステップ

効率的なデータ取り込みの概念を理解したら、Lucene のクエリ アーキテクチャと Azure Cognitive Search におけるフルテキスト検索の動作について詳しく見てみましょう。

> [!div class="nextstepaction"]
> [Azure Cognitive Search でのフルテキスト検索のしくみ](search-lucene-query-architecture.md)