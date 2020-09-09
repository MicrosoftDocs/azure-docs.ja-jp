---
title: Azure BLOB で AI を使用する C# チュートリアル
titleSuffix: Azure Cognitive Search
description: C# と Azure Cognitive Search .NET SDK を使用した、Blob Storage のコンテンツに対するテキスト抽出と自然言語処理の例を、順を追って説明します。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 08/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 09273cf901830e850acca05c57c0b110ffd7e28a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002864"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>チュートリアル:.NET SDK を使用して Azure BLOB から AI で生成する検索可能なコンテンツ

Azure Blob Storage に非構造化テキストまたは画像がある場合、[AI エンリッチメント パイプライン](cognitive-search-concept-intro.md)で情報を抽出し、フルテキスト検索やナレッジ マイニングのシナリオに役立つ新しいコンテンツを作成することができます。 この C# チュートリアルでは、画像に光学式文字認識 (OCR) を適用し、自然言語処理を実行して、クエリ、ファセット、フィルターで活用できる新しいフィールドを作成します。

このチュートリアルでは、C# と [.NET SDK](/dotnet/api/overview/azure/search) を使用して次のタスクを実行します。

> [!div class="checklist"]
> * Azure Blob Storage にアプリケーション ファイルと画像を準備する。
> * OCR、テキスト抽出、言語検出、エンティティ、キー フレーズ認識を追加するためのパイプラインを定義する。
> * 出力 (生コンテンツと、パイプラインで生成された名前と値のペア) を格納するためのインデックスを定義する。
> * 変換と分析を開始し、インデックスを作成して読み込むパイプラインを実行する。
> * フルテキスト検索と豊富なクエリ構文を使用して結果を探索する。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

