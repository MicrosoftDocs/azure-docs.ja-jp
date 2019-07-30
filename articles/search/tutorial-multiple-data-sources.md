---
title: チュートリアル:複数のデータ ソースにインデックスを付ける - Azure Search
description: 複数のデータ ソースから 1 つの Azure Search インデックスにデータをインポートする方法を学習します。
author: RobDixon22
manager: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: v-rodixo
ms.custom: seodec2018
ms.openlocfilehash: 8ce3c66432f3d2d0cb973886498aa46e7820698c
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485253"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-search-index"></a>C# のチュートリアル: 複数のデータ ソースのデータを 1 つの Azure Search インデックスに結合する

Azure Search では、複数のデータ ソースから 1 つの結合検索インデックスにデータをインポートし、分析、インデックス付けを行うことができます。 これは、構造化データが、他のソースのあまり構造化されていないデータやプレーンテキスト データ (テキスト、HTML、JSON ドキュメントなど) と共に集計される状況をサポートします。

このチュートリアルでは、Azure Cosmos DB データ ソースのホテルのデータにインデックスを付け、それを Azure Blob Storage ドキュメントから取得したホテルの部屋の詳細とマージする方法について説明します。 その結果、結合されたホテル検索インデックスには複雑なデータ型が含まれます。

このチュートリアルでは、C#、Azure Search 用 .NET SDK、Azure portal を使用して次のタスクを実行します。

> [!div class="checklist"]
> * サンプル データをアップロードしてデータ ソースを作成する
> * ドキュメント キーを識別する
> * インデックスを定義して作成する
> * Azure Cosmos DB のホテルのデータにインデックスを付ける
> * Blob Storage のホテルの部屋のデータをマージする

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、次のサービス、ツール、およびデータを使用します。 

