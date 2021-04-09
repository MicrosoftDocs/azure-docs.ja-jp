---
title: .NET での BLOB スナップショットの作成と管理
titleSuffix: Azure Storage
description: .NET クライアント ライブラリを使用して BLOB の読み取り専用スナップショットを作成することにより、特定の時刻に BLOB データをバックアップする方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ae1d72c30fb7c45c92f24ec5132859a45c6ae953
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95543376"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>.NET での BLOB スナップショットの作成と管理

スナップショットは、ある時点で作成された読み取り専用の BLOB です。 この記事では、[.NET 用の Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage)を使用して BLOB スナップショットを作成および管理する方法について説明します。

Azure Storage での BLOB のスナップショットの詳細については、「[BLOB のスナップショット](snapshots-overview.md)」を参照してください。

## <a name="create-a-snapshot"></a>スナップショットの作成

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

.NET 用の Azure Storage クライアント ライブラリのバージョン 12.x を使用してブロック BLOB のスナップショットを作成するには、次のいずれかのメソッドを使用します。

- [CreateSnapshot](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

次のコード例は、バージョン 12.x でスナップショットを作成する方法を示しています。 Azure AD の資格情報を使用してサービスへの要求を承認するには、[Azure.Identity](https://www.nuget.org/packages/azure.identity) ライブラリへの参照を含めます。

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

.NET 用の Azure Storage クライアント ライブラリのバージョン 11.x を使用してブロック BLOB のスナップショットを作成するには、次のいずれかのメソッドを使用します。

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

次のコード例は、バージョン 11.x でスナップショットを作成する方法を示しています。 この例では、スナップショットの作成時に別のメタデータを指定しています。

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

## <a name="delete-snapshots"></a>スナップショットの削除

BLOB を削除するには、まずその BLOB のスナップショットをすべて削除する必要があります。 スナップショットは個別に削除することも、コピー元 BLOB が削除されたときにすべてのスナップショットを削除するように指定することもできます。 スナップショットがまだ存在する BLOB を削除しようとすると、エラーが発生します。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

.NET 用の Azure Storage クライアント ライブラリのバージョン 12.x を使用して BLOB とそのスナップショットを削除するには、次のいずれかのメソッドを使用し、[DeleteSnapshotsOption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption) 列挙型を含めます。

- [削除](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

次のコード例は、.NET で BLOB とそのスナップショットを削除する方法を示しています。`blobClient` は [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) 型のオブジェクトです。

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

.NET 用の Azure Storage クライアント ライブラリのバージョン 11.x を使用して BLOB とそのスナップショットを削除するには、次のいずれかの BLOB 削除メソッドを使用し、[DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) 列挙型を含めます。

- [削除](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

次のコード例は、.NET で BLOB とそのスナップショットを削除する方法を示しています。`blockBlob` は [CloudBlockBlob][dotnet_CloudBlockBlob] 型のオブジェクトです。

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>次のステップ

- [BLOB のスナップショット](snapshots-overview.md)
- [BLOB のバージョン](versioning-overview.md)
- [BLOB の論理的な削除](./soft-delete-blob-overview.md)