+ [Azure ストレージ](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [作成](search-create-service-portal.md)または[既存の検索サービスの用意](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> このチュートリアルには無料のサービスを使用できます。 無料の検索サービスでは、3 つのインデックス、3 つのインデクサー、3 つのデータ ソースという制限があります。 このチュートリアルでは、それぞれ 1 つずつ作成します。 開始する前に、ご利用のサービスに新しいリソースを受け入れる余地があることを確認してください。

## <a name="download-files"></a>ファイルのダウンロード

1. こちらの [OneDrive フォルダー](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)を開き、左上隅の **[ダウンロード]** をクリックして、コンピューターにファイルをコピーします。 

1. ZIP ファイルを右クリックし、 **[すべて展開]** を選択します。 さまざまな種類のファイルが 14 個あります。 この演習では、7 個を使用します。

このチュートリアルのソース コードをダウンロードすることもできます。 ソース コードは、[azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) リポジトリの tutorial-ai-enrichment フォルダーにあります。

## <a name="1---create-services"></a>1 - サービスを作成する

このチュートリアルでは、インデックス作成とクエリに Azure Cognitive Search を使用するほか、AI エンリッチメントにはバックエンドで Cognitive Services を、データ提供には Azure Blob Storage を使用します。 このチュートリアルは、Cognitive Services の無料割り当て分 (インデクサーあたり 1 日に 20 トランザクション) を超えないようにしているため、作成する必要のあるサービスは Search と Storage だけです。

可能である場合は、近接性と管理性を高めるために、両方を同じリージョンおよびリソース グループ内に作成してください。 実際には、Azure Storage アカウントは任意のリージョンに置くことができます。

### <a name="start-with-azure-storage"></a>Azure Storage の使用を開始する

1. [Azure portal にサインイン](https://portal.azure.com/)し、 **[+ リソースの作成]** をクリックします。

1. *[ストレージ アカウント]* を検索し、Microsoft のストレージ アカウント オファリングを選択します。

   ![ストレージ アカウントの作成](media/cognitive-search-tutorial-blob/storage-account.png "ストレージ アカウントの作成")

1. [基本] タブでは、次の項目が必要です。 それ以外のすべてのものには、既定値をそのまま使用します。

   + **リソース グループ**。 既存のものを選択するか、新しいものを作成します。ただし、すべてのサービスに同じグループを使用して、それらをまとめて管理できるようにします。

   + **ストレージ アカウント名**。 同じ種類のリソースが複数存在することになると考えられる場合は、名前を使用して、種類とリージョンを基に区別が付くようにします (たとえば、*blobstoragewestus*)。 

   + **場所**。 可能であれば、Azure Cognitive Search と Cognitive Services に使用するのと同じ場所を選択します。 1 つの場所であれば、帯域幅の料金がかかりません。

   + **アカウントの種類**。 既定値の *[StorageV2 (general purpose v2)]\(StorageV2 (汎用 v2)\)* を選択します。

1. **[確認および作成]** をクリックしてサービスを作成します。

1. 作成されたら、 **[Go to the resource]\(リソースに移動\)** をクリックして [概要] ページを開きます。

1. **[BLOB]** サービスをクリックします。

1. **[+ コンテナー]** をクリックしてコンテナーを作成し、*cog-search-demo* という名前を付けます。

1. *[cog-search-demo]* を選択し、 **[アップロード]** をクリックして、ダウンロード ファイルを保存したフォルダーを開きます。 14 ファイルすべてを選択し、 **[OK]** をクリックしてアップロードします。

   ![サンプル ファイルをアップロードする](media/cognitive-search-quickstart-blob/sample-data.png "サンプル ファイルをアップロードする")

1. Azure Storage を終了する前に、Azure Cognitive Search で接続を作成できるように、接続文字列を取得します。 

   1. 自分のストレージ アカウント (例として *blobstoragewestus* を使用しています) の [概要] ページに戻ります。 
   
   1. 左側のナビゲーション ウィンドウで、 **[アクセス キー]** を選択し、いずれかの接続文字列をコピーします。 

   接続文字列は、次の例のような URL です。

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. メモ帳に接続文字列を保存します。 これは、後でデータ ソース接続を設定するときに必要になります。

### <a name="cognitive-services"></a>Cognitive Services

AI エンリッチメントは、自然言語と画像の処理のための Text Analytics や Computer Vision など、Cognitive Services によってサポートされています。 実際のプロトタイプまたはプロジェクトを完成させることが目的であれば、この時点で (Azure Cognitive Search と同じリージョンに) Cognitive Services をプロビジョニングして、インデックス作成操作にアタッチできるようにします。

ただし、この演習では、Azure Cognitive Search がバックグラウンドで Cognitive Services に接続し、インデクサーの実行ごとに 20 個の無料トランザクションを提供できるため、リソースのプロビジョニングをスキップできます。 このチュートリアルで使用するトランザクションは 14 個であるため、無料の割り当てで十分です。 より大規模なプロジェクトの場合は、従量課金制の S0 レベルで Cognitive Services をプロビジョニングすることを計画してください。 詳細については、[Cognitive Services のアタッチ](cognitive-search-attach-cognitive-services.md)に関するページを参照してください。

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

まず Visual Studio を開き、.NET Core 上で実行する新しいコンソール アプリ プロジェクトを作成します。

### <a name="install-nuget-packages"></a>NuGet パッケージのインストール

[Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search) は､HTTP や JSON に関する詳しい知識がなくても､インデックスやデータ ソース､インデクサー､スキルセットの管理､ドキュメントのアップロードと管理､クエリの実行を行うことを可能にするいくつかのクライアント ライブラリから構成されています。 これらのクライアント ライブラリはすべて､NuGet パッケージとして配布されます｡

このプロジェクトでは、`Microsoft.Azure.Search` NuGet パッケージのバージョン 9 以降をインストールします。

1. ブラウザーで [Microsoft.Azure.Search NuGet パッケージ ページ](https://www.nuget.org/packages/Microsoft.Azure.Search)に移動します。

1. 最新バージョン (9 以降) をインストールします。

1. パッケージ マネージャー コマンドをコピーします。

1. パッケージ マネージャー コンソールを開きます。 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に選択します。 

1. 前の手順でコピーしたコマンドを貼り付けて実行します。

次に、最新の `Microsoft.Extensions.Configuration.Json` NuGet パッケージをインストールします。

1. **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[ソリューションの NuGet パッケージの管理]** の順に選択します。 

1. **[参照]** をクリックし、`Microsoft.Extensions.Configuration.Json` NuGet パッケージを検索します。 

1. パッケージを選択してプロジェクトを選択し、最新の安定したバージョンであることを確認してから、 **[インストール]** をクリックします。

### <a name="add-service-connection-information"></a>サービス接続情報の追加

1. ソリューション エクスプローラーでプロジェクトを右クリックし、 **[追加]**  >  **[新しい項目]** を選択します。 

1. ファイルに `appsettings.json` という名前を付けて、 **[追加]** を選択します。 

1. このファイルを出力ディレクトリに追加します。
    1. `appsettings.json` を右クリックし、 **[プロパティ]** を選択します。 
    1. **[出力ディレクトリにコピー]** の値を **[新しい場合はコピーする]** に変更します。

1. 次の JSON を新しい JSON ファイルにコピーします。

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```
    
検索サービスと BLOB ストレージ アカウントの情報を追加します。 ご存じのように、この情報は、前のセクションで示したサービス プロビジョニング手順から入手することができます。

**[SearchServiceName]** には、短いサービス名を入力してください。完全な URL ではありません。

### <a name="add-namespaces"></a>名前空間の追加

`Program.cs` に次の名前空間を追加します。

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>クライアントの作成

`Main` で `SearchServiceClient` クラスのインスタンスを作成します。

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` は、アプリケーションの構成ファイル (appsettings.json) に格納されている値を使用して、新しい `SearchServiceClient` を作成します。

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
> `SearchServiceClient` クラスは検索サービスへの接続を管理します。 開いている接続の数が多くなりすぎないよう、可能であれば、アプリケーションで `SearchServiceClient` の単一のインスタンスを共有する必要があります。 SearchServiceClient のメソッドはスレッド セーフなので、このような共有が可能です。
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>障害時にプログラムを終了するための関数を追加する

このチュートリアルは、インデックス作成パイプラインの各ステップを読者にわかりやすく伝えることを意図しています。 データ ソース、スキルセット、インデックス、またはインデクサーの作成に支障が生じるような重大な問題が発生した場合、プログラムは問題を伝え、対応を促すために、エラー メッセージを出力して終了します。

プログラムの終了を余儀なくされるようなシナリオには、`ExitProgram` を `Main` に追加することで対応します。

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 - パイプラインを作成する

Azure Cognitive Search では、AI 処理はインデックス作成 (またはデータ インジェスト) 中に行われます。 チュートリアルのこの部分では、データ ソース、インデックス定義、スキルセット、インデクサーという 4 つのオブジェクトを作成します。 

### <a name="step-1-create-a-data-source"></a>手順 1:データ ソースを作成する

`SearchServiceClient` には `DataSources` プロパティがあります。 このプロパティは、Azure Cognitive Search データ ソースの作成、一覧表示、更新、または削除に必要なすべてのメソッドを提供します。

`serviceClient.DataSources.CreateOrUpdate(dataSource)` を呼び出して新しい `DataSource` を作成します。 `DataSource.AzureBlobStorage` では、データ ソース名、接続文字列、および BLOB コンテナー名を指定することが必要です。

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "cog-search-demo",
        description: "Demo files to demonstrate cognitive search capabilities.");

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.DataSources.CreateOrUpdate(dataSource);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

要求が成功すると、作成されたデータ ソースがメソッドから返されます。 無効なパラメーターなど、要求に問題がある場合、メソッドは例外をスローします。

先ほど追加した `CreateOrUpdateDataSource` 関数を呼び出す行を `Main` に追加します。

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    // Create or Update the data source
    Console.WriteLine("Creating or updating the data source...");
    DataSource dataSource = CreateOrUpdateDataSource(serviceClient, configuration);
```

ソリューションをビルドして実行します。 これは最初の要求のため、Azure portal を調べて、データ ソースが Azure Cognitive Search で作成されたことを確認します。 Search サービスのダッシュボード ページで、[データ ソース] タイルに新しい項目があることを確認します。 Portal のページが更新されるまで数分かかる場合があります。

  ![ポータルの [データ ソース] タイル](./media/cognitive-search-tutorial-blob/data-source-tile.png "ポータルの [データ ソース] タイル")

### <a name="step-2-create-a-skillset"></a>手順 2:スキルセットを作成する

このセクションでは、データに適用するエンリッチメント ステップのセットを定義します。 それぞれのエンリッチメント ステップを*スキル*と呼び、エンリッチメント ステップのセットを*スキルセット*と呼びます。 このチュートリアルでは、スキルセット用に次の[ビルトイン コグニティブ スキル](cognitive-search-predefined-skills.md)を使用します。

+ [光学式文字認識](cognitive-search-skill-ocr.md)。画像ファイルに印字された手書きテキストを認識します。

+ [テキスト マージ](cognitive-search-skill-textmerger.md)。フィールドのコレクションからのテキストを 1 つのフィールドに統合します。

+ [言語検出](cognitive-search-skill-language-detection.md)。コンテンツの言語を識別します。

+ [テキスト分割](cognitive-search-skill-textsplit.md)。キー フレーズ抽出スキルとエンティティ認識スキルを呼び出す前に、大きいコンテンツを小さいチャンクに分割します。 キー フレーズ抽出およびエンティティ認識では、50,000 字以内の入力を受け取ります。 いくつかのサンプル ファイルは、分割してこの制限内に収める必要があります。

+ [エンティティの認識](cognitive-search-skill-entity-recognition.md)。BLOB コンテナーのコンテンツから組織の名前を抽出します。

+ [キー フレーズ抽出](cognitive-search-skill-keyphrases.md)。上位のキー フレーズを抜き出します。

初期処理中に、Azure Cognitive Search が各ドキュメントを解析して、さまざまなファイル形式からコンテンツを読み取ります。 ソース ファイルから配信されたテキストが見つかると、ドキュメントごとに 1 つずつ、生成された ```content``` フィールドに配置されます。 そのため、```"/document/content"``` として入力を設定し、このテキストを使用します。 

出力はインデックスにマップすることができ、ダウンストリーム スキルへの入力として、または言語コードと同様に両方として使用されます。 インデックスでは、言語コードはフィルター処理に役立ちます。 入力としての言語コードは、テキスト分析スキルによって、単語区切りに基づく言語学的規則を通知するために使用されます。

スキルセットの基礎の詳細については、[スキルセットを定義する方法](cognitive-search-defining-skillset.md)に関するページをご覧ください。

### <a name="ocr-skill"></a>OCR スキル

**OCR** スキルはイメージからテキストを抽出します。 このスキルは、normalized-images フィールドが存在していることを前提としています。 このフィールドを生成するために、このチュートリアルの後半で、```"generateNormalizedImages"``` に対するインデクサー定義で ```"imageAction"``` 構成を設定します。

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "text",
        targetName: "text")
    };

    OcrSkill ocrSkill = new OcrSkill(
        description: "Extract text (plain and structured) from image",
        context: "/document/normalized_images/*",
        inputs: inputMappings,
        outputs: outputMappings,
        defaultLanguageCode: OcrSkillLanguage.En,
        shouldDetectOrientation: true);

    return ocrSkill;
}
```

### <a name="merge-skill"></a>マージ スキル

このセクションでは、ドキュメント コンテンツ フィールドを OCR スキルで生成されたテキストとマージする**マージ** スキルを作成します。

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"),
        new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"),
        new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text")
    };

    MergeSkill mergeSkill = new MergeSkill(
        description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        insertPreTag: " ",
        insertPostTag: " ");

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>言語検出スキル

**言語検出**スキルは、入力テキストの言語を検出し、要求で送信されたすべてのドキュメントごとに 1 つの言語コードを報告します。 **言語検出**スキルの出力を、**テキスト分割**スキルに対する入力の一部として使用します。

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode")
    };

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>テキスト分割スキル

以下の**分割**スキルは、ページごとにテキストを分割し、`String.Length` で測定してページ サイズを 4,000 文字に制限します。 このアルゴリズムは、テキストを最大でサイズが `maximumPageLength` のチャンクに分割しようとします。 この場合、アルゴリズムはできる限り文の境界で文を区切ろうとするため、チャンクのサイズは `maximumPageLength` よりも少し小さくなることがあります。

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"),
        new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages")
    };

    SplitSkill splitSkill = new SplitSkill(
        description: "Split content into pages",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        textSplitMode: TextSplitMode.Pages,
        maximumPageLength: 4000);

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>エンティティ認識スキル

