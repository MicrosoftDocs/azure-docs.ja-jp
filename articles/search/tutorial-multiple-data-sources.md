---
title: 複数の Azure データ ソースのデータにインデックスを付ける C# チュートリアル
titleSuffix: Azure Cognitive Search
description: インデクサーを使用して、複数のデータ ソースから 1 つの Azure Cognitive Search インデックスにデータをインポートする方法について説明します。 このチュートリアルとサンプル コードは C# で記述されています。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: d3dd75d246c1f74253a9ce910e50b05402065464
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998460"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>チュートリアル:.NET SDK を使用して複数のデータ ソースのデータにインデックスを付ける

Azure Cognitive Search では、複数のデータ ソースから 1 つの統合検索インデックスにデータをインポートし、分析、インデックス付けを行うことができます。 これは、構造化データが、他のソースのあまり構造化されていないデータやプレーンテキスト データ (テキスト、HTML、JSON ドキュメントなど) と共に集計される状況をサポートします。

このチュートリアルでは、Azure Cosmos DB データ ソースのホテルのデータにインデックスを付け、それを Azure Blob Storage ドキュメントから取得したホテルの部屋の詳細とマージする方法について説明します。 その結果、結合されたホテル検索インデックスには複雑なデータ型が含まれます。

このチュートリアルでは、C# と [.NET SDK](/dotnet/api/overview/azure/search) を使用します。 このチュートリアルでは、以下のタスクを実行します。

