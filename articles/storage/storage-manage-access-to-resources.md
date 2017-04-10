---
title: "コンテナーと BLOB への匿名読み取りアクセスを管理する | Microsoft Docs"
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
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 931503f56b32ce9d1b11283dff7224d7e2f015ae
ms.openlocfilehash: 4fe41c3aabf5e6d9ae899cea0b9f9b6c9c305cf0


---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>コンテナーと BLOB への匿名読み取りアクセスを管理する
## <a name="overview"></a>Overview
既定では、ストレージ アカウントの所有者のみがそのアカウントを使ってストレージ リソースにアクセスできます。 Blob Storage の場合にのみ、コンテナーとその BLOB への匿名読み取りアクセスを許可するように、コンテナーのアクセス許可を設定できます。これにより、アカウント キーを共有せずにこれらのリソースへのアクセスを許可できます。

匿名アクセスは、特定の BLOB で匿名読み取りアクセスを常に使用できるようにするシナリオに最適です。 詳細な制御では、さまざまなアクセス許可を使用し、指定された期間において、制限付きアクセスを委任するための Shared Access Signature を作成できます。 Shared Access Signature の作成方法の詳細については、「 [Shared Access Signatures (SAS) の使用](storage-dotnet-shared-access-signature-part-1.md)」を参照してください。

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>コンテナーと BLOB への匿名ユーザーのアクセス許可を付与します。
既定では、コンテナーとコンテナー内の BLOB には、上位のストレージ アカウントの所有者のみがアクセスできます。 コンテナーとその BLOB に対する読み取りアクセス許可を匿名ユーザーに付与する場合は、コンテナーのアクセス許可を設定し、パブリック アクセスを許可できます。 パブリック アクセスが許可されたコンテナー内の BLOB は、匿名ユーザーが読み取ることができ、その際に要求の認証は不要です。

コンテナーへのアクセスは次のように管理できます。

* **完全なパブリック読み取りアクセス:** コンテナーと BLOB のデータを匿名の要求で読み取ることができます。 クライアントは匿名要求でコンテナー内の BLOB を列挙できますが、ストレージ アカウント内のコンテナーを列挙することはできません。
* **BLOB に限定したパブリック読み取りアクセス:** 該当するコンテナー内の BLOB データは匿名要求で読み取り可能ですが、コンテナー データは参照できません。 クライアントはコンテナー内の BLOB を匿名要求で列挙することはできません。
* **パブリック読み取りアクセスなし:** コンテナーと BLOB のデータはアカウント所有者に限り読み取ることができます。

コンテナーのアクセス許可は次の方法で設定できます。

* [Azure ポータル](https://portal.azure.com)から設定。
* ストレージ クライアント ライブラリまたは REST API を使用して、プログラムで設定。
* PowerShell を使用して設定。 Azure PowerShell からのコンテナー アクセス許可の設定については、 [Azure Storage での Azure PowerShell の使用](storage-powershell-guide-full.md#how-to-manage-azure-blobs)に関するページを参照してください。

### <a name="setting-container-permissions-from-the-azure-portal"></a>Azure ポータルからコンテナーのアクセス許可を設定する
[Azure ポータル](https://portal.azure.com)からコンテナーのアクセス許可を設定するには、次の手順に従います。

1. ストレージ アカウントのダッシュボードに移動します。
2. 一覧からコンテナーの名前を選択します。 名前をクリックすると、選択したコンテナー内の BLOB が公開されます。
3. ツール バーから **[アクセス ポリシー]** を選択します。
4. 次のスクリーンショットに示すように、 **[アクセスの種類]** フィールドから目的のアクセス許可レベルを選択します。

    ![Edit Container Metadata dialog](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="setting-container-permissions-programmatically-using-net"></a>.NET を使用してプログラムでコンテナーのアクセス許可を設定する
.NET クライアント ライブラリを使用してコンテナーのアクセス許可を設定するには、まず **GetPermissions** メソッドを呼び出して、コンテナーの既存のアクセス許可を取得します。 次に、**GetPermissions** メソッドによって返される **BlobContainerPermissions** オブジェクトに対して **PublicAccess** プロパティを設定します。 最後に、更新されたアクセス許可を使用して **SetPermissions** メソッドを呼び出します。

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

## <a name="see-also"></a>関連項目
* [Azure Storage サービスの認証](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Shared Access Signatures (SAS) の使用](storage-dotnet-shared-access-signature-part-1.md)
* [Shared Access Signature を使用したアクセスの委任](https://msdn.microsoft.com/library/azure/ee395415.aspx)




<!--HONumber=Dec16_HO2-->