この `EntityRecognitionSkill` インスタンスは、カテゴリの種類 `organization` を認識するように設定されています。 **エンティティ認識**スキルでは、カテゴリの種類 `person` および `location` も認識できます。

"context" フィールドが、アスタリスク付きで ```"/document/pages/*"``` に設定されていることに注目してください。これは、エンリッチメント ステップが ```"/document/pages"``` の下にある各ページごとに呼び出されることを意味します。

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations")
    };

    List<EntityCategory> entityCategory = new List<EntityCategory>
    {
        EntityCategory.Organization
    };

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
        description: "Recognize organizations",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings,
        categories: entityCategory,
        defaultLanguageCode: EntityRecognitionSkillLanguage.En);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>キー フレーズ抽出スキル

先ほど作成した `EntityRecognitionSkill` インスタンスと同様に、**キー フレーズ抽出**スキルもドキュメントのページごとに呼び出されます。

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "keyPhrases",
        targetName: "keyPhrases"));

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
        description: "Extract the key phrases",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings);

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>スキルセットを構築および作成する

作成したスキルを使用して `Skillset` を構築します。

```csharp
private static Skillset CreateOrUpdateDemoSkillSet(SearchServiceClient serviceClient, IList<Skill> skills)
{
    Skillset skillset = new Skillset(
        name: "demoskillset",
        description: "Demo skillset",
        skills: skills);

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.Skillsets.CreateOrUpdate(skillset);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

次の行を `Main` に追加します。

```csharp
    // Create the skills
    Console.WriteLine("Creating the skills...");
    OcrSkill ocrSkill = CreateOcrSkill();
    MergeSkill mergeSkill = CreateMergeSkill();
    EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
    LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
    SplitSkill splitSkill = CreateSplitSkill();
    KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

    // Create the skillset
    Console.WriteLine("Creating or updating the skillset...");
    List<Skill> skills = new List<Skill>
    {
        ocrSkill,
        mergeSkill,
        languageDetectionSkill,
        splitSkill,
        entityRecognitionSkill,
        keyPhraseExtractionSkill
    };

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>手順 3:インデックスを作成する

