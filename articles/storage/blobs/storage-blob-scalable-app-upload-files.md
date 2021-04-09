---
title: Azure Storage に大量のランダム データを並列でアップロードする
description: Azure Storage クライアント ライブラリを使用して Azure Storage アカウントに大量のランダム データを並列でアップロードする方法を説明します。
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: ed7020a58f3f15403108934bcc3fab644bd1b627
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584467"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Azure Storage に大量のランダム データを並行でアップロードする

このチュートリアルは、シリーズの第 2 部です。 このチュートリアルでは、大量のランダム データを Azure ストレージ アカウントにアップロードするアプリケーションのデプロイ方法を示します。

シリーズの第 2 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * 接続文字列の構成
> * アプリケーションのビルド
> * アプリケーションの実行
> * 接続数の検証

Microsoft Azure Blob Storage では、データを格納するためのスケーラブルなサービスを提供しています。 アプリケーションのパフォーマンスをできる限り高められるように、Blob ストレージの仕組みを理解することをお勧めします。 Azure BLOB の制限事項に関する知識が重要です。これらの制限事項の詳細については、[Blob ストレージのスケーラビリティおよびパフォーマンスのターゲット](../blobs/scalability-targets.md)に関する記事を参照してください。

[パーティションの名前付け](../blobs/storage-performance-checklist.md#partitioning)は、BLOB を使用して高パフォーマンスのアプリケーションを設計するときに、もう 1 つの重要な要素になる場合があります。 4 MiB 以上のブロック サイズの場合、[高スループット ブロック BLOB](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) が使用され、パーティションの名前付けはパフォーマンスに影響しません。 4 MiB 未満のブロック サイズの場合、Azure Storage では、範囲を基にしたパーティション構成を使用して、拡大縮小および負荷分散を行います。 この構成は、類似の名前付け規則またはプレフィックスを持つファイルが同じパーティションに含まれることを意味します。 このロジックには、ファイルのアップロード先となるコンテナーの名前が含まれます。 このチュートリアルでは、ランダムに生成されたコンテンツと名前に対して GUID を保持するファイルを使用します。 その後、それらのファイルは、ランダムな名前の異なる 5 つのコンテナーにアップロードされます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、前の Storage のチュートリアル「[スケーラブルなアプリケーションの仮想マシンおよびストレージ アカウントの作成][previous-tutorial]」を完了している必要があります。

## <a name="remote-into-your-virtual-machine"></a>仮想マシンへのリモート接続

ローカル コンピューターで次のコマンドを使用して、仮想マシンとのリモート デスクトップ セッションを作成します。 IP アドレスを仮想マシンの publicIPAddress に置き換えます。 メッセージが表示されたら、仮想マシンの作成時に使用した資格情報を入力します。

```console
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>接続文字列の構成

Azure Portal のストレージ アカウントに移動します。 ストレージ アカウントの **[設定]** の下にある **[アクセス キー]** を選択します。 プライマリ キーまたはセカンダリ キーの **接続文字列** をコピーします。 以前のチュートリアルで作成した仮想マシンにログインします。 管理者として **[コマンド プロンプト]** を開き、`/m` スイッチを付加して `setx` コマンドを実行します。このコマンドによって、コンピューター設定の環境変数が保存されます。 環境変数は、**コマンド プロンプト** を再度読み込むまで使用できません。 次のサンプルでは、 **\<storageConnectionString\>** を置き換えます。

```console
setx storageconnectionstring "<storageConnectionString>" /m
```

完了したら、別の **[コマンド プロンプト]** を開き、`D:\git\storage-dotnet-perf-scale-app` に移動し、`dotnet build` を入力してアプリケーションをビルドします。

## <a name="run-the-application"></a>アプリケーションの実行

`D:\git\storage-dotnet-perf-scale-app` に移動します。

`dotnet run` キーを押してアプリケーションを実行します。 `dotnet` を初めて実行するときは、復元速度を向上させてオフライン アクセスを有効にするために、ローカル パッケージ キャッシュを設定します。 このコマンドを完了するには最大 1 分がかかり、処理は一度だけ実行されます。

```console
dotnet run
```

アプリケーションでは、ランダムな名前のコンテナーを 5 つ作成し、ステージング ディレクトリのファイルをストレージ アカウントにアップロードする処理を開始します。

`UploadFilesAsync` メソッドを次の例に示します。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_UploadFilesAsync":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

多数のコンカレント接続が許可されるように、スレッドの最小数と最大数が 100 に設定されています。

```csharp
private static async Task UploadFilesAsync()
{
    // Create five randomly named containers to store the uploaded files.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();

    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // Path to the directory to upload
    string uploadPath = currentdir + "\\upload";

    // Start a timer to measure how long it takes to upload all the files.
    Stopwatch time = Stopwatch.StartNew();

    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);

        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections
        // are retried with a back off policy. As multiple large files are being uploaded using
        // large block sizes, this can cause an issue if an exponential retry policy is not defined.
        // Additionally, parallel operations are enabled with a thread count of 8.
        // This should be a multiple of the number of processor cores in the machine.
        // Lastly, MD5 hash validation is disabled for this example, improving the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };

        // Create a new instance of the SemaphoreSlim class to 
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set the block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;

            await sem.WaitAsync();

            // Create a task for each file to upload. The tasks are
            // added to a collection and all run asynchronously.
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));

            count++;
        }

        // Run all the tasks asynchronously.
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
スレッドの設定と接続制限設定に加えて、[UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) メソッドの [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) が、並行処理を使用し MD5 ハッシュ検証が無効になるように構成されます。 ファイルは 100 MB のブロック単位でアップロードされます。この構成によってパフォーマンスは向上しますが、パフォーマンスが低いネットワークを使用している場合は、100 MB ブロック全体が再試行されるエラーが発生している場合と同然に、負荷が高くなる恐れがあります。

