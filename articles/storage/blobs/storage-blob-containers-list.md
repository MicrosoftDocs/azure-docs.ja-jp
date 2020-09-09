---
title: .NET を使用して BLOB コンテナーを一覧表示する - Azure Storage
description: .NET クライアント ライブラリを使用して、Azure Storage アカウント内の BLOB コンテナーを一覧表示する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: f443cd5603e6ca0f60dc0e69b734bfa46138d476
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018945"
---
# <a name="list-blob-containers-with-net"></a>.NET を使用して BLOB コンテナーを一覧表示する

Azure Storage アカウント内のコンテナーをコードから一覧表示する際には、Azure Storage からの結果の取得方法を管理するためのオプションをいくつか指定できます。 この記事では、[.NET 用の Azure Storage クライアント ライブラリ](/dotnet/api/overview/azure/storage?view=azure-dotnet)を使用してコンテナーを一覧表示する方法について説明します。  

## <a name="understand-container-listing-options"></a>コンテナーの一覧表示オプションについて

ストレージ アカウント内のコンテナーを一覧表示するには、次のいずれかのメソッドを呼び出します。

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

これらのメソッドのオーバーロードを使用すると、一覧表示操作によってコンテナーがどのように返されるかを管理するための、追加のオプションを指定できます。 以降のセクションでは、これらのオプションについて説明します。

### <a name="manage-how-many-results-are-returned"></a>返される結果の数を管理する

既定では、一覧表示操作では一度に最大 5000 の結果が返されます。 返される結果セットを減らすには、いずれかの **ListContainerSegmented** メソッドを呼び出すときに、`maxresults` パラメーターに 0 以外の値を指定します。

ストレージ アカウントに 5000 以上のコンテナーが含まれている場合、または一覧表示操作によってストレージ アカウント内のコンテナーのサブセットが返されるように `maxresults` の値を指定した場合、Azure Storage はコンテナーの一覧と共に*継続トークン*を返します。 継続トークンは、Azure Storage から次の結果セットを取得するために使用できる非透過の値です。

コードでは、継続トークンの値をチェックして、それが null かどうかを確認します。 継続トークンが null の場合、結果セットは完了しています。 継続トークンが null でない場合は、**ListContainersSegmented** または **ListContainersSegmentedAsync** をもう一度呼び出し、継続トークンを渡して次の結果セットを取得し、継続トークンが null になるまでそれを繰り返します。

### <a name="filter-results-with-a-prefix"></a>プレフィックスを使用して結果をフィルター処理する

コンテナーの一覧をフィルター処理するには、 `prefix`パラメーターの文字列を指定します。 プレフィックス文字列には、1 つ以上の文字を含めることができます。 Azure Storage は、名前がそのプレフィックスで始まるコンテナーだけを返します。

### <a name="return-metadata"></a>メタデータを返す

結果と共にコンテナーのメタデータを返すには、[ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) 列挙型の **Metadata** 値を指定します。 Azure Storage は、返される各コンテナーにメタデータを追加します。そのため、コンテナーのメタデータを取得するために、いずれかの **FetchAttributes** メソッドを呼び出す必要はありません。

## <a name="example-list-containers"></a>例:コンテナーの一覧表示

次の例では、指定されたプレフィックスで始まるコンテナーがストレージ アカウント内から非同期的に一覧表示されます。 この例では、一度に 5 つの結果を単位としてコンテナーを一覧表示し、継続トークンを使用して次の結果セグメントを取得しています。 また、この例では、結果と共にコンテナーのメタデータも返されます。

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

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

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>関連項目

[コンテナーの一覧表示](/rest/api/storageservices/list-containers2)
[Enumerating Blob Resources (Blob リソースの列挙)](/rest/api/storageservices/enumerating-blob-resources)