このセクションでは、検索可能なインデックス、および各フィールドの検索属性に含めるフィールドを指定することによって、インデックス スキーマを定義します。 フィールドには型があり、フィールドの使用方法 (検索可能、並べ替え可能など) を決定する属性を取ることができます。 インデックス内のフィールド名は、ソース内のフィールド名と完全に一致する必要はありません。 後のほうの手順では、インデクサーにフィールド マッピングを追加して、ソースとターゲットのフィールドを接続します。 この手順で、検索アプリケーションに関連するフィールドの名前付け規則を使用してインデックスを定義します。

この演習では、次のフィールドとフィールドの型を使用します。

| フィールド名 | フィールドの型 |
| --- | --- |
| id | Edm.String |
| content | Edm.String |
| languageCode | Edm.String |
| keyPhrases | List<Edm.String> |
| organizations | List<Edm.String> |

#### <a name="create-demoindex-class"></a>DemoIndex クラスを作成する

このインデックスのフィールドはモデル クラスを使用して定義されます。 モデル クラスの各プロパティには、対応するインデックス フィールドの検索に関連した動作を決定する属性があります。 

モデル クラスを新しい C# ファイルに追加します。 プロジェクトを右クリックして **[追加]**  >  **[新しい項目...]** を選択し、[クラス] を選択し、ファイルに `DemoIndex.cs` という名前を付けて、 **[追加]** を選択します。

