---
title: インデックス パイプラインで Cognitive Services APIs を呼び出す C# チュートリアル - Azure Search
description: このチュートリアルでは、データの抽出と変換のために Azure Search のインデックス作成で行われる、データ抽出、自然言語、画像の AI 処理の例を段階的に説明していきます。
manager: eladz
author: MarkHeff
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: maheff
ms.openlocfilehash: 2c77d509a0e66fd02bd949e481c5f0316fdd9afb
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672015"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>C# のチュートリアル: Azure Search のインデックス パイプラインで Cognitive Services APIs を呼び出す

このチュートリアルでは、*コグニティブ スキル*を使用した Azure Search でのデータ エンリッチメントのプログラミングのしくみを学習します。 複数のスキルが、Cognitive Services の自然言語処理 (NLP) と画像分析機能によって支えられています。 スキルセットを複合および構成することで、画像やスキャンされたドキュメント ファイルのテキストとテキスト表現を抽出できます。 また、言語、エンティティ、キーフレーズなども検出できます。 最終的に、AI で強化されたインデックス パイプラインによって作成された豊富なコンテンツが Azure Search インデックスに追加されます。

このチュートリアルでは、.NET SDK を使用して以下のタスクを実行します。

> [!div class="checklist"]
> * インデックスへのルートでサンプル データをエンリッチする、インデックス作成パイプラインを作成する
> * 組み込みのスキル (光学式文字認識、テキスト マージャー、言語検出、テキスト分割、エンティティ認識、キー フレーズ抽出) を適用する
> * スキルセットの出力に入力をマップして、スキルをまとめて連結する方法を学習する
> * 要求を実行し、結果を確認する
> * 将来の開発のためにインデックスとインデクサーをリセットする

出力は、Azure Search のフルテキスト検索可能なインデックスです。 インデックスは、[シノニム](search-synonyms.md)、[スコアリング プロファイル](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)、[アナライザー](search-analyzers.md)、および[フィルター](search-filters.md)などの他の標準的な機能を使って強化できます。

このチュートリアルは無料のサービスで実行されますが、無料のトランザクションの数は 1 日あたり 20 のドキュメントまでに制限されます。 このチュートリアルを同じ日に複数回実行する場合は、より小さなファイル セットを使用して、より多くの実行が制限内に収まるようにします。

