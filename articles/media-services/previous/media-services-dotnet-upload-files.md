---
title: .NET を使用した Media Services アカウントへのファイルのアップロード | Microsoft Docs
description: .NET を使用してアセットを作成し、アップロードすることによって、Media Services にメディア コンテンツを取り込む方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ee9bcc6861245ce40c43570126445a3613cd2744
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013727"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>.NET を使用した Media Services アカウントへのファイルのアップロード

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](../latest/index.yml) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

Media Services で、デジタル ファイルをアセットにアップロードし (取り込み) ます。 **Asset** エンティティには、ビデオ、オーディオ、画像、サムネイル コレクション、テキスト トラック、クローズド キャプション ファイル (各ファイルのメタデータを含む) を追加できます。ファイルをアップロードすると、クラウドにコンテンツが安全に保存され、処理したりストリーミングしたりできるようになります。

アセット内のこれらのファイルを **アセットファイル** といいます。 **AssetFile** インスタンスと実際のメディア ファイルは、別々の 2 つのオブジェクトです。 AssetFile インスタンスには、メディア ファイルに関するメタデータが含まれており、メディア ファイルには実際のメディア コンテンツが含まれています。

## <a name="considerations"></a>考慮事項

次の考慮事項が適用されます。
 
 * Media Services は、ストリーミング コンテンツ (たとえば、 http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters) の URL を構築する際に、IAssetFile.Name プロパティの値を使用します。このため、パーセントエンコーディングは利用できません。 **Name** プロパティの値には、[パーセント エンコーディング予約文字](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) (!*'();:@&=+$,/?%#[]") は使用できません。 また、ファイル名拡張子で使用できる "." は 1 つのみです。
* 名前は 260 文字以内で指定する必要があります。
* Media Services での処理についてサポートされている最大ファイル サイズには制限があります。 ファイル サイズの制限の詳細については、[こちら](media-services-quotas-and-limitations.md)の記事を参照してください。
* さまざまな AMS ポリシー (ロケーター ポリシーや ContentKeyAuthorizationPolicy など) に 1,000,000 ポリシーの制限があります。 常に同じ日数、アクセス許可などを使う場合は、同じポリシー ID を使う必要があります (たとえば、長期間存在するように意図されたロケーターのポリシー (非アップロード ポリシー))。 詳細については、[こちらの記事](media-services-dotnet-manage-entities.md#limit-access-policies)を参照してください。

資産を作成する際には、次の暗号化オプションを指定できます。

* **None** : 暗号化は使用されません。 これが既定値です。 このオプションを使用した場合、送信経路上とストレージ内のいずれにおいてもコンテンツが保護されません。
  プログレッシブ ダウンロードを使用して MP4 を配信する場合は、このオプションを使用します。 
* **CommonEncryption** : 既に Common Encryption や PlayReady DRM で暗号化されて保護されているコンテンツ (PlayReady DRM で保護されたスムーズ ストリーミングなど) をアップロードする場合は、このオプションを使用します。
* **EnvelopeEncrypted** : AES で暗号化された HLS をアップロードする場合はこのオプションを使用します。 この場合ファイルは、Transform Manager によってあらかじめエンコードされて暗号化されている必要があります。
* **StorageEncrypted**: ローカルで AES-256 ビット暗号化を使用し、平文のコンテンツを暗号化したうえで、それを Azure Storage にアップロードします。アップロードされたデータは、暗号化された状態で保存されます。 StorageEncrypted で保護されたアセットは、エンコーディングの前に自動的に暗号化が解除され、暗号化されたファイル システムに配置されます。その後、必要に応じて再度暗号化を適用して、新しい出力アセットとして再びアップロードできます。 StorageEncrypted の主な目的は、高品質の入力メディア ファイルを強力な暗号化によって保護したうえでディスクに保存するというニーズに応えることです。
  
    Media Services では、Digital Rights Manager (DRM) のようにネットワーク経由ではなく、資産のオンディスクでのストレージ暗号化を提供します。
  
    資産がストレージで暗号化されている場合は、資産配信ポリシーを構成する必要があります。 詳細については、「[方法: アセットの配信ポリシーを構成する](media-services-dotnet-configure-asset-delivery-policy.md)」を参照してください。

資産を **CommonEncrypted** オプションか **EnvelopeEncypted** オプションで暗号化することを指定した場合、資産を **ContentKey** に関連付ける必要があります。 詳細については、「 [How to create a ContentKey (ContentKey の作成方法)](media-services-dotnet-create-contentkey.md)」をご覧ください 

資産を **StorageEncrypted** オプションで暗号化することを指定した場合、Media Services SDK for .NET によって、資産の **StorageEncrypted** の **ContentKey** が作成されます。

この記事では、Media Services .NET SDK と Media Services .NET SDK Extensions を使用してファイルを Media Services 資産にアップロードする方法を説明します。

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Media Services .NET SDK を使用して 1 つのファイルをアップロードする

以下のコードは、.NET を使用して 1 つのファイルをアップロードします。 AccessPolicy と Locator の作成と破棄は、Upload 関数によって行います。 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Media Services .NET SDK を使用して複数のファイルをアップロードする
次のコードは、資産を作成して複数のファイルをアップロードする方法を示します。

このコードは、次の処理を実行します。

* 前の手順で定義した CreateEmptyAsset メソッドを使用して空の資産を作成します。
* 資産へのアクセス許可とアクセス期間を定義する **AccessPolicy** インスタンスを作成します。
* 資産へのアクセスを提供する **Locator** インスタンスを作成します。
* **BlobTransferClient** インスタンスを作成します。 この型は、Azure BLOB で動作するクライアントを表します。 この例では、クライアントはアップロードの進行状況を監視します。 
* 指定したディレクトリ内のファイルの一覧を取得し、各ファイルの **AssetFile** インスタンスを作成します。
* **UploadAsync** メソッドを使用して、Media Services にファイルをアップロードします。 

> [!NOTE]
> 非ブロッキング操作としてメソッドを呼び出し、複数のファイルを並列的にアップロードできるようにするため、UploadAsync メソッドを使用します。
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AssetFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


アップロードする資産の数が多い場合は、次の点を考慮してください。

* **CloudMediaContext** オブジェクトは、スレッドごとに作成してください。 **CloudMediaContext** クラスはスレッドセーフではありません。
* NumberOfConcurrentTransfers を既定値の 2 から、たとえば 5 のようなより大きな値に増やしてください。 このプロパティの設定は、 **CloudMediaContext** のすべてのインスタンスに影響を与えます。 
* ParallelTransferThreadCount は、既定値の 10 のままにしてください。

## <a name="ingesting-assets-in-bulk-using-media-services-net-sdk"></a><a id="ingest_in_bulk"></a>Media Services .NET SDK を使用したアセットの一括取り込み
サイズの大きい資産ファイルのアップロードは、資産の作成時に、ボトルネックになることがあります。 資産を一括して取り込む "一括取り込み" の場合、アップロード プロセスから資産の作成を切り離すことが必要です。 一括取り込みを行うには、資産とその関連ファイルを記述するマニフェスト (IngestManifest) を作成します。 その後で、お好みのアップロード方法で、マニフェストの BLOB コンテナーに、関連ファイルをアップロードします。 マニフェストに関連付けられている BLOB コンテナーは、Microsoft Azure Media Services によって監視されます。 ファイルが BLOB コンテナーにアップロードされると、Microsoft Azure Media Services は、マニフェスト (IngestManifestAsset) の資産の構成に基づいてア資産の作成を完了させます。

新しい IngestManifest を作成するには、CloudMediaContext の IngestManifests コレクションで公開されている Create メソッドを呼び出します。 このメソッドでは、指定したマニフェスト名で、新しい IngestManifest が作成されます。

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

一括 IngestManifest に関連付ける資産を作成します。 一括取り込みの対象となる資産に必要な暗号化オプションを構成してください。

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

IngestManifestAsset は、資産を、一括取り込みのための一括 IngestManifest に関連付けます。 また、各資産を構成する AssetFiles を関連付けます。 IngestManifestAsset を作成するには、サーバー コンテキストの Create メソッドを使用します。

以下の例では、先に作成した 2 つの資産を一括取り込みマニフェストに関連付ける 2 つの新しい IngestManifestAssets を追加しています。 また、各 IngestManifestAsset は一括取り込み中に、各資産に対してアップロードされる一連のファイルを関連付けます。  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

クライアント アプリケーションには、IngestManifest の **IIngestManifest.BlobStorageUriForUpload** プロパティによって提供された Blob Storage コンテナーの URI に資産ファイルをアップロードする機能のある任意の高速クライアント アプリケーションをご利用いただけます。 

次のコードでは、.NET SDK を使用して資産ファイルをアップロードする方法を示します。

```csharp
    static void UploadBlobFile(string containerName, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

次のコード例には、この記事で使用されているサンプルのアセット ファイルをアップロードするためのコードが表示されています。

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

**IngestManifest** の Statistics プロパティをポーリングすることによって、**IngestManifest** に関連付けられているすべての資産の一括インジェストの進行状況を確認できます。 進行状況の情報を更新するためには、Statistics プロパティをポーリングするごとに、新しい **CloudMediaContext** を使用する必要があります。

次の例では、IngestManifest を、その **Id** でポーリングしています。

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>.NET SDK Extensions を使用したファイルのアップロード
次の例は、.NET SDK Extensions を使用して単一のファイルをアップロードする方法を示しています。 この例では、**CreateFromFile** メソッドを使用していますが、非同期バージョン (**CreateFromFileAsync**) のメソッドも使用できます。 **CreateFromFile** メソッドには、ファイル名、暗号化オプション、ファイルのアップロードの進行状況をレポートするためのコールバックを指定できます。

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

次の例では、UploadFile 関数を呼び出し、資産作成オプションとしてストレージの暗号化を指定しています。  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>次のステップ

これで、アップロードした資産をエンコードできます。 詳細については、 [資産のエンコード](media-services-portal-encode.md)に関するページをご覧ください。

また、Azure Functions を使用すると、構成したコンテナーに到着するファイルに基づいてエンコード ジョブをトリガーすることもできます。 詳細については、[このサンプル](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ )を参照してください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>次のステップ
これで、資産を Media Services にアップロードできました。次は、[メディア プロセッサを取得する方法][How to Get a Media Processor]に関する記事に進んでください。

[How to Get a Media Processor]: media-services-get-media-processor.md
