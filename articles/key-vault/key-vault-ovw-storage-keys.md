---
ms.assetid: 
title: "Azure Key Vault ストレージ アカウント キー"
description: "ストレージ アカウント キーは、Azure Key Vault と Azure Storage アカウントへのキー ベースのアクセス間をシームレスに統合します。"
ms.topic: article
services: key-vault
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 09/14/2017
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: b7b8583e8923e65ff068a2bec060a27a14905485
ms.contentlocale: ja-jp
ms.lasthandoff: 09/15/2017

---
# <a name="azure-key-vault-storage-account-keys"></a>Azure Key Vault ストレージ アカウント キー

Azure Key Vault ストレージ アカウント キーがなかったときは、開発者は自分たちの Azure Storage アカウント (ASA) キーを管理し、手動または外部オートメーションによって回す必要がありました。 現在は、Key Vault ストレージ アカウント キーは [Key Vault シークレット](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets)として実装され、Azure Storage アカウントで認証するようになりました。 

Azure Storage アカウント (ASA) の主要機能により、シークレットのローテーションが管理されます。 また、Shared Access Signature (SAS) をメソッドとして提供することにより、ASA キーを直接扱う必要がなくなりました。 

Azure Storage アカウントの概要情報については、「[Azure Storage アカウントについて](https://docs.microsoft.com/azure/storage/storage-create-storage-account)」をご覧ください。

## <a name="supporting-interfaces"></a>インターフェイスのサポート

Azure Storage アカウント キーの機能は、REST、.NET/C#、PowerShell の各インターフェイスで最初は使用できます。 詳細については、「[Key Vault のドキュメント](https://docs.microsoft.com/azure/key-vault/)」をご覧ください。


## <a name="what-key-vault-manages"></a>Key Vault による管理

Key Vault では、管理対象ストレージ アカウント キーを使用するときに、いくつかの内部管理機能を自動的に実行します。

- Azure Key Vault は、Azure Storage アカウント (ASA) のキーを管理します。
    - 内部的には、Azure Key Vault では、Azure Storage アカウントを使用してキーの一覧表示 (同期) ができます。  
    - Azure Key Vault は定期的にキーを再生成 (回転) します。 
    - キーの値は、呼び出し元に応答で返されることはありません。 
    - Azure Key Vault では、ストレージ アカウントと従来のストレージ アカウントの両方のキーを管理します。 
- Azure Key Vault では、コンテナーやオブジェクトの所有者は SAS (アカウントまたはサービス SAS) 定義を作成できます。
    - SAS 値は SAS 定義を使用して作成され、REST URI パスを経由してシークレットとして返されます。 詳しくは、「[Azure Key Vault storage account operations](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)」(Azure Key Vault ストレージ アカウントの操作) をご覧ください。

## <a name="naming-guidance"></a>名前付けのガイダンス

- ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。  
- SAS 定義名は長さが 1 文字から 102 文字までで、0 ～ 9、a ～ z、A ～ Z のみを含む必要があります。

## <a name="developer-experience"></a>開発者エクスペリエンス

### <a name="before-azure-key-vault-storage-keys"></a>Azure Key Vault ストレージ キー以前 

開発者は、Azure storage にアクセスにするために、ストレージ アカウント キーで以下のことを行う必要がありました。 
 
```powershell
//create an Azure Storage Account using a connection string containing an account name and a storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>Azure Key Vault ストレージ キー以後 

```powershell
//Make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Get secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  

-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get a SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If your SAS token is about to expire, Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>開発者ガイダンス

- ASA キーの管理は、Key Vault のみを許可します。 Key Vault のプロセスと干渉するため、自身で管理しないでください。 
- 複数の Key Vault オブジェクトによって ASA キーを管理しないでください。 
- ASA キーを手動で再生成する必要がある場合は、Key Vault を使用して ASA キーを再生成することをお勧めします。 

## <a name="getting-started"></a>使用の開始

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>ロール ベースのアクセス制御 (RBAC) の権限設定

Key Vault では、ストレージ アカウントのキーを*一覧表示*し*再生成*する権限が必要です。 次の手順に従ってこれらのアクセス許可をセットアップします。

- Key Vault の ObjectId を取得します。 

    `Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093`
    
     or
     
    `Get-AzureRmADServicePrincipal -SearchString "AzureKeyVault"`

- Azure Key Vault ID に Storage Key Operator ロールを割り当てます。 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > 従来の種類のアカウントには、ロールのパラメーターを *"Classic Storage Account Key Operator Service Role"* と設定します。

## <a name="working-example"></a>実際の例

次の例では、Key Vault が管理する Azure Storage アカウントと、関連した Shared Access Signature (SAS) 定義を作成する方法を示します。

### <a name="assumptions"></a>前提条件

次のステートメントは、この作業例用に指定されたものです。

- ストレージ リソースの場所: */subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1*

- Key Vault の名前: *yourtest1*

### <a name="get-a-service-principal"></a>サービス プリンシパルを取得する

```powershell
Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093
```

上記のコマンドの出力には、*yourServicePrincipalId* という ServicePrincipal が含まれます。 

### <a name="set-permissions"></a>アクセス許可を設定する

ストレージのアクセス許可が *[すべて]* に設定されていることを確認します。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourtest1' -ObjectId yourServicePrincipalId -PermissionsToStorage all
```

### <a name="allow-access"></a>アクセスを許可

管理対象のストレージ アカウントと SAS の定義を作成する前に、ストレージ アカウントに、Key Vault サービスへのアクセスを付与する必要があります。

```powershell
New-AzureRmRoleAssignment -ObjectId yourServicePrincipalId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '/subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1'
```

### <a name="create-storage-account"></a>[ストレージ アカウントの作成]

ここで、管理対象のストレージ アカウントと 2 つの SAS 定義を作成します。 アカウント SAS は、さまざまなアクセス許可を持つ BLOB サービスへのアクセスを提供します。

```powershell
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="regeneration"></a>再生成

次のコマンドを使用して再生成期間を設定します。

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>SAS 定義の設定

Key Vault で、管理対象のストレージ アカウント用に SAS 定義を設定します。

```powershell
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourtest1  -AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName yourtest1  -AccountName msak01 -Name blobsas2 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List,Write
```

### <a name="get-token"></a>トークンを取得する

対応する SAS トークンを取得し、ストレージへの呼び出しを行います。

```powershell
$sasToken1 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas1).SecretValueText
$sasToken2 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas2).SecretValueText
```

### <a name="create-storage"></a>ストレージの作成

*$sastoken1* を使用してアクセスしようとすると失敗しますが、*$sastoken2* を使用するとアクセスできることに注意してください。 

```powershell
$context1 = New-AzureStorageContext -SasToken $sasToken1 -StorageAccountName yourtest1
$context2 = New-AzureStorageContext -SasToken $sasToken2 -StorageAccountName yourtest1
Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt"  -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt"  -Context $context2
```

### <a name="example-summary"></a>概要の例

書き込みアクセス権を持つ SAS トークンを使用して、ストレージ BLOB コンテンツにアクセスできます。

### <a name="relevant-powershell-cmdlets"></a>関連した PowerShell コマンドレット

- [Get-AzureKeyVaultManagedStorageAccount ](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount?view=azurermps-4.3.1)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)

