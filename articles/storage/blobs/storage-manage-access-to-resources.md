---
title: "Azure Blob Storage のコンテナーと BLOB に対するパブリック読み取りアクセスを有効にする | Microsoft Docs"
description: "コンテナーと BLOB で匿名アクセスを使用できるようにする方法、およびこれらにプログラムでアクセスする方法について説明します。"
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: a2cffee6-3224-4f2a-8183-66ca23b2d2d7
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: marsma
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 8d4f4c7c208baf0db6155eb78a53e37c4ec1e023
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>コンテナーと BLOB への匿名読み取りアクセスを管理する
Azure Blob Storage のコンテナーとその BLOB に対する匿名のパブリック読み取りアクセスを有効にすることができます。 そうすることで、アカウント キーを共有せず、Shared Access Signature (SAS) も必要とせずに、これらのリソースに対する読み取り専用のアクセスを付与できます。

パブリック読み取りアクセスは、特定の BLOB を匿名読み取りアクセスで常に使用できるようにするシナリオに最適です。 より細かく制御するには、Shared Access Signature を作成できます。 Shared Access Signature を使用すると、特定の期間、異なるアクセス許可を使用して制限付きのアクセスを提供できます。 Shared Access Signature の作成方法の詳細については、[Azure Storage での Shared Access Signature (SAS) の使用](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)に関するページを参照してください。

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>コンテナーと BLOB への匿名ユーザーのアクセス許可を付与します。
既定では、コンテナーとコンテナー内の BLOB には、上位のストレージ アカウントの所有者のみがアクセスできます。 コンテナーとその BLOB に対する読み取りアクセス許可を匿名ユーザーに付与する場合は、コンテナーのアクセス許可を設定し、パブリック アクセスを許可できます。 パブリック アクセスが許可されたコンテナー内の BLOB は、匿名ユーザーが読み取ることができ、その際に要求の認証は不要です。

以下のアクセス許可を備えたコンテナーを構成できます。

* **パブリック読み取りアクセスなし:** コンテナーとその BLOB には、ストレージ アカウント所有者だけがアクセスできます。 これは、すべての新しいコンテナーの既定値です。
* **BLOB に限定したパブリック読み取りアクセス:** コンテナー内の BLOB は匿名要求で読み取り可能ですが、コンテナー データは使用できません。 匿名クライアントはコンテナー内の BLOB を列挙することはできません。
* **完全なパブリック読み取りアクセス:** すべてのコンテナーと BLOB のデータを匿名要求で読み取ることができます。 クライアントは匿名要求でコンテナー内の BLOB を列挙できますが、ストレージ アカウント内のコンテナーを列挙することはできません。

コンテナーのアクセス許可は、次の方法で設定することができます。

* [Azure ポータル](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#how-to-manage-azure-blobs)
* [Azure CLI 2.0](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* ストレージ クライアント ライブラリのいずれか、または REST API を使用して、プログラムで設定

### <a name="set-container-permissions-in-the-azure-portal"></a>Azure Portal でコンテナーのアクセス許可を設定する
[Azure Portal](https://portal.azure.com) でコンテナーのアクセス許可を設定するには、次の手順に従います。

1. ポータルで **[ストレージ アカウント]** ブレードを開きます。 ストレージ アカウントを検索するには、メイン ポータル メニュー ブレードで **[ストレージ アカウント]** を選択します。
1. メニュー ブレードの **[BLOB サービス]** で、**[コンテナー]** を選択します。
1. コンテナーの行を右クリックするか、省略記号を選択して、コンテナーの**コンテキスト メニュー**を開きます。
1. コンテキスト メニューの **[アクセス ポリシー]** を選択します。
1. ドロップダウン メニューの **[アクセスの種類]** を選択します。

    ![Edit Container Metadata dialog](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>.NET でコンテナーのアクセス許可を設定する
C# と .NET 用ストレージ クライアント ライブラリを使用してコンテナーのアクセス許可を設定するには、まず、**GetPermissions** メソッドを呼び出して、コンテナーの既存のアクセス許可を取得します。 次に、**GetPermissions** メソッドによって返される **BlobContainerPermissions** オブジェクトに対して **PublicAccess** プロパティを設定します。 最後に、更新されたアクセス許可を使用して **SetPermissions** メソッドを呼び出します。

次の例では、コンテナーのアクセス許可を完全なパブリック読み取りアクセスに設定します。 アクセス許可を BLOB 限定のパブリック読み取りアクセスに設定するには、**PublicAccess** プロパティを **BlobContainerPublicAccessType.Blob** に設定します。 匿名ユーザーのすべてのアクセス許可を削除するには、このプロパティを **BlobContainerPublicAccessType.Off**に設定します。

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>コンテナーと BLOB に匿名でアクセスする
コンテナーと BLOB に匿名でアクセスするクライアントは、資格情報を必要としないコンストラクターを使用できます。 次の例では、BLOB サービス リソースを匿名で参照するいくつかの方法を示します。

### <a name="create-an-anonymous-client-object"></a>匿名クライアント オブジェクトを作成する
アカウントに BLOB サービス エンドポイントを指定することで、匿名アクセスの対象となる新しいサービス クライアント オブジェクトを作成できます。 ただし、匿名アクセスを使用できる、そのアカウントのコンテナーの名前も知っておく必要があります。

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob service endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
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
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
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
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>匿名ユーザーが利用できる機能
コンテナーの ACL でパブリック アクセスが許可されている場合に、匿名ユーザーが呼び出すことのできる操作を次の表に示します。

| REST 操作 | 完全パブリック読み取りアクセス許可 | BLOB に限定したパブリック読み取りアクセス許可 |
| --- | --- | --- |
| List Containers |所有者のみ |所有者のみ |
| コンテナーの作成 |所有者のみ |所有者のみ |
| コンテナーのプロパティの取得 |すべて |所有者のみ |
| Get Container Metadata |すべて |所有者のみ |
| Set Container Metadata |所有者のみ |所有者のみ |
| コンテナー ACL の取得 |所有者のみ |所有者のみ |
| Set Container ACL |所有者のみ |所有者のみ |
| コンテナーの削除 |所有者のみ |所有者のみ |
| BLOBs の一覧 |すべて |所有者のみ |
| Put Blob |所有者のみ |所有者のみ |
| Get Blob |すべて |すべて |
| BLOB のプロパティの取得 |すべて |すべて |
| Set Blob Properties |所有者のみ |所有者のみ |
| BLOB のメタデータの取得 |すべて |すべて |
| Set Blob Metadata |所有者のみ |所有者のみ |
| Put Block |所有者のみ |所有者のみ |
| Get Block List (REST API) (コミット後のブロックのみ) |すべて |すべて |
| Get Block List (REST API) (コミット前のブロックのみまたは全ブロック) |所有者のみ |所有者のみ |
| Put Block List |所有者のみ |所有者のみ |
| Delete Blob |所有者のみ |所有者のみ |
| BLOB のコピー |所有者のみ |所有者のみ |
| Snapshot Blob |所有者のみ |所有者のみ |
| Lease Blob |所有者のみ |所有者のみ |
| Put Page |所有者のみ |所有者のみ |
| ページ範囲の取得 |すべて |すべて |
| Append Blob |所有者のみ |所有者のみ |

## <a name="next-steps"></a>次のステップ

* [Azure Storage サービスの認証](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Shared Access Signatures (SAS) の使用](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Shared Access Signature を使用したアクセスの委任](https://msdn.microsoft.com/library/azure/ee395415.aspx)

