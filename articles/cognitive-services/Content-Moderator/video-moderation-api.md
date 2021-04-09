---
title: C# で好ましくない要素を検出するためにビデオ コンテンツを分析する - Content Moderator
titleSuffix: Azure Cognitive Services
description: Content Moderator SDK for .NET を使用してさまざまな好ましくない要素を検出するためにビデオ コンテンツを分析する方法
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: how-to
ms.date: 05/18/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f98eac4305333ec7225c90da2777b7e02f050a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96853534"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>C# で好ましくない要素を検出するためにビデオ コンテンツを分析する

この記事では、[Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) を使用して、成人向けまたはわいせつなコンテンツが含まれていないか、ビデオをスキャンするために役立つ情報とコード サンプルを提供します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。 

## <a name="prerequisites"></a>前提条件
- [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション

## <a name="set-up-azure-resources"></a>Azure リソースの設定

Content Moderator のビデオのモデレーション機能は、Azure Media Services (AMS) の無料のパブリック プレビュー **メディア プロセッサ** として利用できます。 Azure Media Services は、ビデオ コンテンツを格納およびストリーミングするために特殊化された Azure サービスです。 

### <a name="create-an-azure-media-services-account"></a>Azure Media Services アカウントの作成

[Azure Media Services アカウントの作成](../../media-services/previous/media-services-portal-create-account.md)の指示に従って、AMS をサブスクライブし、関連付けられた Azure ストレージ アカウントを作成します。 そのストレージ アカウントで、新しい BLOB ストレージ コンテナーを作成します。

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory アプリケーションを作成する

Azure portal で新しい AMS サブスクリプションに移動し、サイド メニューから **[API アクセス]** を選択します。 **[サービス プリンシパルを使って Azure Media Services に接続する]** を選択します。 **[REST API エンドポイント]** フィールドの値をメモします。この値は後で必要になります。

**[Azure AD アプリ]** セクションで、**[新規作成]** を選択し、新しい Azure AD アプリケーションの登録に名前を付けます ("VideoModADApp" など)。 **[保存]** をクリックして、アプリケーションが構成されるまで数分待ちます。 その後、ページの **[Azure AD アプリ]** セクションの下に、新しいアプリの登録が表示されるはずです。

アプリの登録を選択し、その下の **[アプリケーションの管理]** ボタンをクリックします。 **[アプリケーション ID]** フィールドの値をメモします。この値は後で必要になります。 **[設定]**  >  **[キー]** の順に選択し、新しいキーの説明 ("VideoModKey" など) を入力します。 **[保存]** をクリックして、新しいキー値に注目します。 この文字列をコピーし、それをどこか安全な場所に保存します。

上記のプロセスのより詳細なチュートリアルについては、[Azure AD 認証を開始する](../../media-services/previous/media-services-portal-get-started-with-aad.md)のページを参照してください。

これを完了すると、ビデオ モデレーション メディア プロセッサを 2 通りの方法で使用できます。

## <a name="use-azure-media-services-explorer"></a>Azure Media Services エクスプローラーを使用する

Azure Media Services エクスプローラーは、AMS のユーザー フレンドリなフロント エンドです。 これは、AMS アカウントの参照、ビデオのアップロード、Content Moderator メディア プロセッサを使用したコンテンツのスキャンに使用します。 これを [GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases) からダウンロードしてインストールします。または、詳細については、[Azure Media Services エクスプローラーに関するブログ記事](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/)を参照してください。

![Azure Media Services エクスプローラーと Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Visual Studio プロジェクトの作成

1. Visual Studio で、新しい **コンソール アプリ (.NET Framework)** プロジェクトを作成し、**VideoModeration** という名前を付けます。 
1. ソリューションに他のプロジェクトがある場合は、これを単一のスタートアップ プロジェクトとして選択します。
1. 必須の NuGet パッケージを入手します。 ソリューション エクスプローラーでプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。次のパッケージを見つけてインストールします。
    - windowsazure.mediaservices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>ビデオ モデレーション コードを追加する

次に、基本的なコンテンツ モデレーション シナリオを実装するために、このガイドからコードをコピーしてプロジェクトに貼り付けます。

### <a name="update-the-programs-using-statements"></a>プログラムの using ステートメントを更新する

_Program.cs_ ファイルの先頭に次の `using` ステートメントを追加します。

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Collections.Generic;
```

### <a name="set-up-resource-references"></a>リソース参照を設定する

次の静的フィールドを _Program.cs_ 内の **Program** クラスに追加します。 これらのフィールドには、AMS サブスクリプションに接続するために必要な情報が保持されます。 上記の手順で取得した値を使用して、これらのフィールドに入力します。 `CLIENT_ID` はご使用の Azure AD アプリの **アプリケーション ID** 値で、`CLIENT_SECRET` はそのアプリ用に作成した "VideoModKey" の値です。

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

ローカル ビデオ ファイルを使用する場合 (最も簡単な場合) は、そのファイルをプロジェクトに追加して、そのパスを `INPUT_FILE` 値として入力します (相対パスは実行ディレクトリを基準とします)。

現在のディレクトリに _preset.json_ ファイルを作成し、それを使用してバージョン番号を指定する必要もあります。 次に例を示します。

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>入力ビデオを読み込む

**Program** クラスの **Main** メソッドでは、まず Azure メディア コンテキストを作成し、次に Azure Storage コンテキストを作成します (ビデオが BLOB ストレージ内にある場合)。 残りのコードでは、ローカル フォルダー、BLOB、または Azure ストレージ コンテナー内の複数の BLOB からのビデオをスキャンします。 コードの他の行をコメント化することにより、すべてのオプションを試すことができます。

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Azure メディア コンテキストを作成する

**Program** クラスに次のメソッドを追加します。 これは AMS 資格情報を使用して AMS との通信を許可します。

```csharp
// Creates a media context from azure credentials
static void CreateMediaContext()
{
    // Get Azure AD credentials
    var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
        new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
        AzureEnvironments.AzureCloudEnvironment);

    // Initialize an Azure AD token
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

    // Create a media context
    _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
}
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Azure Storage コンテキストを作成するコードを追加する

**Program** クラスに次のメソッドを追加します。 Storage 資格情報から作成されたストレージ コンテキストを使用して、BLOB ストレージにアクセスします。

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>ローカル ファイルと BLOB から Azure メディア資産を作成するコードを追加する

Content Moderator メディア プロセッサは、Azure Media Services プラットフォーム内の **資産** に対してジョブを実行します。
以下のメソッドでは、ローカル ファイルまたは関連付けられた BLOB から資産が作成されます。

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>コンテナー内の (BLOB としての) ビデオのコレクションをスキャンするコードを追加する

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
static void RunContentModeratorJobOnBlobs()
{
    // Get the reference to the list of Blobs. See the following method.
    var blobList = GetBlobsList();

    // Iterate over the Blob list items or work on specific ones as needed
    foreach (var sourceBlob in blobList)
    {
        // Create an Asset
        IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                            _StorageCredentials, AssetCreationOptions.None);
        asset.Update();

        // Submit to Content Moderator
        RunContentModeratorJob(asset);
    }
}

