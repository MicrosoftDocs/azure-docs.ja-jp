---
title: Azure Storage から大量のランダム データをダウンロードする | Microsoft Docs
description: Azure SDK を使用して Azure Storage アカウントから大量のランダム データをダウンロードする方法を説明します。
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c029abd87e1b819cc4d96e906be8824c019f433
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584976"
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Azure Storage から大量のランダム データをダウンロードする

このチュートリアルは、シリーズの第 3 部です。 このチュートリアルでは、Azure Storage から大量のデータをダウンロードする方法を示します。

シリーズの第 3 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションの更新
> * アプリケーションの実行
> * 接続数の検証

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、前の Storage のチュートリアル「[Azure Storage に大量のランダム データを並列でアップロードする][previous-tutorial]」を完了している必要があります。

## <a name="remote-into-your-virtual-machine"></a>仮想マシンへのリモート接続

 仮想マシンとのリモート デスクトップ セッションを作成するには、ローカル コンピューターで次のコマンドを使用します。 IP アドレスを仮想マシンの publicIPAddress に置き換えます。 メッセージが表示されたら、仮想マシンの作成時に使用した資格情報を入力します。

```console
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>アプリケーションの更新

前のチュートリアルでは、ストレージ アカウントへのファイルのアップロードのみを行いました。 テキスト エディターで `D:\git\storage-dotnet-perf-scale-app\Program.cs` を開きます。 `Main` メソッドを次の例に置き換えます。 この例では、アップロード タスクをコメント アウトし、完了時にダウンロード タスクとストレージ アカウント内のコンテンツを削除するタスクをコメント解除します。

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to 
    // ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage 
    // client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        // await UploadFilesAsync();

        // Uncomment the following line to enable downloading of files from the storage account.
        // This is commented out initially to support the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        await DownloadFilesAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.
        // This is commented out initially as the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        // has you upload only for one tutorial and download for the other.
        if (!exception)
        {
            // await DeleteExistingContainersAsync();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

アプリケーションの更新が完了したら、アプリケーションを再度ビルドする必要があります。 `Command Prompt` を開いて `D:\git\storage-dotnet-perf-scale-app` に移動します。 次の例に示すように、`dotnet build` を実行してアプリケーションをリビルドします。

```console
dotnet build
```

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーションのリビルドが完了したら、更新されたコードでアプリケーションを実行します。 まだ開いていない場合は `Command Prompt` を開き、`D:\git\storage-dotnet-perf-scale-app` に移動します。

`dotnet run` キーを押してアプリケーションを実行します。

```console
dotnet run
```

次の例に、`DownloadFilesAsync` タスクを示します。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

アプリケーションは、**storageconnectionstring** で指定されたストレージ アカウント内にあるコンテナーを読み取ります。 [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) メソッドを使用して BLOB を反復処理し、[DownloadToAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) メソッドを使用してローカル コンピューターにダウンロードします。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_DownloadFilesAsync":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

アプリケーションは、**storageconnectionstring** で指定されたストレージ アカウント内にあるコンテナーを読み取ります。 コンテナー内の [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync) メソッドを使用して一度に 10 個の BLOB を反復処理し、[DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) メソッドを使用してローカル コンピューターにダウンロードします。

次の表に、ダウンロード時に各 BLOB に対して定義される [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) を示します。

|プロパティ|値|[説明]|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| このプロパティは、アップロードされたコンテンツの MD5 ハッシュのチェックを無効にします。 MD5 の検証を無効にすると、転送が高速になります。 ただし、転送されるファイルの有効性や整合性は確認されません。 |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| このプロパティは、MD5 ハッシュが計算されて格納されるかどうかを示します。   |

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestOptions on the download, including disabling MD5 
    // hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.
    // Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container, 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

---

### <a name="validate-the-connections"></a>接続の検証

ファイルのダウンロード中に、ストレージ アカウントへのコンカレント接続の数を確認できます。 コンソール ウィンドウを開き、「`netstat -a | find /c "blob:https"`」と入力します。 このコマンドを実行すると、現在開かれている接続の数が示されます。 次の例からわかるように、ストレージ アカウントからファイルをダウンロードするときに、280 個を超える接続が開かれています。

```console
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>次のステップ

シリーズの第 3 部では、次の方法を含め、ストレージ アカウントから大量のデータをダウンロードする方法について学びました。

> [!div class="checklist"]
> * アプリケーションの実行
> * 接続数の検証

シリーズの第 4 部に進んで、スループットと待ち時間のメトリックをポータルで確認します。

> [!div class="nextstepaction"]
> [スループットと待ち時間のメトリックをポータルで確認する](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md
