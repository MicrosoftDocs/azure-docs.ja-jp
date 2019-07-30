---
title: Azure Key Vault と Azure CLI を使用してストレージ アカウント キーを管理する
description: ストレージ アカウント キーは、Azure Key Vault と Azure Storage アカウントへのキー ベースのアクセス間をシームレスに統合します。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 6ac054bc9750e4297080c4ab64030c9c6a5fb55a
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312847"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Azure Key Vault と Azure CLI を使用してストレージ アカウント キーを管理する 

Azure Key Vault は、Azure Storage アカウントと従来のストレージ アカウントのキーを管理します。 Key Vault のマネージド ストレージ アカウント機能を使用して、一部のキー管理機能を自動的に実行することができます。

[Azure ストレージ アカウント](/azure/storage/storage-create-storage-account)は、アカウント名とキーで構成される資格情報を使用します。 キーは自動生成され、暗号キーではなくパスワードとして機能します。 Key Vault は、ストレージ アカウント キーを [Key Vault のシークレット](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)として格納して管理します。 キーは Azure Storage アカウントと一覧表示 (同期) され、定期的に再生成または_ローテーション_されます。 

マネージド ストレージ アカウント キー機能を使用する場合は、次の点を考慮してください。

- キーの値は、呼び出し元への応答で返されることはありません。
- ストレージ アカウント キーの管理は Key Vault のみが行う必要があります。 キーを自分で管理したり、Key Vault のプロセスに干渉したりしないでください。
- ストレージ アカウント キーの管理は、1 つの Key Vault オブジェクトのみが行う必要があります。 複数のオブジェクトからのキー管理を許可しないでください。
- ユーザー プリンシパルを使用してストレージ アカウントを管理するように Key Vault に要求することはできますが、サービス プリンシパルを使用してそれを行うことはできません。
- キーの再生成は、Key Vault のみを使用して行います。 ストレージ アカウント キーを手動で再生成しないでください。 

> [!NOTE]
> Azure Storage と Azure Active Directory (Azure AD) の統合は、Microsoft のクラウドベースの ID およびアクセス管理サービスです。
> Azure AD の統合は、[Azure BLOB とキュー](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)で使用できます。
> 認証および承認には Azure AD を使用します。
> Azure AD は、Azure Key Vault と同様に、Azure Storage に OAuth2 トークン ベースのアクセスを提供します。
>
> Azure AD では、ストレージ アカウントの資格情報ではなく、アプリケーションまたはユーザーの ID を使用してクライアント アプリケーションを認証することができます。
> Azure で実行するときは、[Azure AD マネージド ID](/azure/active-directory/managed-identities-azure-resources/) を使用できます。 マネージド ID を使用すると、クライアント認証やアプリケーションでの資格情報の保存が不要になります。
> Azure AD は、Key Vault でもサポートされているロール ベースのアクセス制御 (RBAC) を使用して承認を管理します。

### <a name="service-principal-application-id"></a>サービス プリンシパルのアプリケーション ID