// Get all blobs in your container
static IEnumerable<IListBlobItem> GetBlobsList()
{
    // Get a reference to the Container within the Storage Account
    // that has the files (blobs) for moderation
    CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
    CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

    // Get the reference to the list of Blobs 
    var blobList = MediaBlobContainer.ListBlobs();
    return blobList;
}
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Content Moderator ジョブを実行するメソッドを追加する

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
            asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
            mp, configuration,
            TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
        AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
            .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}",
                DateTime.Now,
                jobQuery.State));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
            jobQuery.State != JobState.Error &&
            jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
    CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
        error.Code,
        error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

### <a name="add-helper-functions"></a>ヘルパー関数を追加する

これらのメソッドは、Azure Media Services 資産から Content Moderator 出力ファイル (JSON) をダウンロードすると共に、プログラムが実行状態をコンソールに記録できるようにモデレーション ジョブの状態の追跡を支援します。

```csharp
static void DownloadAsset(IAsset asset, string outputDirectory)
{
    foreach (IAssetFile file in asset.AssetFiles)
    {
        file.Download(Path.Combine(outputDirectory, file.Name));
    }
}

// event handler for Job State
static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);
    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job finished.");
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
            Console.WriteLine("Job is canceled.\n");
            break;
        case JobState.Error:
            Console.WriteLine("Job failed.\n");
            break;
        default:
            break;
    }
}
```

### <a name="run-the-program-and-review-the-output"></a>プログラムを実行して出力をレビューする

Content Moderation ジョブが完了したら、JSON 応答を分析します。 以下の要素で構成されます。

- ビデオ情報の概要
- "**フラグメント**" としての **Shots**
- "**イベント**" としての **Key frames** と、**Adult** スコアおよび **Racy** スコアに基づく **reviewRecommended"** (= true または false)" フラグ
- **start**、**duration**、**totalDuration**、**timestamp** は "ティック" 単位です。 秒単位で表される数値を得るために、**timescale** で割り算します。
 
> [!NOTE]
> - `adultScore` は、特定の状況で、性的に露骨な表現または成人向けの表現であると考えられるコンテンツの存在の可能性および予測スコアを表します。
> - `racyScore` は、特定の状況で、性的に挑発的または成熟した表現であると考えられるコンテンツの存在の可能性および予測スコアを表します。
> - `adultScore` と `racyScore` の値は 0 から 1 の範囲です。 このモデルでは、スコアが高いほど、そのカテゴリに該当する可能性が高いと予測しています。 このプレビューは、人がコーディングした結果ではなく、統計モデルに依存しています。 独自のコンテンツを使用してテストを行い、実際の要件に合うように各カテゴリをどのように設定するかを決めることをお勧めします。
> - `reviewRecommended` は、内部スコアのしきい値に応じて true または false のどちらかになります。 ユーザーは、この値を使用するか、独自のコンテンツ ポリシーに基づいてカスタムしきい値を決めるかを見極める必要があります。

```json
{
"version": 2,
"timescale": 90000,
"offset": 0,
"framerate": 50,
"width": 1280,
"height": 720,
"totalDuration": 18696321,
"fragments": [
{
    "start": 0,
    "duration": 18000
},
{
    "start": 18000,
    "duration": 3600,
    "interval": 3600,
    "events": [
    [
        {
        "reviewRecommended": false,
        "adultScore": 0.00001,
        "racyScore": 0.03077,
        "index": 5,
        "timestamp": 18000,
        "shotIndex": 0
        }
    ]
    ]
},
{
    "start": 18386372,
    "duration": 119149,
    "interval": 119149,
    "events": [
    [
        {
        "reviewRecommended": true,
        "adultScore": 0.00000,
        "racyScore": 0.91902,
        "index": 5085,
        "timestamp": 18386372,
        "shotIndex": 62
        }
    ]
    ]
}
]
}
```

## <a name="next-steps"></a>次のステップ

モデレーション出力から[ビデオ レビュー](video-reviews-quickstart-dotnet.md)を生成する方法について説明する。

.NET 用のこのクイック スタートや他の Content Moderator のクイックスタートの [Visual Studio ソリューションをダウンロードする](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)。