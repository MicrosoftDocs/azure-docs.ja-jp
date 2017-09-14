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
ms.date: 07/25/2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 3148088c88236c64e089fd25c98eb8ac7cdcbfea
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="azure-key-vault-storage-account-keys"></a>Azure Key Vault ストレージ アカウント キー

Azure Key Vault ストレージ アカウント キーがなかったときは、開発者は自分たちの Azure Storage アカウント (ASA) キーを管理し、手動または外部オートメーションによって回す必要がありました。 現在は、Key Vault ストレージ アカウント キーは [Key Vault シークレット](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets)として実装され、Azure Storage アカウントで認証するようになりました。 

ASA のキー機能がユーザーの代わりにシークレット ローテーションを管理します。また、Shared Access Signature (SAS) をメソッドとして提供することにより、ASA キーを直接扱う必要がなくなりました。 

Azure Storage アカウントの概要情報については、「[Azure Storage アカウントについて](https://docs.microsoft.com/azure/storage/storage-create-storage-account)」をご覧ください。

## <a name="supporting-interfaces"></a>インターフェイスのサポート

Azure Storage アカウント キーの機能は、REST、.NET/C#、PowerShell の各インターフェイスで最初は使用できます。 詳細については、「[Key Vault のドキュメント](https://docs.microsoft.com/azure/key-vault/)」をご覧ください。


## <a name="storage-account-keys-behavior"></a>ストレージ アカウント キーの動作

### <a name="what-key-vault-manages"></a>Key Vault による管理

Key Vault では、ストレージ アカウント キーを使用するときに、いくつかの内部管理機能を自動的に実行します。

1. Azure Key Vault は、Azure Storage アカウント (ASA) のキーを管理します。 
    - 内部的には、Azure Key Vault では、Azure Storage アカウントを使用してキーの一覧表示 (同期) ができます。  
    - Azure Key Vault は定期的にキーを再生成 (回転) します。 
    - キーの値は、呼び出し元に応答で返されることはありません。 
    - Azure Key Vault では、ストレージ アカウントと従来のストレージ アカウントの両方のキーを管理します。 
2. Azure Key Vault では、コンテナーやオブジェクトの所有者は SAS (アカウントまたはサービス SAS) 定義を作成できます。 
    - SAS 値は SAS 定義を使用して作成され、REST URI パスを経由してシークレットとして返されます。 詳しくは、「[Azure Key Vault storage account operations](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)」(Azure Key Vault ストレージ アカウントの操作) をご覧ください。

### <a name="naming-guidance"></a>名前付けのガイダンス

ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。  
 
SAS 定義名は長さが 1 文字から 102 文字までで、0 ～ 9、a ～ z、A ～ Z のみを含む必要があります。

## <a name="developer-experience"></a>開発者エクスペリエンス 

### <a name="before-azure-key-vault-storage-keys"></a>Azure Key Vault ストレージ キー以前 

開発者は、Azure storage にアクセスにするために、ストレージ アカウント キーで以下のことを行う必要がありました。 
 
 ```
//create storage account using connection string containing account name 
// and the storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>Azure Key Vault ストレージ キー以後 

```
//Please make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Use PowerShell command to get Secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  
-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If SAS token is about to expire then Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);

  ```
 
 ### <a name="developer-best-practices"></a>開発者のベスト プラクティス 

- ASA キーの管理は、Key Vault のみを許可します。 Key Vault のプロセスと干渉するため、自身で管理しないでください。 
- 複数の Key Vault オブジェクトによって ASA キーを管理しないでください。 
- ASA キーを手動で再生成する必要がある場合は、Key Vault を使用して ASA キーを再生成することをお勧めします。 

## <a name="getting-started"></a>使用の開始

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>ロール ベースのアクセス制御 (RBAC) の権限設定

Key Vault では、ストレージ アカウントのキーを*一覧表示*し*再生成*する権限が必要です。 次の手順に従ってこれらのアクセス許可をセットアップします。

- Key Vault の ObjectId を取得します。 

    `Get-AzureRmADServicePrincipal -SearchString "AzureKeyVault"`

- Azure Key Vault ID に Storage Key Operator ロールを割り当てます。 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > 従来の種類のアカウントには、ロールのパラメーターを *"Classic Storage Account Key Operator Service Role"* と設定します。

### <a name="storage-account-onboarding"></a>ストレージ アカウントのオンボード 

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

