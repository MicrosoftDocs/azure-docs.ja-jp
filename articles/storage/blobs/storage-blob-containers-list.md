---
title: .NET を使用して BLOB コンテナーを一覧表示する - Azure Storage
description: .NET クライアント ライブラリを使用して、Azure Storage アカウント内の BLOB コンテナーを一覧表示する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: a12fc991734fe74e450aa14a477f3a4500ba659c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96937255"
---
# <a name="list-blob-containers-with-net"></a>.NET を使用して BLOB コンテナーを一覧表示する

Azure Storage アカウント内のコンテナーをコードから一覧表示する際には、Azure Storage からの結果の取得方法を管理するためのオプションをいくつか指定できます。 この記事では、[.NET 用の Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage)を使用してコンテナーを一覧表示する方法について説明します。  

## <a name="understand-container-listing-options"></a>コンテナーの一覧表示オプションについて

ストレージ アカウント内のコンテナーを一覧表示するには、次のいずれかのメソッドを呼び出します。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

これらのメソッドのオーバーロードを使用すると、一覧表示操作によってコンテナーがどのように返されるかを管理するための、追加のオプションを指定できます。 以降のセクションでは、これらのオプションについて説明します。

### <a name="manage-how-many-results-are-returned"></a>返される結果の数を管理する

既定では、一覧表示操作では一度に最大 5000 の結果が返されます。 返される結果セットが小さくなるようにするには、返される結果ページのサイズに 0 以外の値を指定します。

ストレージ アカウントに 5000 を超えるコンテナーが含まれている場合、または一覧表示操作によってストレージ アカウント内のコンテナーのサブセットが返されるようにページ サイズを指定した場合、Azure Storage はコンテナーの一覧と共に "*継続トークン*" を返します。 継続トークンは、Azure Storage から次の結果セットを取得するために使用できる非透過の値です。

コードでは、継続トークンの値をチェックして、それが空 (.NET v12 の場合) または null (.NET v11 以前の場合) であるかどうかを確認します。 継続トークンが null の場合、結果セットは完了しています。 継続トークンが null でない場合は、継続トークンが null になるまで、一覧表示メソッドをもう一度呼び出し、継続トークンを渡して次の結果セットを取得します。

### <a name="filter-results-with-a-prefix"></a>プレフィックスを使用して結果をフィルター処理する

コンテナーの一覧をフィルター処理するには、 `prefix`パラメーターの文字列を指定します。 プレフィックス文字列には、1 つ以上の文字を含めることができます。 Azure Storage は、名前がそのプレフィックスで始まるコンテナーだけを返します。

### <a name="return-metadata"></a>メタデータを返す

結果と共にコンテナーのメタデータを返すには、[BlobContainerTraits](/dotnet/api/azure.storage.blobs.models.blobcontainertraits) 列挙型 (.NET v12 の場合) または [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) 列挙型 (.NET v11 以前の場合) の **メタデータ** 値を指定します。 Azure Storage では、返される各コンテナーにメタデータが含まれているため、コンテナーのメタデータもフェッチする必要はありません。

## <a name="example-list-containers"></a>例:コンテナーの一覧表示

次の例では、指定されたプレフィックスで始まるコンテナーがストレージ アカウント内から非同期的に一覧表示されます。 この例では、指定されたプレフィックスで始まるコンテナーを一覧表示し、一覧表示操作の呼び出しごとに指定された数の結果を返します。 その後、継続トークンを使用して、結果の次のセグメントを取得します。 また、この例では、結果と共にコンテナーのメタデータも返されます。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="Snippet_ListContainers":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

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

- [コンテナーの一覧表示](/rest/api/storageservices/list-containers2)
- [BLOB リソースの列挙](/rest/api/storageservices/enumerating-blob-resources)
