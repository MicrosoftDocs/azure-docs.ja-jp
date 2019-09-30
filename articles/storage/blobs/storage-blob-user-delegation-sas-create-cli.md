---
title: Azure CLI を使用してコンテナーまたは BLOB のユーザー委任 SAS を作成する (プレビュー) - Azure Storage
description: Azure CLI を使用して、Azure Storage で Azure Active Directory 資格情報を使用するユーザー委任 SAS を作成する方法について学習します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/29/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 6ea4dbf07c8ef99c43dbe7add1ae9270056f708c
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164324"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli-preview"></a>Azure CLI を使用してコンテナーまたは BLOB のユーザー委任 SAS を作成する (プレビュー)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

この記事では、Azure Active Directory (Azure AD) 資格情報を使用して、Azure CLI でコンテナーまたは BLOB のユーザー委任 SAS を作成する方法について示します (プレビュー)。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>最新バージョンの Azure CLI をインストールする

Azure CLI を使用して Azure AD の資格情報で SAS をセキュリティで保護するには、まず最新バージョンの Azure CLI がインストールされていることを確認します。 Azure CLI のインストールの詳細については、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

## <a name="sign-in-with-azure-ad-credentials"></a>Azure AD の資格情報でサインインする

Azure AD の資格情報を使用して、Azure CLI にサインインします。 詳細については、[Azure CLI を使用したサインイン](/cli/azure/authenticate-azure-cli)に関するページを参照してください。

## <a name="assign-permissions-with-rbac"></a>RBAC を使用してアクセス許可を割り当てる

Azure PowerShell からユーザー委任 SAS を作成するには、Azure CLI へのサインインに使用する Azure AD アカウントに、**Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** アクションを含むロールが割り当てられている必要があります。 このアクセス許可により、Azure AD アカウントが*ユーザー委任キー*を要求できるようにします。 ユーザー委任キーは、ユーザー委任 SAS に署名するために使用されます。 **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** アクションを提供するロールは、ストレージ アカウント、リソース グループ、またはサブスクリプションのレベルで割り当てられている必要があります。

Azure AD セキュリティ プリンシパルに RBAC ロールを割り当てるための十分なアクセス許可がない場合は、アカウント所有者または管理者に依頼して、必要なアクセス許可を割り当ててもらう必要がある場合があります。

次の例では、**ストレージ BLOB データ共同作成者**ロールを割り当てます。これには、**Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** アクションが含まれます。 ロールは、ストレージ アカウントのレベルでスコープ設定されます。

山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

**Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** アクションを含む組み込みロールの詳細については、「[Azure リソースの組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Azure AD の資格情報を使用して SAS をセキュリティ保護する

Azure CLI を使用してユーザー委任 SAS を作成すると、SAS への署名に使用されるユーザー委任キーが暗黙的に作成されます。 SAS に指定した開始時刻と有効期限は、ユーザー委任キーの開始時刻と有効期限としても使用されます。

ユーザー委任キーが有効な最大間隔は開始日から 7 日であるため、SAS の有効期限を開始時刻から 7 日以内で指定する必要があります。 ユーザー委任キーの有効期限が切れると SAS は無効になるため、有効期限が 7 日を超える SAS でも、7 日間だけ有効になります。

ユーザー委任 SAS を作成する場合は、`--auth-mode login` と `--as-user parameters` が必要です。 Azure Storage に対して行われた要求が Azure AD の資格情報で承認されるように、`--auth-mode` パラメーターに*ログイン*を指定します。 `--as-user` パラメーターを指定して、返される SAS がユーザー委任 SAS である必要があることを示します。

### <a name="create-a-user-delegation-sas-for-a-container"></a>コンテナー用にユーザー委任 SAS を作成する

Azure CLI でコンテナー用にユーザー委任 SAS を作成するには、[az storage container generate-sas](/cli/azure/storage/container#az-storage-container-generate-sas) コマンドを呼び出します。

コンテナーでユーザー委任 SAS に対してサポートされているアクセス許可には、追加、作成、削除、一覧表示、読み取り、書き込みがあります。 アクセス許可は、単独で指定することも、組み合わせて指定することもできます。 これらのアクセス許可の詳細については、「[ユーザー委任 SAS を作成する](/rest/api/storageservices/create-user-delegation-sas)」を参照してください。

次の例では、コンテナー用のユーザー委任 SAS トークンが返されます。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

返されるユーザー委任 SAS トークンは、次のようになります。

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>BLOB 用のユーザー委任 SAS を作成する

Azure CLI で BLOB 用にユーザー委任 SAS を作成するには、[az storage blob generate-sas](/cli/azure/storage/blob#az-storage-blob-generate-sas) コマンドを呼び出します。

BLOB でユーザー委任 SAS に対してサポートされているアクセス許可には、追加、作成、削除、読み取り、書き込みがあります。 アクセス許可は、単独で指定することも、組み合わせて指定することもできます。 これらのアクセス許可の詳細については、「[ユーザー委任 SAS を作成する](/rest/api/storageservices/create-user-delegation-sas)」を参照してください。

次の構文では、BLOB 用のユーザー委任 SAS が返されます。 この例では、`--full-uri` パラメーターを指定しており、これにより SAS トークンを追加した BLOB URI が返されます。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

返されるユーザー委任 SAS URI は、次のようになります。

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> ユーザー委任 SAS では、保存されているアクセス ポリシーを使用したアクセス許可の定義はサポートされていません。

## <a name="revoke-a-user-delegation-sas"></a>ユーザー委任 SAS を取り消す

Azure CLI からユーザー委任 SAS を取り消すには、[az storage account revoke-delegation-keys](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) コマンドを呼び出します。 このコマンドにより、指定したストレージ アカウントに関連付けられているすべてのユーザー委任キーが取り消されます。 これらのキーに関連付けられているすべての共有アクセス署名が無効になります。

山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> ユーザーの委任キーと RBAC ロールの割り当てはいずれも Azure Storage によってキャッシュされるため、失効プロセスの開始と、既存のユーザーの委任 SAS の無効化の間に、遅延が発生する可能性があります。

## <a name="next-steps"></a>次の手順

- [ユーザー委任 SAS を作成する (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Get User Delegation Key 操作](/rest/api/storageservices/get-user-delegation-key)
