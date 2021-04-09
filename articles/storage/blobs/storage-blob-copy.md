---
title: Azure Storage API を使用して BLOB をコピーする
description: Azure Storage クライアント ライブラリを使用して BLOB をコピーする方法について説明します。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/08/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 28b23555f97e58dde038c5e3e12a4b741b81776d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98059043"
---
# <a name="copy-a-blob-with-azure-storage-client-libraries"></a>Azure Storage クライアント ライブラリを使用して BLOB をコピーする

この記事では、Azure Storage アカウントで BLOB をコピーする方法について説明します。 また、非同期のコピー操作を中止する方法も示します。 コード例では Azure Storage クライアント ライブラリを使用します。

## <a name="about-copying-blobs"></a>BLOB のコピーについて

同じストレージ アカウント内で BLOB をコピーすると、同期操作になります。 アカウントをまたいでコピーする場合は、非同期操作です。

コピー操作のコピー元 BLOB にできるのは、ブロック BLOB、アペンド BLOB、ページ BLOB、またはスナップショットです。 コピー先 BLOB が既に存在する場合、コピー元 BLOB と同じ BLOB の種類である必要があります。 既存のコピー先 BLOB は上書きされます。

コピー操作の進行中は、コピー先 BLOB を変更できません。 コピー先 BLOB には、未完了のコピー操作は 1 つしか存在できません。 言い換えると、1 つの BLOB を、複数の保留中のコピー操作のコピー先にすることはできません。

常に、コピー元の BLOB またはファイル全体がコピーされます。 バイト範囲またはブロックのセットのコピーはサポートされていません。

BLOB がコピーされると、そのシステム プロパティが同じ値でコピー先 BLOB にコピーされます。

コピー操作は、次のいずれかの形態で実行できます。

- コピー元 BLOB を別の名前でコピー先 BLOB にコピーします。 コピー先 BLOB は、同じ BLOB の種類 (ブロック、アペンド、またはページ) の既存の BLOB でも、コピー操作によって作成される新しい BLOB でもかまいません。
- コピー元 BLOB を同じ名前でコピー先 BLOB にコピーして、コピー先 BLOB を実質的に置き換えます。 このようなコピー操作では、コミットされていないブロックはすべて削除され、コピー先 BLOB のメタデータが上書きされます。
- Azure File Service 内のコピー元ファイルをコピー先 BLOB にコピーします。 コピー先 BLOB は、既存のブロック BLOB でも、コピー操作によって作成される新しいブロック BLOB でもかまいません。 ファイルからページ BLOB またはアペンド BLOB へのコピーはサポートされていません。
- スナップショットをベース BLOB にコピーします。 スナップショットをベース BLOB に昇格することにより、BLOB を以前のバージョンに復元できます。
- スナップショットを別の名前でコピー先 BLOB にコピーします。 結果として得られるコピー先 BLOB は書き込み可能な BLOB であり、スナップショットではありません。

## <a name="copy-a-blob"></a>BLOB をコピーする

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

BLOB をコピーするには、次のいずれかのメソッドを呼び出します。

- [StartCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuri)
- [StartCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuriasync)

`StartCopyFromUri` メソッドと `StartCopyFromUriAsync` メソッドは、コピー操作に関する情報が含まれる [CopyFromUriOperation](/dotnet/api/azure.storage.blobs.models.copyfromurioperation) オブジェクトを返します。

次のコード例では、以前に作成した BLOB を表す [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) を取得し、同じコンテナー内の新しい BLOB にコピーします。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_CopyBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

BLOB をコピーするには、次のいずれかのメソッドを呼び出します。

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync)

`StartCopy` および `StartCopyAsync` メソッドは、状態を確認またはコピー操作を中止するために使用されるコピー ID の値を返します。

次のコード例では、以前に作成した BLOB への参照を取得し、同じコンテナー内の新しい BLOB にコピーします。

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation 
        // to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

# <a name="python-v12"></a>[Python v12](#tab/python)

BLOB をコピーするには、[start_copy_from_url](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#start-copy-from-url-source-url--metadata-none--incremental-copy-false----kwargs-) メソッドを呼び出します。 `start_copy_from_url` メソッドは、コピー操作に関する情報を含むディクショナリを返します。

次のコード例では、以前に作成した BLOB を表す [BlobClient](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient) を取得し、同じコンテナー内の新しい BLOB にコピーします。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_BlobCopy":::

---

## <a name="abort-a-copy-operation"></a>コピー操作を中止する

コピー操作を中止すると、コピー先 BLOB の長さは 0 になります。 ただし、コピー先 BLOB のメタデータは、コピー元 BLOB からコピーされた値、またはコピー操作中に明示的に設定された値に変わります。 コピー前のメタデータを元のまま維持するには、いずれかのコピー方法を呼び出す前に、コピー先 BLOB のスナップショットを作成します。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

コピー先 BLOB の [BlobProperties.CopyStatus](/dotnet/api/azure.storage.blobs.models.blobproperties.copystatus) プロパティをチェックして、コピー操作の状態を取得します。 コピーが完了すると、最終 BLOB がコミットされます。

コピー操作を中止すると、コピー先 BLOB のコピーの状態は [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus) に設定されます。

[AbortCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuri) メソッドと [AbortCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuriasync) メソッドによって、進行中のコピー操作がキャンセルされます。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_StopBlobCopy":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

コピー先 BLOB の [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status) プロパティをチェックして、コピー操作の状態を取得します。 コピーが完了すると、最終 BLOB がコミットされます。

コピー操作を中止すると、コピー先 BLOB のコピーの状態は [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus) に設定されます。

[AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy) メソッドと [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync) メソッドによって、進行中のコピー操作がキャンセルされます。

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

# <a name="python-v12"></a>[Python v12](#tab/python)

[get_blob_properties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#get-blob-properties---kwargs-) メソッドによって返された [CopyProperties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) ディクショナリで "status" エントリを調べて、コピー操作の状態を取得します。 コピーが完了すると、最終 BLOB がコミットされます。

コピー操作を中止すると、[状態](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties)が "aborted" に設定されます。

[abort_copy](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#abort-copy-copy-id----kwargs-) メソッドは、進行中のコピー操作をキャンセルします。

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_StopBlobCopy":::

---

## <a name="azure-sdks"></a>Azure SDK

Azure SDK に関する詳細を確認します。

 - [Azure SDK for .NET](https://github.com/azure/azure-sdk-for-net)
 - [Azure SDK for Java](https://github.com/azure/azure-sdk-for-java)
 - [Azure SDK for Python](https://github.com/azure/azure-sdk-for-python)
 - [Azure SDK for JavaScript](https://github.com/azure/azure-sdk-for-js)

## <a name="next-steps"></a>次のステップ

次のトピックでは、Azure REST API を使用した BLOB のコピーと、進行中のコピー操作の中止に関する情報を示しています。

- [Copy Blob](/rest/api/storageservices/copy-blob)
- [BLOB のコピーを中止する](/rest/api/storageservices/abort-copy-blob)
