---
title: Microsoft Azure Storage Data Movement Library を使用してデータを転送する | Microsoft Docs
description: Data Movement Library ユーティリティを使用して、BLOB およびファイル間でデータを移動またはコピーします。 ローカル ファイルから Azure ストレージにデータをコピーする、またはストレージ アカウント内またはその間でデータをコピーします。 Azure Storage にデータを簡単に移行します。
services: storage
author: seguler
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 09/27/2017
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: 0641a097761530285c2dd9aa176ddd8c2c159001
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58878742"
---
# <a name="transfer-data-with-the-microsoft-azure-storage-data-movement-library"></a>Microsoft Azure Storage Data Movement Library を使用してデータを転送する

## <a name="overview"></a>概要
Microsoft Azure Storage Data Movement Library は、Azure Storage BLOB およびファイルの高パフォーマンスのアップロード、ダウンロード、およびコピーを実現するように設計された、クロスプラットフォームのオープン ソース ライブラリです。 このライブラリは、[AzCopy](../storage-use-azcopy.md) を動作させているコア データ移動フレームワークです。 Data Movement Library には、従来の [.NET Azure Storage クライアント ライブラリ](../blobs/storage-dotnet-how-to-use-blobs.md)では使用できない便利な方法が用意されています。 たとえば、並列操作数を設定する、転送の進行状況を追跡する、取り消された転送を簡単に再開する、などの操作を行うことができます。

また、.NET Core が採用されているため、Windows、Linux、macOS 向け .NET アプリを構築するときにも、このライブラリを使用できます。 .NET Core の詳細については、[.NET Core のドキュメント](https://dotnet.github.io/)を参照してください。 このライブラリは、Windows 用の従来の .NET Framework アプリにも利用できます。

このドキュメントでは、Windows、Linux、および macOS で実行される .NET Core コンソール アプリケーションの作成方法について説明し、以下のシナリオを実行します。

- ファイルとディレクトリを Blob Storage にアップロードする。
- データを転送するときに、並列操作の数を定義する。
- データ転送の進捗状況を追跡する。
- 取り消されたデータ転送を再開する。
- URL から Blob Storage にファイルをコピーする。
- Blob Storage 間でコピーする。

**必要なもの:**

* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure ストレージ アカウント](storage-quickstart-create-account.md)