## <a name="storage-account-onboarding"></a>ストレージ アカウントのオンボード 

例: Key Vault オブジェクトの所有者は、ストレージ アカウントをオンボードするために、ストレージ アカウント オブジェクトを Azure Key Vault に追加します。

オンボード時に Key Vault は、オンボードするアカウントの ID にストレージ キーの*一覧表示*と*再生成*の権限があることを確認する必要があります。 これらの権限を確認するために、Key Vault は、認証サービスから OBO (On Behalf Of) トークンを取得し、対象ユーザーを Azure Resource Manager に設定し、Azure Storage サービスの*一覧表示*キーを呼び出します。 *一覧表示*の呼び出しが失敗すると、Key Vault オブジェクトの作成が*禁止*の HTTP 状態コードで失敗します。 この方法で表示されるキーは、キー コンテナー エンティティ ストレージでキャッシュされます。 

Key Vault では、ID がキー再生成の所有権を取得する前に、その ID に*再生成*の権限があることを確認する必要があります。 この ID が、OBO トークン経由で、Key Vault ファースト パーティ ID と同様に権限があることを確認するために、以下のことを行います。

- Key Vault は、ストレージ アカウント リソースに対するRBAC 権限を一覧表示します。
- Key Vault は、アクションとアクション以外の正規表現の照合によって応答を検証します。 

その他の補助的な例は、「[Key Vault に管理されているストレージ アカウント キーのサンプル](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167)」に関するページをご覧ください。

ID に*再生成*の権限がない場合、あるいは Key Vault のファースト パーティ ID に*一覧表示*または*再生成*の権限がない場合、オンボード要求は失敗し、適切なエラー コードとメッセージを返します。 

OBO トークンは、PowerShell または CLI のいずれかのファースト パーティのネイティブ クライアント アプリケーションを使用する場合にのみ機能します。

## <a name="other-applications"></a>他のアプリケーション

- Key Vault ストレージ アカウント キーを使用して構築された SAS トークンは、Azure Storage アカウントへのさらに制御されたアクセスを提供します。 詳細については、[Shared Access Signatures の使用](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1)に関するページをご覧ください。

## <a name="see-also"></a>関連項目

- [キー、シークレット、証明書について](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault チーム ブログ](https://blogs.technet.microsoft.com/kv/)

