---
title: Media Services を使用する Azure 関数の開発
description: このトピックでは、Media Services を使用する Azure 関数を Azure Portal で開発する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ef8e3a2e733247f713fe0240cb8ef6069803fcb7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013960"
---
# <a name="develop-azure-functions-with-media-services"></a>Media Services を使用する Azure 関数の開発

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

この記事では、Media Services を使用した Azure Functions の作成方法について説明しています。 この記事で定義されている Azure Function は、新しい MP4 ファイルの **input** という名前付きストレージ アカウント コンテナーを監視します。 ストレージ コンテナーにファイルを削除すると、BLOB トリガーは関数を実行します。 Azure Functions について確認するには、**Azure Functions** のセクションで [概要](../../azure-functions/functions-overview.md)およびその他のトピックを参照してください。

Azure Media Services を使用する既存の Azure 関数を探してデプロイするには、[Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) をチェックアウトしてください。 このリポジトリには、Media Services を使ったサンプルが格納されています。Blob Storage から直接コンテンツを取り込んだり、エンコードしたり、Blob Storage にコンテンツを書き戻したりする処理に関連するワークフローの例が紹介されています。 また、webhook と Azure キューを介してジョブの通知を監視するサンプルも含まれています。 さらに、[Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) リポジトリの例に基づいて関数をデプロイすることもできます。 関数をデプロイするには、 **[Azure に配置する]** を押します。

## <a name="prerequisites"></a>前提条件

