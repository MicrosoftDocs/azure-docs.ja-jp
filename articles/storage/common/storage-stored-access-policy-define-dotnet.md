---
title: .NET を使用して保存されているアクセス ポリシーを作成する
titleSuffix: Azure Storage
description: Azure Storage と .NET を使用して、保存されているアクセス ポリシーを作成します。 サーバーでのサービスレベルの共有アクセス署名に対する制御のレベルを追加します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cd6147fbe38710bcefd580e71be1d6f5d446a21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89010751"
---
# <a name="create-a-stored-access-policy-with-net"></a>.NET を使用して保存されているアクセス ポリシーを作成する

保存されているアクセス ポリシーを使用すると、サーバー側でのサービス レベルの共有アクセス署名 (SAS) をさらに制御できます。 保存されているアクセス ポリシーを定義することで、共有アクセス署名をグループ化し、ポリシーにバインドされている共有アクセス署名に対して追加の制限を設定できます。 保存されているアクセス ポリシーを使用して、SAS の開始時刻、有効期限、アクセス許可の変更や、その発行後の取り消しを実行できます。
  
次の Azure Storage リソースでは、保存されているアクセス ポリシーがサポートされます。  
  
- BLOB コンテナー  
- ファイル共有  
- キュー  
- テーブル  
  
> [!NOTE]
> コンテナーに関する保存されているアクセス ポリシーでは、コンテナー自体またはコンテナーに含まれている BLOB へのアクセス許可を付与している共有アクセス署名に関連付けることができます。 同様に、ファイル共有に関する保存されているアクセス ポリシーでは、共有自体または共有に含まれているファイルへのアクセス許可を付与している共有アクセス署名に関連付けることができます。  
>
> 保存されているアクセス ポリシーは、サービス SAS に対してのみサポートされます。 保存されているアクセス ポリシーは、アカウント SAS またはユーザー委任 SAS ではサポートされません。  

保存されているアクセス ポリシーの詳細については、「[保存されているアクセス ポリシーの定義](/rest/api/storageservices/define-stored-access-policy)」を参照してください。

## <a name="create-a-stored-access-policy"></a>保存されているアクセス ポリシーを作成する

保存されているアクセス ポリシーを作成するための基礎 REST 操作は、[Set Container ACL](/rest/api/storageservices/set-container-acl) です。 接続文字列でアカウント アクセス キーを使用することで、保存されているアクセス ポリシーを共有キー経由で作成する権限を操作に与える必要があります。 **Set Container ACL** 操作に Azure AD 資格情報で権限を与えることはできません。 詳細については、「[Blob および queue データ操作を呼び出す権限](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)」を参照してください。

次のコード例では、保存されているアクセス ポリシーをコンテナーに作成します。 アクセス ポリシーを使用して、コンテナーまたは blob のサービス SAS に制約を指定します。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Azure Storage 向け .NET クライアント ライブラリのバージョン 12 を使用してコンテナー上に保存されているアクセス ポリシーを作成するには、次のメソッドのいずれかを呼び出します。

- [BlobContainerClient.SetAccessPolicy](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy)
- [BlobContainerClient.SetAccessPolicyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicyasync)

次の例では、1 日有効となり、読み取りと書き込みの権限を与える保存されているアクセス ポリシーが作成されます。

```csharp
async static Task CreateStoredAccessPolicyAsync(string containerName)
{
    string connectionString = "";

    // Use the connection string to authorize the operation to create the access policy.
    // Azure AD does not support the Set Container ACL operation that creates the policy.
    BlobContainerClient containerClient = new BlobContainerClient(connectionString, containerName);

    try
    {
        await containerClient.CreateIfNotExistsAsync();

        // Create one or more stored access policies.
        List<BlobSignedIdentifier> signedIdentifiers = new List<BlobSignedIdentifier>
        {
            new BlobSignedIdentifier
            {
                Id = "mysignedidentifier",
                AccessPolicy = new BlobAccessPolicy
                {
                    StartsOn = DateTimeOffset.UtcNow.AddHours(-1),
                    ExpiresOn = DateTimeOffset.UtcNow.AddDays(1),
                    Permissions = "rw"
                }
            }
        };
        // Set the container's access policy.
        await containerClient.SetAccessPolicyAsync(permissions: signedIdentifiers);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.ErrorCode);
        Console.WriteLine(e.Message);
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Azure Storage 向け .NET クライアント ライブラリのバージョン 12 を使用してコンテナー上に保存されているアクセス ポリシーを作成するには、次のメソッドのいずれかを呼び出します。

- [CloudBlobContainer.SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [CloudBlobContainer.SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

次の例では、1 日有効となり、読み取り、書き込み、一覧表示の権限を与える保存されているアクセス ポリシーが作成されます。

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

---

## <a name="see-also"></a>関連項目

- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](storage-sas-overview.md)
- [保存されているアクセス ポリシーを定義する](/rest/api/storageservices/define-stored-access-policy)
- [Azure Storage の接続文字列を構成する](storage-configure-connection-string.md)
