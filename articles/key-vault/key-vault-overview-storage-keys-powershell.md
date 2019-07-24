---
title: Azure Key Vault のマネージド ストレージ アカウント - PowerShell バージョン
description: マネージド ストレージ アカウント機能によって、Azure Key Vault と Azure ストレージ アカウント間がシームレスに統合されます。
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 9b6089aa828b5667f100c1a8cbff3e69345e4512
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405099"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Azure Key Vault のマネージド ストレージ アカウント - PowerShell

> [!NOTE]
> [現在、Azure ストレージと Azure Active Directory (Azure AD) の統合はプレビュー段階です](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)。 認証と承認には、Azure Key Vault と同様に、Azure ストレージへの OAuth2 トークンベースのアクセスを提供する Azure AD を使用することをお勧めします。 こうすることで以下の操作が可能になります。
> - ストレージ アカウントの資格情報ではなく、アプリケーションまたはユーザーの ID を使用してクライアント アプリケーションを認証する。 
> - Azure 上で実行する場合に [Azure AD マネージド ID](/azure/active-directory/managed-identities-azure-resources/) を使用する。 マネージド ID を使用すると、クライアント認証を併用し、アプリケーションに資格情報を保存する必要がなくなります。
> - 承認の管理にロール ベースのアクセス制御 (RBAC) を使用する (これも Key Vault でサポートされています)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure ストレージ アカウント](/azure/storage/storage-create-storage-account)は、アカウント名とキーで構成される資格情報を使用します。 キーは自動生成され、暗号キーではなく "パスワード" として機能します。 Key Vault は、このようなストレージ アカウント キーを [Key Vault のシークレット](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)として保存することで管理できます。 

## <a name="overview"></a>概要

Key Vault のマネージド ストレージ アカウント機能では、次のように一部の管理機能が自動的に実行されます。

- Azure ストレージ アカウントを使用してキーを一覧表示 (同期) する。
- 定期的にキーを再生成 (ローテーション) する。
- ストレージ アカウントと従来のストレージ アカウントの両方のキーを管理します。
- キーの値は、呼び出し元に応答で返されることはありません。

マネージド ストレージ アカウント キー機能を使用する場合:

- **ストレージ アカウント キーの管理は Key Vault にのみ許可します。** Key Vault のプロセスと干渉するため、自身で管理しないでください。
- **複数の Key Vault オブジェクトによってストレージ アカウント キーを管理しないでください**。
- **ストレージ アカウント キーを手動で再生成しないでください**。 Key Vault を介して再生成することをお勧めします。

次の例は、ストレージ アカウント キーの管理を Key Vault に許可する方法を示しています。

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Key Vault にストレージ アカウントへのアクセスを承認する

> [!IMPORTANT]
> Azure AD テナントからは、登録された各アプリケーションに**[サービス プリンシパル](/azure/active-directory/develop/developer-glossary#service-principal-object)** が提供されます。これはアプリケーションの ID として機能します。 このサービス プリンシパルのアプリケーション ID は、ロールベースのアクセス制御 (RBAC) を介して、他の Azure リソースにアクセスするための承認を与えるときに使用されます。 Key Vault は Microsoft アプリケーションなので、各 Azure クラウド内のすべての Azure AD テナントでは、次のように同じアプリケーション ID で事前登録されています。
> - Azure Government の場合、Azure AD テナントにはアプリケーション ID `7e7c393b-45d0-48b1-a35e-2905ddf8183c` が使用される可能性があります。
> - Azure パブリック クラウドとその他すべてのクラウドの場合、Azure AD テナントにはアプリケーション ID `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` が使用されます。

Key Vault からストレージ アカウント キーにアクセスして管理できるようにするには、ストレージ アカウントへのアクセスを承認する必要があります。 Key Vault アプリケーションでは、ストレージ アカウントのキーを "*一覧表示*" し "*再生成*" するアクセス許可が必要です。 このようなアクセス許可は、組み込みの RBAC ロールである[ストレージ アカウント キー オペレーター サービス ロール](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)で有効になります。 

次の手順に従って、このロールを Key Vault サービス プリンシパルに割り当て、範囲をそのストレージ アカウントに限定します。 スクリプトを実行する前に、必ず `$resourceGroupName`、`$storageAccountName`、`$storageAccountKey`、および `$keyVaultName` の変数を更新してください。

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Authenticate your PowerShell session with Azure AD, for use with Azure Resource Manager cmdlets
$azureProfile = Connect-AzAccount

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
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

Key Vault が既にストレージ アカウントのロールに追加されている場合は、*"ロールの割り当ては既に存在します"* " というエラーが発生する場合があります。 Azure portal でストレージ アカウントの [アクセス制御 (IAM)] ページを使用して、ロールの割り当てを確認することもできます。  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>マネージド ストレージ アカウントにユーザー アカウントのアクセス許可を与える

>[!TIP] 
> Azure AD からアプリケーションの ID に対して**サービス プリンシパル**が提供される場合と同様に、ユーザーの ID には**ユーザー プリンシパル**が提供されます。 ユーザー プリンシパルには、Key Vault のアクセス ポリシーのアクセス許可を使用して、Key Vault にアクセスする承認を与えることができます。

同じ PowerShell セッションを使用して、マネージド ストレージ アカウントの Key Vault アクセス ポリシーを更新します。 この手順では、ストレージ アカウントのアクセス許可をユーザー アカウントに適用し、マネージド ストレージ アカウントの機能にアクセスできるようにします。 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $azureProfile.Context.Account.Id -PermissionsToStorage get, list, listsas, delete, set, update, regeneratekey, recover, backup, restore, purge
```

ストレージ アカウントのアクセス許可は、Azure portal のストレージ アカウントの [アクセス ポリシー] ページで使用できないことに注意してください。

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Key Vault インスタンスにマネージド ストレージ アカウントを追加する

同じ PowerShell セッションを使用して、Key Vault インスタンスにマネージド ストレージ アカウントを作成します。 `-DisableAutoRegenerateKey` スイッチには、ストレージ アカウント キーを再生成しないことを指定します。

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

Key Vault でストレージ アカウント キーを定期的に再生成する場合は、再生成期間を設定できます。 次の例では、3 日間の再生成期間を設定します。 この日数の後、Key Vault は "key1" を再生成し、アクティブ キーを "key2" から "key1" に切り替えます。

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

## <a name="next-steps"></a>次の手順

- [マネージド ストレージ アカウント キーのサンプル](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [キー、シークレット、証明書について](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell リファレンス](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