> [!NOTE]
> このガイドでは、既に [Azure Storage](https://azure.microsoft.com/services/storage/)を使い慣れている前提で説明します。 使い慣れていない場合は、「[Microsoft Azure Storage の概要](storage-introduction.md)」を参照してください。 最も重要なのは、Data Movement Library を使用し始めるには、[ストレージ アカウントを作成する](storage-quickstart-create-account.md)必要がある、ということです。
>
>

## <a name="setup"></a>セットアップ

1. 「[.NET Core Installation Guide (.NET Core インストール ガイド)](https://www.microsoft.com/net/core)」にアクセスして、.NET Core をインストールします。 環境を選択するときは、コマンド ライン オプションを選択します。
2. コマンド ラインで、プロジェクトのディレクトリを作成します。 このディレクトリに移動し、「`dotnet new console -o <sample-project-name>`」と入力して、C# コンソール プロジェクトを作成します。
3. Visual Studio Code でこのディレクトリを開きます。 この手順をコマンド ラインですばやく実行するには、Windows で「`code .`」と入力します。
4. Visual Studio Code Marketplace から [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) をインストールします。 Visual Studio Code を再起動します。
5. この時点で 2 つのプロンプトが表示されます。 1 つは "ビルドとデバッグに必要なアセット" を追加するよう求めるプロンプトで、 [はい] をクリックします。 もう 1 つは、未解決の依存関係を復元するように求めるプロンプトで、 [復元] をクリックします。
6. `.vscode` の `launch.json` を変更し、コンソールとして外部ターミナルを使用するようにします。 この設定は `"console": "externalTerminal"` のようになります。
7. Visual Studio Code を使用すると、.NET Core アプリケーションをデバッグできます。 `F5` キーを押してアプリケーションを実行し、セットアップが動作していることを確認します。 "Hello World!" が コンソールに表示されます。

## <a name="add-data-movement-library-to-your-project"></a>プロジェクトへの Data Movement Library の追加

1. 最新バージョンの Data Movement Library を `<project-name>.csproj` ファイルの `dependencies` セクションに追加します。 この資料の作成時点では、このバージョンは `"Microsoft.Azure.Storage.DataMovement": "0.6.2"` です
2. プロジェクトを復元するように求めるメッセージが表示されます。 [復元] ボタンをクリックします。 プロジェクト ディレクトリのルートで `dotnet restore` コマンドを入力して、コマンド ラインからプロジェクトを復元することもできます。

`<project-name>.csproj` を変更します。

    <Project Sdk="Microsoft.NET.Sdk">

        <PropertyGroup>
            <OutputType>Exe</OutputType>
            <TargetFramework>netcoreapp2.0</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
            <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
            </ItemGroup>
        </Project>

## <a name="set-up-the-skeleton-of-your-application"></a>アプリケーションのスケルトンの設定
最初に アプリケーションの "スケルトン" コードに設定します。 このコードにより、ストレージ アカウント名とアカウント キーを入力するように求められ、その資格情報を使用して `CloudStorageAccount` オブジェクトが作成されます。 このオブジェクトは、すべての転送シナリオでストレージ アカウントを操作するときに使用されます。 また、コードでは、実行する転送操作の種類を選択することも求められます。

`Program.cs` を変更します。

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="transfer-local-file-to-azure-blob"></a>Azure BLOB へのローカル ファイルの転送
`GetSourcePath` メソッドと `GetBlob` メソッドを `Program.cs` に追加します。

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

`TransferLocalFileToAzureBlob` メソッドを変更します。

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

このコードにより、ローカル ファイルへのパス、新規または既存のコンテナーの名前、および新しい BLOB の名前を入力するように求められます。 `TransferManager.UploadAsync` メソッドは、この情報を使用してアップロードを実行します。

`F5` キーを押してアプリケーションを実行します。 アップロードが行われたことを確認するには、[Microsoft Azure ストレージ エクスプ ローラー](https://storageexplorer.com/)でストレージ アカウントを表示します。

## <a name="set-number-of-parallel-operations"></a>並列操作数の設定
Data Movement Library では、並列操作数を設定できます。この優れた機能によって、データ転送のスループットを向上させることができます。 既定では、Data Movement Library の並列操作数は "8 x コンピューター上のコア数" に設定されています。

低帯域幅の環境で大量に並列操作を実行すると、接続するネットワークに過剰な負荷がかかり、操作を完了できなくなる場合があることに注意してください。 利用可能なネットワーク帯域幅で最適に動作する並列操作数を確認するには、この設定をテストする必要があります。

並列操作数を設定するためのコードを追加してみましょう。 さらに、転送が完了するまでの時間を測定するコードも追加します。

`SetNumberOfParallelOperations` メソッドを `Program.cs` に追加します。

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

`SetNumberOfParallelOperations` を使用するように `ExecuteChoice` メソッドを変更します。

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

タイマーを使用するように `TransferLocalFileToAzureBlob` メソッドを変更します。

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>転送の進捗状況の追跡
データの転送にかかった時間がわかっていると便利ですが、 転送操作 "*の間に*" 転送の進捗状況を確認できると、さらに役に立ちます。 このシナリオを実現するには、`TransferContext` オブジェクトを作成する必要があります。 `TransferContext` オブジェクトには `SingleTransferContext` と `DirectoryTransferContext` の 2 つのフォームがあります。 前者は 1 つのファイルを転送するためのフォーム (現在の操作) で、後者はファイルのディレクトリを転送するためのフォームです (後で追加します)。

`GetSingleTransferContext` メソッドと `GetDirectoryTransferContext` メソッドを `Program.cs` に追加します。

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

`GetSingleTransferContext` を使用するように `TransferLocalFileToAzureBlob` メソッドを変更します。

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>取り消された転送の再開
Data Movement Library に用意されている便利な機能として、取り消された転送を再開するというものがあります。 まず「`c`」と入力して、一時的に転送を取り消すためのコードを追加しましょう。そして、3 秒後に転送を再開します。

`TransferLocalFileToAzureBlob` を変更します。

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

これまでは、`checkpoint` 値は常に `null` に設定されていました。 ここで、転送を取り消すと、転送の最後のチェックポイントが取得され、この新しいチェックポイントが転送コンテキストで使用されます。

## <a name="transfer-local-directory-to-azure-blob-directory"></a>Azure BLOB ディレクトリへのローカル ディレクトリの転送
Data Movement Library で転送できるのが一度に 1 ファイルだとすると残念です。 幸い、そうではありません。 Data Movement Library には、ファイルのディレクトリとそのサブディレクトリすべてを転送する機能が用意されています。 それを実現するコードを追加してみましょう。

最初に、`GetBlobDirectory` メソッドを `Program.cs` に追加します。

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

次に、`TransferLocalDirectoryToAzureBlobDirectory` を変更します。

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

このメソッドと、1 つのファイルをアップロードするためのメソッドの間には違いがいくつかあります。 現時点では、`TransferManager.UploadDirectoryAsync` と、前に作成した `getDirectoryTransferContext` メソッドを使用しています。 また、`options` 値をアップロード操作に提供します。これにより、サブディレクトリをアップロードに含めることを指定できます。

## <a name="copy-file-from-url-to-azure-blob"></a>URL から Azure BLOB へのファイルのコピー
ここで、URL から Azure BLOB にファイルをコピーするためのコードを追加してみましょう。

`TransferUrlToAzureBlob` を変更します。

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

この機能の使用事例として重要なのは、別のクラウド サービス (AWS など) からデータを Azure に移動する必要がある場合です。 リソースへのアクセスを提供する URL がある場合は、`TransferManager.CopyAsync` メソッドを使用することで、そのリソースを簡単に Azure BLOB に移動できます。 このメソッドには、新しいブール型パラメーターも導入されています。 このパラメーターを `true` に設定すると、非同期のサーバー側コピーを実行する必要があることを示します。 このパラメーターを `false` に設定した場合は、同期コピーが行われます。つまり、リソースは、ローカル コンピューターにダウンロードされてから、Azure BLOB にアップロードされます。 ただし、同期コピーは、現在 Azure Storage リソース間でのコピーにしか利用できません。

## <a name="transfer-azure-blob-to-azure-blob"></a>Azure BLOB 間の転送
Data Movement Library には、Azure Storage リソース間でのコピーという固有の機能も用意されています。

`TransferAzureBlobToAzureBlob` を変更します。

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

この例では、`TransferManager.CopyAsync` のブール値パラメーターを `false` に設定して、同期コピーを実行する必要があることを指定します。 つまり、リソースは、ローカル コンピューターにダウンロードされてから、Azure BLOB にアップロードされます。 同期コピー オプションは、コピー操作を一定の速度で確実に行うには最適です。 これに対し、非同期のサーバー側コピーの速度は、サーバーで利用可能なネットワーク帯域幅に依存しており、変動します。 ただし、同期コピーでは、非同期コピーと比較すると、追加の送信コストが発生する可能性があります。 この送信コストが発生しないように、同期コピーは、ソース ストレージ アカウントと同じリージョンにある Azure VM で使用することをお勧めします。

## <a name="conclusion"></a>まとめ
これでデータ移動アプリケーションが完成しました。 [完全なコード サンプルは GitHub で入手できます](https://github.com/azure-samples/storage-dotnet-data-movement-library-app)。

## <a name="next-steps"></a>次の手順
この入門では、Azure Storage とやり取りし、Windows、Linux、macOS で実行されるアプリケーションを作成しました。 この入門では、Blob Storage を重点的に取り上げていますが、 これと同じ情報は File Storage にも適用できます。 詳細については、[Azure Storage Data Movement Library のリファレンス ドキュメント](https://azure.github.io/azure-storage-net-data-movement)を参照してください。

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