- [Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このチュートリアル用には、無料のサービスを使用できます。

- サンプルのホテルのデータを格納するための [Azure Cosmos DB アカウントを作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)します。

- サンプルの JSON BLOB データを格納するための [Azure ストレージ アカウントを作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)します。

- IDE として使用するために [Visual Studio をインストール](https://visualstudio.microsoft.com/)します。

### <a name="install-the-project-from-github"></a>GitHub からプロジェクトをインストールする

1. GitHub 上でサンプル リポジトリ [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) を見つけます。
1. **[Clone or download]\(クローンまたはダウンロード\)** を選択し、リポジトリのプライベート ローカル コピーを作成します。
1. Visual Studio を開き、Microsoft Azure Search NuGet パッケージをまだインストールしていない場合はインストールします。 **[ツール]** メニューの **[NuGet パッケージ マネージャー]** 、 **[ソリューションの NuGet パッケージの管理]** の順に選択します。 **[参照]** タブを選択し、検索ボックスに「Azure Search」と入力します。 **[Microsoft.Azure.Search]** が一覧に表示されたら、それをインストールします (バージョン 9.0.1 またはそれ以降)。 インストールを完了するには、クリックしてさらにダイアログを進む必要があります。

    ![NuGet を使用して Azure ライブラリを追加する](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Visual Studio を使用して、自分のローカル リポジトリに移動し、**AzureSearchMultipleDataSources.sln** ソリューション ファイルを開きます。

## <a name="get-a-key-and-url"></a>キーと URL を入手する

自分の Azure Search サービスを操作するには、サービスの URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理者キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

![HTTP エンドポイントとアクセス キーを取得する](media/search-get-started-postman/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーにより、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼が確立されます。

## <a name="prepare-sample-azure-cosmos-db-data"></a>サンプルの Azure Cosmos DB データを準備する

このサンプルでは、7 つの架空のホテルを説明する小さなデータ セットを 2 つ使用します。 一方のセットは、ホテル自体を説明するもので、Azure Cosmos DB データベースに読み込まれます。 もう一方のセットは、ホテルの部屋の詳細を含んでおり、Azure Blob Storage にアップロードする 7 つの個別の JSON ファイルとして提供されます。

1. [Azure portal にサインイン](https://portal.azure.com)し、Azure Cosmos DB アカウントの [概要] ページに移動します。

1. メニュー バーから、[コンテナーの追加] をクリックします。 "新しいデータベースの作成" を指定し、**hotel-rooms-db** という名前を使用します。 コレクション名には「**hotel-rooms**」と入力し、パーティション キーには「 **/HotelId**」と入力します。 **[OK]** をクリックしてデータベースとコンテナーを作成します。

   ![Azure Cosmos DB コンテナーを追加する](media/tutorial-multiple-data-sources/cosmos-add-container.png "Azure Cosmos DB コンテナーを追加する")

1. Cosmos DB データ エクスプローラーに移動し、**hotel-rooms-db** データベース内の **hotels** コンテナーの下にある **items** 要素を選択します。 次に、コマンド バー上の **[Upload Item]\(項目のアップロード\)** をクリックします。

   ![Azure Cosmos DB コレクションにアップロードする](media/tutorial-multiple-data-sources/cosmos-upload.png "Cosmos DB コレクションにアップロードする")

1. アップロード パネルで、フォルダー ボタンをクリックし、プロジェクト フォルダー内の **cosmosdb/HotelsDataSubset_CosmosDb.json** ファイルに移動します。 **[OK]** をクリックしてアップロードを開始します。

   ![アップロードするファイルを選択する](media/tutorial-multiple-data-sources/cosmos-upload2.png "アップロードするファイルを選択する")

1. [最新の情報に更新] ボタンを使用して、hotels コレクション内の項目の表示を更新します。 7 つの新しいデータベース ドキュメントが表示されることがわかります。

## <a name="prepare-sample-blob-data"></a>サンプル BLOB データを準備する

1. [Azure portal にサインインし](https://portal.azure.com)、Azure ストレージ アカウントに移動して **[BLOB]** をクリックし、 **[+ コンテナー]** をクリックします。

1. サンプルのホテルの部屋の JSON ファイルを格納するために、**hotel-rooms** という名前の [BLOB コンテナーを作成](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)します。 パブリック アクセス レベルは、有効な任意の値に設定できます。

   ![BLOB コンテナーを作成する](media/tutorial-multiple-data-sources/blob-add-container.png "BLOB コンテナーを作成する")

1. コンテナーが作成されたら、コンテナーを開いてコマンド バーの **[アップロード]** を選択します。

   ![コマンド バーの [アップロード]](media/search-semi-structured-data/upload-command-bar.png "コマンド バーの [アップロード]")

1. サンプル ファイルを含むフォルダーに移動します。 すべてを選択し、 **[アップロード]** をクリックします。

   ![ファイルをアップロードする](media/tutorial-multiple-data-sources/blob-upload.png "ファイルをアップロードする")

アップロードが完了すると、データ コンテナーの一覧にこのファイルが表示されます。

## <a name="set-up-connections"></a>接続を設定する

検索サービスとデータ ソースの接続情報は、ソリューション内の **appsettings.json** ファイルで指定します。 

1. Visual Studio で、**AzureSearchMultipleDataSources.sln** ファイルを開きます。

1. ソリューション エクスプローラーで、**appsettings.json** ファイルを編集します。  

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

最初の 2 つのエントリでは、Azure Search サービスの URL と管理者キーを使用します。 たとえば、エンドポイントが `https://mydemo.search.windows.net` の場合、指定するサービス名は `mydemo` となります。

それ以降のエントリでは、Azure Blob Storage および Azure Cosmos DB データ ソースのアカウント名と接続文字列情報を指定します。

### <a name="identify-the-document-key"></a>ドキュメント キーを識別する

Azure Search では、キー フィールドはインデックス内の各ドキュメントを一意に識別します。 それぞれの検索インデックスには、 `Edm.String`型のキー フィールドが 1 つだけ必要です。 そのキー フィールドは、インデックスに追加されるデータ ソース内のドキュメントごとに存在する必要があります。 (実際のところ、これは唯一の必須フィールドです)。

複数のデータ ソースのデータにインデックスを付ける場合、各データ ソースのキー値を結合インデックスの同じキー フィールドにマップする必要があります。 それには、インデックスにとって意味のあるドキュメント キーを識別し、それがすべてのデータ ソースに存在することを確認するように事前の計画が必要になることがよくあります。

Azure Search インデクサーでは、フィールド マッピングを使用して、インデックス作成プロセス中にデータ フィールドの名前を変更したり、データ フィールドを再フォーマットしたりすることができます。これにより、ソース データを適切なインデックス フィールドに対応させることができます。

たとえば、この Azure Cosmos DB のサンプル データでは、ホテルの識別子に **HotelId** という名前が付けられています。 一方、ホテルの部屋の JSON BLOB ファイルでは、ホテルの識別子に **Id** という名前が付けられています。このプログラムでは、これを処理するために、BLOB の **Id** フィールドをインデックスの **HotelId** キー フィールドにマップします。

> [!NOTE]
> ほとんどの場合、自動生成されたドキュメント キー (一部のインデクサーによって既定で作成されるものなど) は、結合インデックスに適切なドキュメント キーにはなりません。 一般的には、対象のデータ ソースに既に存在するか簡単に追加できる、わかりやすい一意のキー値を使用します。

## <a name="understand-the-code"></a>コードの理解

データと構成設定が済んだら、**AzureSearchMultipleDataSources.sln** 内のサンプル プログラムをビルドして実行する準備は完了です。

この単純な C# または .NET コンソール アプリは次のタスクを実行します。
* (Address クラスと Room クラスも参照する) C# Hotel クラスのデータ構造に基づいて新しい Azure Search インデックスを作成する。
* Azure Cosmos DB データ ソースと、Azure Cosmos DB データをインデックス フィールドにマップするインデクサーを作成する。
* Azure Cosmos DB インデクサーを実行してホテルのデータを読み込む。
* Azure Blob Storage データ ソースと、JSON BLOB データをインデックス フィールドにマップするインデクサーを作成する。
* Azure Blob Storage インデクサーを実行して部屋のデータを読み込む。

 プログラムを実行する前に、このサンプルのコードのほかインデックスとインデクサーの定義を時間を取って確認しましょう。 関連するコードは次の 2 つのファイルにあります。

  + **Hotel.cs** には、インデックスを定義するスキーマが含まれています。
  + **Program.cs** には、Azure Search のインデックス、データ ソース、インデクサーを作成し、結合された結果をそのインデックスに読み込む関数が含まれています。

### <a name="define-the-index"></a>インデックスを定義する

このサンプル プログラムでは、.NET SDK を使用して、Azure Search のインデックスを定義および作成します。 [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) クラスを利用して、C# データ モデル クラスからインデックス構造を生成します。

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

JSON BLOB には、**HotelId** の代わりに **Id** という名前のキー フィールドが含まれます。 このコードでは、`FieldMapping` クラスを使用して、**Id** フィールド値をインデックス内の **HotelId** ドキュメント キーに対応させるようインデクサーに指示しています。

Blob Storage インデクサーでは、使用する解析モードを指定するパラメーターを使用できます。 この解析モードは、1 つのドキュメントを表す BLOB、または同じ BLOB 内の複数のドキュメントで異なります。 この例では、各 BLOB が単一のインデックス ドキュメントを表します。そのため、このコードでは `IndexingParameters.ParseJson()` パラメーターを使用しています。

JSON BLOB 用のインデクサー解析パラメーターの詳細については、[JSON BLOB のインデックス作成](search-howto-index-json-blobs.md)に関するページを参照してください。 .NET SDK を使用したこれらのパラメーターの指定の詳細については、[IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) クラスに関するページを参照してください。

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

## <a name="search-your-json-files"></a>JSON ファイルの検索

プログラムの実行後に、ポータルの [**Search エクスプローラー**](search-explorer.md)を使用して、設定された検索インデックスを確認できます。

Azure portal で、検索サービスの **[概要]** ページを開き、 **[インデックス]** 一覧内で **hotel-rooms-sample** インデックスを見つけます。

  ![Azure Search のインデックスの一覧](media/tutorial-multiple-data-sources/index-list.png "Azure Search のインデックスの一覧")

一覧にある hotel-rooms-sample インデックスをクリックします。 そのインデックスの Search エクスプローラーのインターフェイスが表示されます。 "Luxury" のような用語のクエリを入力します。 結果には少なくとも 1 つのドキュメントが表示されます。また、このドキュメントには、その Rooms 配列内の部屋オブジェクトの一覧が示されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルの後で最も速くクリーンアップする方法は、Azure Search サービスが含まれているリソース グループを削除することです。 リソース グループを削除することで、そのすべての内容を完全に削除することができます。 ポータルでは、リソース グループ名が Azure Search サービスの [概要] ページに表示されます。

## <a name="next-steps"></a>次の手順

JSON BLOB のインデックス作成には、いくつかの方法と複数のオプションがあります。 対象のソース データに JSON コンテンツが含まれている場合は、これらのオプションの中から自分のシナリオに最適なものを見つけてください。

> [!div class="nextstepaction"]
> [Azure Search BLOB インデクサーを使用して JSON BLOB のインデックスを作成する方法](search-howto-index-json-blobs.md)

1 つのデータ ソースの構造化インデックス データは、構造化されていない BLOB またはフルテキスト コンテンツの認知的に優れたデータで強化することができます。 次のチュートリアルでは、.NET SDK を使用して Cognitive Services を Azure Search と共に使用する方法を紹介します。

> [!div class="nextstepaction"]
> [Azure Search のインデックス作成パイプラインで Cognitive Services APIs を呼び出す](cognitive-search-tutorial-blob-dotnet.md)
