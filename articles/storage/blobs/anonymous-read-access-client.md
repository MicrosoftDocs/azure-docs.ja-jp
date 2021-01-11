---
title: .NET を使用してパブリックのコンテナーと BLOB に匿名でアクセスする
titleSuffix: Azure Storage
description: .NET の Azure Storage クライアント ライブラリを使用して、パブリックのコンテナーと BLOB に匿名でアクセスします。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-csharp
ms.openlocfilehash: 1ba36c7128f72f5181f3fed53f5615ab464e3325
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013539"
---
# <a name="access-public-containers-and-blobs-anonymously-with-net"></a>.NET を使用してパブリックのコンテナーと BLOB に匿名でアクセスする

Azure Storage では、コンテナーと BLOB へのオプションのパブリック読み取りアクセスがサポートされています。 クライアントは、Azure Storage クライアント ライブラリを使用することに加え、Azure Storage へのデータ アクセスをサポートする他のツールやユーティリティを使用して、匿名でパブリックのコンテナーと BLOB にアクセスできます。

この記事では、.NET からパブリックのコンテナーまたは BLOB にアクセスする方法について説明します。 コンテナーに対する匿名読み取りアクセスの構成については、「[コンテナーと BLOB の匿名パブリック読み取りアクセスを構成する](anonymous-read-access-configure.md)」を参照してください。 ストレージ アカウントへのすべての匿名アクセスを禁止することについては、「[コンテナーと BLOB への匿名パブリック読み取りアクセスを防ぐ」](anonymous-read-access-prevent.md)を参照してください。

コンテナーと BLOB に匿名でアクセスするクライアントは、資格情報を必要としないコンストラクターを使用できます。 次の例では、コンテナーと BLOB を匿名で参照するいくつかの方法を示します。

## <a name="create-an-anonymous-client-object"></a>匿名クライアント オブジェクトを作成する

アカウントの BLOB ストレージ エンドポイントを指定することで、匿名アクセスの対象となる新しいサービス クライアント オブジェクトを作成できます。 ただし、匿名アクセスを使用できる、そのアカウントのコンテナーの名前も知っておく必要があります。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

---

## <a name="reference-a-container-anonymously"></a>コンテナーを匿名で参照する

匿名で使用できるコンテナーの URL がある場合は、この URL を使用してコンテナーを直接参照することができます。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

---

## <a name="reference-a-blob-anonymously"></a>BLOB を匿名で参照する

匿名アクセスを使用できる BLOB の URL がある場合は、この URL を使用して BLOB を直接参照することができます。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

---

## <a name="next-steps"></a>次のステップ

- [コンテナーと BLOB の匿名パブリック読み取りアクセスを構成する](anonymous-read-access-configure.md)
- [コンテナーと BLOB への匿名パブリック読み取りアクセスを防ぐ](anonymous-read-access-prevent.md)
- [Azure Storage へのアクセスを承認する](../common/storage-auth.md)
