---
title: "ストレージ アカウントから Azure Media Services 資産に BLOB をコピーする | Microsoft Docs"
description: "このトピックでは、既存の BLOB を Media Services 資産にコピーする方法を説明します。 このサンプルでは、Azure Media Services .NET SDK Extensions を使用します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6a63823f-f3c9-424c-91b8-566f70bec346
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/31/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 14bdbe593d8c6ec5297ce37ce57b759deb533cec
ms.lasthandoff: 03/14/2017


---
# <a name="copying-existing-blobs-into-a-media-services-asset"></a>既存の BLOB を Media Services 資産にコピーする
このトピックでは、[Azure Media Services .NET SDK Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/) を使用して、ストレージ アカウントから新しい Azure Media Services (AMS) 資産に BLOB をコピーする方法について説明します。

この拡張メソッドは、次のものに使用できます。

- 通常の資産。
- ライブ アーカイブ資産 (FragBlob 形式)。
- 異なる Media Services アカウント (異なるデータ センター間でも可) に属する送信元と送信先の資産。 ただし、これによって料金が発生する可能性があります。 価格の詳細については、「 [データ転送](https://azure.microsoft.com/pricing/#header-11)」をご覧ください。

> [!NOTE]
> Media Services によって生成された BLOB コンテナーの内容を変更する場合は、必ず Media Service API を使用してください。
> 

このトピックでは、2 つのコード サンプルを示します。

1. AMS アカウントの資産から別の AMS アカウントの新しい資産に BLOB をコピーします。
2. ストレージ アカウントから AMS アカウントの新しい資産に BLOB をコピーします。

## <a name="copy-blobs-between-two-ams-accounts"></a>2 つの AMS アカウント間で BLOB をコピーする  

### <a name="prerequisites"></a>前提条件

2 つの Media Services アカウント。 「[メディア サービス アカウントの作成方法](media-services-portal-create-account.md)」を参照してください。

### <a name="download-sample"></a>サンプルのダウンロード
この記事の手順に従うか、この記事で説明されているコードを含むサンプルを[ここ](https://azure.microsoft.com/documentation/samples/media-services-dotnet-copy-blob-into-asset/)からダウンロードできます。

### <a name="set-up-your-project"></a>プロジェクトの設定

1. Visual Studio を使用すると、C# コンソール アプリケーション プロジェクトを含む新しいソリューションを作成できます。 
3. [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) を使用して Azure Media Services .NET SDK Extensions (windowsazure.mediaservices.extensions) をインストールして追加します。 このパッケージをインストールすると、Media Services .NET SDK が一緒にインストールされるほか、必要な依存関係がすべて追加されます。
4. このプロジェクト (System.Configuration) に必要なその他の参照を追加します。
6. appSettings セクションを .config ファイルに追加し、Media Services アカウント、送信先のストレージ アカウント、および送信元の資産 ID に基づいて値を更新します。 
   
        <appSettings>
              <add key="MediaServicesSourceAccountName" value="name"/>
              <add key="MediaServicesSourceAccountKey" value="key"/>
              <add key="MediaServicesDestAccountName" value="name"/>
              <add key="MediaServicesDestAccountKey" value="key"/>
              <add key="DestStorageAccountName" value="name"/>
              <add key="DestStorageAccountKey" value="key"/>
              <add key="SourceAssetID" value="nb:cid:UUID:assetID"/>       
        </appSettings>

### <a name="copy-blobs-from-an-asset-in-one-ams-account-into-an-asset-in-another-ams-account"></a>AMS アカウントの資産から別の AMS アカウントの資産に BLOB をコピーする

次のコードは、**IAsset.Copy** 拡張メソッドを使用して、送信元の資産内のすべてのファイルを送信先の資産にコピーします。 非同期をサポートする追加のオーバーロドがあります。

    using System;
    using System.Linq;
    using System.Configuration;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.Storage.Auth;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            static string _sourceAaccountName = ConfigurationManager.AppSettings["MediaServicesSourceAccountName"];
            static string _sourceAccountKey = ConfigurationManager.AppSettings["MediaServicesSourceAccountKey"];
            static string _destAccountName = ConfigurationManager.AppSettings["MediaServicesDestAccountName"];
            static string _destAccountKey = ConfigurationManager.AppSettings["MediaServicesDestAccountKey"];
            static string _destStorageAccountName = ConfigurationManager.AppSettings["DestStorageAccountName"];
            static string _destStorageAccountKey = ConfigurationManager.AppSettings["DestStorageAccountKey"];
            static string _sourceAssetID = ConfigurationManager.AppSettings["SourceAssetID"];
            
    
            private static CloudMediaContext _sourceContext = null;
            private static CloudMediaContext _destContext = null;
    
            static void Main(string[] args)
            {
                // Create the context for your source Media Services account.
    
                // Use the cached credentials to create CloudMediaContext.
                _sourceContext = new CloudMediaContext(new MediaServicesCredentials(
                    _sourceAaccountName,
                    _sourceAccountKey));

                // Create the context for your destination Media Services account.
                _destContext = new CloudMediaContext(new MediaServicesCredentials(
                    _destAccountName,
                    _destAccountKey));
    
                // Get the credentials of the default Storage account bound to your destination Media Services account.
                StorageCredentials destinationStorageCredentials =
                    new StorageCredentials(_destStorageAccountName, _destStorageAccountKey);
    
                // Get a reference to the source asset in the source context.
                IAsset sourceAsset = _sourceContext.Assets.Where(a => a.Id == _sourceAssetID).First();
    
                // Create an empty destination asset in the destination context.
                IAsset destinationAsset = _destContext.Assets.Create(sourceAsset.Name, AssetCreationOptions.None);
    
                // Copy the files in the source asset instance into the destination asset instance.
                // There is an additional overload with async support.
                sourceAsset.Copy(destinationAsset, destinationStorageCredentials);

                
            }
        }
    }


