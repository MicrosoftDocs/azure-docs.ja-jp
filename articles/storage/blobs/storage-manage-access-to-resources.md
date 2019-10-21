---
title: Azure Blob Storage のコンテナーと BLOB に対するパブリック読み取りアクセスを有効にする | Microsoft Docs
description: コンテナーと BLOB で匿名アクセスを使用できるようにする方法、およびこれらにプログラムでアクセスする方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: d0e3121fe773a9725eb7cfd9e8b14d0ed86f3fbb
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673310"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>コンテナーと BLOB への匿名読み取りアクセスを管理する

Azure Blob Storage のコンテナーとその BLOB に対する匿名のパブリック読み取りアクセスを有効にすることができます。 そうすることで、アカウント キーを共有せず、Shared Access Signature (SAS) も必要とせずに、これらのリソースに対する読み取り専用のアクセスを付与できます。

パブリック読み取りアクセスは、特定の BLOB を匿名読み取りアクセスで常に使用できるようにするシナリオに最適です。 より細かく制御するには、Shared Access Signature を作成できます。 Shared Access Signature を使用すると、特定の期間、異なるアクセス許可を使用して制限付きのアクセスを提供できます。 Shared Access Signature の作成方法の詳細については、[Azure Storage での Shared Access Signature (SAS) の使用](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)に関するページを参照してください。

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>コンテナーと BLOB への匿名ユーザーのアクセス許可を付与します。

既定では、コンテナーとその中のすべての BLOB には、適切なアクセス許可を与えられたユーザーのみがアクセスできます。 匿名ユーザーにコンテナーとその BLOB に対する読み取りアクセスを許可するには、コンテナーのパブリック アクセス レベルを設定できます。 コンテナーへのパブリック アクセスを許可すると、匿名ユーザーは、要求を認可しなくても、パブリックにアクセスできるコンテナー内の BLOB を読み取ることができます。

以下のアクセス許可を備えたコンテナーを構成できます。

- **パブリック読み取りアクセスなし:** コンテナーとその BLOB には、ストレージ アカウント所有者だけがアクセスできます。 これは、すべての新しいコンテナーの既定値です。
- **BLOB に限定したパブリック読み取りアクセス:** コンテナー内の BLOB は匿名要求で読み取り可能ですが、コンテナー データは使用できません。 匿名クライアントはコンテナー内の BLOB を列挙することはできません。
- **コンテナーとその BLOB に対するパブリック読み取りアクセス:** すべてのコンテナーと BLOB のデータを匿名要求で読み取ることができます。 クライアントは匿名要求でコンテナー内の BLOB を列挙できますが、ストレージ アカウント内のコンテナーを列挙することはできません。

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Azure portal でコンテナーのパブリック アクセス レベルを設定する

[Azure portal](https://portal.azure.com) から、1 つまたは複数のコンテナーのパブリック アクセス レベルを更新することができます。

1. Azure portal でご利用のストレージ アカウントの概要に移動します。
1. メニュー ブレードの **[Blob service]** で、 **[BLOB]** を選択します。
1. パブリック アクセス レベルを設定するコンテナーを選択します。
1. **[アクセス レベルの変更]** ボタンを使用して、パブリック アクセスの設定を表示します。
1. **[パブリック アクセス レベル]** ドロップダウンから目的のパブリック アクセス レベルを選択し、[OK] ボタンをクリックして選択したコンテナーに変更を適用します。

次のスクリーンショットでは、選択したコンテナーのパブリック アクセス レベルを変更する方法を示します。

![ポータルでパブリック アクセス レベルを設定する方法を示すスクリーンショット](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> 個々の BLOB のパブリック アクセス レベルを変更することはできません。 パブリック アクセス レベルは、コンテナー レベルでのみ設定されます。

### <a name="set-container-public-access-level-with-net"></a>.NET でコンテナーのパブリック アクセス レベルを設定する

.NET 用の Azure Storage クライアント ライブラリを使用してコンテナーのアクセス許可を設定するには、まず、次のいずれかのメソッドを呼び出して、コンテナーの既存のアクセス許可を取得します。

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

次に、**GetPermissions** メソッドによって返される [BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions) オブジェクトに対して **PublicAccess** プロパティを設定します。

最後に、次のいずれかのメソッドを呼び出して、コンテナーのアクセス許可を更新します。

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

次の例では、コンテナーのアクセス許可を完全なパブリック読み取りアクセスに設定します。 アクセス許可を BLOB 限定のパブリック読み取りアクセスに設定するには、**PublicAccess** プロパティを **BlobContainerPublicAccessType.Blob** に設定します。 匿名ユーザーのすべてのアクセス許可を削除するには、このプロパティを **BlobContainerPublicAccessType.Off**に設定します。

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>コンテナーと BLOB に匿名でアクセスする

コンテナーと BLOB に匿名でアクセスするクライアントは、資格情報を必要としないコンストラクターを使用できます。 次の例では、コンテナーと BLOB を匿名で参照するいくつかの方法を示します。

### <a name="create-an-anonymous-client-object"></a>匿名クライアント オブジェクトを作成する

アカウントの BLOB ストレージ エンドポイントを指定することで、匿名アクセスの対象となる新しいサービス クライアント オブジェクトを作成できます。 ただし、匿名アクセスを使用できる、そのアカウントのコンテナーの名前も知っておく必要があります。

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

### <a name="reference-a-container-anonymously"></a>コンテナーを匿名で参照する

匿名で使用できるコンテナーの URL がある場合は、この URL を使用してコンテナーを直接参照することができます。

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

### <a name="reference-a-blob-anonymously"></a>BLOB を匿名で参照する

匿名アクセスを使用できる BLOB の URL がある場合は、この URL を使用して BLOB を直接参照することができます。

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

## <a name="next-steps"></a>次の手順

- [Azure Storage へのアクセスを承認する](../common/storage-auth.md)
- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../common/storage-sas-overview.md)
- [Blob service の REST API](/rest/api/storageservices/blob-service-rest-api)