|プロパティ|値|説明|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| アップロード時に、設定によって BLOB がブロックに分割されます。 最高のパフォーマンスを得るために、この値はコア数の 8 倍にしておく必要があります。 |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| このプロパティは、アップロードされたコンテンツの MD5 ハッシュのチェックを無効にします。 MD5 の検証を無効にすると、転送が高速になります。 ただし、転送されるファイルの有効性や整合性は確認されません。   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| このプロパティは、MD5 ハッシュが計算されてファイルと共に格納されるかどうかを示します。   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| 再試行が最大 10 回行われる 2 秒バックオフ |要求の再試行ポリシーを決定します。 接続エラーが再試行されます。この例では、[ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry) ポリシーが 2 秒バックオフを利用して構成され、再試行回数は最大で 10 回になります。 この設定は、お使いのアプリケーションが BLOB ストレージのスケーラビリティ ターゲットにもう少しで到達するときに重要になります。 詳細については、[Blob ストレージのスケーラビリティおよびパフォーマンスのターゲット](../blobs/scalability-targets.md)に関する記事を参照してください。  |

---

次の例に、Windows システムで実行されたアプリケーションの出力の一部を示します。

```console
Created container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Created container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Created container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Created container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Created container 92480da9-f695-4a42-abe8-fb35e71eb887
Iterating in directory: C:\git\myapp\upload
Found 5 file(s)
Uploading 1d596d16-f6de-4c4c-8058-50ebd8141e4d.pdf to container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Uploading 242ff392-78be-41fb-b9d4-aee8152a6279.pdf to container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Uploading 38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.pdf to container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Uploading 45930d63-b0d0-425f-a766-cda27ff00d32.pdf to container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Uploading 5129b385-5781-43be-8bac-e2fbb7d2bd82.pdf to container 92480da9-f695-4a42-abe8-fb35e71eb887
Uploaded 5 files in 16.9552163 seconds
```

### <a name="validate-the-connections"></a>接続の検証

ファイルのアップロード中に、ストレージ アカウントへのコンカレント接続の数を確認できます。 コンソール ウィンドウを開き、「`netstat -a | find /c "blob:https"`」と入力します。 このコマンドを実行すると、現在開かれている接続の数が示されます。 次の例からわかるように、ストレージ アカウントにランダム ファイルをアップロードするときに、800 個の接続が開かれています。 この値は、アップロードの実行全体を通して変化します。 ブロック チャンクを並行でアップロードすることで、コンテンツの転送に必要な時間が大幅に削減されます。

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>次のステップ

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
