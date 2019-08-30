---
title: .NET を使用して保存されているアクセス ポリシーを定義する - Azure Storage
description: .NET クライアント ライブラリを使用して、保存されているアクセス ポリシーを定義する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990607"
---
# <a name="define-a-stored-access-policy-with-net"></a>.NET を使用して保存されているアクセス ポリシーを定義する

保存されているアクセス ポリシーを使用すると、サーバー側でのサービス レベルの共有アクセス署名 (SAS) をさらに制御できます。 保存されているアクセス ポリシーを定義することで、共有アクセス署名をグループ化し、ポリシーにバインドされている共有アクセス署名に対して追加の制限を設定できます。 保存されているアクセス ポリシーを使用して、SAS の開始時刻、有効期限、アクセス許可の変更や、その発行後の取り消しを実行できます。
  
 次のストレージ リソースで、保存されているアクセス ポリシーがサポートされます。  
  
- BLOB コンテナー  
- ファイル共有  
- キュー  
- テーブル  
  
> [!NOTE]
> コンテナーに関する保存されているアクセス ポリシーでは、コンテナー自体またはコンテナーに含まれている BLOB へのアクセス許可を付与している共有アクセス署名に関連付けることができます。 同様に、ファイル共有に関する保存されているアクセス ポリシーでは、共有自体または共有に含まれているファイルへのアクセス許可を付与している共有アクセス署名に関連付けることができます。  
>
> 保存されているアクセス ポリシーは、サービス SAS に対してのみサポートされます。 保存されているアクセス ポリシーは、アカウント SAS またはユーザー委任 SAS ではサポートされません。  

## <a name="create-a-stored-access-policy"></a>保存されているアクセス ポリシーを作成する

次のコードでは、保存されているアクセス ポリシーをコンテナーに作成します。 アクセス ポリシーを使用して、コンテナーまたは blob のサービス SAS に制約を指定します。

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
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>関連項目

- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](storage-sas-overview.md)
- [保存されているアクセス ポリシーを定義する](/rest/api/storageservices/define-stored-access-policy)