`Microsoft.Azure.Search` および `Microsoft.Azure.Search.Models` 名前空間の種類を使用することを指定します。

以下のモデル クラス定義を `DemoIndex.cs` に追加し、インデックスを作成するのと同じ名前空間に含めます。

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
    // It ensures that Pascal-case property names in the model class are mapped to camel-case
    // field names in the index.
    [SerializePropertyNamesAsCamelCase]
    public class DemoIndex
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsSearchable, IsSortable]
        public string Id { get; set; }

        [IsSearchable]
        public string Content { get; set; }

        [IsSearchable]
        public string LanguageCode { get; set; }

        [IsSearchable]
        public string[] KeyPhrases { get; set; }

        [IsSearchable]
        public string[] Organizations { get; set; }
    }
}
```

モデル クラスの定義が完了したので、`Program.cs`に戻ると、インデックス定義を非常に簡単に作成できます。 このインデックスの名前は `demoindex` になります。 その名前のインデックスが既に存在する場合、そのインデックスは削除されます。

```csharp
private static Index CreateDemoIndex(SearchServiceClient serviceClient)
{
    var index = new Index()
    {
        Name = "demoindex",
        Fields = FieldBuilder.BuildForType<DemoIndex>()
    };

    try
    {
        bool exists = serviceClient.Indexes.Exists(index.Name);

        if (exists)
        {
            serviceClient.Indexes.Delete(index.Name);
        }

        serviceClient.Indexes.Create(index);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

テスト中に、インデックスを複数回作成しようとする場合があります。 このため、作成を試行する前に、作成しようとするインデックスが既に存在していないかを確認してください。

次の行を `Main` に追加します。

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Microsoft.Azure.Search.Models.Index demoIndex = CreateDemoIndex(serviceClient);
```

参照のあいまいさを解消するために、次の using ステートメントを追加します。

```csharp
using Index = Microsoft.Azure.Search.Models.Index;
```

インデックスの定義の詳細については、[インデックスの作成 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index) に関するページを参照してください。

### <a name="step-4-create-and-run-an-indexer"></a>手順 4:インデクサーの作成と実行

ここまでで、データ ソース、スキルセット、およびインデックスを作成しました。 これら 3 つのコンポーネントが、各要素を複数フェーズにわたる 1 つの操作にまとめて入れる[インデクサー](search-indexer-overview.md)の一部になります。 これらをまとめてインデクサーに結び付けるには、フィールド マッピングを定義する必要があります。

+ スキルセットの前に fieldMappings が処理されて、データ ソースのソース フィールドがインデックス内のターゲット フィールドにマッピングされます。 フィールド名と型が両側で共通していれば、マッピングは必要ありません。

+ outputFieldMappings は、スキルセットの後に処理されます。sourceFieldNames がドキュメント解析またはエンリッチメントによって作成されるまでは、存在しない sourceFieldNames が参照されます。 targetFieldName は、インデックス内のフィールドです。

入力を出力につなぐことに加え、フィールドのマッピングを使用して、データ構造をフラット化することもできます。 詳細については、「[強化されたフィールドを検索可能なインデックスにマップする方法](cognitive-search-output-field-mapping.md)」を参照してください。

```csharp
private static Indexer CreateDemoIndexer(SearchServiceClient serviceClient, DataSource dataSource, Skillset skillSet, Index index)
{
    IDictionary<string, object> config = new Dictionary<string, object>();
    config.Add(
        key: "dataToExtract",
        value: "contentAndMetadata");
    config.Add(
        key: "imageAction",
        value: "generateNormalizedImages");

    List<FieldMapping> fieldMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")),
        new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content")
    };

    List<FieldMapping> outputMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"),
        new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"),
        new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode")
    };

    Indexer indexer = new Indexer(
        name: "demoindexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        description: "Demo Indexer",
        skillsetName: skillSet.Name,
        parameters: new IndexingParameters(
            maxFailedItems: -1,
            maxFailedItemsPerBatch: -1,
            configuration: config),
        fieldMappings: fieldMappings,
        outputFieldMappings: outputMappings);

    try
    {
        bool exists = serviceClient.Indexers.Exists(indexer.Name);

        if (exists)
        {
            serviceClient.Indexers.Delete(indexer.Name);
        }

        serviceClient.Indexers.Create(indexer);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```
次の行を `Main` に追加します。

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer and executing the pipeline...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

インデクサーの作成が完了するまで時間は少ししかかかりません。 データ セットが小さい場合でも、分析スキルは計算を集中的に行います。 画像分析などの一部のスキルは、実行時間の長いスキルです。

> [!TIP]
> インデクサーを作成すると、パイプラインが呼び出されます。 データ、入力と出力のマッピング、または操作の順序に及ぶ問題がある場合は、この段階で現れます。

### <a name="explore-creating-the-indexer"></a>インデクサーの作成について

コードで ```"maxFailedItems"``` を -1 に設定します。これにより、インデックス作成エンジンに、データのインポート中のエラーを無視するよう指示します。 デモのデータ ソースにはドキュメントがほとんどないため、これは便利です。 大きいデータ ソースの場合は、この値を 0 より大きい値に設定します。

また、```"dataToExtract"``` が ```"contentAndMetadata"``` に設定されていることに気が付きます。 このステートメントは、さまざまなファイル形式と、各ファイルに関連するメタデータからコンテンツを、自動的に抽出するようにインデクサーに指示します。

コンテンツが抽出されるときに、`imageAction` を設定して、データ ソース内にある画像からテキストを抽出することができます。 ```"generateNormalizedImages"``` 構成に設定された ```"imageAction"``` は、OCR スキルおよびテキスト マージ スキルと組み合わされて、イメージからテキスト (たとえば、一時停止の道路標識から "stop" という単語) を抽出し、それをコンテンツ フィールドの一部として埋め込むよう、インデクサーに指示します。 この動作は、ドキュメントに埋め込まれているイメージ (PDF 内のイメージを考えてください) と、データ ソース内にあるイメージ (たとえば、JPG ファイル) の両方に適用されます。

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - インデックス作成を監視する

インデクサーを定義すると、要求を送信するときに自動的に実行されます。 定義したコグニティブ スキルによっては、インデックス作成に予想よりも時間がかかる場合があります。 インデクサーがまだ実行中かどうかを確認するには、`GetStatus` メソッドを使用します。

```csharp
private static void CheckIndexerOverallStatus(SearchServiceClient serviceClient, Indexer indexer)
{
    try
    {
        IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", e.Message);
    }
}
```

`IndexerExecutionInfo` は、インデクサーの現在の状態と実行履歴を表します。

警告は、一部のソース ファイルとスキルの組み合わせではよく見られ、必ずしも問題を示すわけではありません。 このチュートリアルでは、警告は無害です (たとえば、JPEG ファイルからのテキスト入力がない)。

次の行を `Main` に追加します。

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 - 検索する

インデックス作成が完了したら、個々のフィールドの内容を返すクエリを実行できします。 既定では、Azure Cognitive Search によって上位 50 件の結果が返されます。 サンプル データは小さいため、既定値で問題なく動作します。 ただし、より大きなデータ セットを使用する場合は、より多くの結果が返されるよう、クエリ文字列にパラメーターを含める必要がある場合があります。 手順については、[Azure Cognitive Search における結果の改ページ位置の自動修正の方法](search-pagination-page-layout.md)に関するページを参照してください。

検証手順として、すべてのフィールドのインデックスのクエリを実行します。

次の行を `Main` に追加します。

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` は、アプリケーションの構成ファイル (appsettings.json) に格納されている値を使用して、新しい `SearchIndexClient` を作成します。 管理キーではなく検索サービスのクエリ API キーが使用されることに注意してください。

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

次のコードを `Main` に追加します。 最初の try-catch では、インデックスの定義が、各フィールドの名前、型、属性と共に返されます。 2 つ目はパラメーター化クエリです。結果に含めるフィールドが `Select` によって指定されます (例: `organizations`)。 `"*"` という検索文字列により、1 つのフィールドの内容がすべて返されます。

```csharp
//Verify content is returned after indexing is finished
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
    Console.WriteLine("First query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("First query failed\n Exception message: {0}\n", e.Message);
}

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
    Console.WriteLine("Second query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("Second query failed\n Exception message: {0}\n", e.Message);
}
```

その他のフィールド (この演習では、content、languageCode、keyPhrases、および organizations) でも同様に繰り返します。 [Select](/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) プロパティでコンマ区切りリストを使用すれば、複数のフィールドを取得することができます。

<a name="reset"></a>

## <a name="reset-and-rerun"></a>リセットして再実行する

開発の初期の実験的な段階では、設計反復のための最も実用的なアプローチは、Azure Cognitive Search からオブジェクトを削除してリビルドできるようにすることです。 リソース名は一意です。 オブジェクトを削除すると、同じ名前を使用して再作成することができます。

このチュートリアルのサンプル コードでは、コードを再実行できるよう、既存のオブジェクトをチェックしてそれらを削除しています。

ポータルを使用して、インデックス、インデクサー、データ ソース、スキルセットを削除することもできます。

## <a name="takeaways"></a>重要なポイント

このチュートリアルでは、構成要素 (データ ソース、スキルセット、インデックス、およびインデクサー) の作成によってエンリッチされたインデックス作成パイプラインを作成するための、基本的な手順を示しました。

[組み込みのスキル](cognitive-search-predefined-skills.md)については、スキルセットの定義と、入力と出力を介したスキルの連結のしくみと共に説明しました。 また、Azure Cognitive Search サービス上の検索可能なインデックスに対し、エンリッチされた値をパイプラインからルーティングするには、インデクサーの定義に `outputFieldMappings` が必要であることも学習しました。

最後に、結果をテストし、今後の反復のためにシステムをリセットする方法について学習しました。 インデックスに対するクエリを発行すると、エンリッチされたインデックス作成パイプラインによって作成された出力が返されることを学習しました。 また、インデクサーの状態を確認する方法と、パイプラインを再実行する前に削除すべきオブジェクトについても学習しました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

所有するサブスクリプションを使用している場合は、プロジェクトの終了時に、不要になったリソースを削除することをお勧めします。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ペインにある [すべてのリソース] または [リソース グループ] リンクを使って、リソースを検索および管理できます。

## <a name="next-steps"></a>次のステップ

AI エンリッチメント パイプラインのオブジェクトをすべて理解したら、スキルセットの定義と個々のスキルについて詳しく見てみましょう。

> [!div class="nextstepaction"]
> [スキルセットを作成する方法](cognitive-search-defining-skillset.md)