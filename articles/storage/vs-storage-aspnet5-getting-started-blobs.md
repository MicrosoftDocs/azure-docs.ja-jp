---
title: "Blob Storage と Visual Studio 接続済みサービスの概要 (ASP.NET 5) | Microsoft Docs"
description: "Visual Studio 接続済みサービスを使用してストレージ アカウントを作成した後、Visual Studio の ASP.NET 5 プロジェクトで Azure BLOB ストレージの使用を開始する方法について説明します。"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 88e6ce0deeb5dab276b5ae49f6c99391e37495f4
ms.openlocfilehash: 2a31f6d8aa00e89e8dbbe0089fc76ecbb2102b3c


---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-5"></a>Azure BLOB ストレージと Visual Studio 接続済みサービスの概要 (ASP.NET 5)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概要
この記事では、Visual Studio の [接続済みサービスの追加] ダイアログを使用して ASP.NET 5 プロジェクトで Azure ストレージ アカウントを作成または参照した後、Visual Studio で Azure BLOB ストレージの使用を開始する方法について説明します。

Azure BLOB ストレージは、大量の非構造化データを格納して HTTP または HTTPS を介して世界中のどこからでもアクセスできるようにするサービスです。 1 つの BLOB は任意のサイズにできます。 BLOB として扱えるのは、画像、オーディオ ファイル、ビデオ ファイル、生データ、およびドキュメント ファイルのようなデータです。 この記事では、ASP.NET 5 プロジェクトで、Visual Studio の **[接続済みサービスの追加]** ダイアログを使用して Azure ストレージ アカウントを作成した後、BLOB ストレージを使用する方法について説明します。

ファイルがフォルダーに格納されるのと同様に、ストレージ BLOB はコンテナーに格納されます。 ストレージを作成した後、その内部に&1; つまたは複数のコンテナーを作成します。 たとえば、"Scrapbook" という名前のストレージに、写真を格納するための "images" という名前のコンテナーと、音声ファイルを格納するための "audio" という名前のコンテナーを作成できます。 コンテナーを作成すると、個々の BLOB ファイルをコンテナーにアップロードできるようになります。 プログラムを使用して BLOB を操作する方法の詳細については、「 [.NET を使用して Azure Blob Storage を使用する](storage-dotnet-how-to-use-blobs.md) 」をご覧ください。

## <a name="access-blob-containers-in-code"></a>コードで BLOB コンテナーにアクセスする
ASP.NET 5 プロジェクトでプログラムを使用して BLOB にアクセスするには、次の項目を追加する必要があります (存在していない場合)。

1. プログラムを使用して Azure Storage にアクセスするすべての C# ファイルの冒頭部分に、次の名前空間宣言コードを追加します。
   
        using Microsoft.Extensions.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Extensions.Logging.LogLevel;
2. ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。
   
         CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
            "<storage-account-name>",
            "<access-key>"), true);
   
    **注:** 上記のコードはすべて、以下のセクションに示すコードの前に使用してください。
3. **CloudBlobClient** オブジェクトを使用して、ストレージ アカウント内の既存のコンテナーを参照する **CloudBlobContainer** オブジェクトを取得します。
   
        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
   
        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

## <a name="create-a-container-in-code"></a>コードでコンテナーを作成する
**CloudBlobClient** を使用して、ストレージ アカウント内にコンテナーを作成することもできます。 必要なのは、次のコードのように、 **CreateIfNotExistsAsync** への呼び出しを追加することだけです。

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


**注:** ASP.NET 5 で Azure Storage への呼び出しを実行する API は非同期です。 詳細については、 [Async および Await を使用した非同期プログラミング](http://msdn.microsoft.com/library/hh191443.aspx) に関するページをご覧ください。 次のコードでは、非同期のプログラミング方法を使用していることを前提としています。

コンテナー内のファイルをだれでも利用できるようにするには、次のコードを使ってコンテナーをパブリックに設定できます。

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

## <a name="upload-a-blob-into-a-container"></a>コンテナーに BLOB をアップロードする
BLOB ファイルをコンテナーにアップロードするには、コンテナーの参照を取得し、それを使用して BLOB の参照を取得します。 BLOB の参照を取得した後、**UploadFromStreamAsync** メソッドを呼び出すことで、データの任意のストリームを BLOB にアップロードできます。 この処理により、BLOB が存在しない場合は作成され、存在する場合は上書きされます。 次の例は、BLOB をコンテナーにアップロードする方法を示しています。この例では、既にコンテナーが作成されていることを前提としています。

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する
コンテナー内の BLOB を一覧表示するには、まず、コンテナーの参照を取得します。 次に、コンテナーの **ListBlobsSegmentedAsync** メソッドを呼び出して、その中の BLOB やディレクトリを取得します。 返される **IListBlobItem** のプロパティやメソッドにアクセスするには、**CloudBlockBlob**、**CloudPageBlob**、**CloudBlobDirectory** のいずれかのオブジェクトにキャストする必要があります。 BLOB の種類がわからない場合は、種類の確認を使うとどれにキャストすればよいかがわかります。 次のコードは、コンテナー内の各項目の URI を取得して出力する方法を示しています。

    BlobContinuationToken token = null;
    do
    {
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
        token = resultSegment.ContinuationToken;

        foreach (IListBlobItem item in resultSegment.Results)
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
            }

            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob pageBlob = (CloudPageBlob)item;

                Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
            }

            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory directory = (CloudBlobDirectory)item;

                Console.WriteLine("Directory: {0}", directory.Uri);
            }
        }
    } while (token != null);

BLOB コンテナーの内容を一覧表示する方法は他にもあります。 詳細については、「 [.NET を使用して Azure Blob Storage を使用する](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) 」をご覧ください。

## <a name="download-a-blob"></a>BLOB をダウンロードする
BLOB をダウンロードするには、まず BLOB の参照を取得し、次に **DownloadToStreamAsync** メソッドを呼び出します。 次の例では、 **DownloadToStreamAsync** メソッドを使用して、ローカル ファイルに保存できるストリーム オブジェクトに BLOB の内容を転送します。

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        await blockBlob.DownloadToStreamAsync(fileStream);
    }

BLOB をファイルとして保存する方法は他にもあります。 詳細については、「 [.NET を使用して Azure Blob Storage を使用する](storage-dotnet-how-to-use-blobs.md#download-blobs) 」をご覧ください。

## <a name="delete-a-blob"></a>BLOB を削除する
BLOB を削除するには、まず BLOB の参照を取得し、次に **DeleteAsync** メソッドを呼び出します。

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    await blockBlob.DeleteAsync();

## <a name="next-steps"></a>次のステップ
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]




<!--HONumber=Jan17_HO1-->