- 初めての関数を作成するには、アクティブな Azure アカウントを用意しておく必要があります。 Azure アカウントがまだない場合は、 [無料アカウントを利用できます](https://azure.microsoft.com/free/)。
- Azure Media Services (AMS) アカウントでアクションを実行する Azure 関数を作成したり、Media Services から送信されるイベントをリッスンしたりするためには、[こちら](media-services-portal-create-account.md)の説明に従って AMS アカウントを作成する必要があります。
    
## <a name="create-a-function-app"></a>Function App を作成する

1. [Azure Portal](https://portal.azure.com) に移動し、Azure アカウントでサインインします。
2. [こちら](../../azure-functions/functions-create-function-app-portal.md)の説明に従って関数アプリを作成します。

>[!NOTE]
> **StorageConnection** 環境変数で指定したストレージ アカウント (次の手順を参照してください) は、アプリと同じリージョンにする必要があります。

## <a name="configure-function-app-settings"></a>関数アプリの設定の構成

Media Services の関数を開発するときは、自分が開発するさまざまな関数で使用する環境変数を追加しておくと便利です。 アプリケーション設定を構成するには、[アプリケーション設定の構成] リンクをクリックします。 詳細については、「[Azure Function App の設定の構成方法](../../azure-functions/functions-how-to-use-azure-function-app-settings.md)」を参照してください。 

この記事で定義されている関数では、アプリケーション設定に次の環境変数があることを前提としています。

**AMSAADTenantDomain**: Azure AD テナント エンドポイント。 AMS API への接続の詳細については、[こちら](media-services-use-aad-auth-to-access-ams-api.md)の記事を参照してください。

**AMSRESTAPIEndpoint**: REST API エンドポイントを表す URI。 

**AMSClientId**: Azure AD アプリケーション クライアント ID。

**AMSClientSecret**: Azure AD アプリケーション クライアント シークレット。

**StorageConnection**: Media Services アカウントに関連付けられているアカウントのストレージ接続。 この値は、**function.json** ファイルと **run.csx** ファイルで使用されます (下記参照)。

## <a name="create-a-function"></a>関数を作成する

デプロイした関数アプリは、 **[App Services]** の Azure Functions に表示されます。

1. 目的の関数アプリを選択し、 **[新しい関数]** をクリックします。
2. **C#** 言語と **データ処理** シナリオを選択します。
3. **BlobTrigger** テンプレートを選択します。 BLOB を **input** コンテナーにアップロードするたびに、この関数はトリガーされます。 **input** 名は、次の手順の **Path** で指定されます。

    ![スクリーンショットには、BlobTrigger が選択された [テンプレートを選択] ダイアログ ボックスが示されています。](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. **BlobTrigger** を選択すると、一部の追加のコントロールがページに表示されます。

    ![スクリーンショットには、[関数名の指定] ダイアログ ボックスが示されています。](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. **Create** をクリックしてください。 

## <a name="files"></a>ファイル

Azure 関数は、コード ファイルなど、このセクションで取り上げる各種ファイルに関連付けることになります。 Azure Portal を使用して関数を作成すると、**function.json** および **run.csx** が自動的に作成されます。 ユーザーは、**project.json** ファイルを追加するか、アップロードする必要があります。 このセクションの残りの部分では、各ファイルの簡単な説明とその定義を紹介します。

![スクリーンショットには、プロジェクト内の json ファイルが示されています。](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

function.json ファイルは、関数バインドとその他の構成設定を定義します。 ランタイムはこのファイルを使用して、監視対象のイベントを特定し、関数の実行との間でデータを渡したりデータを受け取ったりする方法を判断します。 詳細については、「[Azure Functions における HTTP と Webhook のバインド](../../azure-functions/functions-reference.md#function-code)」を参照してください。

>[!NOTE]
>関数が実行されることを防ぐために、**disabled** プロパティを **true** に設定します。 

既存の function.json ファイルの内容を次のコードで置き換えます。

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.json

project.json ファイルには、依存関係が含まれています。 以下に Nuget からの必要な .NET Azure Media Services を含む **project.json** ファイルの例を示します。 バージョン番号は、パッケージに最新の更新プログラムがあると変更されることに注意してください。そのため、最新バージョンを確認する必要があります。 

次の定義を project.json に追加します。 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```
    
### <a name="runcsx"></a>run.csx

これは関数の C# コードです。  以下で定義されている関数は、新しい MP4 ファイルの (パスで指定された) **input** という名前付きストレージ アカウント コンテナーを監視します。 ストレージ コンテナーにファイルを削除すると、BLOB トリガーは関数を実行します。
    
このセクションで定義されている例では、次の操作を行います 

1. (AMS 資産に BLOB をコピーして) Media Services アカウントに資産を取り込む方法 
2. Media Encoder Standard の "アダプティブ ストリーミング" プリセットを使用するエンコード ジョブを送信する方法。

現実のシナリオでは、ほとんどの場合、ジョブの進行状況を追跡し、エンコードされた資産を発行します。 詳細については、[Azure WebHook を使用して Media Services ジョブ通知を監視する](media-services-dotnet-check-job-progress-with-webhooks.md)に関する記事を参照してください。 例については、「[Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)」を参照してください。  

既存の run.csx ファイルの内容を次のコードで置き換えます。必要な関数を定義したら、 **[保存および実行]** をクリックします。

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

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
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

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
```

## <a name="test-your-function"></a>関数をテストする

関数をテストするには、接続文字列で指定したストレージ アカウントの **input**  コンテナーに MP4 ファイルをアップロードする必要があります。  

1. **StorageConnection** 環境変数で指定したストレージ アカウントを選択します。
2. **[BLOB]** をクリックします。
3. **[+ Container]** (+ コンテナー) をクリックします。 **input** コンテナーに名前を付けます。
4. **[アップロード]** をクリックして、アップロードする .mp4 ファイルを参照します。

>[!NOTE]
> 従量課金プランで BLOB トリガーを使用していると、関数アプリがアイドル状態になったあと、新しい BLOB の処理が最大で 10 分遅延する場合があります。 関数アプリが実行されると、BLOB は直ちに処理されます。 詳しくは、「[BLOB ストレージ トリガーとバインド](../../azure-functions/functions-bindings-storage-blob.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

これで、Media Services アプリケーションの開発準備が整いました。 
 
カスタム コンテンツ作成ワークフローを作成するために Azure Media Services で Azure Functions と Logic Apps を使用するサンプル/ソリューションの詳細については、[GitHub 上のMedia Services .NET Functions 統合サンプル](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)に関する記事を参照してください。

また、「[Azure WebHook を使用して .NET で Media Services ジョブ通知を監視する](media-services-dotnet-check-job-progress-with-webhooks.md)」も参照してください。 

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