## <a name="copy-blobs-from-a-storage-account-into-an-ams-account"></a>ストレージ アカウントから AMS アカウントに BLOB をコピーする 

### <a name="prerequisites"></a>前提条件

- BLOB をコピーする元の&1; つのストレージ アカウント。
- BLOB をコピーする先の&1; つの AMS アカウント。

### <a name="set-up-your-project"></a>プロジェクトの設定

1. Visual Studio を使用すると、C# コンソール アプリケーション プロジェクトを含む新しいソリューションを作成できます。 
3. [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) を使用して Azure Media Services .NET SDK Extensions (windowsazure.mediaservices.extensions) をインストールして追加します。 このパッケージをインストールすると、Media Services .NET SDK が一緒にインストールされるほか、必要な依存関係がすべて追加されます。
4. このプロジェクト (System.Configuration) に必要なその他の参照を追加します。
6. appSettings セクションを .config ファイルに追加し、コピー元のストレージ アカウントとコピー先の AMS アカウントに基づいて値を更新します。
   
          <appSettings>
            <add key="MediaServicesAccountName" value="name" />
            <add key="MediaServicesAccountKey" value="key" />
            <add key="MediaServicesStorageAccountName" value="name" />
            <add key="MediaServicesStorageAccountKey" value="key" />
            <add key="SourceStorageAccountName" value="name" />
            <add key="SourceStorageAccountKey" value="key" />
          </appSettings>

### <a name="copy-blobs-from-some-storage-account-into-a-new-asset-in-a-ams-account"></a>ストレージ アカウントから AMS アカウントの新しい資産に BLOB をコピーする

次のコードでは、ストレージ アカウントから Media Services 資産に BLOB をコピーします。 