> [!NOTE]
> 処理の頻度を増やしたり、ドキュメントを追加したり、AI アルゴリズムを追加したりすることによってスコープを拡大する場合は、請求対象の Cognitive Services リソースをアタッチする必要があります。 Cognitive Services の API を呼び出すとき、および Azure Search のドキュメントクラッキング段階の一部として画像抽出するときに、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格は、[Azure Search の価格のページ](https://go.microsoft.com/fwlink/?linkid=2042400)で説明されています。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、次のサービス、ツール、およびデータを使用します。 

+ サンプル データの格納のための [Azure ストレージ アカウントを作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)します。 ストレージ アカウントが Azure Search と同じリージョンにあることを確認します。

+ [サンプル データ](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)は、さまざまなタイプの小さいファイル セットで構成されています。 

+ IDE として使用するために [Visual Studio をインストール](https://visualstudio.microsoft.com/)します。

+ [Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このチュートリアル用には、無料のサービスを使用できます。

## <a name="get-a-key-and-url"></a>キーと URL を入手する

Azure Search サービスを使用するには、サービスの URL とアクセス キーが必要になります。 両方を使用して検索サービスが作成されるので、Azure Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理者キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

   ![HTTP エンドポイントとアクセス キーを取得する](media/search-get-started-postman/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

## <a name="prepare-sample-data"></a>サンプル データの準備

エンリッチメント パイプラインは、Azure データ ソースから取得されます。 ソース データは、サポートされているデータ ソースの種類の [Azure Search インデクサー](search-indexer-overview.md)から取得する必要があります。 Cognitive Search では Azure Table Storage はサポートされていません。 この演習では、BLOB ストレージを使用して複数のコンテンツ タイプを示します。

1. [Azure portal にサインインし](https://portal.azure.com)、Azure ストレージ アカウントに移動して **[BLOB]** をクリックし、 **[+ コンテナー]** をクリックします。

1. [BLOB コンテナーを作成](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)してサンプル データを含めます。 パブリック アクセス レベルは、有効な任意の値に設定できます。 このチュートリアルでは、コンテナー名が "basic-demo-data-pr" とします。

1. コンテナーが作成されたら、コンテナーを開いてコマンド バーの **[アップロード]** を選択し、[サンプル データ](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)をアップロードします。

   ![Azure Blob Storage 内のソース ファイル](./media/cognitive-search-quickstart-blob/sample-data.png)

1. サンプル ファイルが読み込まれたら、BLOB ストレージのコンテナー名と接続文字列を取得します。 これは、Azure portal で自分のストレージ アカウントに移動して、 **[アクセス キー]** を選択してから、 **[接続文字列]** フィールドをコピーして行うこともできます。

   接続文字列は次の例のような URL です。

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

共有アクセス署名の提供など、接続文字列を指定する方法は他にもあります。 データ ソースの資格情報の詳細については、「[Azure Blob Storage のインデックスを Azure Search で作成する](search-howto-indexing-azure-blob-storage.md#Credentials)」をご覧ください。

## <a name="set-up-your-environment"></a>環境をセットアップする

まず Visual Studio を開き、.NET Core 上で実行する新しいコンソール アプリ プロジェクトを作成します。

### <a name="install-nuget-packages"></a>NuGet パッケージのインストール

[Azure Search .NET SDK](https://aka.ms/search-sdk) は､HTTP や JSON に関する詳しい知識がなくても､インデックスやデータ ソース､インデクサー､スキルセットの管理､ドキュメントのアップロードと管理､クエリの実行を行うことを可能にするいくつかのクライアント ライブラリから構成されています。 これらのクライアント ライブラリはすべて､NuGet パッケージとして配布されます｡

このプロジェクトでは、バージョン 9 の `Microsoft.Azure.Search` NuGet パッケージと最新の `Microsoft.Extensions.Configuration.Json` NuGet パッケージをインストールする必要があります。

`Microsoft.Azure.Search` NuGet パッケージは、Visual Studio のパッケージ マネージャー コンソールを使用してインストールします。 パッケージ マネージャー コンソールを開くには、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** を選択します。 実行するコマンドを取得するには、[Microsoft.Azure.Search NuGet パッケージ ページ](https://www.nuget.org/packages/Microsoft.Azure.Search)に移動してバージョン 9 を選択し、パッケージ マネージャー コマンドをコピーします。 パッケージ マネージャー コンソールで、このコマンドを実行します。

Visual Studio で `Microsoft.Extensions.Configuration.Json` NuGet パッケージをインストールするには、 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[ソリューションの NuGet パッケージの管理...]** を選択します。[参照] を選択し、`Microsoft.Extensions.Configuration.Json` NuGet パッケージを検索します。 見つかったら、パッケージを選択し、プロジェクトを検索し、バージョンが最新の安定したバージョンであることを確認してから、[インストール] を選択します。

## <a name="add-azure-search-service-information"></a>Azure Search サービス情報の追加

Azure Search サービスに接続するには、検索サービスの情報をプロジェクトに追加する必要があります。 ソリューション エクスプローラーでプロジェクトを右クリックし、 **[追加]**  >  **[新しい項目...]** を選択します。 ファイルに `appsettings.json` という名前を付けて、 **[追加]** を選択します。 

このファイルは出力ディレクトリに含める必要があります。 そのためには、`appsettings.json` を右クリックし、 **[プロパティ]** を選択します。 **[出力ディレクトリにコピー]** の値を **[Copy of newer]\(新しい場合はコピーする\)** に変更します。

次の JSON を新しい JSON ファイルにコピーします。

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

検索サービスと BLOB ストレージ アカウントの情報を追加します。

検索サービスの情報は Azure portal の検索アカウント ページで取得できます。 アカウント名はメイン ページにあり、キーは **[キー]** を選択すると見つかります。

BLOB の接続文字列を取得するには、Azure portal で自分のストレージ アカウントに移動して、 **[アクセス キー]** を選択してから、 **[接続文字列]** フィールドをコピーします。

## <a name="add-namespaces"></a>名前空間の追加

このチュートリアルでは、さまざまな名前空間のさまざまな種類を使用します。 それらの種類を使用するためには、以下を `Program.cs` に追加します。

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>クライアントの作成

`SearchServiceClient` クラスのインスタンスを作成します。

```csharp
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
> 

## <a name="create-a-data-source"></a>データ ソースを作成する

`DataSource.AzureBlobStorage` を呼び出して新しい `DataSource` を作成します。 `DataSource.AzureBlobStorage` では、データ ソース名、接続文字列、および BLOB コンテナー名を指定することが必要です。

このチュートリアルでは使用されませんが、論理削除ポリシーも定義されます。これは、削除された BLOB を論理削除列の値に基づいて特定するために使用されます。 次のポリシーでは、BLOB のメタデータ プロパティ `IsDeleted` の値が `true` のときに、その BLOB が削除されるものと見なされます。

```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

`DataSource` オブジェクトを初期化したところで、データ ソースを作成します。 `SearchServiceClient` には `DataSources` プロパティがあります。 このプロパティは、Azure Search データ ソースの作成、一覧表示、更新、または削除に必要なすべてのメソッドを提供します。

要求が成功すると、作成されたデータ ソースがメソッドから返されます。 無効なパラメーターなど、要求に問題がある場合、メソッドは例外をスローします。

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
```

これは最初の要求のため、Azure Portal を調べて、データ ソースが Azure Search で作成されたことを確認します。 Search サービスのダッシュボード ページで、[データ ソース] タイルに新しい項目があることを確認します。 Portal のページが更新されるまで数分かかる場合があります。

  ![Portal の [データ ソース] タイル](./media/cognitive-search-tutorial-blob/data-source-tile.png "Portal の [データ ソース] タイル")

## <a name="create-a-skillset"></a>スキルセットを作成する

このセクションでは、データに適用するエンリッチメント ステップのセットを定義します。 それぞれのエンリッチメント ステップを*スキル*と呼び、エンリッチメント ステップのセットを*スキルセット*と呼びます。 このチュートリアルでは、スキルセット用に次の[定義済みのコグニティブ スキル](cognitive-search-predefined-skills.md)を使用します。

+ [光学式文字認識](cognitive-search-skill-ocr.md)。画像ファイルに印字された手書きテキストを認識します。

+ [テキスト マージ](cognitive-search-skill-textmerger.md)。フィールドのコレクションからのテキストを 1 つのフィールドに統合します。

+ [言語検出](cognitive-search-skill-language-detection.md)。コンテンツの言語を識別します。

+ [テキスト分割](cognitive-search-skill-textsplit.md)。キー フレーズ抽出スキルとエンティティ認識スキルを呼び出す前に、大きいコンテンツを小さいチャンクに分割します。 キー フレーズ抽出およびエンティティ認識では、50,000 字以内の入力を受け取ります。 いくつかのサンプル ファイルは、分割してこの制限内に収める必要があります。

+ [エンティティの認識](cognitive-search-skill-entity-recognition.md)。BLOB コンテナーのコンテンツから組織の名前を抽出します。

+ [キー フレーズ抽出](cognitive-search-skill-keyphrases.md)。上位のキー フレーズを抜き出します。

初期処理中に、Azure Search が各ドキュメントを解読して、さまざまなファイル形式からコンテンツを読み取ります。 ソース ファイルから配信されたテキストが見つかると、ドキュメントごとに 1 つずつ、生成された ```content``` フィールドに配置されます。 そのため、```"/document/content"``` として入力を設定し、このテキストを使用します。 

出力はインデックスにマップすることができ、ダウンストリーム スキルへの入力として、または言語コードと同様に両方として使用されます。 インデックスでは、言語コードはフィルター処理に役立ちます。 入力としての言語コードは、テキスト分析スキルによって、単語区切りに基づく言語学的規則を通知するために使用されます。

スキルセットの基礎の詳細については、[スキルセットを定義する方法](cognitive-search-defining-skillset.md)に関するページをご覧ください。

### <a name="ocr-skill"></a>OCR スキル

**OCR** スキルはイメージからテキストを抽出します。 このスキルは、normalized-images フィールドが存在していることを前提としています。 このフィールドを生成するために、このチュートリアルの後半で、```"generateNormalizedImages"``` に対するインデクサー定義で ```"imageAction"``` 構成を設定します。

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "image",
    source: "/document/normalized_images/*"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "text",
    targetName: "text"));

OcrSkill ocrSkill = new OcrSkill(
    description: "Extract text (plain and structured) from image",
    context: "/document/normalized_images/*",
    inputs: inputMappings,
    outputs: outputMappings,
    defaultLanguageCode: OcrSkillLanguage.En,
    shouldDetectOrientation: true);
```

### <a name="merge-skill"></a>マージ スキル

このセクションでは、ドキュメント コンテンツ フィールドを OCR スキルで生成されたテキストとマージする**マージ** スキルを作成します。

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/content"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "itemsToInsert",
    source: "/document/normalized_images/*/text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "offsets",
    source: "/document/normalized_images/*/contentOffset"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "mergedText",
    targetName: "merged_text"));

MergeSkill mergeSkill = new MergeSkill(
    description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    insertPreTag: " ",
    insertPostTag: " ");
```

### <a name="language-detection-skill"></a>言語検出スキル

**言語検出**スキルは、入力テキストの言語を検出し、要求で送信されたすべてのドキュメントごとに 1 つの言語コードを報告します。 **言語検出**スキルの出力を、**テキスト分割**スキルに対する入力の一部として使用します。

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "languageCode",
    targetName: "languageCode"));

LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
    description: "Detect the language used in the document",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="text-split-skill"></a>テキスト分割スキル

以下の**分割**スキルは、ページごとにテキストを分割し、`String.Length` で測定してページ サイズを 4,000 文字に制限します。 このアルゴリズムは、テキストを最大でサイズが `maximumPageLength` のチャンクに分割しようとします。 この場合、アルゴリズムはできる限り文の境界で文を区切ろうとするため、チャンクのサイズは `maximumPageLength` よりも少し小さくなることがあります。

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "languageCode",
    source: "/document/languageCode"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "textItems",
    targetName: "pages"));

SplitSkill splitSkill = new SplitSkill(
    description: "Split content into pages",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    textSplitMode: TextSplitMode.Pages,
    maximumPageLength: 4000);
```

### <a name="entity-recognition-skill"></a>エンティティ認識スキル

この `EntityRecognitionSkill` インスタンスは、カテゴリの種類 `organization` を認識するように設定されています。 **エンティティ認識**スキルでは、カテゴリの種類 `person` および `location` も認識できます。

"context" フィールドが、アスタリスク付きで ```"/document/pages/*"``` に設定されていることに注目してください。これは、エンリッチメント ステップが ```"/document/pages"``` の下にある各ページごとに呼び出されることを意味します。

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
    
List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "organizations",
    targetName: "organizations"));

List<EntityCategory> entityCategory = new List<EntityCategory>();
entityCategory.Add(EntityCategory.Organization);
    
EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
    description: "Recognize organizations",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings,
    categories: entityCategory,
    defaultLanguageCode: EntityRecognitionSkillLanguage.En);
```

### <a name="key-phrase-extraction-skill"></a>キー フレーズ抽出スキル

先ほど作成した `EntityRecognitionSkill` インスタンスと同様に、**キー フレーズ抽出**スキルもドキュメントのページごとに呼び出されます。

```csharp
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
```

### <a name="build-and-create-the-skillset"></a>スキルセットを構築および作成する

作成したスキルを使用して `Skillset` を構築します。

```csharp
List<Skill> skills = new List<Skill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

Skillset skillset = new Skillset(
    name: "demoskillset",
    description: "Demo skillset",
    skills: skills);
```

検索サービスでスキルセットを作成します。

```csharp
try
{
    serviceClient.Skillsets.CreateOrUpdate(skillset);
}
catch (Exception e)
{
    // Handle exception
}
```

## <a name="create-an-index"></a>インデックスを作成する

このセクションでは、検索可能なインデックス、および各フィールドの検索属性に含めるフィールドを指定することによって、インデックス スキーマを定義します。 フィールドには型があり、フィールドの使用方法 (検索可能、並べ替え可能など) を決定する属性を取ることができます。 インデックス内のフィールド名は、ソース内のフィールド名と完全に一致する必要はありません。 後のほうの手順では、インデクサーにフィールド マッピングを追加して、ソースとターゲットのフィールドを接続します。 この手順で、検索アプリケーションに関連するフィールドの名前付け規則を使用してインデックスを定義します。

この演習では、次のフィールドとフィールドの型を使用します。

| field-names: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="create-demoindex-class"></a>DemoIndex クラスを作成する

このインデックスのフィールドはモデル クラスを使用して定義されます。 モデル クラスの各プロパティには、対応するインデックス フィールドの検索に関連した動作を決定する属性があります。 

モデル クラスを新しい C# ファイルに追加します。 プロジェクトを右クリックして **[追加]**  >  **[新しい項目...]** を選択し、[クラス] を選択し、ファイルに `DemoIndex.cs` という名前を付けて、 **[追加]** を選択します。

`Microsoft.Azure.Search` および `Microsoft.Azure.Search.Models` 名前空間の種類を使用することを指定します。

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

以下のモデル クラス定義を `DemoIndex.cs` に追加し、インデックスを作成するのと同じ名前空間に含めます。

```csharp
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
```

モデル クラスの定義が完了したので、`Program.cs`に戻ると、インデックス定義を非常に簡単に作成できます。 このインデックスの名前は "demoindex" になります。

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

テスト中に、インデックスを複数回作成しようとする場合があります。 このため、作成を試行する前に、作成しようとするインデックスが既に存在していないかを確認してください。

```csharp
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
    // Handle exception
}
```

インデックスの定義の詳細については、[インデックスの作成 (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) に関するページをご覧ください。

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>インデクサーを作成し、フィールドをマップし、変換を実行する

ここまでで、データ ソース、スキルセット、およびインデックスを作成しました。 これら 3 つのコンポーネントが、各要素を複数フェーズにわたる 1 つの操作にまとめて入れる[インデクサー](search-indexer-overview.md)の一部になります。 これらをまとめてインデクサーに結び付けるには、フィールド マッピングを定義する必要があります。

+ スキルセットの前に fieldMappings が処理されて、データ ソースのソース フィールドがインデックス内のターゲット フィールドにマッピングされます。 フィールド名と型が両側で共通していれば、マッピングは必要ありません。

+ outputFieldMappings は、スキルセットの後に処理されます。sourceFieldNames がドキュメント解析またはエンリッチメントによって作成されるまでは、存在しない sourceFieldNames が参照されます。 targetFieldName は、インデックス内のフィールドです。

入力を出力につなぐことに加え、フィールドのマッピングを使用して、データ構造をフラット化することもできます。 詳細については、「[強化されたフィールドを検索可能なインデックスにマップする方法](cognitive-search-output-field-mapping.md)」を参照してください。

```csharp
IDictionary<string, object> config = new Dictionary<string, object>();
config.Add(
    key: "dataToExtract",
    value: "contentAndMetadata");
config.Add(
    key: "imageAction",
    value: "generateNormalizedImages");

List<FieldMapping> fieldMappings = new List<FieldMapping>();
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "metadata_storage_path",
    targetFieldName: "id",
    mappingFunction: new FieldMappingFunction(
        name: "base64Encode")));
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "content",
    targetFieldName: "content"));

