---
title: Azure Key Vault のマネージド ストレージ アカウント - PowerShell バージョン
description: マネージド ストレージ アカウント機能によって、Azure Key Vault と Azure ストレージ アカウント間がシームレスに統合されます。
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 75561c643c2b8881e4b390be885b77f2fff4971d
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673451"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>コードでの Shared Access Signature トークンの取得

キー コンテナー内の [Shared Access Signature トークン](../storage/common/storage-dotnet-shared-access-signature-part-1.md)を使用して、ストレージ アカウントを管理できます。 この記事では、SAS トークンをフェッチし、それを使用して操作を実行する C# コードの例を示します。  SAS トークンを作成して保存する方法の詳細については、「[Key Vault と Azure CLI を使用してストレージ アカウント キーを管理する](key-vault-ovw-storage-keys.md)」または「[Key Vault と Azure PowerShell を使用してストレージ アカウント キーを管理する](key-vault-overview-storage-keys-powershell.md)」を参照してください。

## <a name="code-samples"></a>コード サンプル

この例では、コードはキー コンテナーから SAS トークンをフェッチし、それを使用して新しいストレージ アカウントを作成し、新しい Blob service クライアントを作成します。  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

お使いの Shared Access Signature トークンの有効期限が間もなく切れる場合は、キー コンテナーから Shared Access Signature トークンをフェッチして、コードを更新できます。

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>次の手順
- [Azure PowerShell](key-vault-overview-storage-keys-powershell.md) または [Azure CLI と Key Vault を使用してストレージ アカウント キーを管理する](key-vault-ovw-storage-keys.md)方法を確認する。
- [マネージド ストレージ アカウント キーのサンプル](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)を参照する
- [キー、シークレット、証明書について](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell リファレンス](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
