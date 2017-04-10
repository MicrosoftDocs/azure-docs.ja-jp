---
title: ".NET を使用して Azure Blob Storage (オブジェクト ストレージ) を使用する | Microsoft Docs"
description: "Azure BLOB ストレージ (オブジェクト ストレージ) を使用して、非構造化データをクラウドに格納します。"
services: storage
documentationcenter: .net
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: d18a8fc8-97cb-4d37-a408-a6f8107ea8b3
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 29d8693dd1d6d1ef26ccb21e3a5b29cf1adbfcc5
ms.lasthandoff: 03/28/2017

---
# <a name="get-started-with-azure-blob-storage-using-net"></a>.NET を使用して Azure Blob Storage を使用する

[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

Azure Blob Storage は、非構造化データをクラウド内にオブジェクト/BLOB として格納するサービスです。 Blob Storage は、ドキュメント、メディア ファイル、アプリケーション インストーラーなど、任意の種類のテキスト データやバイナリ データを格納できます。 Blob Storage は、オブジェクト ストレージとも呼ばれます。

### <a name="about-this-tutorial"></a>このチュートリアルについて
このチュートリアルでは、Azure Blob Storage を使用していくつかの一般的なシナリオの .NET コードを記述する方法を示します。 紹介するシナリオは、BLOB のアップロード、一覧表示、ダウンロード、および削除です。

**前提条件:**

* [Microsoft Visual Studio](https://www.visualstudio.com/)
* [.NET 用 Azure Storage クライアント ライブラリ](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [.NET 用 Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Azure ストレージ アカウント](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>その他のサンプル
Blob Storage を使用したその他の例については、「 [Getting Started with Azure Blob Storage in .NET (.NET での Azure Blob Storage の使用)](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)」を参照してください。 サンプル アプリケーションをダウンロードして実行することも、GitHub でコードを参照することもできます。

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-using-directives"></a>using ディレクティブを追加する
次の **using** ディレクティブを `Program.cs` ファイルの先頭に追加します。

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types
```

### <a name="parse-the-connection-string"></a>接続文字列を解析する
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>BLOB サービス クライアントの作成
**CloudBlobClient** クラスを使用すると、Blob Storage 内に格納されているコンテナーと BLOB を取得できます。 サービス クライアントを作成する方法の 1 つを次に示します。

```csharp
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```
これで、Blob Storage に対してデータの読み取りと書き込みを実行するコードを記述する準備が整いました。

## <a name="create-a-container"></a>コンテナーを作成する
[!INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

この例は、コンテナーがない場合に、コンテナーを作成する方法を示しています。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();
```

既定では、新しいコンテナーはプライベートなので、このコンテナーから BLOB をダウンロードするにはストレージ アクセス キーを指定する必要があります。 コンテナー内のファイルをだれでも利用できるようにする場合は、次のコードを使ってコンテナーをパブリックに設定できます。

```csharp
container.SetPermissions(
    new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });
```

パブリック コンテナー内の BLOB は、インターネットに接続しているすべてのユーザーが表示できます。 ただし、それらを変更または削除できるのは、適切なアカウント アクセス キーまたは Shared Access Signature を持っているユーザーだけです。

## <a name="upload-a-blob-into-a-container"></a>コンテナーに BLOB をアップロードする
Azure Blob Storage では、ブロック BLOB とページ BLOB がサポートされています。  ほとんどの場合は、ブロック BLOB を使用することをお勧めします。

ファイルをブロック blob にアップロードするには、コンテナーの参照を取得し、それを使用してブロック blob の参照を取得します。 BLOB の参照を取得したら、 **UploadFromStream** メソッドを呼び出すことによって、データの任意のストリームを BLOB にアップロードできます。 この操作により、BLOB がまだ存在しない場合は作成され、存在する場合は上書きされます。

次の例は、BLOB をコンテナーにアップロードする方法を示しています。この例では、既にコンテナーが作成されていることを前提としています。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    blockBlob.UploadFromStream(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する
コンテナー内の BLOB を一覧表示するには、まず、コンテナーの参照を取得します。 次に、コンテナーの **ListBlobs** メソッドを使用して、その中の BLOB やディレクトリを取得できます。 返される **IListBlobItem** のプロパティやメソッドにアクセスするには、**CloudBlockBlob**、**CloudPageBlob**、**CloudBlobDirectory** のいずれかのオブジェクトにキャストする必要があります。 型がわからない場合は、型チェックを使うとどれにキャストすればよいかがわかります。 次のコードは、_photos_ コンテナー内の各アイテムの URI を取得して出力する方法を示しています。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("photos");

// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, false))
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
```

BLOB の名前にパス情報を含めることで、従来のファイル システムと同じように、整理およびスキャン可能な仮想ディレクトリ構造を作成できます。 ディレクトリ構造は仮想のみであり、Blob Storage で使用できるリソースはコンテナーと BLOB のみです。 ただし、ストレージ クライアント ライブラリでは、仮想ディレクトリを参照し、この方法で整理される BLOB の操作プロセスを簡略化するための **CloudBlobDirectory** オブジェクトが提供されます。

たとえば、*photos* というコンテナーに次のブロック BLOB があるとします。

```
photo1.jpg
2010/architecture/description.txt
2010/architecture/photo3.jpg
2010/architecture/photo4.jpg
2011/architecture/photo5.jpg
2011/architecture/photo6.jpg
2011/architecture/description.txt
2011/photo7.jpg
```

*photos* コンテナーの **ListBlobs** を呼び出すと (前のコード スニペットを参照)、階層化された一覧が返されます。 これには **CloudBlobDirectory** と **CloudBlockBlob** の両方のオブジェクトが含まれ、コンテナー内のディレクトリと BLOB をそれぞれ表します。 結果の出力は次のようになります。

```
Directory: https://<accountname>.blob.core.windows.net/photos/2010/
Directory: https://<accountname>.blob.core.windows.net/photos/2011/
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

必要に応じて、**ListBlobs** メソッドの **UseFlatBlobListing** パラメーターを **true** に設定することもできます。 この場合、コンテナー内のすべての BLOB は **CloudBlockBlob** オブジェクトとして返されます。 フラットな一覧を返す **ListBlobs** の呼び出しは次のようになります。

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, true))
{
    ...
}
```

結果は次のようになります。

```
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

## <a name="download-blobs"></a>BLOB をダウンロードする
BLOB をダウンロードするには、まず BLOB の参照を取得し、次に **DownloadToStream** メソッドを呼び出します。 次の例は、 **DownloadToStream** メソッドを使用して、ローカル ファイルに保存できるストリーム オブジェクトに BLOB の内容を転送します。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save blob contents to a file.
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    blockBlob.DownloadToStream(fileStream);
}
```

**DownloadToStream** メソッドを使用して BLOB の内容をテキスト文字列としてダウンロードすることもできます。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob.txt"
CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

string text;
using (var memoryStream = new MemoryStream())
{
    blockBlob2.DownloadToStream(memoryStream);
    text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
}
```

## <a name="delete-blobs"></a>BLOB を削除する
BLOB を削除するには、まず、BLOB の参照を取得し、次にその **Delete** メソッドを呼び出します。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
blockBlob.Delete();
```

## <a name="list-blobs-in-pages-asynchronously"></a>BLOB をページで非同期に一覧表示する
多数の BLOB を一覧表示する場合や、1 回の一覧表示操作で返される結果の数を制御する場合には、BLOB の一覧を結果のページで表示できます。 この例は、大きな結果のセットを返すために待機している間に実行がブロックされないように、結果をページで非同期に返す方法を示しています。

この例では、BLOB をフラットな一覧で表示しますが、**ListBlobsSegmentedAsync** メソッドの _useFlatBlobListing_ パラメーターを _false_ に設定することで、階層化された一覧で表示することもできます。

サンプル メソッドは非同期メソッドを呼び出すため、先頭に _async_ キーワードを付ける必要があり、また、**Task** オブジェクトを返す必要があります。 **ListBlobsSegmentedAsync** メソッドに対して指定された await キーワードは、一覧表示タスクが完了するまで、サンプル メソッドの実行を中断します。

```csharp
async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
{
    //List blobs to the console window, with paging.
    Console.WriteLine("List blobs in pages:");

    int i = 0;
    BlobContinuationToken continuationToken = null;
    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

## <a name="writing-to-an-append-blob"></a>追加 BLOB への書き込み
追加 BLOB は、ログ記録などの追加操作のために最適化されています。 ブロック BLOB のように、追加 BLOB はブロックで構成されますが、追加 BLOB に新しいブロックを追加する場合は常に BLOB の最後に追加されます。 追加 BLOB の既存のブロックは更新したり、削除することはできません。 追加 BLOB のブロック ID はブロック BLOB 用のため、公開されることはありません。

追加 BLOB 内の各ブロックは、最大 4 MB のサイズにすることができます。また追加 BLOB には最大 50,000 のブロックを含めることができます。 よって追加 BLOB の最大サイズは 195 GB (4 MB X 50,000 ブロック) よりも少し大きくなります。

次の例では、新しい追加 BLOB を作成し、データを追加してシンプルなログ記録操作をシミュレートしています。

```csharp
//Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

//Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

//Create the container if it does not already exist.
container.CreateIfNotExists();

//Get a reference to an append blob.
CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

//Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
//You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
appendBlob.CreateOrReplace();

int numBlocks = 10;

//Generate an array of random bytes.
Random rnd = new Random();
byte[] bytes = new byte[numBlocks];
rnd.NextBytes(bytes);

//Simulate a logging operation by writing text data and byte data to the end of the append blob.
for (int i = 0; i < numBlocks; i++)
{
    appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
        DateTime.UtcNow, bytes[i], Environment.NewLine));
}

//Read the append blob to the console window.
Console.WriteLine(appendBlob.DownloadText());
```

3 つの BLOB タイプにおける違いの詳細については、「 [Understanding Block Blobs, Page Blobs, and Append Blobs (ブロック BLOB、ページ BLOB、追加 BLOB を理解する)](/rest/api/storageservices/fileservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) 」をご覧ください。

## <a name="managing-security-for-blobs"></a>BLOB のセキュリティの管理
既定では、Azure Storage はアカウント所有者へのアクセスを制限することによってデータのセキュリティを維持します。アカウントの所有者は、アカウント アクセス キーを所持している人です。 ストレージ アカウント内の BLOB データを共有する必要がある場合は、アカウント アクセス キーのセキュリティを損なわずに共有することが重要です。 また、ネットワーク経由の送信と Azure Storage でのセキュリティを確保するために、BLOB データを暗号化することもできます。

[!INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>BLOB データへのアクセスの制御
既定では、ストレージ アカウント内の BLOB データには、ストレージ アカウント所有者だけがアクセスできます。 Blob Storage に対する認証要求には、既定ではアカウント アクセス キーが必要です。 ただし、特定の BLOB データは他のユーザーが使用できるようにしたい場合があります。 2 つのオプションがあります。

* **匿名アクセス:** コンテナーまたはその BLOB を匿名アクセスに対して公開することができます。 詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](storage-manage-access-to-resources.md) 」を参照してください。
* **Shared Access Signature**: Shared Access Signature (SAS) をクライアントに提供することができます。この署名は、ストレージ アカウント内のリソースへの委任アクセスと指定されたアクセス許可を、指定された期間にわたって提供します。 詳細については、「 [Using Shared Access Signatures (SAS) (Shared Access Signature (SAS) の使用)](storage-dotnet-shared-access-signature-part-1.md) 」を参照してください。

### <a name="encrypting-blob-data"></a>BLOB データの暗号化
Azure Storage では、クライアント側とサーバー側の両方で、BLOB データの暗号化をサポートしています。

* **クライアント側の暗号化** : .NET 用ストレージ クライアント ライブラリは、開発者が Azure Storage にアップロードする前にクライアント アプリケーション内のデータを暗号化し、クライアントにダウンロードするときにデータを復号化する作業を支援します。 また、このライブラリは Azure Key Vault との統合にも役立ち、ストレージ アカウント キー管理に利用することができます。 詳細については、 [.NET による Microsoft Azure Storage のクライアント側の暗号化](storage-client-side-encryption.md) についてのページを参照してください。 また、「 [チュートリアル: Azure Key Vault を使用した Microsoft Azure Storage 内の BLOB の暗号化と復号化](storage-encrypt-decrypt-blobs-key-vault.md)」も参照してください。
* **サーバー側の暗号化**: Azure Storage は現在、サーバー側の暗号化をサポートしています。 「 [Azure Storage Service Encryption for Data at Rest (プレビュー)](storage-service-encryption.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
これで、Blob Storage の基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure ストレージ エクスプローラー
* [Microsoft Azure ストレージ エクスプローラー (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md) は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。

### <a name="blob-storage-samples"></a>Blob Storage のサンプル
* [.Net での Azure Blob Storage の概要](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Blob Storage リファレンス
* [.NET 用ストレージ クライアント ライブラリ リファレンス](https://msdn.microsoft.com/library/azure/mt347887.aspx)
* [REST API リファレンス](/rest/api/storageservices/fileservices/azure-storage-services-rest-api-reference)

### <a name="conceptual-guides"></a>概念的なガイド
* [AzCopy コマンド ライン ユーティリティを使ったデータの転送](storage-use-azcopy.md)
* [.NET 用の File Storage の概要](storage-dotnet-how-to-use-files.md)
* [Web ジョブ SDK で Azure Blob Storage を使用する方法](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

