---
title: PowerShell を使用してコンテナーまたは BLOB のユーザー委任 SAS を作成する
titleSuffix: Azure Storage
description: PowerShell を使用して、Azure Active Directory 資格情報によってユーザー委任 SAS を作成する方法について学習します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 875b2a9f35562dd8f0d5df3c631e5ade1e3fbf75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91714533"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>PowerShell を使用してコンテナーまたは BLOB のユーザー委任 SAS を作成する

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

この記事では、Azure Active Directory (Azure AD) 資格情報を使用して、Azure PowerShell でコンテナーまたは BLOB のユーザー委任 SAS を作成する方法について説明します。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>PowerShell モジュールをインストールする

PowerShell を使用してユーザー委任 SAS を作成するには、バージョン 1.10.0 以降の Az. Storage モジュールをインストールします。 次の手順のようにしてモジュールの最新バージョンをインストールします。

1. Azure PowerShell の以前のインストールがある場合はアンインストールします。

    - **[設定]** の **[アプリと機能]** 設定を使用して、Windows から Azure PowerShell の以前のインストールを削除します。
    - `%Program Files%\WindowsPowerShell\Modules` からすべての **Azure** モジュールを削除します。

1. 最新バージョンの PowerShellGet がインストールされていることを確認します。 Windows PowerShell ウィンドウを開き、次のコマンドを実行して最新バージョンをインストールします。

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. PowerShellGet のインストール後、PowerShell ウィンドウを閉じて再び開きます。

1. 最新バージョンの Azure PowerShell をインストールします。

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Azure PowerShell バージョン 3.2.0 以降がインストールされていることを確認します。 次のコマンドを実行して、最新バージョンの Azure Storage PowerShell モジュールをインストールします。

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. PowerShell ウィンドウを閉じて再び開きます。

インストールされている Az.Storage モジュールのバージョンを確認するには、次のコマンドを実行します。

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Azure PowerShell のインストールの詳細については、[PowerShellGet を使用した Azure PowerShell のインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Azure AD で Azure PowerShell にサインインする

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドを呼び出して、Azure AD アカウントでサインインします。

```powershell
Connect-AzAccount
```

PowerShell を使用したサインインの詳細については、「[Azure PowerShell を使用してサインインする](/powershell/azure/authenticate-azureps)」を参照してください。

## <a name="assign-permissions-with-azure-rbac"></a>Azure RBAC を使用してアクセス許可を割り当てる

Azure PowerShell からユーザー委任 SAS を作成するには、PowerShell へのサインインに使用する Azure AD アカウントに、**Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** アクションを含むロールが割り当てられている必要があります。 このアクセス許可により、Azure AD アカウントが *ユーザー委任キー* を要求できるようにします。 ユーザー委任キーは、ユーザー委任 SAS に署名するために使用されます。 **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** アクションを提供するロールは、ストレージ アカウント、リソース グループ、またはサブスクリプションのレベルで割り当てられている必要があります。 ユーザー委任 SAS を作成するための Azure RBAC アクセス許可の詳細については、「[ユーザー委任 SAS を作成する](/rest/api/storageservices/create-user-delegation-sas)」の **Azure RBAC によるアクセス許可の割り当て** に関するセクションを参照してください。

Azure AD セキュリティ プリンシパルに Azure ロールを割り当てるための十分なアクセス許可がない場合は、アカウント所有者または管理者に依頼して、必要なアクセス許可を割り当ててもらう必要がある場合があります。

次の例では、**ストレージ BLOB データ共同作成者** ロールを割り当てます。これには、**Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** アクションが含まれます。 ロールは、ストレージ アカウントのレベルでスコープ設定されます。

山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

**Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** アクションを含む組み込みロールの詳細については、「[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Azure AD の資格情報を使用して SAS をセキュリティ保護する

Azure PowerShell を使用してユーザー委任 SAS を作成すると、SAS への署名に使用されるユーザー委任キーが暗黙的に作成されます。 SAS に指定した開始時刻と有効期限は、ユーザー委任キーの開始時刻と有効期限としても使用されます。 

ユーザー委任キーが有効な最大間隔は開始日から 7 日であるため、SAS の有効期限を開始時刻から 7 日以内で指定する必要があります。 ユーザー委任キーの有効期限が切れると SAS は無効になるため、有効期限が 7 日を超える SAS でも、7 日間だけ有効になります。

Azure PowerShell を使用してコンテナーまたは BLOB のユーザー委任 SAS を作成するには、まず、`-UseConnectedAccount` パラメーターを指定して新しい Azure Storage コンテキスト オブジェクトを作成します。 `-UseConnectedAccount` パラメーターは、コマンドで、サインインに使用した Azure AD アカウントの下にコンテキスト オブジェクトを作成することを指定します。

山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>コンテナー用にユーザー委任 SAS を作成する

コンテナーのユーザー委任 SAS トークンを返すには、[New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) コマンドを呼び出し、前に作成した Azure Storage コンテキスト オブジェクトを渡します。

次の例では、コンテナー用のユーザー委任 SAS トークンが返されます。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

返されるユーザー委任 SAS トークンは、次のようになります。

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>BLOB 用のユーザー委任 SAS を作成する

BLOB のユーザー委任 SAS トークンを返すには、[New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) コマンドを呼び出し、前に作成した Azure Storage コンテキスト オブジェクトを渡します。

次の構文では、BLOB 用のユーザー委任 SAS が返されます。 この例では、`-FullUri` パラメーターを指定しており、これにより SAS トークンを追加した BLOB URI が返されます。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

返されるユーザー委任 SAS URI は、次のようになります。

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> ユーザー委任 SAS では、保存されているアクセス ポリシーを使用したアクセス許可の定義はサポートされていません。

## <a name="revoke-a-user-delegation-sas"></a>ユーザー委任 SAS を取り消す

Azure PowerShell からユーザー委任 SAS を取り消すには、**Revoke-AzStorageAccountUserDelegationKeys** コマンドを呼び出します。 このコマンドにより、指定したストレージ アカウントに関連付けられているすべてのユーザー委任キーが取り消されます。 これらのキーに関連付けられているすべての共有アクセス署名が無効になります。

山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> ユーザーの委任キーと Azure ロールの割り当てはいずれも Azure Storage によってキャッシュされるため、失効プロセスの開始と、既存のユーザーの委任 SAS の無効化の間に、遅延が発生する可能性があります。

## <a name="next-steps"></a>次のステップ

- [ユーザー委任 SAS を作成する (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Get User Delegation Key 操作](/rest/api/storageservices/get-user-delegation-key)
