---
title: コードでの Shared Access Signature トークンの取得 | Azure Key Vault
description: マネージド ストレージ アカウント機能によって、Azure Key Vault と Azure ストレージ アカウント間がシームレスに統合されます。
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 8ca89d06ea0d5e2396c820b25490b30e25c99f10
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002931"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>コードでの Shared Access Signature トークンの取得

キー コンテナー内に格納されている Shared Access Signature (SAS) トークンを使用して、ご利用のストレージ アカウントを管理できます。 詳細については、[SAS を使用した Azure Storage リソースへの制限付きアクセスの許可](../../storage/common/storage-sas-overview.md)に関するページを参照してください。

この記事では、SAS トークンを取得し、それを使用して操作を実行する .NET コードの例を示します。 SAS トークンを作成して保存する方法の詳細については、「[Key Vault と Azure CLI を使用してストレージ アカウント キーを管理する](overview-storage-keys.md)」または「[Key Vault と Azure PowerShell を使用してストレージ アカウント キーを管理する](overview-storage-keys-powershell.md)」を参照してください。

## <a name="code-samples"></a>コード サンプル

この例では、コードはキー コンテナーから SAS トークンをフェッチし、それを使用して新しいストレージ アカウントを作成し、新しい Blob service クライアントを作成します。

```cs
// The shared access signature is stored as a secret in keyvault. 
// After you get a security token, create a new SecretClient with vault credentials and the key vault URI.
// The format for the key vault URI (kvuri) is https://<YourKeyVaultName>.vault.azure.net

var kv = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

// Now retrive your storage SAS token from Key Vault using the name of the secret (secretName).

KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;

// Create new storage credentials using the SAS token.
StorageCredentials accountSAS = new StorageCredentials(sasToken);

// Use these credentials and your storage account name to create a Blob service client.
CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();
```

お使いの Shared Access Signature トークンの有効期限が間もなく切れる場合は、キー コンテナーから Shared Access Signature トークンをフェッチして、コードを更新できます。

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>次のステップ
- [SAS を使用して Azure Storage リソースへの制限付きアクセスを許可する](../../storage/common/storage-sas-overview.md)方法について学習します。
- [Azure PowerShell](overview-storage-keys-powershell.md) または [Azure CLI と Key Vault を使用してストレージ アカウント キーを管理する](overview-storage-keys.md)方法を確認する。
- [マネージド ストレージ アカウント キーのサンプル](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)を参照する
