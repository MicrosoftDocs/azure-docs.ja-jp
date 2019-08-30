---
title: PowerShell を使用してコンテナーまたは BLOB のユーザー委任 SAS を作成する (プレビュー) - Azure Storage
description: PowerShell を使用して、Azure Storage で Azure Active Directory 資格情報を使用して、Shared Access Signature (SAS) を作成する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: bdb66ec65d493c6af2f33bf6ed6e4a2bb2154235
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897039"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell-preview"></a>PowerShell を使用してコンテナーまたは BLOB のユーザー委任 SAS を作成する (プレビュー)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

この記事では、Azure Active Directory (Azure AD) 資格情報を使用して、Azure PowerShell でコンテナーまたは BLOB のユーザー委任 SAS を作成する方法について説明します。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-module"></a>プレビュー モジュールをインストールする

PowerShell を使用してユーザー委任 SAS を作成するには、最初に Az.Storage 1.3.1-preview モジュールをインストールする必要があります。 次の手順のようにしてモジュールをインストールします。

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

1. ユーザー委任 SAS をサポートする Azure Storage プレビュー モジュールをインストールします。

    ```powershell
    Install-Module Az.Storage `
        –Repository PSGallery `
        -RequiredVersion 1.3.1-preview `
        –AllowPrerelease `
        –AllowClobber `
        –Force
    ```

1. PowerShell ウィンドウを閉じて再び開きます。

PowerShell によって、既定で最新の Az.Storage モジュールが読み込まれるため、コンソールを起動するときに、1.3.1-preview モジュールを明示的に読み込む必要がある場合があります。 プレビュー モジュールを明示的に読み込むには、[Import-Module](/powershell/module/microsoft.powershell.core/import-module) コマンドを実行します。

```powershell
Import-Module Az.Storage -RequiredVersion 1.3.1
```

Azure PowerShell のインストールの詳細については、[PowerShellGet を使用した Azure PowerShell のインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Azure AD で Azure PowerShell にサインインする

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドを呼び出して、Azure AD アカウントでサインインします。

```powershell
Connect-AzAccount
```

PowerShell を使用したサインインの詳細については、「[Azure PowerShell を使用してサインインする](/powershell/azure/authenticate-azureps)」を参照してください。

## <a name="assign-permissions-with-rbac"></a>RBAC を使用してアクセス許可を割り当てる

Azure PowerShell からユーザー委任 SAS を作成するには、PowerShell へのサインインに使用する Azure AD アカウントに、**Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** アクションを含むロールが割り当てられている必要があります。 このアクセス許可により、Azure AD アカウントが*ユーザー委任キー*を要求できるようにします。 ユーザー委任キーは、ユーザー委任 SAS に署名するために使用します。 **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** アクションを提供するロールは、ストレージ アカウント、リソース グループ、またはサブスクリプションのレベルで割り当てられている必要があります。 ユーザー委任 SAS を作成するための RBAC アクセス許可の詳細については、「[Create a user delegation SAS](/rest/api/storageservices/create-user-delegation-sas)」 (ユーザー委任 SAS の作成) の「**Assign permissions with RBAC**」 (RBAC によるアクセス許可の割り当て) セクションを参照してください。

Azure AD セキュリティ プリンシパルに RBAC ロールを割り当てるための十分なアクセス許可がない場合は、アカウント所有者または管理者に依頼して、必要なアクセス許可を割り当ててもらう必要がある場合があります。

次の例では、**ストレージ BLOB データ共同作成者**ロールを割り当てます。これには、**Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** アクションが含まれます。 ロールは、ストレージ アカウントのレベルでスコープ設定されます。

山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

**Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** アクションを含む組み込みロールの詳細については、「[Azure リソースの組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Azure AD 資格情報を使用して SAS をセキュリティ保護する

Azure PowerShell を使用してユーザー委任 SAS を作成すると、SAS への署名に使用されるユーザー委任キーが暗黙的に作成されます。 SAS に指定した開始時刻と有効期限は、ユーザー委任キーの開始時刻と有効期限としても使用されます。 

ユーザー委任キーの最大有効期間は開始日から 7 日であるため、開始時刻から 7 日以内の SAS の有効期限を指定する必要があります。 ユーザー委任キーの有効期限が切れると SAS は無効になるため、有効期限が 7 日を超える SAS でも、7 日間だけ有効になります。

Azure PowerShell を使用してコンテナーまたは BLOB のユーザー委任 SAS を作成するには、まず、`-UseConnectedAccount` パラメーターを指定して新しい Azure Storage コンテキスト オブジェクトを作成します。 `-UseConnectedAccount` パラメーターは、コマンドで、サインインに使用した Azure AD アカウントの下にコンテキスト オブジェクトを作成することを指定します。

山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>コンテナーのユーザー委任 SAS を作成する

コンテナーのユーザー委任 SAS トークンを返すには、[New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) コマンドを呼び出し、前に作成した Azure Storage コンテキスト オブジェクトを渡します。

次の例では、コンテナーのユーザー委任 SAS トークンを返します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

返されるユーザー委任 SAS トークンは次のようになります。

```
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>BLOB のユーザー委任 SAS を作成する

BLOB のユーザー委任 SAS トークンを返すには、[New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) コマンドを呼び出し、前に作成した Azure Storage コンテキスト オブジェクトを渡します。

次の構文では、BLOB のユーザー委任 SAS を返します。 この例では、`-FullUri` パラメーターを指定しており、これは SAS トークンを追加した BLOB URI を返します。 角かっこ内のプレースホルダー値を独自の値で置き換えてください。

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

返されるユーザー委任 SAS URI は次のようになります。

```
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

## <a name="next-steps"></a>次の手順

- [ユーザー委任 SAS を作成する (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [ユーザー委任キーの取得操作](/rest/api/storageservices/get-user-delegation-key)
