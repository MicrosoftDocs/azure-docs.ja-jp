---
title: Azure Key Vault のマネージド ストレージ アカウント - PowerShell バージョン
description: マネージド ストレージ アカウント機能によって、Azure Key Vault と Azure ストレージ アカウント間がシームレスに統合されます。
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3bced101516e91259ea9018fe3c4aa44f867cbe6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023110"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Key Vault と Azure PowerShell を使用してストレージ アカウント キーを管理する
> [!IMPORTANT]
> Azure Storage と Azure Active Directory (Azure AD) の統合 (Microsoft のクラウドベースの ID およびアクセス管理サービス) を使用することをお勧めします。 Azure AD 統合は [Azure BLOB およびキュー](../../storage/common/storage-auth-aad.md)で利用できます。また、Azure Key Vault と同様に、Azure Storage へのトークンベースの OAuth2 アクセスが提供されます。
> Azure AD では、ストレージ アカウントの資格情報ではなく、アプリケーションまたはユーザーの ID を使用してクライアント アプリケーションを認証することができます。 Azure で実行するときは、[Azure AD マネージド ID](../../active-directory/managed-identities-azure-resources/index.yml) を使用できます。 マネージド ID を使用すると、クライアント認証やアプリケーションでの資格情報の保存が不要になります。 Azure AD 認証が不可能な場合にのみ、以下のソリューションを使用してください。

Azure ストレージ アカウントでは、アカウント名とキーで構成された資格情報が使用されます。 キーは自動生成され、暗号キーではなくパスワードとして機能します。 Key Vault では、ストレージ アカウントでストレージ アカウント キーを定期的に再生成することでそのようなキーの管理が行われることに加え、ストレージ アカウント内のリソースへの委任アクセス用の Shared Access Signature トークンが提供されます。

Key Vault マネージド ストレージ アカウント キー機能を使用して、Azure ストレージ アカウントのキーを一覧表示し (同期)、定期的にキーを再生成 (ローテーション) できます。 ストレージ アカウントと従来のストレージ アカウントの両方のキーを管理できます。

マネージド ストレージ アカウント キー機能を使用する場合は、次の点を考慮してください。

- キーの値は、呼び出し元への応答で返されることはありません。
- ストレージ アカウント キーの管理は Key Vault のみが行う必要があります。 キーを自分で管理したり、Key Vault のプロセスに干渉したりしないでください。
- ストレージ アカウント キーの管理は、1 つの Key Vault オブジェクトのみが行う必要があります。 複数のオブジェクトからのキー管理を許可しないでください。
- キーの再生成は、Key Vault のみを使用して行います。 ストレージ アカウント キーを手動で再生成しないでください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>サービス プリンシパルのアプリケーション ID

