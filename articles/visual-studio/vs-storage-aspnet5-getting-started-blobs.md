---
title: BLOB Storage と Visual Studio 接続済みサービスの概要 (ASP.NET Core) | Microsoft Docs
description: Visual Studio 接続済みサービスを使用してストレージ アカウントを作成した後、Visual Studio の ASP.NET Core プロジェクトで Azure BLOB ストレージの使用を開始する方法について説明します。
services: storage
author: ghogen
manager: jillfra
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: c0f4124ffdb03a7a193791515444c4c7001378d7
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69511566"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Azure Blob ストレージと Visual Studio 接続済みサービスの概要 (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

この記事では、Visual Studio の**接続済みサービス**機能を使用して ASP.NET Core プロジェクトで Azure Storage アカウントを作成または参照した後、Visual Studio で Azure Blob Storage の使用を開始する方法について説明します。 **接続済みサービス**の操作によって、プロジェクト内の Azure Storage にアクセスする適切な NuGet パッケージがインストールされ、プロジェクトの構成ファイルにストレージ アカウントの接続文字列が追加されます。 (Azure Storage の一般情報については、「[Azure Storage のドキュメント](https://azure.microsoft.com/documentation/services/storage/)」をご覧ください。)

Azure BLOB ストレージは、大量の非構造化データを格納して HTTP または HTTPS を介して世界中のどこからでもアクセスできるようにするサービスです。 1 つの BLOB は任意のサイズにできます。 BLOB として扱えるのは、画像、オーディオ ファイル、ビデオ ファイル、生データ、およびドキュメント ファイルのようなデータです。 この記事では、ASP.NET Core プロジェクトで、Visual Studio の**接続済みサービス**機能を使用して Azure Storage アカウントを作成した後、Blob Storage の使用を開始する方法について説明します。

ファイルがフォルダーに格納されるのと同様に、ストレージ BLOB はコンテナーに格納されます。 BLOB を作成した後、その BLOB 内に 1 つ以上のコンテナーを作成します。 たとえば、"Scrapbook" という名前の BLOB に、写真を格納するための "images" という名前のコンテナーと、音声ファイルを格納するための "audio" という名前のコンテナーを作成できます。 コンテナーを作成すると、そこに個別のファイルをアップロードできるようになります。 「[クイック スタート:.NET を使用して BLOB をアップロード、ダウンロード、および一覧表示する](../storage/blobs/storage-quickstart-blobs-dotnet.md)」で、プログラムを使用して BLOB を操作する方法の詳細を参照してください。

一部の Azure Storage API は非同期であるため、この記事のコードでは非同期メソッドが使用されている前提とします。 詳細については、[非同期プログラミング](https://docs.microsoft.com/dotnet/csharp/async)に関するページをご覧ください。

## <a name="access-blob-containers-in-code"></a>コードで BLOB コンテナーにアクセスする

ASP.NET Core プロジェクトでプログラムを使用して BLOB にアクセスするには、次のコードを追加する必要があります (まだ存在していない場合)。

1. 必要な `using` ステートメントを追加します。

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. お客様のストレージ アカウント情報を表す `CloudStorageAccount` オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得します。

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. `CloudBlobClient` オブジェクトを使用して、ストレージ アカウント内の既存のコンテナーへの `CloudBlobContainer` 参照を取得します。

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>コードでコンテナーを作成する

`CloudBlobClient` を使用して、`CreateIfNotExistsAsync` を呼び出すことによりストレージ アカウント内にコンテナーを作成することもできます。

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

コンテナー内のファイルをだれでも利用できるようにするには、コンテナーをパブリックに設定します。

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>コンテナーに BLOB をアップロードする

BLOB ファイルをコンテナーにアップロードするには、コンテナーの参照を取得し、それを使用して BLOB の参照を取得します。 その後、`UploadFromStreamAsync` メソッドを呼び出すことにより、その参照にデータ ストリームをアップロードします。 この操作により、BLOB が存在しない場合は作成され、存在する場合は上書きされます。 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

コンテナー内の BLOB を一覧表示するには、最初にコンテナー参照を取得してから、その `ListBlobsSegmentedAsync` メソッドを呼び出してその中の BLOB やディレクトリを取得します。 返された `IListBlobItem` の豊富なプロパティやメソッド セットにアクセスするには、`CloudBlockBlob`、`CloudPageBlob`、または `CloudBlobDirectory` オブジェクトにそれをキャストします。 BLOB の種類がわからない場合は、種類の確認を使って、どれにキャストするかを判断します。

```cs
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
```

「[クイック スタート:.NET を使用して BLOB をアップロード、ダウンロード、および一覧表示する](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container)」で、BLOB コンテナーのコンテンツを一覧表示する他の方法を参照してください。

## <a name="download-a-blob"></a>BLOB をダウンロードする

BLOB をダウンロードするには、まず BLOB の参照を取得し、次に `DownloadToStreamAsync` メソッドを呼び出します。 次の例では、`DownloadToStreamAsync` メソッドを使用して、ローカル ファイルとして保存できるストリーム オブジェクトに BLOB の内容を転送します。

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

「[クイック スタート:.NET を使用して BLOB をアップロード、ダウンロード、および一覧表示する](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs)」で、BLOB をファイルとして保存する他の方法を参照してください。

## <a name="delete-a-blob"></a>BLOB を削除する

BLOB を削除するには、まず BLOB の参照を取得し、次に `DeleteAsync` メソッドを呼び出します。

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>次の手順

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