List<FieldMapping> outputMappings = new List<FieldMapping>();
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/organizations/*",
    targetFieldName: "organizations"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/keyPhrases/*",
    targetFieldName: "keyPhrases"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/languageCode",
    targetFieldName: "languageCode"));

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
    // Handle exception
}
```

インデクサーの作成が完了するまで時間は少ししかかかりません。 データ セットが小さい場合でも、分析スキルは計算を集中的に行います。 画像分析などの一部のスキルは、実行時間の長いスキルです。

> [!TIP]
> インデクサーを作成すると、パイプラインが呼び出されます。 データ、入力と出力のマッピング、または操作の順序に及ぶ問題がある場合は、この段階で現れます。

### <a name="explore-creating-the-indexer"></a>インデクサーの作成について

コードで ```"maxFailedItems"``` を -1 に設定します。これにより、インデックス作成エンジンに、データのインポート中のエラーを無視するよう指示します。 デモのデータ ソースにはドキュメントがほとんどないため、これは便利です。 大きいデータ ソースの場合は、この値を 0 より大きい値に設定します。

また、```"dataToExtract"``` が ```"contentAndMetadata"``` に設定されていることに気が付きます。 このステートメントは、さまざまなファイル形式と、各ファイルに関連するメタデータからコンテンツを、自動的に抽出するようにインデクサーに指示します。

コンテンツが抽出されるときに、`imageAction` を設定して、データ ソース内にある画像からテキストを抽出することができます。 ```"generateNormalizedImages"``` 構成に設定された ```"imageAction"``` は、OCR スキルおよびテキスト マージ スキルと組み合わされて、イメージからテキスト (たとえば、一時停止の道路標識から "stop" という単語) を抽出し、それをコンテンツ フィールドの一部として埋め込むよう、インデクサーに指示します。 この動作は、ドキュメントに埋め込まれているイメージ (PDF 内のイメージを考えてください) と、データ ソース内にあるイメージ (たとえば、JPG ファイル) の両方に適用されます。

## <a name="check-indexer-status"></a>インデクサーの状態を確認する

インデクサーを定義すると、要求を送信するときに自動的に実行されます。 定義したコグニティブ スキルによっては、インデックス作成に予想よりも時間がかかる場合があります。 インデクサーがまだ実行中かどうかを確認するには、`GetStatus` メソッドを使用します。

```csharp
try
{
    IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

    switch (demoIndexerExecutionInfo.Status)
    {
        case IndexerStatus.Error:
            Console.WriteLine("Indexer has error status");
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
    // Handle exception
}
```

`IndexerExecutionInfo` は、インデクサーの現在の状態と実行履歴を表します。

警告は、一部のソース ファイルとスキルの組み合わせではよく見られ、必ずしも問題を示すわけではありません。 このチュートリアルでは、警告は無害です (たとえば、JPEG ファイルからのテキスト入力がない)。
 
## <a name="query-your-index"></a>インデックスの照会

インデックス作成が完了したら、個々のフィールドの内容を返すクエリを実行できします。 既定では、Azure Search によって上位 50 件の結果が返されます。 サンプル データは小さいため、既定値で問題なく動作します。 ただし、より大きなデータ セットを使用する場合は、より多くの結果が返されるよう、クエリ文字列にパラメーターを含める必要がある場合があります。 手順については、「[Azure Search でのページ検索結果の表示方法](search-pagination-page-layout.md)」をご覧ください。

検証手順として、すべてのフィールドのインデックスのクエリを実行します。

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
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

出力は、インデックス スキーマと、各フィールドの名前、型、および属性です。

`"*"` の 2 番目のクエリを送信して、`organizations` などの 1 つのフィールドのすべての内容を返します。

```csharp
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

その他のフィールド (この演習では、content、languageCode、keyPhrases、および organizations) でも同様に繰り返します。 コンマ区切りリストを使用して、`$select` を介して複数のフィールドを返すことができます。

<a name="reset"></a>

## <a name="reset-and-rerun"></a>リセットして再実行する

開発の初期の実験的な段階では、設計反復のための最も実用的なアプローチは、Azure Search からオブジェクトを削除してリビルドできるようにすることです。 リソース名は一意です。 オブジェクトを削除すると、同じ名前を使用して再作成することができます。

このチュートリアルでは、既存のインデクサーとインデックスをチェックして、既に存在している場合はコードを再実行できるようにそれらを削除します。

ポータルを使用して、インデックス、インデクサー、およびスキルセットを削除することもできます。

コードが成熟したら、リビルド戦略を改善することもできます。 詳細については、[インデックスをリビルドする方法](search-howto-reindex.md)に関するページをご覧ください。

## <a name="takeaways"></a>重要なポイント

このチュートリアルでは、構成要素 (データ ソース、スキルセット、インデックス、およびインデクサー) の作成によってエンリッチされたインデックス作成パイプラインを作成するための、基本的な手順を示しました。

[定義済みのスキル](cognitive-search-predefined-skills.md)については、スキルセットの定義と、入力と出力を介したスキルの連結のしくみとともに説明しました。 また、Azure Search サービスでエンリッチされた値をパイプラインから検索可能なインデックス内にルーティングするには、インデクサーの定義に `outputFieldMappings` が必要であることも学習しました。

最後に、結果をテストし、今後の反復のためにシステムをリセットする方法について学習しました。 インデックスに対するクエリを発行すると、エンリッチされたインデックス作成パイプラインによって作成された出力が返されることを学習しました。 また、インデクサーの状態を確認する方法と、パイプラインを再実行する前に削除すべきオブジェクトについても学習しました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルの後で最も速くクリーンアップする方法は、Azure Search サービスと Azure BLOB サービスが含まれているリソース グループを削除することです。 両方のサービスを同じグループに配置すると仮定した場合は、ここでリソース グループを削除すると、このチュートリアル用に作成したサービスと保存したコンテンツを含み、そのリソース グループ内のすべてのものが完全に削除されます。 Portal では、リソース グループ名は各サービスの [概要] ページに表示されます。

## <a name="next-steps"></a>次の手順

カスタム スキルを使ってパイプラインをカスタマイズまたは拡張します。 カスタム スキルを作成してスキルセットに追加すると、自分で作成したテキストまたは画像分析をオンボードできます。

> [!div class="nextstepaction"]
> [例:コグニティブ検索用のカスタム スキルを作成する](cognitive-search-create-custom-skill-example.md)