Azure AD テナントは、登録されている各アプリケーションに[サービス プリンシパル](../../active-directory/develop/developer-glossary.md#service-principal-object)を提供します。 サービス プリンシパルはアプリケーション ID として機能し、Azure RBAC を介した他の Azure リソースへのアクセスに対する承認のセットアップ時に使用されます。

Key Vault は、すべての Azure AD テナントに事前登録されている Microsoft アプリケーションです。 Key Vault は、各 Azure クラウド内に同じアプリケーション ID で登録されています。

| テナント | クラウド | アプリケーション ID |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure Public | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| その他  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>前提条件

このガイドを完了するには、まず次の手順を実行する必要があります。

- [Azure PowerShell モジュールをインストールします](/powershell/azure/install-az-ps)。
- [Key Vault を作成します](quick-create-powershell.md)
- [Azure Storage アカウント](../../storage/common/storage-account-create.md?tabs=azure-powershell)を作成します。 ストレージ アカウント名には小文字と数字のみを使用する必要があります。 名前の長さは 3 文字から 24 文字でなければなりません。


## <a name="manage-storage-account-keys"></a>ストレージ アカウント キーを管理する

### <a name="connect-to-your-azure-account"></a>Azure アカウントに接続する

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して PowerShell セッションを認証します。

```azurepowershell-interactive
Connect-AzAccount
```
複数の Azure サブスクリプションを持っている場合は、[Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) コマンドレットを使用してそれらを一覧表示し、[Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットで使用するサブスクリプションを指定できます。

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>変数の設定

まず、次の手順で、PowerShell コマンドレットによって使用される変数を設定します。 プレースホルダー "YourResourceGroupName"、"YourStorageAccountName"、"YourKeyVaultName" を更新し、`cfa8b339-82a2-471a-a3c9-0fc0be7a4093` に $keyVaultSpAppId を設定します (上記の「[サービス プリンシパルのアプリケーション ID](#service-principal-application-id)」で指定)。

Azure ストレージ アカウントのユーザー ID とコンテキストを取得するために、Azure PowerShell の [Get-AzContext](/powershell/module/az.accounts/get-azcontext) および [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) コマンドレットも使用します。

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1" #(key1 or key2 are allowed)

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

```
>[!Note]
> 従来のストレージ アカウントの場合、$storageAccountKey には、"primary" と "secondary" を使用します。 <br>
> 従来のストレージ アカウント用の "Get-AzStorageAccount" ではなく、"Get-AzResource -Name "ClassicStorageAccountName" -ResourceGroupName $resourceGroupName" を使用してください。

### <a name="give-key-vault-access-to-your-storage-account"></a>Key Vault にストレージ アカウントへのアクセス権を付与する

Key Vault からストレージ アカウント キーにアクセスして管理できるようにするには、ストレージ アカウントへのアクセスを承認する必要があります。 Key Vault アプリケーションでは、ストレージ アカウントのキーを "*一覧表示*" し "*再生成*" するアクセス許可が必要です。 このようなアクセス許可は、Azure 組み込みロールである[ストレージ アカウント キー オペレーターのサービス ロール](../../role-based-access-control/built-in-roles.md#storage-account-key-operator-service-role)で有効になります。

Azure PowerShell の [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) コマンドレットを使用して、このロールを Key Vault サービス プリンシパルに割り当て、範囲をそのストレージ アカウントに限定します。

```azurepowershell-interactive
# Assign Azure role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role."
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

ロールの割り当てが成功すると、次のような出力が表示されます。

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Key Vault が既にストレージ アカウントのロールに追加されている場合は、 *"ロールの割り当ては既に存在します"* " というエラーが発生する場合があります。 Azure portal でストレージ アカウントの [アクセス制御 (IAM)] ページを使用して、ロールの割り当てを確認することもできます。

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>マネージド ストレージ アカウントにユーザー アカウントのアクセス許可を与える

Azure PowerShell の [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) コマンドレットを使用して、Key Vault アクセス ポリシーを更新し、ストレージ アカウントのアクセス許可をユーザー アカウントに付与します。

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

ストレージ アカウントのアクセス許可は、Azure portal のストレージ アカウントの [アクセス ポリシー] ページで使用できないことに注意してください。

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Key Vault インスタンスにマネージド ストレージ アカウントを追加する

Azure PowerShell の [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) コマンドレットを使用して、Key Vault インスタンスにマネージド ストレージ アカウントを作成します。 `-DisableAutoRegenerateKey` スイッチには、ストレージ アカウント キーを再生成しないことを指定します。

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

キーの再生成を行わないストレージ アカウントの追加に成功すると、次の例のような出力が表示されます。

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                :
```

### <a name="enable-key-regeneration"></a>キーの再生成を有効にする

Key Vault でストレージ アカウント キーを定期的に再生成する場合は、Azure PowerShell の [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) コマンドレットを使用して、再生成期間を設定できます。 この例では、3 日間の再生成期間を設定します。 ローテーションする時間になると、Key Vault はアクティブでないキーを再生成し、新しく作成されたキーをアクティブとして設定します。 SAS トークンを発行するために使用されるキーは、常に 1 つだけです。 これがアクティブなキーです。

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

キーの再生成を行うストレージ アカウントの追加に成功すると、次の例のような出力が表示されます。

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                :
```

## <a name="shared-access-signature-tokens"></a>Shared Access Signature トークン

Shared Access Signature トークンを生成するように Key Vault に指示することもできます。 Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 アカウント キーを共有することなく、ストレージ アカウント内のリソースへのアクセス権をクライアントに付与できます。 Shared Access Signature により、アカウント キーを侵害されることなくストレージ リソースを安全に共有することができます。

このセクションのコマンドは、次の操作を実行します。

- アカウントの Shared Access Signature 定義を設定します。
- BLOB、ファイル、テーブル、およびキューの各サービスに対してアカウントの Shared Access Signature トークンを作成します。 サービス、コンテナー、およびオブジェクトのリソースの種類に対してトークンが作成されます。 トークンは、すべてのアクセス許可を持ち、https で、指定した開始日と終了日を使用して作成されます。
- Key Vault マネージド ストレージの Shared Access Signature 定義をコンテナーに設定します。 この定義には、作成された Sared Access Signature トークンのテンプレート URI があります。 この定義は、Shared Access Signature の種類 `account` を持っており、N 日間有効です。
- Shared Access Signature がキー コンテナーにシークレットとして保存されていることを確認します。
-
### <a name="set-variables"></a>変数の設定

まず、次の手順で、PowerShell コマンドレットによって使用される変数を設定します。 プレースホルダー <YourStorageAccountName> および <YourKeyVaultName> を更新してください。

Azure ストレージ アカウントのコンテキストを取得するために、Azure PowerShell の [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) コマンドレットも使用します。

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>Shared Access Signature トークンの作成

Azure PowerShell の [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken) コマンドレットを使用して、Shared Access Signatur 定義を作成します。

```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
$SasToken の値は、次のようになります。

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Shared Access Signature 定義の生成

Azure PowerShell の [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition) コマンドレットを使用して、Shared Access Signature 定義を作成します。  `-Name` パラメーターには任意の名前を指定できます。

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Shared Access Signature 定義の検証

Shared Access Signature 定義がキー コンテナーに格納されていることを確認するには、Azure PowerShell の [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) コマンドレットを使用します。

最初に、キー コンテナーで Shared Access Signature 定義を見つけます。

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

SAS 定義に対応するシークレットには、次のプロパティがあります。

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

[Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) コマンドレットと、`VaultName` および `Name` プロパティを使用して、そのシークレットの内容を表示できます。

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

このコマンドの出力で SAS 定義文字列が表示されます。

## <a name="next-steps"></a>次のステップ

- [マネージド ストレージ アカウント キーのサンプル](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Key Vault PowerShell リファレンス](/powershell/module/az.keyvault/#key_vault)
