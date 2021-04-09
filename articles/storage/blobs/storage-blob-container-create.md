---
title: .NET を使用して BLOB コンテナーを作成または削除する - Azure Storage
description: .NET クライアント ライブラリを使用して、Azure Storage アカウントで BLOB コンテナーを作成または削除する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/04/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: dac50d8e35080a083e42891732512e012fae8fbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576449"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>.NET を使用して Azure Storage 内でコンテナーを作成または削除する

Azure Storage 内の BLOB はコンテナーにまとめられます。 BLOB をアップロードする前には、まずコンテナーを作成する必要があります。 この記事では、[.NET 用の Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage)を使用してコンテナーを作成および削除する方法について説明します。

## <a name="name-a-container"></a>コンテナーの名前を指定する

コンテナー名は、コンテナーまたはその BLOB をアドレス指定するために使用される一意の URI の一部になるため、有効な DNS 名である必要があります。 コンテナーに名前を付けるときは、次の規則に従います。

- コンテナー名の長さは 3 ～ 63 文字にする必要があります。
- コンテナー名は英文字または数字で始まり、英小文字、数字、ダッシュ (-) 文字のみを含めることができます。
- 2 つ以上の連続するダッシュ文字は、コンテナー名には使用できません。

コンテナーの URI の形式は次のとおりです。

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーを作成するには、次のいずれかのメソッドを呼び出します。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [CreateBlobContainer](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainer)
- [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync)

同じ名前のコンテナーが既に存在する場合、これらのメソッドは例外をスローします。

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [作成](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

同じ名前のコンテナーが既に存在する場合、**Create** および **CreateAsync** メソッドは例外をスローします。

**CreateIfNotExists** および **CreateIfNotExistsAsync** メソッドは、コンテナーが作成されたかどうかを示すブール値を返します。 同じ名前のコンテナーが既に存在する場合、新しいコンテナーが作成されなかったことを示すために、これらのメソッドは **False** を返します。

---

コンテナーは、ストレージ アカウントの直下に作成されます。 コンテナーを別のコンテナーの下に入れ子にすることはできません。

次の例では、コンテナーが非同期的に作成されます。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```
---

## <a name="create-the-root-container"></a>ルート コンテナーを作成する

ルート コンテナーは、ストレージ アカウントの既定のコンテナーとして機能します。 各ストレージ アカウントには、 *$root.* という名前のルート コンテナーを 1 つ含めることができます。 ルート コンテナーは明示的に作成または削除する必要があります。

ルート コンテナーに格納されている BLOB は、ルート コンテナー名を指定せずに参照できます。 ルート コンテナーを使用すると、ストレージ アカウント階層の最上位レベルにある BLOB を参照できます。 たとえば、ルート コンテナー内に存在する BLOB は、次の方法で参照できます。

`https://myaccount.blob.core.windows.net/default.html`

次の例では、ルート コンテナーが非同期的に作成されます。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateRootContainer":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```
---

## <a name="delete-a-container"></a>コンテナーを削除する

.NET でコンテナーを削除するには、次のいずれかのメソッドを呼び出します。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [削除](/dotnet/api/azure.storage.blobs.blobcontainerclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexistsasync)

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [削除](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)
---

**Delete** および **DeleteAsync** メソッドは、コンテナーが存在しない場合に例外をスローします。

**DeleteIfExists** および **DeleteIfExistsAsync** メソッドは、コンテナーが削除されたかどうかを示すブール値を返します。 指定されたコンテナーが存在しない場合、これらのメソッドは、コンテナーが削除されなかったことを示す **False** を返します。

コンテナーを削除した後、*少なくとも* 30 秒間は同じ名前のコンテナーを作成することはできません。 同じ名前のコンテナーを作成しようとすると、HTTP エラー コード 409 (Conflict) が返されて処理が失敗します。 コンテナーまたはそれに含まれる BLOB に対して他の操作を実行しようとすると、HTTP エラー コード 404 (Not Found) が返されて処理が失敗します。

次の例では、指定されたコンテナーが削除され、コンテナーが存在しない場合には例外が処理されます。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

次の例は、指定したプレフィックスで始まるすべてのコンテナーを削除する方法を示しています。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteContainersWithPrefixAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            await container.DeleteAsync();
        }

        Console.WriteLine();
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>関連項目

- [コンテナーの作成操作](/rest/api/storageservices/create-container)
- [コンテナーの削除操作](/rest/api/storageservices/delete-container)