> [!div class="checklist"]
> * サンプル データをアップロードしてデータ ソースを作成する
> * ドキュメント キーを識別する
> * インデックスを定義して作成する
> * Azure Cosmos DB のホテルのデータにインデックスを付ける
> * Blob Storage のホテルの部屋のデータをマージする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Azure ストレージ](../storage/common/storage-account-create.md)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [作成](search-create-service-portal.md)または[既存の検索サービスの用意](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> このチュートリアルには無料のサービスを使用できます。 無料の検索サービスでは、3 つのインデックス、3 つのインデクサー、3 つのデータ ソースという制限があります。 このチュートリアルでは、それぞれ 1 つずつ作成します。 開始する前に、ご利用のサービスに新しいリソースを受け入れる余地があることを確認してください。

## <a name="download-files"></a>ファイルのダウンロード

このチュートリアルのソース コードは、[azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) GitHub リポジトリの [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources) フォルダーにあります。

## <a name="1---create-services"></a>1 - サービスを作成する

このチュートリアルでは、インデックス作成とクエリに Azure Cognitive Search を使用します。また、1 つ目のデータ セットには Azure Cosmos DB を、2 つ目のデータ セットには Azure Blob Storage を使用します。 

可能である場合は、近接性と管理性を高めるために、すべてのサービスを同じリージョンおよびリソース グループ内に作成してください。 実際には、任意のリージョンにサービスを置くことができます。

このサンプルでは、7 つの架空のホテルを説明する小さなデータ セットを 2 つ使用します。 一方のセットは、ホテル自体を説明するもので、Azure Cosmos DB データベースに読み込まれます。 もう一方のセットは、ホテルの部屋の詳細を含んでおり、Azure Blob Storage にアップロードする 7 つの個別の JSON ファイルとして提供されます。

### <a name="start-with-cosmos-db"></a>Cosmos DB を準備する

1. [Azure portal](https://portal.azure.com) にサインインし、Azure Cosmos DB アカウントの [概要] ページに移動します。

1. **[データ エクスプローラー]** を選択し、 **[新しいデータベース]** を選択します。

   ![新しいデータベースの作成](media/tutorial-multiple-data-sources/cosmos-newdb.png "新しいデータベースの作成")

1. 「**hotel-rooms-db**」という名前を入力します。 残りの設定は既定値のままにします。

   ![データベースを構成する](media/tutorial-multiple-data-sources/cosmos-dbname.png "データベースを構成する")

1. 新しいコンテナーを作成します。 先ほど作成した既存のデータベースを使用します。 コンテナー名として「**hotels**」と入力し、[パーティション キー] に「 **/HotelId**」を使用します。

   ![コンテナーを追加する](media/tutorial-multiple-data-sources/cosmos-add-container.png "コンテナーの追加")

1. **hotels** の下にある **[項目]** を選択し、コマンド バーの **[Upload Item]\(項目のアップロード\)** をクリックします。 プロジェクト フォルダー内のファイル **cosmosdb/HotelsDataSubset_CosmosDb.json** に移動して選択します。

   ![Azure Cosmos DB コレクションにアップロードする](media/tutorial-multiple-data-sources/cosmos-upload.png "Cosmos DB コレクションにアップロードする")

1. [最新の情報に更新] ボタンを使用して、hotels コレクション内の項目の表示を更新します。 7 つの新しいデータベース ドキュメントが表示されることがわかります。

### <a name="azure-blob-storage"></a>Azure BLOB ストレージ

1. [Azure portal](https://portal.azure.com) にサインインし、Azure ストレージ アカウントに移動して **[BLOB]** をクリックし、 **[+ コンテナー]** をクリックします。

1. サンプルのホテルの部屋の JSON ファイルを格納するために、**hotel-rooms** という名前の [BLOB コンテナーを作成](../storage/blobs/storage-quickstart-blobs-portal.md)します。 パブリック アクセス レベルは、有効な任意の値に設定できます。

   ![BLOB コンテナーを作成する](media/tutorial-multiple-data-sources/blob-add-container.png "BLOB コンテナーを作成する")

1. コンテナーが作成されたら、コンテナーを開いてコマンド バーの **[アップロード]** を選択します。 サンプル ファイルを含むフォルダーに移動します。 すべてを選択し、 **[アップロード]** をクリックします。

   ![ファイルをアップロードする](media/tutorial-multiple-data-sources/blob-upload.png "ファイルをアップロードする")

アップロードが完了すると、データ コンテナーの一覧にこのファイルが表示されます。

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

3 番目のコンポーネントは Azure Cognitive Search であり、[ポータルで作成](search-create-service-portal.md)できます。 このチュートリアルは Free レベルを使用して完了できます。 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Cognitive Search のための管理者 API キーと URL を取得する

自分の Azure Cognitive Search サービスを操作するには、サービスの URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Cognitive Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

   クエリ キーも入手します。 読み取り専用アクセスを使用してクエリ要求を発行することをお勧めします。

   ![サービス名、管理キー、クエリ キーの取得](media/search-get-started-nodejs/service-name-and-keys.png)

有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

## <a name="2---set-up-your-environment"></a>2 - 環境を設定する

1. Visual Studio 2019 を起動し、 **[ツール]** メニューから、 **[Nuget パッケージ マネージャー]** 、 **[ソリューションの NuGet パッケージの管理]** の順に選択します。 

1. **[Browse]\(参照\)** タブで、**Microsoft.Azure.Search** (バージョン 9.0.1 以降) を見つけてインストールします。 インストールを完了するには、クリックしてさらにダイアログを進む必要があります。

    ![NuGet を使用して Azure ライブラリを追加する](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. 同様に、**Microsoft.Extensions.Configuration.Json** NuGet パッケージを検索してインストールします。

1. ソリューション ファイル **AzureSearchMultipleDataSources.sln** を開きます。

1. ソリューション エクスプローラーで、**appsettings.json** ファイルを編集し、接続情報を追加します。  

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "BlobStorageAccountName": "Put your Azure Storage account name here",
      "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
      "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

最初の 2 つのエントリでは、Azure Cognitive Search サービスの URL と管理者キーを使用します。 たとえば、エンドポイントが `https://mydemo.search.windows.net` の場合、指定するサービス名は `mydemo` となります。

それ以降のエントリでは、Azure Blob Storage および Azure Cosmos DB データ ソースのアカウント名と接続文字列情報を指定します。

## <a name="3---map-key-fields"></a>3 - キー フィールドをマップする

コンテンツをマージするには、両方のデータ ストリームが検索インデックス内の同じドキュメントを対象としている必要があります。 

Azure Cognitive Search では、各ドキュメントがキー フィールドによって一意に識別されます。 それぞれの検索インデックスには、 `Edm.String`型のキー フィールドが 1 つだけ必要です。 そのキー フィールドは、インデックスに追加されるデータ ソース内のドキュメントごとに存在する必要があります。 (実際のところ、これは唯一の必須フィールドです)。

複数のデータ ソースのデータにインデックスを作成するときは、入力行または入力ドキュメントに共通のドキュメント キーが存在することを確認したうえで、物理的に異なる 2 つのソース ドキュメントのデータを結合インデックスの新しい検索ドキュメントにマージしてください。 

多くの場合、インデックスにとって意味のあるドキュメント キーを特定し、両方のデータ ソースに存在させるには、事前の計画が必要です。 このデモでは、Cosmos DB の各ホテルの `HotelId` キーは、Blob Storage の部屋の JSON BLOB にも存在します。

Azure Cognitive Search インデクサーでは、フィールド マッピングを使用して、インデックス作成プロセス中にデータ フィールドの名前を変更したり、データ フィールドを再フォーマットしたりすることができます。これにより、ソース データを適切なインデックス フィールドに対応させることができます。 たとえば Cosmos DB では、ホテルの識別子に **`HotelId`** という名前が付けられています。 一方、ホテルの部屋の JSON BLOB ファイルでは、ホテルの識別子に **`Id`** という名前が付けられています。 このプログラムでは、これを処理するために、BLOB の **`Id`** フィールドをインデックスの **`HotelId`** キー フィールドにマップします。

> [!NOTE]
> ほとんどの場合、自動生成されたドキュメント キー (一部のインデクサーによって既定で作成されるものなど) は、結合インデックスに適切なドキュメント キーにはなりません。 一般的には、対象のデータ ソースに既に存在するか簡単に追加できる、わかりやすい一意のキー値を使用します。

## <a name="4---explore-the-code"></a>4 - コードを調べる

データと構成設定が済んだら、**AzureSearchMultipleDataSources.sln** 内のサンプル プログラムをビルドして実行する準備は完了です。

この単純な C# または .NET コンソール アプリは次のタスクを実行します。

* (Address クラスと Room クラスも参照する) C# Hotel クラスのデータ構造に基づいて新しいインデックスを作成します。
* 新しいデータソースと、インデックス フィールドに Azure Cosmos DB データをマップするインデクサーを作成します。 これらはどちらも Azure Cognitive Search のオブジェクトです。
* インデクサーを実行して Cosmos DB からホテル データを読み込みます。
* 2 番目のデータ ソースと、JSON BLOB データをインデックス フィールドにマップするインデクサーを作成します。
* 2 番目のインデクサーを実行して、Blob ストレージから部屋データを読み込みます。

 プログラムを実行する前に、このサンプルのコードのほかインデックスとインデクサーの定義を時間を取って確認しましょう。 関連するコードは次の 2 つのファイルにあります。

  + **Hotel.cs** には、インデックスを定義するスキーマが含まれています。
  + **Program.cs** には、Azure Cognitive Search のインデックス、データ ソース、インデクサーを作成し、結合された結果をそのインデックスに読み込む関数が含まれています。

### <a name="create-an-index"></a>インデックスを作成する

このサンプル プログラムでは、.NET SDK を使用して、Azure Cognitive Search のインデックスを定義および作成します。 [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder) クラスを利用して、C# データ モデル クラスからインデックス構造を生成します。

このデータ モデルは、Address クラスと Room クラスへの参照も含む Hotel クラスで定義されています。 FieldBuilder は、複数のクラス定義をドリルダウンして、このインデックスの複雑なデータ構造を生成します。 メタデータ タグは、検索や並べ替えが可能かどうかなど、各フィールドの属性を定義するために使用されます。

**Hotel.cs** ファイルの次のスニペットは、単一のフィールドと、別のデータ モデル クラスへの参照を指定する方法を示しています。

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

**Program.cs** ファイルでは、インデックスが、`FieldBuilder.BuildForType<Hotel>()` メソッドで生成された名前とフィールド コレクションを使用して定義され、次のように作成されます。

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Azure Cosmos DB データ ソースとインデクサーを作成する

次に、メイン プログラムには、ホテルのデータ用の Azure Cosmos DB データ ソースを作成するためのロジックが含まれています。

最初に、Azure Cosmos DB データベース名を接続文字列に連結します。 その後、[useChangeDetection] プロパティなど、Azure Cosmos DB ソースに固有の設定を含むデータ ソース オブジェクトを定義します。

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

データ ソースが作成された後、このプログラムは、**hotel-rooms-cosmos-indexer** という名前の Azure Cosmos DB インデクサーを設定します。

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
このプログラムは、この例を複数回実行する場合に備えて、新しいインデクサーを作成する前に同じ名前の既存のインデクサーを削除します。

この例では、1 日に 1 回実行されるようにインデクサーのスケジュールが定義されています。 今後自動的にインデクサーが再実行されないようにする場合は、この呼び出しから schedule プロパティを削除できます。

### <a name="index-azure-cosmos-db-data"></a>Azure Cosmos DB のデータにインデックスを付ける

データ ソースとインデクサーの作成が完了したら、インデクサーを実行するコードは簡単です。

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

この例には、実行中にエラーが発生した場合にレポートするための簡単な try-catch ブロックが含まれています。

Azure Cosmos DB インデクサーの実行が完了すると、検索インデックスには、サンプルのホテルのドキュメント一式が含まれます。 ただし、Azure Cosmos DB データ ソースには部屋の詳細が含まれていないため、各ホテルの部屋フィールドは空の配列になります。 次に、プログラムは、Blob Storage からプルし、部屋のデータを読み込んでマージします。

### <a name="create-blob-storage-data-source-and-indexer"></a>Blob Storage のデータ ソースとインデクサーを作成する

部屋の詳細を取得するために、このプログラムでは、最初に、個々の JSON BLOB ファイルのセットを参照するよう Blob Storage データ ソースを設定します。

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

データ ソースが作成された後、このプログラムでは、**hotel-rooms-blob-indexer** という名前の BLOB インデクサーを設定します。

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

JSON BLOB には、 **`HotelId`** ではなく、 **`Id`** という名前のキー フィールドが含まれています。 このコードでは、`FieldMapping` クラスを使用して、 **`Id`** フィールド値をインデックス内の **`HotelId`** ドキュメント キーに対応させるようインデクサーに指示しています。

Blob Storage インデクサーでは、使用する解析モードを指定するパラメーターを使用できます。 この解析モードは、1 つのドキュメントを表す BLOB、または同じ BLOB 内の複数のドキュメントで異なります。 この例では、各 BLOB が単一のインデックス ドキュメントを表します。そのため、このコードでは `IndexingParameters.ParseJson()` パラメーターを使用しています。

JSON BLOB 用のインデクサー解析パラメーターの詳細については、[JSON BLOB のインデックス作成](search-howto-index-json-blobs.md)に関するページを参照してください。 .NET SDK を使用したこれらのパラメーターの指定の詳細については、[IndexerParametersExtension](/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) クラスに関するページを参照してください。

このプログラムは、この例を複数回実行する場合に備えて、新しいインデクサーを作成する前に同じ名前の既存のインデクサーを削除します。

この例では、1 日に 1 回実行されるようにインデクサーのスケジュールが定義されています。 今後自動的にインデクサーが再実行されないようにする場合は、この呼び出しから schedule プロパティを削除できます。

### <a name="index-blob-data"></a>BLOB データにインデックスを付ける

Blob Storage データ ソースとインデクサーの作成が完了したら、インデクサーを実行するコードは簡単です。

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

インデックスには Azure Cosmos DB データベースのホテルのデータが既に設定されているため、BLOB インデクサーを使用して、インデックス内の既存のドキュメントを更新し、部屋の詳細を追加します。

> [!NOTE]
> 対象のデータ ソースの両方に同じ非キー フィールドがあり、それらのフィールド内のデータが一致しない場合は、直近で実行されたインデクサーの値がインデックスに含まれます。 この例では、両方のデータ ソースに **HotelName** フィールドが含まれています。 なんらかの理由により同じキー値を持つドキュメントについてこのフィールドのデータが異なる場合、直近でインデックス付けされたデータ ソースの **HotelName** データが、インデックスに格納される値になります。

## <a name="5---search"></a>5 - 検索する

プログラムの実行後に、ポータルの [**Search エクスプローラー**](search-explorer.md)を使用して、設定された検索インデックスを確認できます。

Azure portal で、検索サービスの **[概要]** ページを開き、 **[インデックス]** 一覧内で **hotel-rooms-sample** インデックスを見つけます。

  ![Azure Cognitive Search インデックスの一覧](media/tutorial-multiple-data-sources/index-list.png "Azure Cognitive Search インデックスの一覧")

一覧にある hotel-rooms-sample インデックスをクリックします。 そのインデックスの Search エクスプローラーのインターフェイスが表示されます。 "Luxury" のような用語のクエリを入力します。 結果には少なくとも 1 つのドキュメントが表示されます。また、このドキュメントには、その Rooms 配列内の部屋オブジェクトの一覧が示されます。

## <a name="reset-and-rerun"></a>リセットして再実行する

開発の初期の実験的な段階では、設計反復のための最も実用的なアプローチは、Azure Cognitive Search からオブジェクトを削除してリビルドできるようにすることです。 リソース名は一意です。 オブジェクトを削除すると、同じ名前を使用して再作成することができます。

このチュートリアルのサンプル コードでは、コードを再実行できるよう、既存のオブジェクトをチェックしてそれらを削除しています。

ポータルを使用して、インデックス、インデクサー、およびデータ ソースを削除することもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

所有するサブスクリプションを使用している場合は、プロジェクトの終了時に、不要になったリソースを削除することをお勧めします。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ペインにある [すべてのリソース] または [リソース グループ] リンクを使って、リソースを検索および管理できます。

## <a name="next-steps"></a>次のステップ

複数ソースからのデータ取り込みの概念を理解したら、まず Cosmos DB を例にインデクサーの構成について詳しく見てみましょう。

> [!div class="nextstepaction"]
> [Azure Cosmos DB インデクサーの構成](search-howto-index-cosmosdb.md)