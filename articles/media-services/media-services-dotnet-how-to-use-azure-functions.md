---
title: "Media Services を使用する Azure 関数の開発"
description: "このトピックでは、Media Services を使用する Azure 関数を Azure Portal で開発する方法について説明します。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/21/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 0cde1f36af2d41697fa956c62cc882e5429707e8
ms.lasthandoff: 03/29/2017


---
#<a name="develop-azure-functions-with-media-services"></a>Media Services を使用する Azure 関数の開発

このトピックでは、Media Services を使用する Azure 関数を Azure Portal で開発する方法について説明します。 

**[Azure に配置する]** ボタンを押して既存の [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) をデプロイすることもできます。 このリポジトリには、Azure Media Services を使った Azure 関数のサンプルが格納されています。Blob Storage から直接コンテンツを取り込んだり、エンコードしたり、Blob Storage にコンテンツを書き戻したりする処理に関連するワークフローの例が紹介されています。 また、webhook と Azure キューを介してジョブの通知を監視するサンプルも含まれています。 さらに、[Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) リポジトリの例に基づいて関数をデプロイすることもできます。 

このトピックでは、Media Services を使用した Azure 関数の作成方法について説明しています。 このトピックで定義されている Azure Function は、新しい MP4 ファイルの **input** という名前付きストレージ アカウント コンテナーを監視します。 ストレージ コンテナーにファイルを削除すると、BLOB トリガーは関数を実行します。

## <a name="prerequisites"></a>前提条件

