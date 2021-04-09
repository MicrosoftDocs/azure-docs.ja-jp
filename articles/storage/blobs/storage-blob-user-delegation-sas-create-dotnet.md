---
title: .NET を使用してコンテナー、ディレクトリ、または BLOB のユーザー委任 SAS を作成する
titleSuffix: Azure Storage
description: Azure Storage 用の .NET クライアント ライブラリを使用して、Azure Active Directory 資格情報によるユーザー委任 SAS を作成する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/03/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 13491735f73cb1696f3c36f3434cc781a1e2b739
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526807"
---
# <a name="create-a-user-delegation-sas-for-a-container-directory-or-blob-with-net"></a>コンテナー、ディレクトリ、または BLOB のユーザー委任 SAS を .NET で作成する

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

この記事では、Azure Active Directory (Azure AD) 資格情報を使用して、コンテナー、ディレクトリ、または BLOB のユーザー委任 SAS を、バージョン 12 の .NET 用 Azure Storage クライアント ライブラリで作成する方法について説明します。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-azure-roles-for-access-to-data"></a>データにアクセスするための Azure ロールを割り当る

Azure AD セキュリティ プリンシパルが Blob データにアクセスしようとする場合、そのセキュリティ プリンシパルはリソースへのアクセス許可を保持している必要があります。 セキュリティ プリンシパルが Azure 内のマネージド ID であるか、開発環境でコードを実行している Azure AD ユーザー アカウントであるかにかかわらず、Azure Storage での BLOB データへのアクセスを許可する Azure ロールをセキュリティ プリンシパルに割り当てる必要があります。 Azure RBAC 経由でのアクセス許可の割り当てについては、[Azure Active Directory を使用した Azure BLOB とキューへのアクセスの承認](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)に関する記事の「**アクセス権に Azure ロールを割り当てる**」というタイトルのセクションを参照してください。

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Azure Storage から Azure ID クライアント ライブラリを使用して認証する方法の詳細については、[Azure Active Directory と Azure リソースのマネージ ID を使用した BLOB およびキューへのアクセスの認証](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)に関するページの「**Azure ID ライブラリを使用した認証**」を参照してください。

## <a name="get-an-authenticated-token-credential"></a>認証済みのトークン資格情報を取得する

Azure Storage への要求を承認するためにコード上で使用できるトークン資格情報を取得するには、[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) クラスのインスタンスを作成します。

次のコード スニペットでは、認証されたトークン資格情報を取得し、それを使用して Blob ストレージ用のサービス クライアントを作成する方法が示されています。

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>ユーザー委任キーを取得する

すべての SAS はキーによって署名されます。 ユーザー委任 SAS を作成するには、まず、SAS に署名するために使用されるユーザー委任キーを要求する必要があります。 ユーザー委任キーは、サービス SAS またはアカウント SAS に署名するために使用されるアカウント キーに似ていますが、Azure AD の資格情報に依存している点が異なります。 クライアントで OAuth 2.0 トークンを使用してユーザー委任キーが要求されると、ユーザーに代わって Azure Storage によってユーザー委任キーが返されます。

ユーザー委任キーを取得したら、そのキーを使用して、キーの有効期間にわたって、任意の数のユーザー委任共有アクセス署名を作成できます。 ユーザー委任キーは、それを取得するために使用された OAuth 2.0 トークンに依存しないため、キーが有効である限り、トークンを更新する必要はありません。 キーの有効期間として、最大 7 日間を指定できます。

ユーザー委任キーを要求するには、次のいずれかの方法を使用します。

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

次のコード スニペットでは、ユーザー委任キーが取得され、そのプロパティが書き出されます。

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="get-a-user-delegation-sas-for-a-blob"></a>BLOB 用のユーザー委任 SAS を取得する

次のコード例は、セキュリティ プリンシパルを認証し、BLOB 用のユーザー委任 SAS を作成するためのコード全体を示しています。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasBlob":::

次の例では、前の例で作成されたユーザー委任 SAS を、シミュレートされたクライアント アプリケーションからテストします。 SAS が有効であれば、クライアント アプリケーションで BLOB の内容を読み取ることができます。 SAS が無効な場合 (たとえば、有効期限が切れている場合)、Azure Storage によってエラー コード 403 (禁止) が返されます。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ReadBlobWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-container"></a>コンテナー用のユーザー委任 SAS を取得する

次のコード例は、コンテナー用のユーザー委任 SAS を生成する方法を示しています。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasContainer":::

次の例では、前の例で作成されたユーザー委任 SAS を、シミュレートされたクライアント アプリケーションからテストします。 SAS が有効であれば、クライアント アプリケーションで BLOB の内容を読み取ることができます。 SAS が無効な場合 (たとえば、有効期限が切れている場合)、Azure Storage によってエラー コード 403 (禁止) が返されます。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListBlobsWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-directory"></a>ディレクトリ用のユーザー委任 SAS を取得する

次のコード例は、階層型名前空間がストレージ アカウントに対して有効のときに、ディレクトリ用のユーザー委任 SAS を生成する方法を示しています。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasDirectory":::

次の例では、前の例で作成されたユーザー委任 SAS を、シミュレートされたクライアント アプリケーションからテストします。 SAS が有効であれば、クライアント アプリケーションでこのディレクトリに対するファイル パスの一覧を表示することができます。 SAS が無効な場合 (たとえば、有効期限が切れている場合)、Azure Storage によってエラー コード 403 (禁止) が返されます。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListFilePathsWithDirectorySasAsync":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>関連項目

- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../common/storage-sas-overview.md)
- [Get User Delegation Key 操作](/rest/api/storageservices/get-user-delegation-key)
- [ユーザー委任 SAS を作成する (REST API)](/rest/api/storageservices/create-user-delegation-sas)