>[!NOTE]
>さまざまな AMS ポリシー (ロケーター ポリシーや ContentKeyAuthorizationPolicy など) に 1,000,000 ポリシーの制限があります。 常に同じ日数、アクセス許可などを使う場合は、同じポリシー ID を使う必要があります (たとえば、長期間存在するように意図されたロケーターのポリシー (非アップロード ポリシー))。 詳細については、 [こちらの](media-services-dotnet-manage-entities.md#limit-access-policies) トピックを参照してください。

    using System;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    
    namespace CopyExistingBlobsIntoAsset
    {
        class Program
        {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
            private static readonly string _mediaServicesStorageAccountName =
                ConfigurationManager.AppSettings["MediaServicesStorageAccountName"];
            private static readonly string _mediaServicesStorageAccountKey =
                ConfigurationManager.AppSettings["MediaServicesStorageAccountKey"];
            private static readonly string _sourceStorageAccountName =
         ConfigurationManager.AppSettings["SourceStorageAccountName"];
            private static readonly string _sourceStorageAccountKey =
                ConfigurationManager.AppSettings["SourceStorageAccountKey"];
    
            // Field for service context.
            private static CloudMediaContext _context = null;
            private static CloudStorageAccount _sourceStorageAccount = null;
            private static CloudStorageAccount _destinationStorageAccount = null;
    
            static void Main(string[] args)
            {
    
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey));
    
                _sourceStorageAccount = 
                    new CloudStorageAccount(new StorageCredentials(_sourceStorageAccountName, 
                        _sourceStorageAccountKey), true);
    
                _destinationStorageAccount = 
                    new CloudStorageAccount(new StorageCredentials(_mediaServicesStorageAccountName, 
                        _mediaServicesStorageAccountKey), true);
                
                CloudBlobClient sourceCloudBlobClient = 
                    _sourceStorageAccount.CreateCloudBlobClient();
                CloudBlobContainer sourceContainer = 
                    sourceCloudBlobClient.GetContainerReference("NameOfBlobContainerYouWantToCopy");
    
                CreateAssetFromExistingBlobs(sourceContainer);
            }
            
            static public IAsset CreateAssetFromExistingBlobs(CloudBlobContainer sourceBlobContainer)
            {
                CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();
    
                // Create a new asset. 
                IAsset asset = _context.Assets.Create("NewAsset_" + Guid.NewGuid(), AssetCreationOptions.None);
    
                IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
                    TimeSpan.FromHours(24), AccessPermissions.Write);
    
                ILocator destinationLocator = 
                    _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    
                // Get the asset container URI and Blob copy from mediaContainer to assetContainer. 
                CloudBlobContainer destAssetContainer =
                    destBlobStorage.GetContainerReference((new Uri(destinationLocator.Path)).Segments[1]);
    
                if (destAssetContainer.CreateIfNotExists())
                {
                    destAssetContainer.SetPermissions(new BlobContainerPermissions
                    {
                        PublicAccess = BlobContainerPublicAccessType.Blob
                    });
                }
    
                var blobList = sourceBlobContainer.ListBlobs();
    
                foreach (var sourceBlob in blobList)
                {
                    var assetFile = asset.AssetFiles.Create((sourceBlob as ICloudBlob).Name);
    
                    ICloudBlob destinationBlob = destAssetContainer.GetBlockBlobReference(assetFile.Name);
    
                    // Call the CopyBlobHelpers.CopyBlobAsync extension method to copy blobs.
                    using (Task task = 
                        CopyBlobHelpers.CopyBlobAsync((CloudBlockBlob)sourceBlob, 
                            (CloudBlockBlob)destinationBlob, 
                            new BlobRequestOptions(), 
                            CancellationToken.None))
                    {
                        task.Wait();
                    }
    
                    assetFile.ContentFileSize = (sourceBlob as ICloudBlob).Properties.Length;
                    assetFile.Update();
                    Console.WriteLine("File {0} is of {1} size", assetFile.Name, assetFile.ContentFileSize);
                }
    
                asset.Update();
    
                destinationLocator.Delete();
                writePolicy.Delete();
    
                // Set the primary asset file.
                // If, for example, we copied a set of Smooth Streaming files, 
                // set the .ism file to be the primary file. 
                // If we, for example, copied an .mp4, then the mp4 would be the primary file. 
                var ismAssetFiles = asset.AssetFiles.ToList().
                    Where(f => f.Name.EndsWith(".ism", StringComparison.OrdinalIgnoreCase)).ToArray();
    
                // The following code assigns the first .ism file as the primary file in the asset.
                // An asset should have one .ism file.  
                ismAssetFiles.First().IsPrimary = true;
                ismAssetFiles.First().Update();
    
                return asset;
            }
        }
    }
    
## <a name="next-steps"></a>次のステップ

これで、アップロードした資産をエンコードできます。 詳細については、 [資産のエンコード](media-services-portal-encode.md)に関するページをご覧ください。

また、Azure Functions を使用すると、構成したコンテナーに到着するファイルに基づいてエンコード ジョブをトリガーすることもできます。 詳細については、[このサンプル](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ )を参照してください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


