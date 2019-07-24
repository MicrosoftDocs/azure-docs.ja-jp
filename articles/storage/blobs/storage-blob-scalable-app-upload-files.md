---
title: Azure Storage に大量のランダム データを並行でアップロードする | Microsoft Docs
description: Azure SDK を使用して Azure Storage アカウントに大量のランダム データを並行でアップロードする方法を説明します。
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: a1dba92a9e156c82f49b9f6f85faf227fc652029
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55240082"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Azure Storage に大量のランダム データを並行でアップロードする

このチュートリアルは、シリーズの第 2 部です。 このチュートリアルでは、大量のランダム データを Azure ストレージ アカウントにアップロードするアプリケーションのデプロイ方法を示します。

シリーズの第 2 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * 接続文字列の構成
> * アプリケーションのビルド
> * アプリケーションの実行
> * 接続数の検証

Azure BLOB Storage では、データを格納するためのスケーラブルなサービスを提供しています。 アプリケーションのパフォーマンスをできる限り高められるように、Blob ストレージの仕組みを理解することをお勧めします。 Azure BLOB の制限事項に関する知識が重要です。これらの制限事項の詳細については、[Blob Storage のスケーラビリティ ターゲット](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets)に関する記述をご覧ください。

[パーティションの名前付け](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#subheading47)は、BLOB を使用して高パフォーマンスのアプリケーションを設計するときの、もう 1 つの重要な要素です。 Azure Storage では、範囲を基にしたパーティション構成を使用して、拡大縮小および負荷分散を行います。 この構成は、類似の名前付け規則またはプレフィックスを持つファイルが同じパーティションに含まれることを意味します。 このロジックには、ファイルのアップロード先となるコンテナーの名前が含まれます。 このチュートリアルでは、ランダムに生成されたコンテンツと名前に対して GUID を保持するファイルを使用します。 その後、それらのファイルは、ランダムな名前の異なる 5 つのコンテナーにアップロードされます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、前の Storage のチュートリアル「[スケーラブルなアプリケーションの仮想マシンおよびストレージ アカウントの作成][previous-tutorial]」を完了している必要があります。

## <a name="remote-into-your-virtual-machine"></a>仮想マシンへのリモート接続

ローカル コンピューターで次のコマンドを使用して、仮想マシンとのリモート デスクトップ セッションを作成します。 IP アドレスを仮想マシンの publicIPAddress に置き換えます。 メッセージが表示されたら、仮想マシンの作成時に使用した資格情報を入力します。

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>接続文字列の構成

Azure Portal のストレージ アカウントに移動します。 ストレージ アカウントの **[設定]** の下にある **[アクセス キー]** を選択します。 プライマリ キーまたはセカンダリ キーの**接続文字列**をコピーします。 以前のチュートリアルで作成した仮想マシンにログインします。 管理者として **[コマンド プロンプト]** を開き、`/m` スイッチを付加して `setx` コマンドを実行します。このコマンドによって、コンピューター設定の環境変数が保存されます。 環境変数は、**コマンド プロンプト**を再度読み込むまで使用できません。 次のサンプルの **\<storageConnectionString\>** を置き換えます。

```
setx storageconnectionstring "<storageConnectionString>" /m
```

完了したら、別の **[コマンド プロンプト]** を開き、`D:\git\storage-dotnet-perf-scale-app` に移動し、`dotnet build` を入力してアプリケーションをビルドします。

## <a name="run-the-application"></a>アプリケーションの実行

`D:\git\storage-dotnet-perf-scale-app` に移動します。

`dotnet run` キーを押してアプリケーションを実行します。 `dotnet` を初めて実行するときは、復元速度を向上させてオフライン アクセスを有効にするために、ローカル パッケージ キャッシュを設定します。 このコマンドを完了するには最大 1 分がかかり、処理は一度だけ実行されます。

```
dotnet run
```

アプリケーションでは、ランダムな名前のコンテナーを 5 つ作成し、ステージング ディレクトリのファイルをストレージ アカウントにアップロードする処理を開始します。 アプリケーションの実行時に大規模数のコンカレント接続が確実に許可されるように、アプリケーションではスレッドの最小数を 100 に、また、[DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) を 100 に設定します。

スレッドの設定と接続制限設定に加えて、[UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) メソッドの [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) が、並行処理を使用し MD5 ハッシュ検証が無効になるように構成されます。 ファイルは 100 MB のブロック単位でアップロードされます。この構成によってパフォーマンスは向上しますが、パフォーマンスが低いネットワークを使用している場合は、100 MB ブロック全体が再試行されるエラーが発生している場合と同然に、負荷が高くなる恐れがあります。

|プロパティ|値|説明|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.paralleloperationthreadcount?view=azure-dotnet)| 8| アップロード時に、設定によって BLOB がブロックに分割されます。 最高のパフォーマンスを得るために、この値はコア数の 8 倍にしておく必要があります。 |
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| true| このプロパティは、アップロードされたコンテンツの MD5 ハッシュのチェックを無効にします。 MD5 の検証を無効にすると、転送が高速になります。 ただし、転送されるファイルの有効性や整合性は確認されません。   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| このプロパティは、MD5 ハッシュが計算されてファイルと共に格納されるかどうかを示します。   |
| [RetryPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.retrypolicy?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_RetryPolicy)| 再試行が最大 10 回行われる 2 秒バックオフ |要求の再試行ポリシーを決定します。 接続エラーが再試行されます。この例では、[ExponentialRetry](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.exponentialretry?view=azure-dotnet) ポリシーが 2 秒バックオフを利用して構成され、再試行回数は最大で 10 回になります。 お使いのアプリケーションが [BLOB ストレージのスケーラビリティ ターゲット](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets)にもう少しで到達する場合は、この設定が重要になります。  |

次の例に、`UploadFilesAsync` タスクを示します。

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

次の例に、Windows システムで実行されたアプリケーションの出力の一部を示します。

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>接続の検証

ファイルのアップロード中に、ストレージ アカウントへのコンカレント接続の数を確認できます。 **コマンド プロンプト**を開き、`netstat -a | find /c "blob:https"` を入力します。 このコマンドは、現在 `netstat` を使用して開かれている接続の数を示します。 実際にチュートリアルを実行したときに表示される出力の例を以下に示します。 この例からわかるように、ストレージ アカウントにランダム ファイルをアップロードするときに、800 個の接続が開かれました。 この値は、アップロードの実行全体を通して変化します。 ブロック チャンクを並行でアップロードすることで、コンテンツの転送に必要な時間が大幅に削減されます。

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>次の手順

シリーズの第 2 部では、次の手順をはじめ、ストレージ アカウントに大量のランダム データを並行でアップロードする方法について学びました。

> [!div class="checklist"]
> * 接続文字列の構成
> * アプリケーションのビルド
> * アプリケーションの実行
> * 接続数の検証

シリーズの第 3 部では、ストレージ アカウントから大量のデータをダウンロードする方法へと進みます。

> [!div class="nextstepaction"]
> [Azure Storage から大量のランダム データをダウンロードする](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