- 初めての関数を作成するには、アクティブな Azure アカウントを用意しておく必要があります。 Azure アカウントがない場合は、 [無料アカウントを利用できます](https://azure.microsoft.com/free/)。
- Azure Media Services (AMS) アカウントでアクションを実行する Azure 関数を作成したり、Media Services から送信されるイベントをリッスンしたりするためには、[こちら](media-services-portal-create-account.md)の説明に従って AMS アカウントを作成する必要があります。
- [Azure Functions を使用する方法](../azure-functions/functions-overview.md)の説明。 また、次を確認してください。
    - [Azure Functions における HTTP と Webhook のバインド](../azure-functions/functions-triggers-bindings.md)
    - [Azure Function App の設定の構成方法](../azure-functions/functions-how-to-use-azure-function-app-settings.md)

## <a name="create-a-function-app"></a>Function App を作成する

1. [Azure Portal](http://portal.azure.com) に移動し、Azure アカウントでサインインします。
2. [こちら](../azure-functions/functions-create-first-azure-function-azure-portal.md#create-a-function-app)の説明に従って関数アプリを作成します。

>[!NOTE]
> **StorageConnection** 環境変数で指定したストレージ アカウント (次の手順を参照してください) は、アプリと同じリージョンにする必要があります。

## <a name="configure-function-app-settings"></a>関数アプリの設定の構成

Media Services の関数を開発するときは、自分が開発するさまざまな関数で使用する環境変数を追加しておくと便利です。 アプリケーション設定を構成するには、[アプリケーション設定の構成] リンクをクリックします。 詳細については、「[Azure Function App の設定の構成方法](../azure-functions/functions-how-to-use-azure-function-app-settings.md)」を参照してください。 

次に例を示します。

![[設定]](./media/media-services-azure-functions/media-services-azure-functions001.png)

この記事で定義されている関数では、アプリケーション設定に次の環境変数があることを前提としています。

**AMSAccount** : *AMS アカウント名* (例: testams)

**AMSKey** : *AMS アカウント キー* (例: IHOySnH+XX3LGPfraE5fKPl0EnzvEPKkOPKCr59aiMM=)

**MediaServicesStorageAccountName** : *ストレージ アカウント名* (例: testamsstorage)

**MediaServicesStorageAccountKey** : *ストレージ アカウント キー* (例: xx7RN7mvpcipkuXvn5g7jwxnKh5MwYQ/awZAzkSIxQA8tmCtn93rqobjgjt41Wb0zwTZWeWQHY5kSZF0XXXXXX==)

**StorageConnection** : *ストレージ接続* (例: DefaultEndpointsProtocol=https;AccountName=testamsstorage;AccountKey=xx7RN7mvpcipkuXvn5g7jwxnKh5MwYQ/awZAzkSIxQA8tmCtn93rqobjgjt41Wb0zwTZWeWQHY5kSZF0XXXXX==)

## <a name="create-a-function"></a>関数を作成する

デプロイした関数アプリは、**[App Services]** の Azure Functions に表示されます。

1. 目的の関数アプリを選択し、**[新しい関数]** をクリックします。
2. **C#** 言語と**データ処理**シナリオを選択します。
3. **BlobTrigger** テンプレートを選択します。 BLOB を **input** コンテナーにアップロードするたびに、この関数はトリガーされます。 **input** 名は、次の手順の **Path** で指定されます。

    ![ファイルのアップロード](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. **BlobTrigger** を選択すると、一部の追加のコントロールがページに表示されます。

    ![ファイルのアップロード](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. **[作成]**をクリックします。 


## <a name="files"></a>ファイル

開発した Azure 関数は、コード ファイルなど、このセクションで取り上げる各種ファイルに関連付けることになります。 既定では、**function.json** ファイルと **run.csx** (C#) ファイルに関数が関連付けられます。 **project.json** ファイルを追加する必要があります。 以降このセクションでは、これらのファイルの定義を紹介していきます。

![ファイルのアップロード](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

function.json ファイルは、関数バインドとその他の構成設定を定義します。 ランタイムはこのファイルを使用して、監視対象のイベントを特定し、関数の実行との間でデータを渡したりデータを受け取ったりする方法を判断します。 詳細については、「[Azure Functions における HTTP と Webhook のバインド](../azure-functions/functions-reference.md#function-code)」を参照してください。

>[!NOTE]
>関数が実行されることを防ぐために、**disabled** プロパティを **true** に設定します。 


以下に **function.json** ファイルの例を示します。

    {
    "bindings": [
      {
        "name": "myBlob",
        "type": "blobTrigger",
        "direction": "in",
        "path": "input/{fileName}.mp4",
        "connection": "StorageConnection"
      }
    ],
    "disabled": false
    }

### <a name="projectjson"></a>project.json

project.json ファイルには、依存関係が含まれています。 以下に Nuget からの必要な .NET Azure Media Services を含む **project.json** ファイルの例を示します。 バージョン番号は、パッケージに最新の更新プログラムがあると変更されることに注意してください。そのため、最新バージョンを確認する必要があります。 

    {
      "frameworks": {
        "net46":{
          "dependencies": {
        "windowsazure.mediaservices": "3.8.0.5",
        "windowsazure.mediaservices.extensions": "3.8.0.3"
          }
        }
       }
    }
    
### <a name="runcsx"></a>run.csx

これは関数の C# コードです。  以下で定義されている関数は、新しい MP4 ファイルの (パスで指定された) **input** という名前付きストレージ アカウント コンテナーを監視します。 ストレージ コンテナーにファイルを削除すると、BLOB トリガーは関数を実行します。
    
このセクションで定義されている例では、次の操作を行います 

1. (AMS 資産に BLOB をコピーして) Media Services アカウントに資産を取り込む方法 
2. Media Encoder Standard の "アダプティブ ストリーミング" プリセットを使用するエンコード ジョブを送信する方法。

現実のシナリオでは、ほとんどの場合、ジョブの進行状況を追跡し、エンコードされた資産を発行します。 詳細については、[Azure WebHook を使用して Media Services ジョブ通知を監視する](media-services-dotnet-check-job-progress-with-webhooks.md)に関する記事を参照してください。 例については、「[Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)」を参照してください。  

必要な関数を定義したら、**[保存および実行]** をクリックします。

    #r "Microsoft.WindowsAzure.Storage"
    #r "Newtonsoft.Json"
    #r "System.Web"

    using System;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage.Auth;

    private static readonly string _mediaServicesAccountName = Environment.GetEnvironmentVariable("AMSAccount");
    private static readonly string _mediaServicesAccountKey = Environment.GetEnvironmentVariable("AMSKey");

    static string _storageAccountName = Environment.GetEnvironmentVariable("MediaServicesStorageAccountName");
    static string _storageAccountKey = Environment.GetEnvironmentVariable("MediaServicesStorageAccountKey");

    private static CloudStorageAccount _destinationStorageAccount = null;

    // Field for service context.
    private static CloudMediaContext _context = null;
    private static MediaServicesCredentials _cachedCredentials = null;

    public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
    {
        // NOTE that the variables {fileName} here come from the path setting in function.json
        // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
        // was dropped into the input container for the function. 

        // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
        // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

        log.Info($"C# Blob trigger function processed: {fileName}.mp4");
        log.Info($"Using Azure Media Services account : {_mediaServicesAccountName}");


        try
        {
        // Create and cache the Media Services credentials in a static class variable.
        _cachedCredentials = new MediaServicesCredentials(
                _mediaServicesAccountName,
                _mediaServicesAccountKey);

        // Used the chached credentials to create CloudMediaContext.
        _context = new CloudMediaContext(_cachedCredentials);

        // Step 1:  Copy the Blob into a new Input Asset for the Job
        // ***NOTE: Ideally we would have a method to ingest a Blob directly here somehow. 
        // using code from this sample - https://azure.microsoft.com/en-us/documentation/articles/media-services-copying-existing-blob/

        StorageCredentials mediaServicesStorageCredentials =
            new StorageCredentials(_storageAccountName, _storageAccountKey);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

        }
        catch (Exception ex)
        {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
        }
    }

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


    public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
        IAsset newAsset = null;

        try{
            Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
            newAsset = await copyAssetTask;
            log.Info($"Asset Copied : {newAsset.Id}");
        }
        catch(Exception ex){
            log.Info("Copy Failed");
            log.Info($"ERROR : {ex.Message}");
            throw ex;
        }

        return newAsset;
    }

    /// <summary>
    /// Creates a new asset and copies blobs from the specifed storage account.
    /// </summary>
    /// <param name="blob">The specified blob.</param>
    /// <returns>The new asset.</returns>
    public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
    {
         //Get a reference to the storage account that is associated with the Media Services account. 
        StorageCredentials mediaServicesStorageCredentials =
        new StorageCredentials(_storageAccountName, _storageAccountKey);
        _destinationStorageAccount = new CloudStorageAccount(mediaServicesStorageCredentials, false);

        // Create a new asset. 
        var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
        log.Info($"Created new asset {asset.Name}");

        IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
        TimeSpan.FromHours(4), AccessPermissions.Write);
        ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
        CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

        // Get the destination asset container reference
        string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
        CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

        try{
        assetContainer.CreateIfNotExists();
        }
        catch (Exception ex)
        {
        log.Error ("ERROR:" + ex.Message);
        }

        log.Info("Created asset.");

        // Get hold of the destination blob
        CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

        // Copy Blob
        try
        {
        using (var stream = await blob.OpenReadAsync()) 
        {            
            await destinationBlob.UploadFromStreamAsync(stream);          
        }

        log.Info("Copy Complete.");

        var assetFile = asset.AssetFiles.Create(blob.Name);
        assetFile.ContentFileSize = blob.Properties.Length;
        assetFile.IsPrimary = true;
        assetFile.Update();
        asset.Update();
        }
        catch (Exception ex)
        {
        log.Error(ex.Message);
        log.Info (ex.StackTrace);
        log.Info ("Copy Failed.");
        throw;
        }

        destinationLocator.Delete();
        writePolicy.Delete();

        return asset;
    }
##<a name="test-your-function"></a>関数をテストする

関数をテストするには、接続文字列で指定したストレージ アカウントの **input** コンテナーに MP4 ファイルをアップロードする必要があります。  

## <a name="next-step"></a>次のステップ

これで、Media Services アプリケーションの開発準備が整いました。 
 
カスタム コンテンツ作成ワークフローを作成するために Azure Media Services で Azure Functions と Logic Apps を使用するサンプル/ソリューションの詳細については、[GitHub 上のMedia Services .NET Functions 統合サンプル](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)に関する記事を参照してください

また、「[Azure WebHook を使用して .NET で Media Services ジョブ通知を監視する](media-services-dotnet-check-job-progress-with-webhooks.md)」も参照してください。 

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