Azure AD テナントは、登録されている各アプリケーションに[サービス プリンシパル](/azure/active-directory/develop/developer-glossary#service-principal-object)を提供します。 サービス プリンシパルは、アプリケーション ID として機能します。 アプリケーション ID は、RBAC を介した他の Azure リソースへのアクセスに対する承認のセットアップ時に使用されます。

Key Vault は、すべての Azure AD テナントに事前登録されている Microsoft アプリケーションです。 Key Vault は、同じアプリケーション ID で、各 Azure クラウド内に登録されています。

| テナント | クラウド | アプリケーション ID |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure Public | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| その他  | 任意 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>前提条件

Key Vault を使用してストレージ アカウント キーを管理する前に、前提条件を確認してください。

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) をインストールします。
- [Azure ストレージ アカウント](https://azure.microsoft.com/services/storage/)を作成します。 [これらの手順](https://docs.microsoft.com/azure/storage/)に従います。
- ストレージ アカウント名には小文字と数字のみを使用する必要があります。 名前の長さは 3 文字から 24 文字でなければなりません。        
      
## <a name="manage-storage-account-keys"></a>ストレージ アカウント キーを管理する

Key Vault を使用してストレージ アカウント キーを管理するには、4 つの基本的な手順があります。

1. 既存のストレージ アカウントを取得します。
1. 既存の Key Vault をフェッチします。
1. Key Vault のマネージド ストレージ アカウントをコンテナーに追加します。 `key1` を、再生成期間 180 日でアクティブ キーとして設定します。
1. `key1` を使用して、指定したストレージ アカウント用のストレージ コンテキストを設定します。

> [!NOTE]
> サービスとしての Key Vault には、ストレージ アカウントのオペレーター アクセス許可が割り当てられます。
> 
> Azure Key Vault のマネージド ストレージ アカウント キーを設定した後は、キーの変更は Key Vault を使用してのみ行います。
> マネージド ストレージ アカウント キーの場合、Key Vault はストレージ アカウント キーのローテーションを管理します。

1. ストレージ アカウントを作成したら、次のコマンドを実行して、管理するストレージ アカウントのリソース ID を取得します。
    ```
    az storage account show -n storageaccountname
    ```

    コマンド出力からリソース ID の値をコピーします。
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    出力例:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Key Vault に "ストレージ アカウント キー オペレーターのサービス ロール" の RBAC ロールを割り当てます。 このロールは、アクセス スコープをお使いのストレージ アカウントに制限します。 従来のストレージ アカウントには、"従来のストレージ アカウント キー オペレーターのサービス ロール" のロールを使用します。

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` は、Azure パブリック クラウド内の Key Vault のオブジェクト ID です。 Azure Government クラウド内の Key Vault のオブジェクト ID を取得するには、「[サービス プリンシパルのアプリケーション ID](#service-principal-application-id)」をご覧ください。
    
1. Key Vault のマネージド ストレージ アカウントを作成します。

    90 日間の再生成期間を設定します。 90 日後、Key Vault は `key1` を再生成し、アクティブ キーを `key2` から `key1` に交換します。 そして、`key1` がアクティブ キーとしてマークされます。 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>トークンを作成および生成する

Shared Access Signature トークンを生成するように Key Vault に指示することもできます。 Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 アカウント キーを共有することなく、ストレージ アカウント内のリソースへのアクセス権をクライアントに付与できます。 Shared Access Signature により、アカウント キーを侵害されることなくストレージ リソースを安全に共有することができます。

このセクションのコマンドは、次の操作を実行します。

- アカウントの Shared Access Signature 定義 `<YourSASDefinitionName>` を設定します。 この定義は、Key Vault `<VaultName>` 内の Key Vault マネージド ストレージ アカウント `<YourStorageAccountName>` に設定されます。
- BLOB、ファイル、テーブル、およびキューの各サービスに対してアカウントの Shared Access Signature トークンを作成します。 サービス、コンテナー、およびオブジェクトのリソースの種類に対してトークンが作成されます。 トークンは、すべてのアクセス許可を持ち、https で、指定した開始日と終了日を使用して作成されます。
- Key Vault マネージド ストレージの Shared Access Signature 定義をコンテナーに設定します。 この定義には、作成された Sared Access Signature トークンのテンプレート URI があります。 この定義は、Shared Access Signature の種類 `account` を持っており、N 日間有効です。
- Shared Access Signature 定義に対応する Key Vault シークレットから実際のアクセス トークンを取得します。

前のセクションの手順を完了したら、次のコマンドを実行して、Shared Access Signature トークンを生成するように Key Vault に指示します。 

1. Shared Access Signature 定義を作成します。 Shared Access Signature 定義が作成されたら、追加の Shared Access Signature トークンを生成するように Key Vault に指示します。 この操作には `storage` と `setsas` のアクセス許可が必要です。
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    この操作のヘルプについては、「[az storage account generate-sas](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)」参照ドキュメントをご覧ください。

    操作が正常に実行されたら、出力をコピーします。
    ```console
       "se=2020-01-01&sp=***"
    ```

1. 前のコマンドで生成された `$sasToken` を使用し、Shared Access Signature 定義を作成します。 コマンド パラメーターの詳細については、「[az keyvault storage sas-definition create](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters)」参照ドキュメントをご覧ください。
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    ユーザーがストレージ アカウントへのアクセス許可を持っていない場合は、まずユーザーのオブジェクト ID を取得します。
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>コードでトークンをフェッチする

Key Vault から [Shared Access Signature トークン](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)をフェッチして、ストレージ アカウントに対する操作を実行します。

次の 3 つの方法で Key Vault を認証します。

- マネージド サービス ID を使用します。 この方法を強くお勧めします。
- サービス プリンシパルと証明書を使用します。 
- サービス プリンシパルとパスワードを使用します。 この方法は推奨されません。

詳細については、「[Azure Key Vault:基本的な概念](key-vault-whatis.md#basic-concepts)」を参照してください。

次の例では、Shared Access Signature トークンをフェッチする方法を示します。 Shared Access Signature 定義を作成したら、トークンをフェッチします。 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

お使いの Shared Access Signature トークンの有効期限が間もなく切れる場合は、Key Vault からもう一度 Shared Access Signature トークンをフェッチして、コードを更新します。

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Azure CLI コマンド

マネージド ストレージ アカウントに関連する Azure CLI コマンドの詳細については、「[az keyvault storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)」参照ドキュメントをご覧ください。

## <a name="next-steps"></a>次の手順

- [キー、シークレット、証明書](https://docs.microsoft.com/rest/api/keyvault/)について学習します。
- [Azure Key Vault チームのブログ](https://blogs.technet.microsoft.com/kv/)の記事を確認します。
